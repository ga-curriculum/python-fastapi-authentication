<h1>
  <span class="headline">FastAPI Authentication</span>
  <span class="subhead">Creating User Model</span>
</h1>

**Learning objective:** By the end of this lesson, you will be able to create a new SQLAlchemy user model.

## Overview

In this lesson, we will create a **user model** to represent users in our database.

In SQLAlchemy, all data models—whether for users, products, or any other entity—follow the same basic structure. This means that creating a `UserModel` is similar to creating a model for any other type of data, such as a list of teas or blog posts.

## Creating the User Model

Below is the Python code for defining our **UserModel** using SQLAlchemy.

```py
# models/user.py

from sqlalchemy import Column, Integer, String
from .base import BaseModel

class UserModel(BaseModel):

    __tablename__ = "users"

    id = Column(Integer, primary_key=True, index=True)
    username = Column(String, unique=True)  # Each username must be unique
    email = Column(String, unique=True)  # Each email must be unique
```

Let's go over key parts of this model:

1. **Inheriting from `BaseModel`**

   - `UserModel` is inheriting from `BaseModel`.
   - `BaseModel` is a class that we are using as a foundation for all our models. It includes shared functionality for all tables in our database.

2. **Unique `username` and `email`**

   - `unique=True` ensures that no two users can have the same username or email.

### Why Use `unique=True`?

By making `username` and `email` **unique**, we prevent users from creating duplicate accounts with the same information. This is useful for maintaining data integrity and avoiding conflicts in our system.
