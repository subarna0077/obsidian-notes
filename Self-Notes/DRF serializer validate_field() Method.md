
#django #drf #validation #serializers #field-level-validation
## Overview

Field-level validation methods in Django REST Framework follow the pattern `validate_<field_name>(self, value)`. These methods validate individual field values **before** object-level validation runs.

---

## Method Pattern

```python
def validate_<field_name>(self, value):
    """
    Field-level validation for specific field
    Args:
        value: The field value to validate (already converted to Python type)
    Returns:
        The validated value (can be modified)
    Raises:
        serializers.ValidationError: If validation fails
    """
    # Your validation logic here
    return value
```

---

## Key Characteristics

### Naming Convention

- Method name **must exactly match** the field name
- Use underscores for field names with underscores
- Case sensitive

```python
# Field: email -> Method: validate_email
def validate_email(self, value):
    pass

# Field: first_name -> Method: validate_first_name  
def validate_first_name(self, value):
    pass

# Field: user_id -> Method: validate_user_id
def validate_user_id(self, value):
    pass
```

### Execution Order

1. **Field type conversion** (e.g., string to integer)
2. **Built-in field validation** (e.g., max_length, required)
3. **Custom field validation** (`validate_<field>()` methods)
4. **Object-level validation** (`validate()` method)

### Input/Output

- **Input**: Single field value (already type-converted)
- **Output**: Must return the validated value
- **Can modify**: The value before returning it

---

## Common Use Cases

### 1. Format Validation

Validate field format beyond basic type checking:

```python
def validate_phone_number(self, value):
    import re
    phone_pattern = r'^\+?1?\d{9,15}$'
    if not re.match(phone_pattern, value):
        raise serializers.ValidationError("Invalid phone number format")
    return value

def validate_postal_code(self, value):
    if len(value) != 5 or not value.isdigit():
        raise serializers.ValidationError("Postal code must be 5 digits")
    return value
```

### 2. Uniqueness Validation

Check for unique values in database:

```python
def validate_email(self, value):
    if User.objects.filter(email=value).exists():
        raise serializers.ValidationError("Email already exists")
    return value

def validate_username(self, value):
    # Exclude current instance during updates
    queryset = User.objects.filter(username=value)
    if self.instance:
        queryset = queryset.exclude(pk=self.instance.pk)
    
    if queryset.exists():
        raise serializers.ValidationError("Username already taken")
    return value
```

### 3. Business Rule Validation

Enforce specific business constraints:

```python
def validate_age(self, value):
    if value < 18:
        raise serializers.ValidationError("Must be 18 or older")
    if value > 120:
        raise serializers.ValidationError("Invalid age")
    return value

def validate_price(self, value):
    if value <= 0:
        raise serializers.ValidationError("Price must be positive")
    if value > 10000:
        raise serializers.ValidationError("Price cannot exceed $10,000")
    return value
```

### 4. Data Normalization

Clean and normalize field values:

```python
def validate_email(self, value):
    # Normalize to lowercase
    return value.lower().strip()

def validate_name(self, value):
    # Remove extra whitespace and title case
    return value.strip().title()

def validate_slug(self, value):
    import re
    # Convert to lowercase and replace spaces with hyphens
    slug = re.sub(r'[^\w\s-]', '', value.lower())
    slug = re.sub(r'[\s_-]+', '-', slug)
    return slug.strip('-')
```

### 5. External API Validation

Validate against external services:

```python
def validate_api_key(self, value):
    if not external_service.validate_api_key(value):
        raise serializers.ValidationError("Invalid API key")
    return value

def validate_credit_card(self, value):
    if not luhn_algorithm_check(value):
        raise serializers.ValidationError("Invalid credit card number")
    return value
```

---

## Advanced Patterns

### Accessing Other Field Values

Sometimes you need context from other fields:

```python
def validate_end_date(self, value):
    # Access initial_data for other field values during creation
    start_date = self.initial_data.get('start_date')
    if start_date and value <= start_date:
        raise serializers.ValidationError("End date must be after start date")
    return value

# Better approach: use object-level validation for cross-field validation
```

### Conditional Validation

Different validation based on context:

```python
def validate_password(self, value):
    # Different validation for admin users
    request = self.context.get('request')
    if request and request.user.is_staff:
        min_length = 12
    else:
        min_length = 8
    
    if len(value) < min_length:
        raise serializers.ValidationError(f"Password must be at least {min_length} characters")
    
    return value
```

### Dynamic Validation

Validation based on model state:

```python
def validate_status(self, value):
    # Only allow certain status transitions
    if self.instance and self.instance.status == 'published':
        if value not in ['published', 'archived']:
            raise serializers.ValidationError("Cannot change status from published to draft")
    return value
```

---

## Working with Related Fields

### Foreign Key Validation

```python
def validate_author(self, value):
    # Ensure author exists and is active
    if not value.is_active:
        raise serializers.ValidationError("Cannot assign inactive author")
    return value

def validate_category_id(self, value):
    # Validate category exists and user has permission
    request = self.context.get('request')
    try:
        category = Category.objects.get(id=value, owner=request.user)
    except Category.DoesNotExist:
        raise serializers.ValidationError("Category not found or access denied")
    return value
```

### Many-to-Many Validation

```python
def validate_tags(self, value):
    # Limit number of tags
    if len(value) > 5:
        raise serializers.ValidationError("Cannot have more than 5 tags")
    
    # Ensure all tags are active
    inactive_tags = [tag for tag in value if not tag.is_active]
    if inactive_tags:
        raise serializers.ValidationError("Some tags are inactive")
    
    return value
```

---

## File Upload Validation

```python
def validate_avatar(self, value):
    # Check file size (5MB limit)
    if value.size > 5 * 1024 * 1024:
        raise serializers.ValidationError("File size cannot exceed 5MB")
    
    # Check file type
    allowed_types = ['image/jpeg', 'image/png', 'image/gif']
    if value.content_type not in allowed_types:
        raise serializers.ValidationError("Only JPEG, PNG, and GIF files are allowed")
    
    return value

def validate_document(self, value):
    # Check file extension
    import os
    ext = os.path.splitext(value.name)[1].lower()
    if ext not in ['.pdf', '.doc', '.docx']:
        raise serializers.ValidationError("Only PDF and Word documents are allowed")
    
    return value
```

---

## Error Handling Patterns

### Simple Error

```python
def validate_username(self, value):
    if len(value) < 3:
        raise serializers.ValidationError("Username too short")
    return value
```

### Multiple Validation Checks

```python
def validate_password(self, value):
    errors = []
    
    if len(value) < 8:
        errors.append("Password must be at least 8 characters")
    
    if not any(c.isupper() for c in value):
        errors.append("Password must contain uppercase letter")
    
    if not any(c.isdigit() for c in value):
        errors.append("Password must contain a number")
    
    if errors:
        raise serializers.ValidationError(errors)
    
    return value
```

### Custom Error Messages

```python
def validate_email(self, value):
    if '@' not in value:
        raise serializers.ValidationError(
            "Please enter a valid email address",
            code='invalid_email'
        )
    return value
```

---

## Best Practices

### ✅ Do's

- Always return the validated value
- Use descriptive error messages
- Keep validation logic simple and focused
- Normalize data when appropriate
- Handle edge cases (None, empty strings)
- Use field-level validation for single-field checks

### ❌ Don'ts

- Don't access other field values (use object-level validation instead)
- Don't perform heavy database operations
- Don't modify the serializer state
- Don't forget to return the value
- Don't validate relationships between fields here

---

## Testing Field Validation

```python
def test_email_validation(self):
    # Test valid email
    serializer = UserSerializer(data={'email': 'user@example.com'})
    self.assertTrue(serializer.is_valid())
    
    # Test invalid email
    serializer = UserSerializer(data={'email': 'invalid-email'})
    self.assertFalse(serializer.is_valid())
    self.assertIn('email', serializer.errors)

def test_password_strength(self):
    test_cases = [
        ('weak', False),           # Too weak
        ('StrongPass123!', True),  # Strong enough
        ('short', False),          # Too short
    ]
    
    for password, should_be_valid in test_cases:
        serializer = UserSerializer(data={'password': password})
        self.assertEqual(serializer.is_valid(), should_be_valid)
```

---

## Common Gotchas

### 1. Forgetting to Return Value

```python
# ❌ Wrong - doesn't return value
def validate_email(self, value):
    if '@' not in value:
        raise serializers.ValidationError("Invalid email")
    # Missing return statement!

# ✅ Correct
def validate_email(self, value):
    if '@' not in value:
        raise serializers.ValidationError("Invalid email")
    return value
```

### 2. Wrong Method Name

```python
# ❌ Wrong - method name doesn't match field
class UserSerializer(serializers.ModelSerializer):
    email_address = serializers.EmailField()
    
    def validate_email(self, value):  # Should be validate_email_address
        return value

# ✅ Correct
def validate_email_address(self, value):
    return value
```

### 3. Handling None Values

```python
# ❌ Wrong - doesn't handle None
def validate_name(self, value):
    return value.strip().title()  # AttributeError if value is None

# ✅ Correct
def validate_name(self, value):
    if value is None:
        return value
    return value.strip().title()
```

---

## Performance Considerations

### Expensive Operations

```python
# ❌ Slow - database query for each validation
def validate_email(self, value):
    if User.objects.filter(email=value).exists():  # Database hit
        raise serializers.ValidationError("Email exists")
    return value

# ✅ Better - batch validation or cache results
def validate(self, data):  # Object-level validation
    emails_to_check = [data.get('email'), data.get('backup_email')]
    existing_emails = set(User.objects.filter(
        email__in=emails_to_check
    ).values_list('email', flat=True))
    
    if data.get('email') in existing_emails:
        raise serializers.ValidationError({'email': 'Email already exists'})
    
    return data
```

---

## Related Topics

- [[DRF Serializer - validate() Method Deep Dive]]
- [[Custom Serializer Fields]] - Creating reusable field types
- [[DRF Validation Pipeline]] - Complete validation flow
- [[Model Validation vs Serializer Validation]]

## See Also

- [[Form Field Validation in Django]]
- [[Model Field Validators]]
- [[DRF Error Handling]]

## Tags

#field-validation #single-field #data-cleaning #business-rules #format-validation