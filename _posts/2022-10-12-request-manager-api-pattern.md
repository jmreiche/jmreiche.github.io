---
title: "Request Manager API Pattern"
layout: post
date: 2022-10-12 06:46:04
categories: programming
---

Coding guidelines and code reviews are good ways to ensure code quality and enforce structure. But they also have their limitations. When time is in short demand and the lead developer/architect have no chance of keeping up with the new code being developed, the architecture, structure and thereby quality of the code-base declines. This makes it more prone to errors, which in turn are harder to fix. Furthermore random code structure also makes it hard for new/other developers to comprehend code in new business areas and understand the basics. They do not understand the business area and they do not understand the structure and therefore have no point of reference.

Therefore setting and **keeping** code structure and guidelines is a very smart move. This Request Manager API Pattern helps with exactly this.

But how do we go about doing this in a way that is sustainable even in a hectic project with a tight deadline(As they all are). Well enter generics and base classes. With clever use of those and a little help from general structuring of code, we can actually make sure that the code fulfills the needs we have – no matter who implements it – and further more make it easier to review and understand for new/other developers.

## The story and origination of this pattern

So while I will lay claims to having come up with this pattern for the project in which I used it, I won't claim that I am the only one to come up with something like it or that everything came out of thin air in my head. Part of this pattern is actually taken from another pattern a very good architect and old mentor of mine used to poll a database that acted as "worker queue" for an old airline system. That pattern planted the seed of using generics and abstract methods to "force" a good implementation on the implementers. But the distinct implementation and use of these tools in this pattern came from my head and the problem I was asked to solve.

The project was a rather big one with 10-12 developers when we were most. A combination of permanent employees and freelancers (whih I also was) and also spread out across different countries. What quickly became clear to me was that we had to bring some structure to both the layers of code but also actual implementations, to not drown in code reviews and differnt implementations. 

The biggest road block was that everyone was already started on their implementation of the API they had the responsibility for. So asking a lot of people to re-implement and change their code was a very difficult decision to make. But I am very glad that I did and as you can read at the end of this it also payed of in an unexpected and fun way.

I will introduce the Request Manager API Pattern with a simple scenario and try my best to explain it with both text and code. The entire repository is free and hosted on BitBucket so you can clone it and play aroud with it. I will provide the link at the end.

## Setting the scene

For this example we have a scenario where we are building a SaaS product. This specific product has several business specific API endpoints that each has a number of operations/methods. 

Across all methods/operations we have common things that needs to be validated, primarily to do with authorization in one way or another. These are things that all implementers(developers) need to take into account and therefore a prime candidate for general implementation. 

Furthermore, apart from the general validation of general properties, it is required that all operations/methods validate the incoming request with their own business specific validation for the operation/method. 

The actual validation of this, we cannot generalize, as it is request-specific. But we can “force” the developer to actually validate an incoming request before working on it. And further more also make the developers implement handling of invalid and potentially “warning” requests. 

## Creating request and response base classes

To do so, we will make good use of inheritance and generics. First we need base classes for requests and responses.

```c#
public abstract class RequestBase
{
		// This is empty for now but could have properties shared among all		        
}
```

```c#
/// <summary>
/// Base class for all responses
/// </summary>
public abstract class ResponseBase
{
  /// <summary>
  /// Error code for the specific error
  /// </summary>
  public int ErrorCode { get; set; }
  /// <summary>
  /// Error level
  /// </summary>
  public ErrorLevelType ErrorLevel { get; set; }
  /// <summary>
  /// Error message
  /// </summary>
  public string ErrorMessage { get; set; }
}
```

In the example included I have actually made two levels of base classes, as I have RequestBase and ResponseBase that are at the top of the hierarchy and then in this case; ExternalRequestBase and ExternalResponseBase just below. 

```c#
public class ExternalRequestBase : RequestBase
{
	public int ApplicationId { get; set; }
}
```

```c#
public class ExternalResponseBase : ResponseBase
{
  // This just inherits the properties from ResponseBase in this example
}
```

This opens up the possibility of using the same pattern for internal request/response objects or whatever else you would want to distinguish on. But they could absolutely be omitted.

Having base classes for request/response objects is needed in order to have a generic processor class that can handle processing of all classes that inherits from those.

## Creating validation interfaces

I our example we needed to have some general validation of all incoming requests and then some custom validation for each specific operation.

In other words an interface for a generic validator, that needs to take a request-class as input and then return a validation result. Using this generic approach enables us to use the same structure for the general validation as for the specific validation.

```c#
/// <summary>
/// Interface for request validators
/// </summary>
/// <typeparam name="Req"></typeparam>
public interface IRequestValidator<Req> where Req : RequestBase
{
  IInputValidationResult Validate(Req request);
}
```

And the return value interface:

```c#
public interface IInputValidationResult
{
	ValidationResultType ValidationResult { get; set; }
}
```

The ValidationResultType here is just an enum with Success, Warning, Error options.

The IInputValidationResult can be implemented by any specific business area, if extra return values are needed, but we also implement a default that can be used by everyone out of the box.

```c#
public class InputValidationResult : IInputValidationResult
{
  public ValidationResultType ValidationResult { get; set; }
}
```

One of my main issues with explaining the use of this pattern to other developers probably revolves around this area, where you can create your own implementation of the IInputValidationResult and in turn have your own properties available inside the HandleValid, HandleInvalid and HandWarningRequest as we will get to.

But this can be used to pass information from the validator to the specific request manager. We used it if we had heavy validation-logic that ran through structures with information we might as well save for the actual processing inside the request manager.

## Creating main generic request manager

So speaking of which, let us look at the IRequestManager interface, as we have all the building blocks in place:

```c#
public interface IRequestManager<Req, Resp> where Req : RequestBase where Resp : ResponseBase
{
  Resp ProcessRequest(Req request);
}
```

This is a very basic interface and all it does is expose a "ProcessRequest" method that takes a "Req" of type RequestBase and returns a "Resp" which is of type ResponseBase. The reason for having an interface here is that we can inject different IRequestManagers and always just call "ProcessRequest".

But this is only part of the magic, the rest happens inside the RequestManagerBase class, which is the biggest part of this pattern:

```C#
/// <summary>
/// Base class for all RequestManagers. Handles the flow with processing of requests including uniform way of validation
/// </summary>
/// <typeparam name="Req">The request type</typeparam>
/// <typeparam name="Resp">The response type</typeparam>
/// <typeparam name="InpValRes">The input validation type that is returned from validation of request type</typeparam>
public abstract class RequestManagerBase<Req, Resp, InpValRes> : IRequestManager<Req, Resp> where Req : RequestBase where Resp : ResponseBase where InpValRes : IInputValidationResult
{
  private readonly IRequestValidator<RequestBase> _baseValidator;
  public RequestManagerBase(IRequestValidator<RequestBase> baseRequestValidator)
  {
    _baseValidator = baseRequestValidator;
  }
  public Resp ProcessRequest(Req request)
  {
    Resp result = null;

    // Validate BaseRequest
    IInputValidationResult baseValidationResult = _baseValidator.Validate(request);

    // If BaseRequest is valid - then validate specific request
    if (baseValidationResult.ValidationResult == ValidationResultType.Success)
    {
      // GetInputRequestValidator from implementing class
      IRequestValidator<Req> validator = GetInputRequestValidator(request);

      // Validate with validator from above
      InpValRes validationResult = (InpValRes)validator.Validate(request);

      // Call corresponding method based on result
      // With this pattern we have this code only once!
      switch (validationResult.ValidationResult)
      {
        case ValidationResultType.Success:
          result = HandleValidRequest(request, validationResult);
          break;
        case ValidationResultType.Warning:
          result = HandleWarningRequest(request, validationResult);
          break;
        case ValidationResultType.Error:
          result = HandleInvalidRequest(request, validationResult);
          break;
        default:
          throw new ArgumentOutOfRangeException("Unknown ValidationResult");
      }
    }
    else
    {
      // Should we handle it here in the base manager or send it back to the implementing class as HandleInvalidRequest ?
      // I think we should handle it here
      // TODO - Decorate response from BaseRequestValidator with enough information to set these properties below with valid
      // information across all interfaces
      result = HandleInvalidBaseRequest(baseValidationResult);
    }

    // TODO Log outgoing request - perhaps? 

    return result;
  }

  private Resp HandleInvalidBaseRequest(IInputValidationResult failedBaseValidation)
  {
    // TODO we may have an issue if the request class suddenly needs parameters in the constructor
    // In that case we may need to "push" the instantiation to the implementer class
    Resp result = (Resp)Activator.CreateInstance(typeof(Resp));
    result.ErrorCode = 1;
    result.ErrorMessage = "Validation of request failed";
    result.ErrorLevel = ErrorLevelType.Error;
    return result;
  }

  protected abstract IRequestValidator<Req> GetInputRequestValidator(Req request);

  protected abstract Resp HandleInvalidRequest(Req request, InpValRes failedValidation);

  protected abstract Resp HandleWarningRequest(Req request, InpValRes warningValidation);

  protected abstract Resp HandleValidRequest(Req request, InpValRes successfulValidation);
}
```

The RequestManagerBase class is abstract as it is not supposed to be instantiated, but only used as base class for actual implementations. It inherits the IRequestManager interface which we created earlier. In the constructor it takes an IRequestValidator which is used as the baseValidator, meaning it will be used to validate the common properties across all requests. 

From the IRequestManager interface it gets its only public method, being ProcessRequest. This is where the main logic of this request manager API pattern resides. The logic is explained in comments inside the code itself, but basically it first validates the properties from the BaseRequest and if valid then it asks the implementing class through the abstract method GetInputRequestValidator for the validator that should be used to validate the incoming request. Once this is returned it will then call this validator and ask it to validate the request. Depending on what the validator returns it will then call either of the abstract methods HandleValidRequest, HandleWarningRequest or HandleInvalidRequest.

Finally we have the four abstract methods described in description above. By way of being abstract we force the implementing class to implement these methods. We will look at an example of what this looks like once you create a class that inherits from the RequestManagerBase class. But this concludes the RequestManagerBase class.

## Implementing specific request manager

I won't go into detail with the specific implementations of the request and response classes as that it too simple to show. But you can of course clone the repository (link is at the end of the post) and see for yourself.

But I will just show the simple implementation of a IRequestValidator that is being used in the unit tests of the project.

```c#
public class MockRequestValidator : IRequestValidator<MockExternalRequest>
    {
        public IInputValidationResult Validate(MockExternalRequest request)
        {
            if (request != null)
            {
                if (request.MyInputString.Equals("Success"))
                    return new InputValidationResult() { ValidationResult = ValidationResultType.Success };
                else if (request.MyInputString.Equals("Warning"))
                    return new InputValidationResult { ValidationResult = ValidationResultType.Warning };
                else
                    return new InputValidationResult() { ValidationResult = ValidationResultType.Error };
            }
            else
                return new InputValidationResult() { ValidationResult = ValidationResultType.Error };
        }
    }
```

As you can see this implementation is very basic and oly to show the functionality. What is important is that depending on InputValidationResult either HandleValidRequest, HandleInvalidRequest or HandleWarningRequest will be called on the implementing request manager.

So let us look at the request manager from the unit testing:

```c#
public class MockRequestManager : RequestManagerBase<MockExternalRequest, MockExternalResponse, InputValidationResult>
    {
        private IRequestValidator<MockExternalRequest> _requestValidator;

        public MockRequestManager(IRequestValidator<RequestBase> baseRequestValidator, IRequestValidator<MockExternalRequest> requestValidator) : base(baseRequestValidator)
        {
            _requestValidator = requestValidator;
        }

        protected override IRequestValidator<MockExternalRequest> GetInputRequestValidator(MockExternalRequest request)
        {
            return _requestValidator;
        }

        protected override MockExternalResponse HandleInvalidRequest(MockExternalRequest request, InputValidationResult failedValidation)
        {
            MockExternalResponse response = new MockExternalResponse
            {
                ErrorCode = 2,
                ErrorMessage = "Invalid MockExternalRequest",
                ErrorLevel = ErrorLevelType.Error
            };

            return response;
        }

        protected override MockExternalResponse HandleValidRequest(MockExternalRequest request, InputValidationResult successfulValidation)
        {
            MockExternalResponse response = new MockExternalResponse
            {
                ErrorCode = 0,
                ErrorLevel = ErrorLevelType.None,
                MyOutputString = "Success"
            };

            return response;
        }

        protected override MockExternalResponse HandleWarningRequest(MockExternalRequest request, InputValidationResult warningValidation)
        {
            MockExternalResponse response = new MockExternalResponse
            {
                ErrorCode = 3,
                ErrorLevel = ErrorLevelType.Warning,
                MyOutputString = "Warning"
            };

            return response;
        }
    }
```

As you can see in the first line, we inherit from RequestManagerBase and in doing so also specifies which actual request and response classes to be used and also which validator. By doing so all the protected override methods will automatically be generated and are ready to be filled with logic required for handling the specific requests.

## Praise from security

The full power of using this API request manager pattern can of course be a little hard to see with such simple examples. But I can finish of with a little anecdote from the project where I actually introduced this pattern. I think it initially took a little time for all to see the advantages of this pattern. It does require some extra work around creation of classes - but not too much in my opinion. 

But one of the real eye openings for the other developers and a big victory for my part, was when we had to have our project security approved. Everyone feared this as it usually took very long and we were under a lot of time pressure. As part of this security assesment there was a dreaded code review to be performed which almost always caused a lot of hassle and additional changes.

The funny thing happened that, after I walked the security approver through the code base, which I had taken responsibility for structuring apart from also implementing the API request manager pattern, he actually ended up not just approving our solution but actually praising this exact pattern highly. He could clearly see the advantages of ensuring that all requests were validated by "forcing" developers to think about all the scenarios and also making it a lot easier for code reviews to spot potential loop-holes.

And that is how the department I was atteached as a freelancer ended up being applauded and given praise from the security department.

It essentially boils down to this:

> Easy to do the right thing - hard to do the wrong thing

We make it easy for developers to do the right thing and hard to do the wrong thing. And in my opinion we do so without taking initiative and freedom away from the developer.

### Potential improvements

This pattern is of course by no means perfect and can probably be enhanced in a number of ways. I have just listed a few things that have come to mind while watching this pattern in use with a medium project with 5-10 developers attached.

- RequestManagerBase could take a special IRequest**Base**Validator to further ensure that the BaseRequest is validated by the right validator. With current implementation it would be possible to use a "wrong" validator for BaseRequest - but it would be easy to spot on code review at least.
- Originally I had planned for HandleValidRequest method to only take the request as input, HandleInvalidRequest to only take validation result and HandleWarningRequest was the only one who took both request and validation result as parameters. This looked a lot cleaner, but over time it just hindered the scenarios where you needed for instance a lot of work in the validator which could be re-used in the HandleValidRequest method - then suddenly we had no way of passing that. Hence I ended up giving all methods both parameters. 

This concludes my introduction to the Request Manager API Pattern. I hope you find it understandable and potentialy useful. I have included link to the public repository on BitBucket, so have fun with it.

You can clone use and play around with the code from here: https://bitbucket.org/jmreiche/requestmanager/src/master/ 

Feel free to reach out to me with questions or suggestions for improvements.