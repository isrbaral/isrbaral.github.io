---
title: "Calculating Approval Duration in ServiceNow: Community Solutions"
date: 2025-06-09T10:07:04-05:00
categories:
  - blog
tags:
  - servicenow
  - approvals
  - metrics
  - business-rules
  - ai-generated
---
>### Attribution
>[DISCLAIMER] This is an AI-generated blog post based on a Slack conversation. I've created this to preserve and share the valuable knowledge discussed, here are those who contributed:  
>@eric  
>@romeo  
>@vk  
>Here's a link to the original discussion (might be dead): [Slack Discussion](https://sndevs.slack.com/archives/C0E2G2681/p1749156330572709)

# How to Calculate Approval Duration in ServiceNow

When you need to track how long an approval takes from request to completion in ServiceNow, you have several technical approaches to choose from. This guide walks through the best practices for calculating approval duration based on state changes.

## The Challenge

You want to measure the time it takes for an approval to move from "requested" to "approved" state. The key challenge is capturing the exact timestamp when the state first changes to "requested" and then calculating the duration when it changes to "approved."

## Solution Options

### Option 1: Business Rules (Simple Snapshot Approach)

**Best for:** Single point-in-time calculations where you only need the final duration after approval is complete.

**Implementation:**
- Create a "Duration" field on your approval table
- Use an **After** Business Rule that triggers when the approval state changes to "approved"
- Calculate the duration between the initial request time and approval time

**Pros:**
- Simple to implement
- Low performance impact
- Good if you only need final duration data

**Cons:**
- No data available until approval is completed
- Doesn't track intermediate state durations

### Option 2: Metrics (Recommended for Comprehensive Tracking)

**Best for:** When you want to track duration in every state and need historical reporting capabilities.

**Why Metrics Are Ideal:**
- Automatically captures state changes without custom fields
- Provides historical data for all state transitions
- Built-in reporting capabilities
- No performance impact from calculated fields

**Implementation Steps:**

1. **Create a Metric Definition:**
   - Navigate to `metric_definition` table
   - Set up metric to track your approval state field
   - Configure to capture duration for each state

2. **View Metric Data:**
   - Metric instances are stored in `metric_instance` table
   - Can be displayed as related lists on approval records
   - Use database views (like `incident_metric`) for reporting

3. **Find Reference Examples:**
   - Search existing relationships: `/sys_relationship_list.do?sysparm_query=basic_query_fromLIKEmetric&sysparm_view=`
   - Look at out-of-the-box tables that already use metrics as related lists

### Option 3: Calculated Fields (Not Recommended)

**Why to avoid:** Calculated fields trigger on every record update, causing significant performance issues, especially on high-volume tables.

### Option 4: Flows (Alternative Custom Approach)

**When to use:** If you need custom logic beyond what metrics provide.

**Implementation:**
- Create custom datetime fields to capture state change timestamps
- Use Flow to populate timestamp when state changes to "requested"
- Calculate duration when state changes to "approved"

## Handling the "Requested" Timestamp Challenge

A common issue is determining when the approval was first "requested," since records might be created in different states.

**Solutions:**
1. **Use Metrics:** Automatically handles this by tracking all state changes
2. **Custom Field + Flow:** Create a "Requested Date" field and populate it via Flow when state first changes to "requested"
3. **Avoid using Created Date:** Records may be created in states other than "requested"

## Best Practice Recommendation

**Use Metrics** for approval duration tracking because they:
- Require no custom field development
- Provide comprehensive state duration data
- Offer built-in reporting capabilities
- Scale well with high record volumes
- Give you flexibility for future analytics needs

## Implementation Checklist

- [ ] Create metric definition for your approval state field
- [ ] Test metric instance generation with state changes
- [ ] Set up related list display on approval records (optional)
- [ ] Create database views for reporting (if needed)
- [ ] Verify performance with your expected record volumes

This approach gives you a robust, scalable solution for tracking approval durations without the performance overhead of calculated fields or the limitations of simple business rules.

<!--   Thursday at 3:45 PM
Quick question - I need to calculate the duration of approval(state changes from approval->approved) for which i create a "duration" type field. Do i use a business rule to calculate the duration or use "calculated value" feature on the field level? (edited) 
17 replies
  Thursday at 3:47 PM
if you just need the 1 snapshot in time after the approval is approved you want a BR (as long as you are Ok with not having any data until it is approved). but a metric would be good if you want to know how long it was in every state
:mvp:  Thursday at 3:49 PM
calculated field may cause performance issues since it will trigger for every update of every record (edited) 
  Thursday at 3:49 PM
got it. So an after or before business rule?
  Thursday at 3:50 PM
why not async?
3:51
or a Flow
  Thursday at 3:55 PM
hmm now i'm starting to think how to get the time when state was "requested". I was thinking to use the createdBy time but the record could be created with state=not requested :confused:
:mvp:  Thursday at 3:57 PM
you can create a custom date time field and populate it when state is requested using a flow.
  Thursday at 3:59 PM
that is a reason that running a Metric on the state field may be your solution
3:59
no custom fields needed to get the data
  Thursday at 4:26 PM
got it. So if i were to create a metric, it needs to be reported of the metric_definition table correct? or can i be shown as a related list on the approval record?
  Thursday at 4:27 PM
metric_definition is the definition, you want the metric_instance and yes, it can be shown as a related list if you want
4:29
most commonly for reporting a database view is used to combine the tables like incident_metric  does
  Thursday at 4:50 PM
thanks. Are there any ootb tables that show metrics as a related list so i can use for reference?
  Thursday at 5:03 PM
/sys_relationship_list.do?sysparm_query=basic_query_fromLIKEmetric&sysparm_view=
  Thursday at 5:38 PM
!pp
APP  Thursday at 5:38 PM
Way to help out
@Romeo
you now have 41 points (1515 total) :star2:
Congrats
@eric
you now have 73 points (3892 total) :first_place_medal:
  Thursday at 5:38 PM
thanks -->