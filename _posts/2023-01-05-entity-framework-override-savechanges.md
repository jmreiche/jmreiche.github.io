---
title: Entity Framework Override SaveChanges 
layout: post
date: 2023-01-05 15:46:04
categories: programming dotnet
canonical_url:
---



Entity Framework can be both a blessing and a curse - especially EF database first. For startups and small clean-sheet projects, it really works wonders. You can get so much done in very little time. But once you have to work within the constraints of corporate structure and corporate security, then you quickly run into its limitations. Or perhaps I have just been incredibly unlucky as I have had to find special workarounds every time I have been involved in projects using Entity Framework in larger corporate settings.

EF database first is usually used in scenarios where restraints in the environment does not allow the code to update the database automatically or when database and release-procedures for the database is already in place. Perhaps there is even a fully allocated database administrator for the project. Or I should rephrase. When selecting EF database first, then there better be a skilled distinct database administrator. If not then you are betting on your developers being better SQL engineers, than the entire SQL division at Microsoft.

Usually EF database first means that you have to work with what the database gives you. Meaning that you cannot do generic processing of your entities before saving etc. But in some cases it does not even do that. For instance DateTime fields, where you would like to give a default value via (getdate()). These are blatantly ignored by EF database first. If you insert records vi MSSQL it will comply and insert with the default value. But if you insert from code it will just insert DateTime.Min value in whatever field you had the rule.

This can be corrected by manually selecting the property from the edmx designer and change the StoreGeneratedPattern to Compute. This value is saved as long as you only update model from database. But as anyone who has worked with EF database first and edmx knows - when - and yes it is only a matter of *when* - your model is broken and you cannot fix it, then the last resort is deleting every model from the designer and add them from scratch again. Then suddenly you have to remember to manually update every column that had default values to comply with your database-rules. Not pretty! And extremely error-prone. Not good!

Then as all entities created from the edmx-file are partial classes you could create a pseudo set of partial classes that set your computed fields. But this still feels quite cumbersome both to create and maintain - and again error-prone going forward.

What you want is a solution that complies with whatever rules **every time.** Both now and going forward. For my project it was an UpdateTime that we had to have on all tables. All the members of the team, and even the architect, had actually settled on manual procedures. And thereby having each developer remember to set UpdateTime on their entities manually - which I absolutely did not like.

After a bit of searching I found absolutely no viable solution. Every one came up with the suggestion of having another set of partial classes that set whatever property needed setting in the constructor. But as explained above - it is slightly better than the manual setting - but not by much.

So after a bit of hacking I came up with the solution below. It does exactly what I wanted. It automates something that lends itself extremely well to automation. Shutting the door for potential mistakes going forward. You of course still needs to comply with naming standards for properties for this to work. But that is easier to spot in a code-review of sql-changes, than changes to entities in code where you should remember whether this entity has a UpdateTime or not.

```c#
/// <summary>
/// Partial class used to override SaveChanges
/// </summary>
public partial class MyDb : DbContext
{
    /// <summary>
    /// As entity framework ignores default values as (getutcnow()) 
    /// we need to manually set these from code.
    /// Therefore we have an override to the SaveChanges method 
    /// that automatically checks for a property named UpdateTime
    /// And if it is found then it will set it to DateTime.UtcNow.
    ///
    /// Then we avoid each developer having to remember to manually 
    /// put it in for each table/entity
    /// </summary>
    /// <returns></returns>
    public override int SaveChanges()
    {
        // Get entities that are either added or modified
        var changedEntities = ChangeTracker.Entries().Where(
            e => e.State == EntityState.Added || 
            e.State == EntityState.Modified).ToList();

        // Run through entities look for property named UpdateTime
        changedEntities.ForEach(e =>
        {
            // Tro to get property "UpdateTime"
            var updTimeProperty = e.Entity.GetType()
                                .GetProperty("UpdateTime");
            // If the entity has this property then set it
            if (updTimeProperty != null)
            {
                // Set to UTC now
                updTimeProperty.SetValue(e.Entity, DateTime.UtcNow);
            }
        });

        return base.SaveChanges();
    }

}
```