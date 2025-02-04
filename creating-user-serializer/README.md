<h1>
  <span class="headline">FastAPI Authentication</span>
  <span class="subhead">Creating User Serializer</span>
</h1>

**Learning objective:** By the end of this lesson, you will be able to define a user serializer in a Python FastAPI application, including handling hashed passwords.

## Creating the user serializer

In a FastAPI application, **serializers** help define the structure of data that we send and receive through our API.

A **user serializer** ensures that only the necessary fields—such as `username`, `email`, and now `password` (hashed)—are included when exchanging data.

In this section, we will:

- Define a `UserSchema` serializer for handling user data.
- Add password hashing support while ensuring security best practices.

Create a new file called `serializers/user.py` and add the following code:

```py
# serializers/user.py

from pydantic import BaseModel

class UserSchema(BaseModel):
    username: str  # User's unique name
    email: str  # User's email address
    password: str  # Plain text password for user registration (will be hashed before saving)

    class Config:
        orm_mode = True  # Enables compatibility with ORM models

# Schema for returning user data (without exposing the password)
class UserResponseSchema(BaseModel):
    username: str
    email: str

    class Config:
        orm_mode = True
```

## Why Separate `UserSchema` and `UserResponseSchema`?

Notice that we created two separate schemas for user data, and there's a good reason for this. Each schema serves a different purpose.

| Schema               | Purpose                                           | Includes Password?                  |
| -------------------- | ------------------------------------------------- | ----------------------------------- |
| `UserSchema`         | Used when receiving user input (ex: registration) | ✅ Yes (plain text, before hashing) |
| `UserResponseSchema` | Used when returning user data from API            | ❌ No (to protect security)         |

This separation prevents accidentally exposing user passwords when retrieving user data.

#### Let's review the code:

```py
class UserSchema(BaseModel):
    username: str
    email: str
    password: str
```

- This schema is used when a user **registers** and provides their **plain text password**.
- The password will **not be stored directly** but will be **hashed before saving** in the database.

```py
class UserResponseSchema(BaseModel):
    username: str
    email: str
```

- The API should **never return passwords**, _even_ if they are hashed.
- `UserResponseSchema` ensures only **safe** user data is sent in responses.

```py
class Config:
    orm_mode = True
```

- This allows FastAPI to **convert SQLAlchemy models** into JSON responses.
