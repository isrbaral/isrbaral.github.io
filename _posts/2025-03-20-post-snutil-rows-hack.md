---
title: "ServiceNow Row Count Hacks: Tips from the Community"
date: 2025-03-20T12:10:04-05:00
categories:
  - blog
tags:
  - servicenow
  - sn-utils
  - slashcommand
  - ai-generated
---

>### Attribution
>[DISCLAIMER] This is an AI-generated blog post based on a Slack conversation. I've created this to preserve and share the valuable knowledge discussed, here are those who contributed:  
>@nick.c-137  
>@wiz0floyd  
>@arnoudkooi  
>Here's a link to the original discussion (might be dead): [Slack Discussion](https://sndevs.slack.com/archives/CKB5Q8DNK/p1742489712036399?thread_ts=1742405573.778519&cid=CKB5Q8DNK)

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
Open the extension from your toolbar, scroll down and click here on the settings tab
![screenshot of where to find the switch editor in sn-utils](/assets/images/slashcommand_rows2.png)

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

![screenshot of the slashcommand in sn-utils](/assets/images/slashcommand_rows.png)

## Refinements and Best Practices

The community discussion didn't stop at just sharing solutions—they also discussed potential improvements:

1. **Removing Redundant Checks**: For slash commands used only within ServiceNow, the domain checking code can be eliminated.

2. **Parameter Duplication**: A suggestion was made to check if the parameter already exists in the URL and replace it rather than appending a duplicate, which would prevent issues when using the command multiple times.

As one member reflected, "This ended up being way simpler than I thought," highlighting how community collaboration often leads to elegant solutions for common challenges.

## Conclusion

This conversation demonstrates the power of community knowledge sharing in solving everyday technical challenges. What began as a simple question about remembering a parameter name evolved into multiple practical solutions that can save ServiceNow administrators and developers valuable time.

Whether you prefer bookmarklets, custom query parts, or slash commands, these approaches provide convenient ways to view more records at once in ServiceNow—proving that sometimes the most helpful tools are the ones that solve small but persistent frustrations in our daily workflows.


<!-- For various reasons I occasionally have the need to add sysparm_force_row_count=NUMBER to the URL to display like 2 or 300 records in a list all on one page. The problem is, I always forget the name of that parameter for some reason lol.Something like this should be doable in a slash command using JS yeah? Just parse the URL and find a spot to add the new parameter. It seems pretty simple but wanted to make sure I wasn’t underthinking it before I decided to throw something together. Thanks!
27 replies
:coffee_parrot:  Yesterday at 12:36 PM
I've got it in a bookmarklet
12:36
Slash command could work easily enough too
  Yesterday at 12:37 PM
Sheeiiittt
If you don’t mind sharing it, might get me started quickly? Haha
:coffee_parrot:  Yesterday at 12:37 PM
Yeah. Out of pocket, will try to remember when I'm at my desk
  Yesterday at 12:37 PM
All good man, no rush at all
12:37
Appreciate you
:coffee_parrot:  Yesterday at 12:38 PM
Actually might have it synced to my phone
12:38

javascript: void(function(){ var loc = String(top.location);%20 if (loc.indexOf('service-now.com') > -1 || loc.indexOf('servicenowservices.com') > -1) { top.location += '&sysparm_userpref_rowcount=' + Number(%s);}})()

  Yesterday at 12:38 PM
Hah, I’m stuck in a couple meetings anyway, don’t go to any extra trouble for me, won’t be able to do anything with it yet anyway
:coffee_parrot:  Yesterday at 12:38 PM
I have it saved as a custom search engine
  Yesterday at 12:39 PM
Ignore what I said you’re the best lol
@wiz0floyd
++
APP  Yesterday at 12:39 PM
Good going
@wiz0floyd
you now have 77 points (2618 total) :second_place_medal:
  Yesterday at 12:54 PM
A custom querypart switch should work
  Yesterday at 12:56 PM
I was unaware we could even make our own switches? Lol
  Yesterday at 1:00 PM

{
    "r200": {
        "description": "200 rows",
        "value": "&sysparm_userpref_rowcount=200",
        "type": "querypart"
    }
}

1:02
You can apply it to current list, or in any slash command. It doesnt support variables at this point
This file was deleted.
1:05
CleanShot 2025-03-19 at 19.04.53@2x.png 
CleanShot 2025-03-19 at 19.04.53@2x.png
  Yesterday at 1:05 PM
Well shit, this is super simple. Thanks guys!
!pp
APP  Yesterday at 1:05 PM
Way to help out
@wiz0floyd
you now have 78 points (2619 total) :second_place_medal:
Good going
@arnoudkooi
you now have 8 points (550 total) :sparkles:
  Yesterday at 1:13 PM
So I made /frc $0 to take custom input then made a switch for -r200 and -r1000That should pretty much cover any use case I’d ever have. This ended up being way simpler than I thought. You guys are the greatest
  Yesterday at 1:18 PM
whats the /frc $0 do?
  Yesterday at 1:32 PM
It’s mostly just wiz’s script to allow me to put in a custom number for the force_row_count param.Specifically this:

javascript: void(function(){ var loc = String(top.location);%20 if (loc.indexOf('service-now.com') > -1 || loc.indexOf('servicenowservices.com') > -1) { top.location += '&sysparm_force_row_count=$0';}})()

(edited)
1:32
So like /frc 317 will force the row count on the page to 317 (edited) 
:coffee_parrot:  Yesterday at 1:50 PM
You can probably drop all of the checking if it's a servicenow instance if you're using it in a slash command. I needed that when I was on a locked down computer and couldn't add plugins to my browser.
  Yesterday at 2:33 PM
Hah, I think I’m going to swap it out a bit and check if the parameter is already in the URL and replace it if so.
Because right now it just adds to the end and if I try to change it on a URL that already has the parameter it won’t work.Also that situation probably almost never happens so I’m not sure if I care haha
:coffee_parrot:  Yesterday at 2:42 PM
Could happen if you use it twice in a row
  Yesterday at 6:48 PM
This is excellent.   I would use this so much -->
