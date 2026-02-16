# Exercises and Practice Activities

- Define a new model: Create a Customer model in your myapp/models.py file. It should have the following fields:

```bash
first_name (CharField, max_length=100)
last_name (CharField, max_length=100)
email (EmailField, unique=True)
phone_number (CharField, max_length=20, blank=True, null=True)
address (TextField, blank=True, null=True)
date_joined (DateTimeField, automatically set when the customer is created)
Include a __str__ method that returns the customer's full name.
Add verbose_name_plural = "Customers" to its Meta class.
```

- **Run migrations:** After defining the Customer model, run makemigrations and migrate to create the corresponding database table.
Interact in the Django shell: Open the Django shell (python manage.py shell) and perform the following operations:
Create: Create at least three Customer objects with different details.

- **Retrieve:**
Get the customer with a specific email address using get().
Filter all customers whose first_name starts with a particular letter (e.g., 'J') using filter() with startswith.
Get all customers who do not have a phone number using filter() with isnull.

- **Update:**
Update the phone_number for one of your created customers.
Change the address for all customers whose last name is 'Smith' (if any exist) using a QuerySet update.

- **Delete:** Delete one specific customer by their email address.

- **Extend a relationship:** Add a ForeignKey field to your Order model (from the examples) that links to your new Customer model. This signifies that each order belongs to a specific customer. Remember to handle the on_delete behavior appropriately (e.g., models.CASCADE if you want to delete orders when a customer is deleted, or models.SET_NULL if you want to keep orders but clear the customer link). Run migrations again and then try creating an Order linked to one of your Customer objects in the shell.

---