# ServiceNow spDatePicker Directive Documentation

## Overview

The `spDatePicker` directive is an AngularJS component that provides a comprehensive date and time picker interface for ServiceNow Service Portal applications. It extends the functionality of a standard HTML input field with calendar and time selection capabilities, complete with internationalization, accessibility features, and keyboard navigation support.

## Basic Usage

```html
<sp-date-picker 
    field="fieldObject"
    sn-disabled="false"
    sn-include-time="true"
    sn-change="onDateChange(newValue)"
    sn-max-date="maxDate"
    sn-min-date="minDate">
</sp-date-picker>
```

## Attributes

| Attribute | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `field` | Object | Yes | - | The field object containing date information and validation properties |
| `sn-disabled` | Boolean | No | `false` | When true, disables the date picker input |
| `sn-include-time` | Boolean | No | `false` | When true, includes time selection (hours, minutes, seconds) |
| `sn-change` | Function | No | - | Callback function triggered when the date value changes |
| `sn-max-date` | Date/String | No | - | Maximum selectable date |
| `sn-min-date` | Date/String | No | - | Minimum selectable date |

## Field Object Properties

The `field` object should contain the following properties:

```javascript
{
    name: "field_name",           // Field identifier
    label: "Date Field",          // Display label for accessibility
    value: "2024-01-15",         // Current field value
    placeholder: "Select date",   // Placeholder text
    isMandatory: function() {},   // Function returning if field is required
    isInvalidDateFormat: false,   // Flag for format validation errors
    isInvalid: false,            // General validation error flag
    stagedValue: ""              // Temporarily stored value during editing
}
```

## Features

### Date and Time Selection
- **Date Only**: Select year, month, and day
- **Date and Time**: Additionally select hours, minutes, and seconds
- **Multiple Views**: Switch between day, month, and year selection views

### Keyboard Navigation

The directive supports extensive keyboard navigation:

#### Date Navigation
- **Arrow Keys**: Navigate between dates/months/years
  - Left/Right: Previous/Next day
  - Up/Down: Previous/Next week
  - Page Up/Down: Previous/Next month
  - Alt + Page Up/Down: Previous/Next year
  
#### Time Navigation
- **Arrow Keys**: Adjust time values
  - Up/Down: Increment/Decrement minutes
  - Page Up/Down: Increment/Decrement hours
  
#### Special Keys
- **Alt + Up/Down**: Toggle between date and time picker
- **Enter**: Select current highlighted option
- **Space**: Close picker (apply selection)
- **Escape**: Cancel and close picker
- **Tab**: Navigate through focusable elements

### Accessibility Features

- **ARIA Labels**: Full screen reader support
- **Live Regions**: Announce changes to screen readers
- **Keyboard Navigation**: Complete keyboard accessibility
- **Focus Management**: Proper focus handling for modal interactions
- **Tooltips**: Descriptive tooltips for all interactive elements

### Internationalization

- Supports multiple locales through moment.js
- Translatable date formats and messages
- Locale-specific date formatting
- RTL (Right-to-Left) language support

### Validation

- **Format Validation**: Ensures dates match expected format
- **Range Validation**: Respects min/max date constraints
- **Real-time Feedback**: Immediate validation on input change
- **Error States**: Visual and accessible error indicators

## Usage Examples

### Basic Date Picker

```html
<sp-date-picker field="vm.dateField"></sp-date-picker>
```

```javascript
// Controller
vm.dateField = {
    name: 'start_date',
    label: 'Start Date',
    value: '2024-01-15'
};
```

### Date and Time Picker

```html
<sp-date-picker 
    field="vm.dateTimeField"
    sn-include-time="true">
</sp-date-picker>
```

### With Constraints and Change Handler

```html
<sp-date-picker 
    field="vm.dateField"
    sn-min-date="vm.minDate"
    sn-max-date="vm.maxDate"
    sn-change="vm.handleDateChange(newValue)">
</sp-date-picker>
```

```javascript
// Controller
vm.minDate = moment().format('YYYY-MM-DD');
vm.maxDate = moment().add(1, 'year').format('YYYY-MM-DD');

vm.handleDateChange = function(newValue) {
    console.log('Date changed to:', newValue);
    // Handle date change logic
};
```

### Disabled State

```html
<sp-date-picker 
    field="vm.dateField"
    sn-disabled="vm.isReadOnly">
</sp-date-picker>
```

## Configuration

### Date Formats

The directive uses ServiceNow's global date format settings:
- `g_user_date_format`: User's preferred date format
- `g_user_date_time_format`: User's preferred date-time format
- Falls back to system defaults if user preferences not set

Supported format tokens (converted to moment.js format):
- `Y/y` → Year
- `M` → Month
- `D/d` → Day
- `H/h` → Hour
- `m` → Minute
- `s` → Second
- `A/a` → AM/PM

### Global Settings

```javascript
// Enable/disable date translation
window.NOW.sp.enableDateTranslation = true;

// Enable/disable date validation
g_datepicker_validation_enable = true;
```

## Events

### dp.change
Fired when the selected date changes:
```javascript
element.on('dp.change', function(event) {
    console.log('New date:', event.date);
    console.log('Old date:', event.oldDate);
});
```

### dp.show/dp.hide
Fired when the picker opens or closes:
```javascript
element.on('dp.show', function(event) {
    console.log('Picker opened');
});

element.on('dp.hide', function(event) {
    console.log('Picker closed');
});
```

## Styling

The directive uses Bootstrap classes and ServiceNow-specific CSS:

```css
/* Main container */
.sp-date-input-group { }
.input-group { }

/* Input field */
.form-control { }

/* Picker button */
.datepickericon { }

/* Error states */
.has-error { }

/* Format info */
.sp-date-format-info { }
```

## Browser Support

- Chrome (full support)
- Safari (with polite aria-live regions)
- Firefox
- Internet Explorer 11+ (with touch event handling)
- Mobile browsers (responsive design)

## Dependencies

- AngularJS 1.x
- moment.js
- Bootstrap 3.x
- ServiceNow's spAriaUtil service
- ServiceNow's i18n service
- jQuery (for DOM manipulation)

## Error Handling

The directive handles various error states:

1. **Invalid Date Format**: Shows format warning message
2. **Out of Range**: Prevents selection of dates outside min/max bounds
3. **Parse Errors**: Gracefully handles malformed date strings
4. **Network Issues**: Handles dependency loading failures

## Performance Considerations

- Lazy loading of locale-specific dependencies
- Debounced scroll event handlers
- Efficient DOM manipulation with jQuery
- Memory cleanup on directive destruction

## Security Notes

- All user input is validated and sanitized
- XSS protection through Angular's built-in mechanisms
- No direct DOM innerHTML usage for user content

## Migration Notes

When upgrading from older versions:
1. Check date format configurations
2. Verify accessibility attributes
3. Test keyboard navigation
4. Validate internationalization settings

## Troubleshooting

### Common Issues

1. **Date not displaying**: Check that `field.value` is in correct format
2. **Validation errors**: Verify date format matches user locale
3. **Accessibility issues**: Ensure proper ARIA labels are set
4. **Mobile rendering**: Check viewport meta tag configuration

### Debug Mode

Enable debug logging:
```javascript
// In browser console
localStorage.setItem('sp.datepicker.debug', 'true');
```

## Related Components

- `spFormField`: Base form field component
- `spAriaUtil`: Accessibility utilities
- `spDatePickerUtil`: Date picker utility functions
- `select2EventBroker`: Handles select2 integration events