# 🧩 Django REST Framework Serializers

## 🔹 What is a Serializer?
Serializers in DRF convert **complex data types** (like Django models or querysets) to **native Python datatypes** — and then to JSON for APIs.  
They also **validate and transform** incoming data (e.g., from requests) into usable model instances.

---

## 🔹 Common Types of Serializers
- **ModelSerializer** – Auto-generates fields from a Django model.
- **Serializer** – Manual control; define fields and logic explicitly.

---

Let us first define a model:
``` python
class Product(models.Model):

    name = models.CharField(max_length=200)

    description = models.TextField()

    price = models.DecimalField(max_digits=10, decimal_places=2)

    stock = models.PositiveIntegerField()

    image = models.ImageField(upload_to='products/', blank=True, null=True)

  

    @property

    def in_stock(self):

        return self.stock > 0

    def __str__(self):

        return self.name

class Order(models.Model):

    class StatusChoices(models.TextChoices):

        PENDING = "Pending"

        CONFIRMED = 'Confirmed'

        CANCELLED = 'Cancelled'

  

    order_id = models.UUIDField(primary_key=True, default=uuid.uuid4)

    user = models.ForeignKey(User, on_delete=models.CASCADE)

    created_at = models.DateTimeField(auto_now_add=True)

	products = models.ManyToManyField(Product, 
	through= 'OrderItem', related_name="orders")
		status=models.CharField(max_length=20,choices=StatusChoices.choices,
		default = StatusChoices.PENDING

                              )

    def __str__(self):
		return f" Order {self.order_id} 
			by {self.user.username}"

class OrderItem(models.Model):

    order = models.ForeignKey(Order, on_delete=
     models.CASCADE, related_name='items')

    product = models.ForeignKey(Product, 
    on_delete=models.CASCADE,
    related_name='items')

    quantity = models.PositiveIntegerField()

    @property
    def item_subtotal(self):
		return self.product.price * self.quantity

    def __str__(self):
		return f" {self.quantity} x 
			{self.product.name} in order 
			{self.order.order_id}"
        
```


## 🔹 Basic Example

```python
from rest_framework import serializers
from .models import Product, OrderItem, Order

class ProductSerializer(serializers.ModelSerializer):
    class Meta:
        model = Product
        fields = ['id', 'name', 'price', 'stock']

class OrderItemSerializer(serializers.ModelSerializer):
	product_name = serializers.charField(source = 
	'product.name")
	product_price = serializeers.charField(source= "p
	roduct.price")
	
	class Meta:
	
		model = OrderItem
		fields = ['product_name', 'product_price, 
		'quantity']

class OrderSerializer(serializers.ModelSerializer):
	items = OrderItemSerializer()
	class Meta:
		model = Order
		fields = ['user', 'order_id','items']
		
        
```


[[through in m to m relation]] - for detailed learning of through keyword in m to m relations.


