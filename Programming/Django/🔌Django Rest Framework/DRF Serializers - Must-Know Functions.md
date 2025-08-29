# Django REST Framework Serializers - Must-Know Functions

#django #drf #serializers #python #webdev

## Overview

Django REST Framework serializers are essential for converting between Django model instances and JSON/other formats. Understanding these core functions is crucial for effective API development.

---

## Core Serializer Methods

### Data Conversion Methods

#### `to_representation(self, instance)`

- **Purpose**: Converts model instance → Python dictionary → JSON output
- **When to override**: Custom output formatting, field transformation
- **Example Use**: Format dates, combine fields, add computed values

```python
def to_representation(self, instance):
    data = super().to_representation(instance)
    data['full_name'] = f"{instance.first_name} {instance.last_name}"
    return data
```

#### `to_internal_value(self, data)`

- **Purpose**: Converts incoming data (JSON) → Python types for validation
- **When to override**: Custom input processing, data normalization
- **Example Use**: Clean input data, handle special formats

```python
def to_internal_value(self, data):
    if 'email' in data:
        data['email'] = data['email'].lower().strip()
    return super().to_internal_value(data)
```

---

## Validation Methods

### Object-Level Validation

#### `validate(self, data)`

- **Purpose**: Validates entire dataset (cross-field validation)
- **Use for**: Business logic requiring multiple fields
- **Must return**: The validated data dictionary

```python
def validate(self, data):
    if data['start_date'] > data['end_date']:
        raise serializers.ValidationError("End date must be after start date")
    return data
```

### Field-Level Validation

#### `validate_<field_name>(self, value)`

- **Purpose**: Validates individual field values
- **Naming**: Must match field name exactly
- **Must return**: The validated value

```python
def validate_email(self, value):
    if User.objects.filter(email=value).exists():
        raise serializers.ValidationError("Email already exists")
    return value
```

---

## CRUD Operations

### Creating Objects

#### `create(self, validated_data)`

- **Purpose**: Defines object creation logic
- **When to override**: Custom creation requirements, related objects
- **Must return**: The created instance

```python
def create(self, validated_data):
    # Handle nested data or custom logic
    tags_data = validated_data.pop('tags', [])
    instance = super().create(validated_data)
    instance.tags.set(tags_data)
    return instance
```

### Updating Objects

#### `update(self, instance, validated_data)`

- **Purpose**: Defines object update logic
- **When to override**: Custom update requirements, partial updates
- **Must return**: The updated instance

```python
def update(self, instance, validated_data):
    # Custom update logic
    instance.modified_by = self.context['request'].user
    return super().update(instance, validated_data)
```

---

## Essential Properties & Methods

### Validation Flow

#### `is_valid(raise_exception=False)`

- **Purpose**: Validates serializer data
- **Returns**: `True` if valid, `False` otherwise
- **Parameter**: `raise_exception=True` auto-raises validation errors
- **Side effect**: Populates `validated_data` or `errors`

#### `save()`

- **Purpose**: Creates new or updates existing instance
- **Logic**: Calls `create()` if no instance, `update()` if instance exists
- **Can pass**: Additional keyword arguments to `create()`/`update()`

### Data Access

#### `validated_data`

- **Type**: Dictionary
- **Available**: After successful `is_valid()` call
- **Contains**: Clean, validated data ready for model operations

#### `errors`

- **Type**: Dictionary
- **Available**: After failed `is_valid()` call
- **Contains**: Field-specific and non-field validation errors

#### `data`

- **Type**: Dictionary
- **Contains**: Serialized representation of the object
- **Use**: API response data

---

## Meta Class Configuration

### ModelSerializer Options

#### `model`

- **Purpose**: Specifies target Django model
- **Required**: For ModelSerializer subclasses

#### `fields`

- **Options**:
    - `'__all__'` - Include all model fields
    - `['field1', 'field2']` - Specific fields list
    - Tuple format also accepted

#### `exclude`

- **Purpose**: Exclude specific fields from serialization
- **Format**: List or tuple of field names
- **Cannot use**: With `fields = '__all__'`

#### `read_only_fields`

- **Purpose**: Make fields read-only (no input validation)
- **Format**: List or tuple of field names
- **Common use**: Auto-generated fields, timestamps

```python
class Meta:
    model = MyModel
    fields = ['id', 'name', 'email', 'created_at']
    read_only_fields = ['id', 'created_at']
```

---

## Common Usage Patterns

### Basic Usage Flow

1. Initialize serializer with data: `serializer = MySerializer(data=request.data)`
2. Validate: `if serializer.is_valid():`
3. Save: `instance = serializer.save()`
4. Return: `return Response(serializer.data)`

### Update Pattern

```python
serializer = MySerializer(instance, data=request.data, partial=True)
if serializer.is_valid():
    serializer.save()
```

---

## Key Takeaways

- **Validation methods** ensure data integrity and business rules
- **to_representation/to_internal_value** provide fine-grained data control
- **create/update** methods handle custom CRUD logic
- **Meta class** configures automatic ModelSerializer behavior
- Always call `is_valid()` before accessing `validated_data` or calling `save()`

---

## Related Topics

- [[Django Models]]
- [[Django REST Framework Views]]
- [[API Design Patterns]]
- [[Validation Strategies]]

## Tags

#django #drf #api #validation #serialization