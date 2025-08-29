

#django #drf #validation #serializers #object-level-validation
## Overview

The `validate()` method in Django REST Framework serializers performs **object-level validation** - validation that requires access to multiple fields or complex business logic that can't be handled at the field level.

---

## Method Signature

```python
def validate(self, data):
    """
    Object-level validation
    Args:
        data (dict): Dictionary of field values after field-level validation
    Returns:
        dict: The validated data (must return the data)
    Raises:
        serializers.ValidationError: If validation fails
    """
    # Your validation logic here
    return data
```

---

## Key Characteristics

### Execution Order

1. **Field-level validation** (`validate_<field_name>()`) runs first
2. **Object-level validation** (`validate()`) runs after all fields pass
3. Only runs if all individual field validations succeed

### Input Parameter

- `data` contains **all validated field data** as a dictionary
- Already cleaned and processed by field-level validators
- Contains only fields being serialized (respects `fields`/`exclude`)

### Return Requirement

- **MUST return the data dictionary** (modified or unmodified)
- Failing to return data will cause issues
- Can modify the data before returning

---

## Common Use Cases

### 1. Cross-Field Validation

Validate relationships between multiple fields:

```python
def validate(self, data):
    if data['start_date'] >= data['end_date']:
        raise serializers.ValidationError(
            "End date must be after start date"
        )
    return data
```

### 2. Conditional Validation

Validate based on field combinations:

```python
def validate(self, data):
    if data['type'] == 'premium' and not data.get('subscription_id'):
        raise serializers.ValidationError(
            "Premium accounts require a subscription ID"
        )
    return data
```

### 3. Business Logic Validation

Enforce complex business rules:

```python
def validate(self, data):
    user = self.context['request'].user
    
    # Check user permissions
    if data['priority'] == 'high' and not user.has_perm('tasks.high_priority'):
        raise serializers.ValidationError(
            "You don't have permission to create high priority tasks"
        )
    
    # Check business constraints
    if data['budget'] > user.profile.max_budget:
        raise serializers.ValidationError(
            f"Budget cannot exceed ${user.profile.max_budget}"
        )
    
    return data
```

### 4. Data Transformation

Modify data during validation:

```python
def validate(self, data):
    # Auto-calculate total if not provided
    if 'total' not in data and 'quantity' in data and 'price' in data:
        data['total'] = data['quantity'] * data['price']
    
    # Normalize email domain
    if 'email' in data:
        data['email'] = data['email'].lower()
    
    return data
```

---

## Error Handling

### Simple Error Message

```python
def validate(self, data):
    if some_condition:
        raise serializers.ValidationError("Something went wrong")
    return data
```

### Field-Specific Errors

```python
def validate(self, data):
    if data['password'] != data['password_confirm']:
        raise serializers.ValidationError({
            'password_confirm': 'Passwords do not match'
        })
    return data
```

### Multiple Errors

```python
def validate(self, data):
    errors = {}
    
    if data['start_date'] >= data['end_date']:
        errors['end_date'] = 'Must be after start date'
    
    if data['budget'] <= 0:
        errors['budget'] = 'Budget must be positive'
    
    if errors:
        raise serializers.ValidationError(errors)
    
    return data
```

### Non-Field Errors

```python
def validate(self, data):
    if some_general_condition:
        raise serializers.ValidationError(
            "This combination of values is not allowed"
        )
    return data
```

---

## Advanced Patterns

### Accessing Context

```python
def validate(self, data):
    request = self.context.get('request')
    view = self.context.get('view')
    
    # Use request user
    if data['owner'] != request.user.id:
        raise serializers.ValidationError("You can only create items for yourself")
    
    return data
```

### Conditional Validation Based on Action

```python
def validate(self, data):
    # Different validation for create vs update
    if self.instance is None:  # Creating new object
        if 'required_field' not in data:
            raise serializers.ValidationError("Required field missing for new objects")
    else:  # Updating existing object
        if data.get('status') == 'published' and not self.instance.is_ready:
            raise serializers.ValidationError("Cannot publish unready content")
    
    return data
```

### External API Validation

```python
def validate(self, data):
    # Validate against external service
    if 'api_key' in data:
        if not external_service.validate_key(data['api_key']):
            raise serializers.ValidationError({
                'api_key': 'Invalid API key'
            })
    
    return data
```

---

## Best Practices

### ✅ Do's

- Always return the data dictionary
- Use descriptive error messages
- Group related validations together
- Keep validation logic in serializers, not views
- Use field-specific errors when possible
- Test edge cases thoroughly

### ❌ Don'ts

- Don't forget to return data
- Don't perform database writes in validation
- Don't validate individual fields (use `validate_<field>()` instead)
- Don't ignore the `data` parameter
- Don't raise generic exceptions (use `ValidationError`)

---

## Testing validate() Method

```python
def test_date_validation(self):
    data = {
        'start_date': '2023-01-15',
        'end_date': '2023-01-10'  # Before start date
    }
    serializer = MySerializer(data=data)
    
    self.assertFalse(serializer.is_valid())
    self.assertIn('end_date', serializer.errors)
    # or for non-field errors:
    # self.assertIn('non_field_errors', serializer.errors)
```

---

## Common Gotchas

### 1. Forgetting to Return Data

```python
# ❌ Wrong - doesn't return data
def validate(self, data):
    if condition:
        raise serializers.ValidationError("Error")
    # Missing return statement!

# ✅ Correct
def validate(self, data):
    if condition:
        raise serializers.ValidationError("Error")
    return data
```

### 2. Modifying Original Data

```python
# ❌ Risky - modifies original reference
def validate(self, data):
    data['field'] = modified_value  # Modifies original
    return data

# ✅ Safer - copy if needed
def validate(self, data):
    data = data.copy()  # If you need to preserve original
    data['field'] = modified_value
    return data
```

### 3. Field Not Available

```python
# ❌ Wrong - field might not be in data
def validate(self, data):
    if data['optional_field'] == 'value':  # KeyError if not provided
        # validation logic
    return data

# ✅ Correct - check existence
def validate(self, data):
    if data.get('optional_field') == 'value':
        # validation logic
    return data
```

---

## Related Methods

- [[validate_field() Methods]] - Field-level validation
- [[to_internal_value()]] - Raw data processing
- [[clean() in Django Models]] - Model-level validation
- [[Form.clean() in Django Forms]] - Form validation equivalent

## See Also

- [[DRF Validation Strategies]]
- [[Custom Serializer Fields]]
- [[Error Handling in DRF]]

## Tags

#validation #business-logic #cross-field #error-handling #best-practices