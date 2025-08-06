# Python Interview Questions Collection

A comprehensive collection of Python interview questions covering various levels and topics, from fundamentals to advanced concepts.

## Table of Contents

- [Python Fundamentals - Easy](#python-fundamentals---easy)
- [Python Fundamentals - Medium](#python-fundamentals---medium)
- [Pandas](#pandas)
- [API Integration](#api-integration)
- [Git](#git)
- [Advanced Python](#advanced-python)
- [ORM (Object Relational Mapping)](#orm-object-relational-mapping)

---

## Python Fundamentals - Easy

### Q1. Filter Even Numbers from a List
**Problem:** You are given a list of integers. Filter even numbers from the list.
```python
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```

**Solution:**
```python
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
even_numbers = list(filter(lambda x: x % 2 == 0, numbers))
print(even_numbers)  # Output: [2, 4, 6, 8, 10]
```

### Q2. Find First Vowel Index using enumerate()
**Problem:** Write a Python function that uses the `enumerate()` function to find the index of the first vowel (a, e, i, o, u) in a word. If no vowel is found, return -1.

```python
def find_first_vowel_index(word):
    pass
```

**Test Cases:**
- `find_first_vowel_index("apple")` → 0
- `find_first_vowel_index("strength")` → 3
- `find_first_vowel_index("rhythm")` → -1

**Solution:**
```python
def find_first_vowel_index(word):
    vowels = 'aeiou'
    for index, char in enumerate(word.lower()):
        if char in vowels:
            return index
    return -1
```

---

## Python Fundamentals - Medium

### Q1. Understanding `==` vs `is` operators
**Question:** What's the difference between `==` and `is` in Python?

**Example:**
```python
a = [1, 2, 3]
b = [1, 2, 3]

print(a == b)  # True
print(a is b)  # False

x = None
y = None

print(x == y)  # True
print(x is y)  # True
```

**Explanation:**
- `==` compares **values** (content equality)
- `is` compares **identity** (memory location)
- `None` is a singleton, so `x is y` returns `True` for None values

### Q2. Extract Unique Three Characters
**Problem:** Process a string based on these rules:
1. Remove all repeating characters, keeping only the first occurrence
2. Extract the first three unique characters
3. If fewer than 3 unique characters, pad with underscores ('_')

```python
def extract_unique_three(s: str) -> str:
    pass
```

**Follow-up:** Create a DataFrame and add a "Company_Code" column using this function.

**Solution:**
```python
def extract_unique_three(s: str) -> str:
    seen = set()
    unique_chars = []
    
    for char in s:
        if char not in seen:
            seen.add(char)
            unique_chars.append(char)
    
    result = ''.join(unique_chars[:3])
    return result.ljust(3, '_')

# DataFrame example
import pandas as pd

df_data = {
    "Company": ["Amazon", "Google", "Facebook", "Netflix", "LG"],
    "Revenue_2024": [574.8, 891.2, 116.3, 40.2, 394.3]
}

df = pd.DataFrame(df_data)
df["Code"] = df["Company"].str.lower().apply(extract_unique_three)
```

### Q3. Most Frequent Word
**Problem:** Write a function that returns the most frequently occurring word from a text block. In case of a tie, return the alphabetically first word.

Requirements:
1. Normalize text (lowercase)
2. Remove punctuation
3. Tokenize into words
4. Count word frequencies
5. Return most frequent word (alphabetically smallest in case of tie)

**Test Cases:**
- `most_frequent_word("The cat and the hat")` → "the"
- `most_frequent_word("apple ball apple ball cat")` → "apple"

**Solution:**
```python
import re
from collections import Counter

def most_frequent_word(text: str) -> str:
    # Normalize text: lowercase + remove punctuation
    cleaned = re.sub(r'[^\w\s]', '', text.lower())
    words = cleaned.split()
    
    # Count word frequencies
    word_counts = Counter(words)
    
    # Find max frequency
    max_freq = max(word_counts.values(), default=0)
    
    # Get all words with max frequency
    most_frequent_words = [word for word, count in word_counts.items() if count == max_freq]
    
    # Return alphabetically first word
    return min(most_frequent_words)
```

---

## Pandas

### Sample Data Setup
```python
import pandas as pd

# Customers DataFrame
customers_data = {
    "CustomerID": [1, 2, 3, 4, 5],
    "CustomerName": ["Alice", "Bob", "Charlie", "Diana", "Eve"],
    "Region": ["North", "South", "East", "West", "North"],
    "Age": [30, 45, 28, 35, 50]
}

# Products DataFrame
products_data = {
    "ProductID": [1001, 1002, 1003, 1004],
    "ProductName": ["Laptop", "Desk", "Chair", "Monitor"],
    "Category": ["Electronics", "Furniture", "Furniture", "Electronics"],
    "UnitPrice": [100, 150, 100, 200]
}

# Orders DataFrame
orders_data = {
    "OrderID": [101, 101, 102, 103, 103, 103, 104, 105, 105, 106, 106, 107, 108, 109],
    "CustomerID": [1, 1, 2, 1, 1, 1, 3, 2, 2, 4, 4, 5, 2, 3],
    "ProductID": [1001, 1003, 1002, 1001, 1002, 1003, 1001, 1001, 1003, 1004, 1003, 1001, 1002, 1003],
    "Quantity": [1, 2, 1, 1, 2, 1, 3, 1, 2, 1, 1, 1, 2, 1],
    "TotalSales": [100, 200, 150, 100, 300, 100, 300, 100, 200, 200, 100, 100, 300, 100]
}

customers_df = pd.DataFrame(customers_data)
orders_df = pd.DataFrame(orders_data)
products_df = pd.DataFrame(products_data)
```

### Q1. Calculate Total Unique Orders per Customer
**Objective:** For each customer, determine how many distinct orders they have placed.
**Expected Output:** CustomerID, CustomerName, TotalOrders

**Solution:**
```python
orders_per_customer = orders_df.groupby("CustomerID")["OrderID"].nunique().reset_index()
orders_per_customer.columns = ["CustomerID", "TotalOrders"]
orders_per_customer = orders_per_customer.merge(
    customers_df[["CustomerID", "CustomerName"]], 
    on="CustomerID", 
    how="left"
)
```

### Q2. Identify Repeat Customers
**Problem:** Identify customers who have placed more than one distinct order. Calculate the percentage of repeat customers.

**Solution:**
```python
repeating_customers = orders_per_customer[orders_per_customer["TotalOrders"] > 1]
total_customers = customers_df["CustomerID"].nunique()
repeat_percentage = round((len(repeating_customers) / total_customers) * 100, 2)
```

---

## API Integration

### Q1. Random User API Integration
**Problem:** Use the public API `https://randomuser.me/api/` to generate a dataset of 10 random users.

**Fields to extract:**
- First Name, Last Name, Email, Gender, Country, Age, Phone, Username

**Solution:**
```python
import requests
import pandas as pd

response = requests.get("https://randomuser.me/api/?results=10")
if response.status_code == 200:
    users = response.json()["results"]
    
    user_data = []
    for user in users:
        user_info = {
            "FirstName": user["name"]["first"],
            "LastName": user["name"]["last"],
            "Email": user["email"],
            "Gender": user["gender"],
            "Country": user["location"]["country"],
            "Age": user["dob"]["age"],
            "Phone": user["phone"],
            "Username": user["login"]["username"]
        }
        user_data.append(user_info)
    
    df = pd.DataFrame(user_data)
    print(df.head())
```

---

## Git

### Q1. Difference between `git fetch` and `git pull`
**Answer:**
- **`git fetch`**: Downloads changes from remote repository without modifying your local working branch
- **`git pull`**: Does a `git fetch` followed by a `git merge` (or rebase)

**Key Differences:**
- `git fetch` is safe - lets you review changes before applying
- `git pull` is automatic - applies changes immediately, may cause conflicts

**Example:**
```bash
git fetch origin        # Get latest from remote
git log origin/main     # Review changes
git merge origin/main   # Apply changes manually
```

### Q2. Git Stash Usage
**Question:** How do you stash your changes in Git?

**Answer:**
`git stash` temporarily saves uncommitted changes so you can work on something else.

**Commands:**
```bash
git stash               # Stash current changes
git stash list          # Show all stashed changes
git stash apply         # Apply most recent stash
git stash pop           # Apply and remove most recent stash
```

### Q3. What does `git add` do?
**Answer:**
`git add` stages changes in your working directory for the next commit. It tells Git which files you want to include in your next commit.

---

## Advanced Python

### Q1. Decorator Pattern - Timing Decorator
**Question:** Can you explain this code?

```python
import time
from functools import wraps

def timing(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        start_time = time.time()
        result = func(*args, **kwargs)
        end_time = time.time()
        print(f"{func.__name__} took {end_time - start_time:.4f} seconds")
        return result
    return wrapper

@timing
def slow_function():
    time.sleep(1)
    return "Done"
```

**Explanation:**
This is a **decorator** that measures execution time of any function:
- `@wraps(func)` preserves original function metadata
- `wrapper` function times the execution
- `@timing` applies the decorator to `slow_function`

### Q2. Generator Functions - Fibonacci Series
**Problem:** Print Fibonacci series using a Generator Function.

**Solution:**
```python
def fibonacci_generator(n):
    a, b = 0, 1
    for _ in range(n):
        yield a
        a, b = b, a + b

# Usage
for num in fibonacci_generator(10):
    print(num, end=' ')
# Output: 0 1 1 2 3 5 8 13 21 34
```

### Q3. Module vs Package
**Question:** What's the difference between a module and a package in Python?

**Answer:**
- **Module**: A single Python file (.py) containing functions, classes, or variables
  - Example: `math.py` is a module
  - Import: `import math`

- **Package**: A directory containing multiple modules and an `__init__.py` file
  - Example structure:
    ```
    my_package/
    ├── __init__.py
    ├── utils.py
    └── models.py
    ```

---

## ORM (Object Relational Mapping)

### Q1. What is an ORM in Python?
**Answer:**
ORM stands for **Object Relational Mapping**. It's a programming technique that allows developers to interact with databases using Python objects rather than writing raw SQL queries.

**Popular Python ORMs:**
1. SQLAlchemy
2. Django ORM
3. Peewee
4. Tortoise ORM

### Q2. Connection String Components
**Question:** What are the typical components of a connection string?

**Answer:**
Most connection strings include:
- **Driver** (postgresql, mysql, sqlite, mssql)
- **Username**
- **Password**
- **Host**
- **Port**
- **Database name**
- **Optional parameters** (timeout, sslmode, etc.)

**Example:**
```
postgresql://username:password@host:port/dbname
```

