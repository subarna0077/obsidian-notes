### Basic Queries

```python
# Get all objects
Model.objects.all()

# Filter objects
Model.objects.filter(field=value)
Model.objects.filter(field__contains='text')
Model.objects.exclude(field=value)

# Get single object
Model.objects.get(id=1)
Model.objects.first()
Model.objects.last()

# Check existence
Model.objects.filter(field=value).exists()

# Count objects
Model.objects.count()
Model.objects.filter(field=value).count()
```

### Field Lookups

```python
__exact         # Exact match
__iexact        # Case-insensitive exact match
__contains      # Contains substring
__icontains     # Case-insensitive contains
__startswith    # Starts with
__endswith      # Ends with
__gt            # Greater than
__gte           # Greater than or equal
__lt            # Less than
__lte           # Less than or equal
__in            # In list
__isnull        # Is null
__year          # Year part of date
__month         # Month part of date
__day           # Day part of date
```

### Ordering and Limiting

```python
Model.objects.order_by('field')                  # Ascending
Model.objects.order_by('-field')                 # Descending
Model.objects.order_by('field1', '-field2')      # Multiple fields
Model.objects.all()[:5]                          # First 5 objects
Model.objects.all()[5:10]                        # Objects 5-10
```

### Aggregation

```python
from django.db.models import Count, Sum, Avg, Max, Min

Model.objects.aggregate(
    total=Count('id'),
    avg_price=Avg('price'),
    max_price=Max('price')
)

# Annotation
Model.objects.annotate(
    comment_count=Count('comments')
)
```

## Model Methods

### Common Model Methods

```python
class MyModel(models.Model):
    def __str__(self):
        return self.name
    
    def get_absolute_url(self):
        return reverse('model_detail', args=[self.id])
    
    def save(self, *args, **kwargs):
        # Custom save logic
        super().save(*args, **kwargs)
    
    def delete(self, *args, **kwargs):
```

