
# ServiceNow Row Count Hacks: Tips from the Community

## The Problem: Displaying More Records on a Single Page

If you've worked with ServiceNow, you've likely encountered the need to view more records on a single page than the default setting allows. While ServiceNow limits how many records display per page for performance reasons, sometimes you need to see more data at once for analysis or troubleshooting purposes.

The solution lies in a URL parameter called `sysparm_force_row_count`, but as one community member noted, "I always forget the name of that parameter for some reason." This seemingly small challenge sparked a collaborative conversation that revealed several clever approaches to solve this common problem.

## Solution 1: The Bookmarklet Approach

One of the first solutions shared was a JavaScript bookmarklet that could be saved in your browser:

```javascript
javascript: void(function(){ 
  var loc = String(top.location);
  if (loc.indexOf('service-now.com') > -1 || loc.indexOf('servicenowservices.com') > -1) { 
    top.location += '&sysparm_userpref_rowcount=' + Number(%s);
  }
})()
```

This bookmarklet checks if you're on a ServiceNow instance and then appends the necessary parameter to display more rows. The contributor mentioned they had it set up as a custom search engine in their browser for quick access.

## Solution 2: Custom Query Part Switches

Another approach uses ServiceNow's custom query part switches, a feature some users weren't even aware existed:

```json
{
    "r200": {
        "description": "200 rows",
        "value": "&sysparm_userpref_rowcount=200",
        "type": "querypart"
    }
}
```

This configuration allows you to apply the row count parameter to any list or slash command. The beauty of this approach is its simplicity and integration with ServiceNow's native functionality.

## Solution 3: The Custom Slash Command

Building on these ideas, the original poster created a custom slash command called `/frc $0` (for "force row count") that combines the best of both approaches:

```javascript
javascript: void(function(){ 
  var loc = String(top.location);
  if (loc.indexOf('service-now.com') > -1 || loc.indexOf('servicenowservices.com') > -1) { 
    top.location += '&sysparm_force_row_count=$0';
  }
})()
```

This command allows for the input of any custom number. For example, typing `/frc 317` would force the row count to display 317 records. For convenience, they also created switches for common values like `-r200` and `-r1000`.

## Refinements and Best Practices

The community discussion didn't stop at just sharing solutions—they also discussed potential improvements:

1. **Removing Redundant Checks**: For slash commands used only within ServiceNow, the domain checking code can be eliminated.

2. **Parameter Duplication**: A suggestion was made to check if the parameter already exists in the URL and replace it rather than appending a duplicate, which would prevent issues when using the command multiple times.

As one member reflected, "This ended up being way simpler than I thought," highlighting how community collaboration often leads to elegant solutions for common challenges.

## Conclusion

This conversation demonstrates the power of community knowledge sharing in solving everyday technical challenges. What began as a simple question about remembering a parameter name evolved into multiple practical solutions that can save ServiceNow administrators and developers valuable time.

Whether you prefer bookmarklets, custom query parts, or slash commands, these approaches provide convenient ways to view more records at once in ServiceNow—proving that sometimes the most helpful tools are the ones that solve small but persistent frustrations in our daily workflows.

---

## Attribution
[DISCLAIMER] This is an AI-generated blog post based on a Slack conversation. I've created this to preserve and share the valuable knowledge discussed, here are those who contributed:
@nick.c-137
@wiz0floyd
@arnoudkooi
