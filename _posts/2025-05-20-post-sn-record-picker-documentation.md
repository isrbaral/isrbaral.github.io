---
title: "SP Record Picker Docs"
date: 2025-05-20T10:10:04-05:00
categories:
  - blog
tags:
  - servicenow
  - service portal
  - angularJS
  - directive
  - record-picker
  - ai-generated
---
>### Attribution
>[DISCLAIMER] This is an AI-generated blog post based on importing scripts/sn/common/controls/directive.snRecordPicker.js into claude and asking it to generate a document explaining how to use it.

# ServiceNow snRecordPicker Directive Documentation

## Overview

The `snRecordPicker` directive is an AngularJS component that provides an enhanced reference field selector for ServiceNow Service Portal applications. It creates a searchable dropdown that allows users to select records from a specified table in ServiceNow.

## Features

- Dynamic record searching with server-side filtering
- Single or multiple record selection
- Customizable display fields
- Accessibility support
- Configurable query parameters
- Support for default values
- Event handling for value changes

## Directive Usage

```html
<sn-record-picker 
    table="table_name"
    field="fieldObject"
    default-query="encoded_query"
    startswith="true|false"
    search-fields="comma,separated,fields"
    value-field="field_name"
    display-field="field_name"
    display-fields="comma,separated,fields"
    page-size="20"
    on-change="functionName()"
    sn-disabled="true|false"
    multiple="true|false"
    options="optionsObject"
    placeholder="Placeholder text"
    sn-aria-label="Accessibility label"
    multiple-value-delimiter=",">
</sn-record-picker>
```

## Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `table` | String | Yes | The table name to query records from |
| `field` | Object | No | The field object that will store the selected value(s) |
| `defaultQuery` | String | No | Default encoded query to filter results |
| `startswith` | Boolean | No | If true, search uses STARTSWITH instead of CONTAINS |
| `searchFields` | String | No | Comma-separated list of fields to search |
| `valueField` | String | No | Field to use as the value (default: 'sys_id') |
| `displayField` | String | No | Field to use for display |
| `displayFields` | String | No | Comma-separated list of additional fields to display |
| `pageSize` | Number | No | Number of records per page (default: 20) |
| `onChange` | Function | No | Function to call when selection changes |
| `snDisabled` | Boolean | No | If true, the control is disabled |
| `multiple` | Boolean | No | If true, allows multiple selections |
| `options` | Object | No | Additional configuration options |
| `placeholder` | String | No | Placeholder text to display when empty |
| `snAriaLabel` | String | No | Accessibility label for screen readers |
| `multipleValueDelimiter` | String | No | Delimiter for multiple values (default: ',') |

## Field Object Structure

The `field` parameter expects an object with the following properties:

```javascript
{
  name: "field_name",      // Name of the field
  value: "selected_value", // Selected value(s) (sys_id by default)
  displayValue: "Display Value" // Display value(s) for the selected item(s)
}
```

For multiple selections, `value` contains comma-separated values and `displayValue` contains tokenized display values.

## Options Object

The `options` parameter accepts the following properties:

```javascript
{
  cache: true|false,    // Whether to cache API results
  allowClear: true|false // Whether to allow clearing the selection
}
```

## Events

The directive emits the following events:

- `field.change`: Emitted when the field value changes
- `field.change.[fieldname]`: Field-specific change event
- `select2.ready`: Emitted when the select2 initialization is complete

## Examples

### Basic Usage

```html
<sn-record-picker 
    table="incident" 
    field="data.selectedIncident">
</sn-record-picker>
```

### Advanced Configuration

```html
<sn-record-picker 
    table="sys_user" 
    field="data.selectedUser"
    search-fields="name,email,employee_number"
    display-field="name"
    display-fields="email,title,department.name"
    default-query="active=true"
    page-size="50"
    on-change="c.userSelected()"
    multiple="true"
    options="{cache: true, allowClear: true}"
    placeholder="Select users...">
</sn-record-picker>
```

### Within Client Controller

```javascript
function($scope) {
  var c = this;
  
  $scope.data = {
    selectedUser: {
      name: 'user_field',
      value: '',
      displayValue: ''
    }
  };
  
  c.userSelected = function() {
    console.log("Selected user: " + $scope.data.selectedUser.displayValue);
    // Perform additional actions with the selected value
  };
}
```

## Accessibility Features

The directive includes several accessibility enhancements:

- ARIA attributes for screen reader support
- Keyboard navigation
- Focus management
- Accessible labels for removal actions
- Screen reader announcements for state changes

## Internal Implementation Details

The directive uses the Select2 library to create the enhanced dropdown interface and makes REST API calls to the ServiceNow instance to fetch records. It handles query building, result formatting, and value management internally.

## Notes and Best Practices

1. Always set a meaningful `displayField` to improve user experience
2. Use `searchFields` to optimize search performance
3. Use `defaultQuery` to pre-filter available options
4. For complex displays, use `displayFields` to show additional information
5. Consider enabling caching for frequently accessed tables
6. For reference fields that may contain many records, adjust `pageSize` accordingly
7. Use the `onChange` callback for dependent field logic
8. Set appropriate `placeholder` text to guide users

## Troubleshooting

- If selections are not being saved, ensure the `field` object has the correct structure
- If search results are unexpected, check the `searchFields` and `defaultQuery` parameters
- For performance issues, consider increasing `pageSize` or enabling caching
- If display values are not appearing correctly, verify the `displayField` exists in the table

## Dependencies

- AngularJS
- Select2 library
- ServiceNow REST API
- ServiceNow UI Framework components