## 🧩 Goal: Represent this real-world relationship

- A customer places an **Order**
    
- An **Order** can contain **multiple Products**
    
- Each **Product** can appear in **multiple Orders**
    
- Additionally, we want to store:
    
    - Quantity of each product in the order
        
    - Subtotal of each item (optional)


✅ Django Models (With `through`)

 ```
from django.db import models
from django.contrib.auth.models import User

class Product(models.Model):
    name = models.CharField(max_length=100)
    price = models.DecimalField(max_digits=8, 
	    decimal_places=2)

    def __str__(self):
        return self.name


class Order(models.Model):
    customer = models.ForeignKey(User, 
    on_delete=models.CASCADE)
    date_ordered = 
	    models.DateTimeField(auto_now_add=True)
	products = models.ManyToManyField(Product, 
	    through='OrderItem', related_name='orders')

    def __str__(self):
        return f"Order #{self.id}"


class OrderItem(models.Model):
    order = models.ForeignKey(Order,
     on_delete=models.CASCADE)
    product = models.ForeignKey(Product, 
    on_delete=models.CASCADE)
    quantity = models.PositiveIntegerField()
    item_subtotal = models.DecimalField(max_digits=10,
	     decimal_places=2)

    def __str__(self):
        return f"{self.quantity} of 
        {self.product.name} in Order #{self.order.id}"

``` 

🗂️ What tables will this create?
### 1. `product` table

|id|name|price|
|---|---|---|
|1|iPhone|1000.00|
|2|Headphones|200.00|

### 2. `order` table

|id|customer_id|date_ordered|
|---|---|---|
|1|2|2025-08-06 11:45AM|
|2|3|2025-08-07 01:15PM|

### 3. `orderitem` (join table, created manually)

|id|order_id|product_id|quantity|item_subtotal|
|---|---|---|---|---|
|1|1|1|2|2000.00|
|2|1|2|1|200.00|
|3|2|2|3|600.00|

---

[[🔍 Why use `through='OrderItem'`? ]]

If you **don't** use `through`, Django will auto-create a hidden join table like this:

|id|order_id|product_id|
|---|---|---|
|1|1|1|
|2|1|2|

❌ But you **cannot** add extra data like `quantity`, `item_subtotal`, etc.  
✅ With a **custom `through` model**, you have full control over the relationship.

---

## 🧠 Summary

|Concept|Description|
|---|---|
|**ManyToManyField**|Creates a many-to-many relationship|
|**through=Model**|Lets you customize the join table|
|**OrderItem**|Intermediate model that connects Product & Order with extra fields|
|**Why needed**|To store quantity, subtotal, etc., per product in each order|

#through #joins #relations #django #database 

yo this is neww
