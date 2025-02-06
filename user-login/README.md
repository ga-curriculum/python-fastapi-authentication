<h1>
  <span class="headline">FastAPI Authentication</span>
  <span class="subhead">User Login</span>
</h1>

**Learning objective:** By the end of this lesson, you will be able to implement a login route that authenticates a user using their username and password and generates a JWT token in a Python FastAPI application.

## User login and `JWT` token creation

In this lesson, we'll implement a **login route** where users can authenticate by providing their **username** and **password**. We will verify the provided password against the hashed password stored in the database using a `verify_password` method. If the credentials are correct, we’ll generate and return a **JWT token**.

### Installing dependencies

Before implementing authentication, ensure you have installed `pyjwt`, which is required for generating JWT tokens:

```bash
pipenv install pyjwt
```

### 1. Updating the `user` model

Since the **UserModel** already includes the `set_password` method to hash and store passwords, we will now add two additional methods:

1. A `verify_password` method to check if the entered password matches the stored hashed password. This will help us confirm the user's identity during login.

2. A `generate_token` method to create a **JWT token**. This token will be returned to the user after successful authentication, allowing them to access protected routes.

```py
# models/user.py

from sqlalchemy import Column, Integer, String
from .base import Base
from passlib.context import CryptContext
from datetime import datetime, timedelta  # New import for timestamps
import jwt  # New import for token generation

# Import the secret from the environment file
from config.environment import secret

pwd_context = CryptContext(schemes=["bcrypt"], deprecated="auto")

class UserModel(Base):

    __tablename__ = "users"

    id = Column(Integer, primary_key=True, index=True)
    username = Column(String, nullable=False, unique=True)
    email = Column(String, nullable=False, unique=True)
    password_hash = Column(String, nullable=True)

    def set_password(self, password: str):
        self.password_hash = pwd_context.hash(password)

    # Method to verify the password
    def verify_password(self, password: str) -> bool:
        return pwd_context.verify(password, self.password_hash)

    # Method to generate a JWT token
    def generate_token(self):
        # Define the payload
        payload = {
            "exp": datetime.now(timezone.utc) + timedelta(days=1),  # Expiration time (1 day)
            "iat": datetime.now(timezone.utc),  # Issued at time
            "sub": self.id,  # Subject - the user ID
        }

        # Create the JWT token
        token = jwt.encode(payload, secret, algorithm="HS256")

        return token
```

> The `generate_token` method has been added to the `UserModel` class, which means `self` refers to the specific user object that is logging in. This method creates a JWT token using the `encode` function from the `jwt` library.

To secure the token, we use a secret key, which is imported from the `config/environment.py` file. This secret key will be used during token encoding to ensure that only authorized users can generate valid tokens.

In the `config/environment.py` file, we'll define a secret key for JWT generation:

```py
# config/environment.py

db_URI = "postgresql://postgres:postgres@localhost:5432/teas_db"
secret = "mysecretcode"  # This secret is used to sign the JWT token
```

### 2. Updating the serializer

Next, we will need to update the serializer by defining the input and output schemas needed for login:

```py
# serializers/user.py

from pydantic import BaseModel

class UserSchema(BaseModel):
    username: str
    email: str
    password: str

    class Config:
        orm_mode = True

class UserResponseSchema(BaseModel):
    username: str
    email: str

# New schema for user login (captures username and password during login)
class UserLogin(BaseModel):
    username: str  # Username provided by the user during login
    password: str  # Plain text password provided by the user during login

# New schema for the response (containing the JWT token and a success message)
class UserToken(BaseModel):
    token: str  # JWT token generated upon successful login
    message: str  # Success message

    class Config:
        orm_mode = True
```

### 2. Creating the login route

Next, we will create the **login route** where users can send their **username** and **password**. We’ll check if the provided credentials are correct by verifying the password against the stored hashed password. If they match, we will generate a **JWT token**.

```py
# controllers/users.py

from fastapi import APIRouter, Depends, HTTPException
from sqlalchemy.orm import Session
from models.user import UserModel
from serializers.user import UserSchema, UserLogin, UserToken # Add new serializers for login
from database import get_db

router = APIRouter()

@router.post("/register", response_model=UserSchema)
def create_user(user: UserSchema, db: Session = Depends(get_db)):
  # ...


@router.post("/login", response_model=UserToken)
def login(user: UserLogin, db: Session = Depends(get_db)):

    # Find the user by username
    db_user = db.query(UserModel).filter(UserModel.username == user.username).first()

    # Check if the user exists and if the password is correct
    if not db_user or not db_user.verify_password(user.password):
        raise HTTPException(status_code=400, detail="Invalid username or password")

    # Generate JWT token
    token = db_user.generate_token()

    # Return token and a success message
    return {"token": token, "message": "Login successful"}
```

### 4. Testing the login route

Start the development server:

```bash
pipenv run uvicorn main:app --reload
```

- You should now have the app running. Visit [`http://127.0.0.1:8000`](http://127.0.0.1:8000) in your browser to confirm it’s working.

Now you can test each endpoint using FastAPI’s built-in documentation.

- Navigate to FastAPI Documentation: Open [`http://localhost:8000/docs`](http://localhost:8000/docs) in your browser.

**Register a User** by sending a **POST request** to `/register` with the username, email, and password. This step ensures the password is hashed upon registration.

Example request body:

```json
{
  "username": "li_wei",
  "email": "li.wei@email.cn",
  "password": "securepassword6"
}
```

**Login** by sending a **POST request** to `/login` with the **username** and **password** you registered:

Example request body:

```json
{
  "username": "li_wei",
  "password": "securepassword6"
}
```

If the authentication is successful, you will receive a response with the **JWT token** and a **success message**:

Example response:

```json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "message": "Login successful"
}
```

This JWT token can now be used for **authorization** in future requests. 🚀
