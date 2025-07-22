---
title: "Creating Dynamic UI Elements with HTML Variables in ServiceNow Catalog Items"
date: 2025-07-21T10:07:04-05:00
categories:
  - blog
tags:
  - servicenow
  - catalog-items
  - html-variables
  - client-scripts
  - ui-development
---

# Creating Dynamic UI Elements with HTML Variables in ServiceNow Catalog Items

So I ran into this interesting problem the other day that got me thinking about creative ways to use HTML variables in ServiceNow. You know how sometimes the obvious solution just doesn't feel quite right? Well, this was one of those times.

## The Problem I Was Trying to Solve

We needed to build a contract renewal form where managers could renew existing contracts. Simple enough, right? But here's the thing - for contractor renewals specifically, the managers wanted to be able to quickly see past contracts so they could check when rates had changed over time.

My first thought was to just throw in a reference field or maybe a plain text link. But honestly? That felt pretty boring and didn't really fit the user experience we were going for. I wanted something that looked good, felt integrated, and actually helped users get their work done faster.

## Why I Didn't Go the "Proper" Route

Now, before you ask - yes, I could have built a custom widget for this. That would probably be the "correct" ServiceNow approach. But here's the thing: widgets are inherently complex beasts. You're talking about server-side scripts, client controllers, HTML templates, CSS files - suddenly what should be a simple dynamic link becomes a whole project.

Plus, custom widgets don't always play nice with record producers. I've seen too many cases where a widget works perfectly in Service Portal but breaks or behaves weirdly when embedded in a catalog item. And let's be honest - widgets require ongoing maintenance. Every time ServiceNow updates, there's a chance something could break, and now you're debugging widget dependencies instead of focusing on actual business logic.

Sometimes the "hacky" solution that works reliably and requires minimal maintenance is actually the pragmatic choice, especially for smaller features like this.

## What I Came Up With

Here's where it gets fun. I realized that HTML variables don't have to be static - you can actually update them dynamically with client scripts. It's like having a little canvas where you can paint whatever HTML you want, whenever you want.

So I decided to create a dynamic button that would change based on what the user typed in the contractor name field. When they enter a name, boom - a nice-looking button appears that takes them directly to a filtered list of that contractor's past contracts.

## How I Built It

Let me walk you through what I did:

### Setting Up the HTML Variable

First, I created an HTML variable in the catalog item. Nothing fancy here:
- Made it read-only (obviously - we don't want users messing with our HTML)
- Left the name field blank (had to use sn-utils to get around the mandatory field thing)
- Called it `link_to_past_contracts`

### The Magic: Client Script Time

This is where the fun happens. I wrote an onChange client script that runs whenever a new contract is selected (which automatically updates the name field):

```javascript
function onChange(control, oldValue, newValue, isLoading) {
    if (isLoading || newValue === '') return;
    
    // Build the URL with the contractor name filter
    const url = `
        <a style="color: #FFFFFF;"
           href="/now/nav/ui/classic/params/target/ast_contract_list.do?sysparm_query=short_descriptionLIKE${newValue}^ORDERBYDESCstarts"
           target="_blank">
          ${newValue}'s past contracts
        </a>
    `;
    
    // Wrap it in a nice-looking button
    const button = `
        <div style="text-align: center;">
          <button
            sp-ellipsis-tooltip="Past Contracts"
            sp-ellipsis-tooltip-title="Past Contracts"
            name="Past Contracts"
            class="btn btn-primary sc-btn text-overflow-ellipsis ng-scope"
            data-toggle="tooltip"
            data-placement="auto"
            data-container="body"
            title="">
            <i class="icon-save pull-left cart-icon-margin" aria-hidden="true"></i>
            ${url}
          </button>
        </div>
    `;
    
    // Update our HTML variable with the shiny new button
    g_form.setValue('link_to_past_contracts', button);
}
```

## What's Actually Happening Here

The cool part here is that because the html field is set to read only the edit bar isn't visible, and the html appears to be part of the form. Additionally, because the field name is blank, that doesn't show either so the button doesn't look like it's a field at all. The button itself uses ServiceNow's standard CSS classes, so it looks like it belongs there. Finally by using `target="_blank"` it opens in a new tab, so users don't lose their place in the form.

Here's what it looks like:

![screenshot of the button](/assets/images/html_variable.png)

## Why I Like This Approach

There are a few things that make this solution pretty neat:

**It looks native** - Using ServiceNow's existing CSS classes means the button doesn't stick out like a sore thumb

**It's responsive** - As soon as someone types a name, the button updates automatically

**It's cleverly hacky** - We're basically overloading the HTML variable type to do something it wasn't designed for, which is oddly satisfying

**It's flexible** - You could easily adapt this pattern for all sorts of other scenarios

## Other Ways You Could Use This

Once I got this working, I started thinking about all the other places this pattern could be useful. Sometimes you need dynamic UI elements but don't want the overhead of building full custom widgets.

Really, anywhere you want to show contextual information or links that change based on user input without the complexity and maintenance burden of custom widgets.

## A Few Things to Keep in Mind

If you decide to try this out, here are some things I learned along the way:

**Watch out for special characters** - Make sure you're properly encoding any dynamic values that go into URLs. You don't want someone's name with an apostrophe breaking your query.

**Test on mobile** - The button styling might need some tweaks for smaller screens.

**Don't go overboard** - This is great for specific use cases, but don't try to rebuild your entire UI this way.

**Think about accessibility** - I included tooltip attributes and proper labels, which is always a good idea.

## Wrapping Up

I have to say, I was pretty happy with how this turned out. Sometimes the best solutions come from thinking outside the box a little and using platform features in ways they weren't necessarily designed for.

HTML variables are usually just for static content, but with a little creativity and some client script magic, they can become powerful tools for creating dynamic, interactive experiences. Plus, it was way easier than building a custom widget from scratch.

Have you ever found yourself using ServiceNow features in unexpected ways? I'd love to hear about it - these kinds of creative solutions are often the most interesting and useful ones.
