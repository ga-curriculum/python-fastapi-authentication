<h1>
  <span class="headline">FastAPI Authentication</span>
  <span class="subhead">Creating User Controller</span>
</h1>

**Learning objective:** By the end of this lesson, you will be able to create and mount a user router module in a Python FastAPI application.

## Creating and mounting a `user` router

Now that we have a user model and serializer, it's time to create a controller to handle user-related API requests.

### 1. Creating the `user` controller

A **controller** is responsible for handling requests related to users.

Create a new file: `controllers/users.py`

```sh
touch controllers/users.py
```

Add the following `POST` route for creating or "registering" new users in our app:

```py
# controllers/users.py

from fastapi import APIRouter, Depends, HTTPException
from sqlalchemy.orm import Session
from models.user import UserModel
from serializers.user import UserSchema
from database import get_db

router = APIRouter()

@router.post("/register", response_model=UserSchema)
def create_user(user: UserSchema, db: Session = Depends(get_db)):
    # Check if the username or email already exists
    existing_user = db.query(UserModel).filter(
        (UserModel.username == user.username) | (UserModel.email == user.email)
    ).first()

    if existing_user:
        raise HTTPException(status_code=400, detail="Username or email already exists")

    new_user = UserModel(username=user.username, email=user.email)
    # Use the set_password method to hash the password
    new_user.set_password(user.password)

    db.add(new_user)
    db.commit()
    db.refresh(new_user)

    return new_user
```

#### Let's take a look at key parts of this controller code

1. **Checks if the username or email already exists**

   - This prevents duplicate users.
   - If the user exists, it returns an error (`400 Bad Request`).

2. **Hashes the password before storing it**
   - Uses `passlib` to hash the password for security.

## 2. Mounting the `user` router in `main.py`

Now that we have created the user controller, we need to **register it** in our FastAPI app.

Update `main.py` as follows:

```py
# main.py

from fastapi import FastAPI
from controllers.teas import router as TeasRouter
from controllers.users import router as UsersRouter  # Import users router

app = FastAPI()

# Register API routes
app.include_router(TeasRouter, prefix="/api")
app.include_router(UsersRouter, prefix="/api")  # Include users router

@app.get('/')
def home():
    return 'Hello World!'
```
