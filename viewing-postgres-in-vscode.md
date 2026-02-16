# VS Code PostgreSQL extension

## Step 1: Install SQLTools extension

1. Open VS Code.
2. Press:
```bash
Ctrl + Shift + X
```

***This opens Extensions.***

3. Search:

SQLTools

4. Install:

SQLTools (by Matheus Teixeira)

***You’ll see a database icon appear on the left sidebar after installing.***

---

## Step 2: Install PostgreSQL driver

1. Still in Extensions search:

SQLTools PostgreSQL

2. Install:

SQLTools PostgreSQL/CockroachDB Driver
Author: SQLTools Team

***This allows SQLTools to talk to Postgres.***

---

## Step 3: Open SQLTools panel

1. Look at the left sidebar.

2. Click the icon that looks like a database:

🗄️ SQLTools

You’ll see:
No connections yet

3. Click:

Add New Connection

---

## Step 4: Select PostgreSQL

1. You’ll see connection types.
- Click:

PostgreSQL

---

## Step 5: Enter your Django database credentials

1. Get these from your Django settings.py

Example:

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'myproject_db',
        'USER': 'postgres',
        'PASSWORD': 'mypassword',
        'HOST': 'localhost',
        'PORT': '5432',
    }
}
```

2. Enter into SQLTools exactly:

Connection name: Django Postgres
Server address: localhost
Port: 5432
Database: myproject_db
Username: postgres
Password: mypassword

3. Leave everything else default.

4. Click:

Test Connection

5. You should see:

Connection successful

6. Then click:

Save Connection

---

## Step 6: Open your tables

1. Now in SQLTools sidebar, you’ll see:

Django Postgres

2. Click arrow:

Django Postgres ▸

3. Then:

```bash
Databases ▸
    myproject_db ▸
        Schemas ▸
            public ▸
                Tables ▸
```

4. You’ll see:

```bash
myapp_customer
myapp_order
django_migrations
auth_user
```

---

## Step 7: View your customers table

1. Click:

myapp_customer

2. Then click:

Show Table Records

3. You’ll see a grid like SQLite viewer:

```bash
id | first_name	| last_name | email |phone_number
```

***These are your Django objects.***

---

## Step 8: View orders table

1. Click:

myapp_order

2. Click:

Show Table Records

3. You’ll see:

```bash
| id | order_number | customer_id | created_at |
```

---

## Step 9: Run SQL queries manually (optional but powerful)

1. Right-click your connection:

New Query

2. Run:
``bash
SELECT * FROM myapp_customer;
```

3. Click:

Run Query ▶

4. You’ll see results instantly.

---

## Step 10: Confirm Django ORM ↔ Postgres link

1. When you run in Django shell:

```python
Customer.objects.create(...)
```

2. SQLTools will show that row in:

```python
myapp_customer
```

***This proves Django ORM is writing to Postgres correctly.***

***What your final SQLTools structure should look like***

```python
SQLTools
  ▸ Django Postgres
      ▸ Databases
          ▸ myproject_db
              ▸ Schemas
                  ▸ public
                      ▸ Tables
                          ▸ myapp_customer
                          ▸ myapp_order
```

----
