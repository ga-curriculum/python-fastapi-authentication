<h1>
  <span class="headline">FastAPI Authentication</span>
  <span class="subhead">User Password Hashing</span>
</h1>

**Learning objective:** By the end of this lesson, you will be able to implement password hashing for user authentication in Python using the `passlib` library.

## Updating `user` model with hashed password

When storing user passwords in a database, we **should never store them as plain text**. If an attacker gains access to the database, they could easily see all user passwords.

To prevent this, we use **password hashing**. Hashing transforms a password into an unreadable format that cannot be reversed. When a user logs in, we **compare the hashed version** of the password they provide with the one stored in the database.

In this lesson, we will:

- Install the `passlib` library to hash passwords
- Update our `UserModel` to store hashed passwords
- Create a method to hash passwords before storing them

## Installing `passlib`

To hash passwords in Python, we use the [`passlib`](https://pypi.org/project/passlib/) library with the [`bcrypt`](https://pypi.org/project/bcrypt/) hashing algorithm.

Run the following command to install these packages:

```sh
pipenv install passlib bcrypt
```

### Why Use `bcrypt`?

- 🔒 **Secure:** It automatically adds **salt** (random data) to prevent attacks.
- 🛡️ **Slow Hashing:** Designed to make brute-force attacks difficult.
- 🔄 **Adaptive:** Can be reconfigured to remain secure over time.

## Updating the user model

To store hashed passwords, we need to update our `UserModel`:

1. Add a new field called `password_hash` to store the **hashed** password.
2. Create a method called `set_password()` to hash passwords before storing them.

```py
# models/user.py

from sqlalchemy import Column, Integer, String
from .base import Base
from passlib.context import CryptContext # Import new package

# Creating a password hashing context using bcrypt
pwd_context = CryptContext(schemes=["bcrypt"], deprecated="auto")

class UserModel(Base):

    __tablename__ = "users"

    id = Column(Integer, primary_key=True, index=True)
    username = Column(String, nullable=False, unique=True)
    email = Column(String, nullable=False, unique=True)
    password_hash = Column(String, nullable=True)  # Add new field for storing the hashed password

    # Method to hash and store the password
    def set_password(self, password: str):
        self.password_hash = pwd_context.hash(password)

```

### Let's review our changes

```py
pwd_context = CryptContext(schemes=["bcrypt"], deprecated="auto")
```

- `CryptContext` is a tool from `passlib` that helps manage password hashing.
- We specify `"bcrypt"` as the hashing algorithm.
- `deprecated="auto"` ensures we use the latest secure version.

```py
password_hash = Column(String, nullable=True)
```

- This new column stores the **hashed password** (not the plain text password).
- `nullable=True` means it can be empty (useful for test accounts without passwords). Optional, can be removed.

```py
def set_password(self, password: str):
    self.password_hash = pwd_context.hash(password)
```

- This method takes a **plain text password** and hashes it using `bcrypt`.
- The hashed password is then stored in `password_hash`.
