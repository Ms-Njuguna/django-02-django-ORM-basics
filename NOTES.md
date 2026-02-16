# Defining Model Fields

Model fields are instances of django.db.models.Field subclasses, such as CharField, IntegerField, DateField, and ForeignKey. Each field type corresponds to a specific database column type and comes with a set of options that customize its behavior.

- CharField: Used for storing strings. Requires a max_length argument.

```python

from django.db import models
class Product(models.Model):
    name = models.CharField(max_length=255) # The name of the product, limited to 255 characters
    description = models.TextField(blank=True, null=True) # Optional longer text description
    price = models.DecimalField(max_digits=10, decimal_places=2) # Price with up to 10 digits, 2 after decimal
    stock_quantity = models.IntegerField(default=0) # Number of items in stock, defaults to 0
    is_available = models.BooleanField(default=True) # Whether the product is available, defaults to True
    created_at = models.DateTimeField(auto_now_add=True) # Automatically set when object is first created
    updated_at = models.DateTimeField(auto_now=True) # Automatically updated every time the object is saved
    def __str__(self):
        return self.name
```

- In this Product model:
   - name is a CharField for short text, requiring max_length.
   - description is a TextField for longer text, with blank=True allowing it to be empty in forms and null=True allowing it to be NULL in the database.
   - price is a DecimalField suitable for monetary values, requiring max_digits (total number of digits) and decimal_places (digits after the decimal point).
   - stock_quantity is an IntegerField for whole numbers, with a default value.
   - is_available is a BooleanField for true/false values.
   - created_at and updated_at are DateTimeField fields that automatically handle creation and update timestamps.

---

## Relationship Fields
Django's ORM allows defining relationships between models, mirroring relational database concepts like one-to-one, one-to-many, and many-to-many.

- ForeignKey: Defines a one-to-many relationship. A child model can have one parent, but a parent can have many children.

```python

from django.db import models
class Category(models.Model):
    name = models.CharField(max_length=100, unique=True) # Category name, must be unique
    def __str__(self):
        return self.name
class Product(models.Model):
    name = models.CharField(max_length=255)
    price = models.DecimalField(max_digits=10, decimal_places=2)
    category = models.ForeignKey(Category, on_delete=models.SET_NULL, null=True, blank=True)
    # Each product belongs to one category. If the category is deleted, the product's category field becomes NULL.
    # null=True allows the field to be NULL in the database, blank=True allows it to be empty in forms.
    def __str__(self):
        return self.name
``` 

*Here, the Product model has a ForeignKey to Category. This means multiple products can belong to the same category, but each product belongs to only one category. The on_delete argument specifies what happens when the referenced Category object is deleted. models.SET_NULL is a common choice, making the category field NULL when the associated category is removed.*

- ManyToManyField: Defines a many-to-many relationship. An object can be associated with multiple objects of another model, and vice-versa.

```python

from django.db import models
class Tag(models.Model):
    name = models.CharField(max_length=50, unique=True)
    def __str__(self):
        return self.name
class Article(models.Model):
    title = models.CharField(max_length=200)
    content = models.TextField()
    tags = models.ManyToManyField(Tag, blank=True)
    # An article can have multiple tags, and a tag can be applied to multiple articles.
    # blank=True means it's optional to add tags when creating an Article.
    def __str__(self):
        return self.title
```

*In this Article and Tag example, an Article can have many Tags, and a Tag can be associated with many Articles. Django automatically creates an intermediary table to manage these relationships.*

- OneToOneField: Defines a one-to-one relationship. An object can be associated with exactly one object of another model.

```python

from django.db import models
class Author(models.Model):
    name = models.CharField(max_length=100)
    email = models.EmailField(unique=True)
    def __str__(self):
        return self.name
class AuthorProfile(models.Model):
    author = models.OneToOneField(Author, on_delete=models.CASCADE, primary_key=True)
    # Each Author has exactly one AuthorProfile, and each AuthorProfile belongs to exactly one Author.
    # on_delete=models.CASCADE means if the Author is deleted, the corresponding AuthorProfile is also deleted.
    # primary_key=True makes this field also the primary key of AuthorProfile, indicating a shared primary key strategy.
    bio = models.TextField(blank=True, null=True)
    website = models.URLField(blank=True, null=True)
    def __str__(self):
        return f"Profile of {self.author.name}"
```

*Here, an Author has one AuthorProfile, and an AuthorProfile belongs to one Author. on_delete=models.CASCADE specifies that if an Author is deleted, its corresponding AuthorProfile will also be deleted.*

---

## Model Metadata with Meta Class
Inside a model class, you can define a nested Meta class to provide metadata options for the model. These options do not create database fields but affect model behavior, such as database table name, default ordering, and verbose names.

```python

from django.db import models
class Order(models.Model):
    customer_name = models.CharField(max_length=200)
    order_date = models.DateTimeField(auto_now_add=True)
    total_amount = models.DecimalField(max_digits=10, decimal_places=2)
    class Meta:
        verbose_name = "Customer Order" # A human-readable name for the single object
        verbose_name_plural = "Customer Orders" # A human-readable name for the set of objects
        ordering = ['-order_date'] # Default ordering for querysets (descending by order_date)
        db_table = 'app_orders' # Explicitly set the database table name
    def __str__(self):
        return f"Order {self.id} by {self.customer_name}"
```

- In this Order model, the Meta class defines:

    - verbose_name and verbose_name_plural: Human-readable names for the model, often used in the Django admin interface.
    - ordering: A tuple or list specifying the default order for query results. '-order_date' means descending order by the order_date field.
    - db_table: Overrides Django's default table naming convention (which would typically be myapp_order).

---

## Practical Examples and Demonstrations

After defining models, the next step is to make Django create the corresponding database tables. This involves two commands, which were briefly touched upon in the "Running Your First Migrations" lesson.

- makemigrations: This command inspects your models and creates new migration files in your application's migrations directory. These files are Python scripts that record the changes needed to be applied to your database schema.

```bash

python manage.py makemigrations myapp
(Replace myapp with the actual name of your Django application where the models are defined).

```

- migrate: This command applies the changes recorded in the migration files to your database. It translates the Python model definitions and migrations into actual SQL commands and executes them against your configured database.

``` bash

python manage.py migrate
Once the migrations are applied, you can interact with your models using the Django shell.

```

- Interacting with Models
- The Django shell allows you to interact with your project's database directly using the ORM.

```bash

python manage.py shell
Inside the shell, you can perform Create, Read, Update, and Delete (CRUD) operations.

```

### Creating Objects
- To create a new record in the database, instantiate your model and call its save() method.

```python

# Assuming 'myapp' is the name of your app and Product, Category are defined within it
from myapp.models import Category, Product, Author, AuthorProfile, Article, Tag
# Create a Category object
electronics = Category.objects.create(name='Electronics')
# Or:
# electronics = Category(name='Electronics')
# electronics.save()
print(f"Created category: {electronics.name}, ID: {electronics.id}")
# Create a Product object associated with the 'electronics' category
laptop = Product.objects.create(
    name='Super Laptop Pro',
    description='A high-performance laptop for professionals.',
    price=1200.00,
    stock_quantity=15,
    is_available=True,
    category=electronics # Assign the ForeignKey object directly
)
print(f"Created product: {laptop.name}, Price: {laptop.price}, Category: {laptop.category.name}")
# Create an Author and AuthorProfile
author1 = Author.objects.create(name='Jane Doe', email='jane.doe@example.com')
profile1 = AuthorProfile.objects.create(
    author=author1,
    bio='Award-winning tech writer and enthusiast.',
    website='http://www.janedoe.com'
)
print(f"Created author: {author1.name} with profile bio: {profile1.bio}")
# Create some Tags
tag1 = Tag.objects.create(name='Programming')
tag2 = Tag.objects.create(name='Web Development')
tag3 = Tag.objects.create(name='Python')
# Create an Article and assign tags
article1 = Article.objects.create(
    title='Getting Started with Django',
    content='This article covers the basics of Django development.'
)
article1.tags.add(tag1, tag2, tag3) # Add multiple tags
article1.save() # Save the changes to the many-to-many relationship
print(f"Created article: '{article1.title}' with tags: {[tag.name for tag in article1.tags.all()]}")

```

### Retrieving Objects (QuerySets)
- The primary way to retrieve objects from the database is by using a QuerySet. Every Manager (the default manager is objects) attached to a model provides methods for querying the database.

- all(): Returns all objects from the database for that model.

```python

all_products = Product.objects.all()
print("All Products:")
for product in all_products:
    print(f"- {product.name} ({product.category.name if product.category else 'N/A'})")
all_categories = Category.objects.all().order_by('name') # Order categories alphabetically
print("\nAll Categories (ordered):")
for category in all_categories:
    print(f"- {category.name}")

```

- get(): Retrieves a single object matching the given lookup parameters. Raises DoesNotExist if no object is found, and MultipleObjectsReturned if more than one object is found.

```python

try:
    specific_laptop = Product.objects.get(name='Super Laptop Pro')
    print(f"\nRetrieved product by name: {specific_laptop.name}, Price: {specific_laptop.price}")
except Product.DoesNotExist:
    print("\nProduct 'Super Laptop Pro' not found.")
except Product.MultipleObjectsReturned:
    print("\nMultiple products named 'Super Laptop Pro' found.")
try:
    tech_category = Category.objects.get(name='Electronics')
    print(f"Retrieved category: {tech_category.name}")
except Category.DoesNotExist:
    print("Category 'Electronics' not found.")

```

- filter(): Returns a QuerySet of objects matching the given lookup parameters.

```python

# Products with price greater than 1000
expensive_products = Product.objects.filter(price__gt=1000)
print("\nExpensive Products (price > 1000):")
for p in expensive_products:
    print(f"- {p.name} (${p.price})")
# Products that are available and have more than 10 stock
available_and_stocked = Product.objects.filter(is_available=True, stock_quantity__gt=10)
print("\nAvailable & Well-Stocked Products:")
for p in available_and_stocked:
    print(f"- {p.name} (Stock: {p.stock_quantity})")
# Products belonging to the 'Electronics' category
electronics_products = Product.objects.filter(category__name='Electronics')
print("\nProducts in 'Electronics' category:")
for p in electronics_products:
    print(f"- {p.name}")
# Articles tagged with 'Python'
python_articles = Article.objects.filter(tags__name='Python')
print("\nArticles tagged with 'Python':")
for article in python_articles:
    print(f"- {article.title}")

```

- Lookup parameters use field__lookup_type syntax. Common lookups include:

   - exact (default): exact match (e.g., name='Laptop')
   - iexact: case-insensitive exact match
   - contains: case-sensitive containment test
   - icontains: case-insensitive containment test
   - startswith, istartswith, endswith, iendswith
   - gt, gte, lt, lte: greater than, greater than or equal to, less than, less than or equal to
   - in: a list of values
   - range: between two values (inclusive)
   - isnull: True or False
   - exclude(): Returns a QuerySet of objects that do not match the given lookup parameters.

```python

# Products not in the 'Electronics' category
non_electronics = Product.objects.exclude(category__name='Electronics')
print("\nProducts not in 'Electronics' category:")
for p in non_electronics:
    print(f"- {p.name} (Category: {p.category.name if p.category else 'None'})")

```

### Updating Objects
- To update an existing object, retrieve it, modify its attributes, and then call its save() method.

```python

# Update an existing product
try:
    keyboard = Product.objects.create(name='Wireless Keyboard', price=75.00, stock_quantity=50, is_available=True, category=electronics)
    print(f"\nCreated keyboard: {keyboard.name}")
    keyboard.price = 69.99 # Change the price
    keyboard.stock_quantity += 10 # Increase stock
    keyboard.save() # Save the changes to the database
    print(f"Updated keyboard: {keyboard.name}, New Price: {keyboard.price}, New Stock: {keyboard.stock_quantity}")
except Exception as e:
    print(f"Error updating keyboard: {e}")
# Update multiple objects at once (QuerySet update)
# Increase stock for all available electronics products by 5
Product.objects.filter(category=electronics, is_available=True).update(stock_quantity=models.F('stock_quantity') + 5)
print("\nStock updated for available electronics products.")

```

*models.F() expressions allow you to reference a model field and perform database operations directly on its value without pulling it into Python memory. This is more efficient and prevents race conditions.*

### Deleting Objects
- To delete an object, retrieve it and call its delete() method.

```python

# Delete a specific product
try:
    outdated_product = Product.objects.create(name='Old Mouse', price=10.00, stock_quantity=5, is_available=False, category=electronics)
    print(f"\nCreated outdated product: {outdated_product.name}")
    outdated_product.delete()
    print(f"Deleted outdated product: {outdated_product.name}")
except Product.DoesNotExist:
    print("\nProduct 'Old Mouse' not found.")
# Delete multiple objects (QuerySet delete)
# Delete all products that are not available
unavailable_products_count = Product.objects.filter(is_available=False).delete()
print(f"\nDeleted {unavailable_products_count[0]} unavailable products.")

```

*Calling delete() on a QuerySet efficiently deletes multiple objects at once, issuing a single SQL query. It also returns a tuple indicating the number of objects deleted and a dictionary of the number of deletions per object type.*