 Assignment Report – Django Multi-Database Threaded Insert Simulation

 Tredexa Technologies – Python/Django Developer Assignment
Author: Harsh Tiwari
Submission Date: 1/11/2025

---

**Objective**
The goal of this assignment was to simulate a distributed system using Django, where three different datasets — Users, Products, and Orders — are stored in three separate SQLite databases.
The task required performing concurrent insert operations using Python’s `threading` module, while handling all data validations in application logic (not in the database).
The output had to display the success or failure of each insertion operation.

---

**Models Created**

1. **User Model (users.db)**
   Fields:

* id: Integer (Primary Key)
* name: String
* email: String

Validations (in code):

* Name must not be empty
* Email must be in a valid format (`@` present)
* No duplicate email addresses

---

2. **Product Model (products.db)**
   Fields:

* id: Integer (Primary Key)
* name: String
* price: Float

Validations (in code):

* Price must be non-negative

---

3. **Order Model (orders.db)**
   Fields:

* id: Integer (Primary Key)
* user_id: Foreign key to Users
* product_id: Foreign key to Products
* quantity: Integer

Validations (in code):

* Quantity must be positive
* User ID must exist in Users database
* Product ID must exist in Products database

---

**Code Components Explained**

1. models.py
   Defines all three Django models: MyUser, MyProduct, and MyOrder.
   Each model is linked to a different SQLite database using Django ORM.

```python
class MyUser(models.Model):
    name = models.CharField(max_length=50)
    email = models.CharField(max_length=100)

    class Meta:
        app_label = 'mainapp'
```

---

2. db_router.py
   This file defines how each model interacts with its corresponding database. It directs all read and write operations to the correct database file based on the model.

```python
class MultiDBRouter:
    def db_for_read(self, model, **hints):
        if model.__name__ == 'MyUser':
            return 'users_db'
        elif model.__name__ == 'MyProduct':
            return 'products_db'
        elif model.__name__ == 'MyOrder':
            return 'orders_db'
```

This routing ensures that data for each model is stored and retrieved from its specific database.

---

3. run_inserts.py (Management Command)
   This script is responsible for data validation, threading, and concurrent insert operations.
   It inserts all provided data sets (Users, Products, and Orders) simultaneously into their databases.

```python
threading.Thread(target=insert_users).start()
threading.Thread(target=insert_products).start()
threading.Thread(target=insert_orders).start()
```

Each insertion function performs validation before saving records to the database.

**Example Validation**

```python
if not name:
    print(f"User | id={id} | Failed: Empty name")
elif MyUser.objects.filter(email=email).exists():
    print(f"User | id={id} | Failed: Duplicate email")
else:
    MyUser.objects.create(id=id, name=name, email=email)
```

This ensures that invalid or duplicate data never gets inserted.

---

**Threading Logic**
The `threading` module was used to simulate concurrent insertions.
Each model’s insertion operation runs in a separate thread, allowing parallel database operations.

Steps:

1. Create a thread for each model’s insertion function.
2. Start all threads simultaneously.
3. Wait for all threads to finish.
4. Print the insertion results in the console once all threads complete.

---

**Tools and Technologies Used**

| Tool / Library               | Purpose                                   |
| ---------------------------- | ----------------------------------------- |
| Python 3.12                  | Programming language                      |
| Django                       | Framework for models and ORM              |
| SQLite                       | Lightweight database (3 separate DBs)     |
| Threading                    | Concurrency module for parallel insertion |
| Application-level Validation | Ensures data integrity before insertion   |

---

**Summary of the Assignment**

| Aspect          | Implementation                                     |
| --------------- | -------------------------------------------------- |
| Concept         | Distributed data handling using multiple databases |
| Concurrency     | Implemented with Python `threading`                |
| Database Router | Directed model operations to the correct database  |
| Validation      | Handled at the application level before insertions |
| Output          | Displayed success or failure messages in console   |

---

**Challenges Faced**

* Setting up Django with three different databases.
* Handling data integrity across multiple databases.
* Managing concurrent threads safely.
* Ensuring proper logging of insertion results.

---

**Results**
The command executed successfully and displayed insert results for all models.
Each insertion was validated before saving, and invalid records (like negative prices or duplicate emails) were correctly flagged.

Example Output:

```
Results:
---------
User     | id=1 | Inserted
User     | id=8 | Failed: Duplicate email
Product  | id=10 | Failed: Negative price
Order    | id=8 | Failed: Invalid quantity
Order    | id=10 | Failed: User 10 missing
```

---

**Key Takeaways**

* Learned Django’s multi-database setup using routers.
* Gained experience with threading and concurrent operations.
* Understood how to apply validation in application logic instead of database constraints.
* Practiced writing modular, structured Django code.

---

**Conclusion**
This project fulfills all assignment requirements:

* Three separate SQLite databases
* Concurrent data insertions with threading
* No database-level validation
* Application-level data checks and clear result output

---

**Author**
Name: Harsh Tiwari
Role: Python/Django Developer (Trainee)
Company: Tredexa Technologies
Contact: harsh.tiwari5423@gmail.com
