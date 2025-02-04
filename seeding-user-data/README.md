<h1>
  <span class="headline">FastAPI Authentication</span>
  <span class="subhead">Seeding User Data</span>
</h1>

**Learning objective:** By the end of this lesson, you will be able to seed a database with test user data in a Python FastAPI MVC application, including securely storing passwords using hashing.

## Seeding the database with test `user` data

To quickly test our API, we need some **preloaded data** in our database. This is called **seeding** the database. We will update our `seed.py` file to handle this, along with a `user_data.py` file containing a list of test users.

### Update the seed script in `seed.py`

This script resets the database and adds initial test data, including `users`, `teas`, and `comments`.

```py
# seed.py

from sqlalchemy.orm import Session, sessionmaker
from models.base import Base
from models.tea import TeaModel
from models.comment import CommentModel
from models.user import UserModel
from data.tea_data import teas_list, comments_list
from data.user_data import user_list # Add user list
from config.environment import db_URI
from sqlalchemy import create_engine

engine = create_engine(db_URI)
SessionLocal = sessionmaker(bind=engine)

try:
    print("Recreating database...")
    Base.metadata.drop_all(bind=engine)
    Base.metadata.create_all(bind=engine)

    print("Seeding database...")
    db = SessionLocal()

    db.add_all(teas_list)
    db.commit()

    db.add_all(comments_list)
    db.commit()

    # add users
    db.add_all(user_list)
    db.commit()

    db.close()

    print("Database seeding complete! 👋")
except Exception as e:
    print("An error occurred:", e)
```

### Adding `user` data in `data/user_data.py`

We'll create a new data file separate from our `tea` and `comments` data to hold our test users.

```sh
touch data/user_data.py
```

Since we added `password hashing` to our user model, we must ensure that passwords are securely hashed before storing them in the database. To do this, we first create a user instance and then use the `set_password` method from our `user` model to hash the password before saving it.


```py
# data/user_data.py

from models.user import UserModel

def create_test_users():
    user1 = UserModel(username="arjun_dev", email="arjun@devmail.in")
    user1.set_password("securepassword1")
    user2 = UserModel(username="emma_johnson", email="emma.johnson@email.com")
    user user2.set_password("securepassword2")
    user3 = UserModel(username="fatima_ali", email="fatima.ali@mail.ae")
    user3.set_password("securepassword3")
    user4 = UserModel(username="lucas_silva", email="lucas.silva@correo.br")
    user4.set_password("securepassword4")
    user5 = UserModel(username="elena_popov", email="elena.popov@mail.ru")
    user5.set_password("securepassword5")

    return [user1, user2, user3, user4, user5]

user_list = create_test_users()
```

## Running the seed script

To seed your database (which will also create the users table if it doesn’t exist), run:

```sh
pipenv run python seed.py
```

If everything is working correctly, your database is now populated with test users! 🎉

### Optional: Check the `users` table

If you want to see if the `set_password` hashing function was successful, you can run a quick `sql` script in your terminal to see the `users` table.

Once inside the `psql` shell, run the following query to list all users:

```sql
SELECT * FROM users;
```

If the seeding worked, you should see a table output displaying the test `users` you added.

Example output:

| id  | username     | email                  | password_hash    |
| --- | ------------ | ---------------------- | ---------------- |
| 1   | arjun_dev    | arjun@devmail.in       | $2b$12$abc123... |
| 2   | emma_johnson | emma.johnson@email.com | $2b$12$def456... |
| 3   | fatima_ali   | fatima.ali@mail.ae     | $2b$12$ghi789... |
| 4   | lucas_silva  | lucas.silva@correo.br  | $2b$12$jkl012... |
| 5   | elena_popov  | elena.popov@mail.ru    | $2b$12$mno345... |

> Note: The password_hash column should contain `hashed` passwords, so you won’t see the actual plain-text passwords.

Once you're done, type:

```sh
 \q
```

This will exit the `psql` shell and return you to your terminal.
