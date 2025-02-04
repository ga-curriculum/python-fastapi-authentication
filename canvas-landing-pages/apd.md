<h1>
  <span class="prefix">Python</span>
  <span class="headline">FastAPI Authentication</span>
</h1>

## About

This module covers the foundational concepts and practical implementations needed to authenticate users in a Python FastAPI application. Learners will explore the process of securely managing user credentials, from storing passwords using hashing techniques to generating and using JWT (JSON Web Tokens) for secure API access.

## Content

| Lesson                                                            | Est. Delivery Time | Skills                                                                                                                                                    |
| ----------------------------------------------------------------- | :----------------: | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Setup](../setup/README.md)                                       |       5 min        | Set up the development environment, including installing dependencies and configuring project structure.                                                  |
| [Concepts](../concepts/README.md)                                 |       15 min       | Learn about JWT (JSON Web Tokens) Authentication, including its purpose, structure, and usage in securing APIs.                                           |
| [Creating User Model](../creating-user-model/README.md)           |       10 min       | Create a SQLAlchemy model for a user, defining fields such as `username`, `email`, and `password_hash`, and database relations.                           |
| [User Password Hashing](../user-password-hashing/README.md)       |       15 min       | Implement password hashing for user authentication using the `passlib` library to securely store user passwords.                                          |
| [Creating User Serializer](../creating-user-serializer/README.md) |       10 min       | Define a Pydantic serializer to handle the validation and transformation of user data, including hashed passwords.                                        |
| [Creating User Controller](../creating-user-controller/README.md) |       15 min       | Create and mount a FastAPI router to handle user-related API requests such as registration, login, and password validation.                               |
| [Seeding User Data](../seeding-user-data/README.md)               |       10 min       | Seed a database with test user data to populate the application with example users for testing purposes.                                                  |
| [User Login](../user-login/README.md)                             |       25 min       | Implement a login route that authenticates users by validating their credentials (username and password) and generates a JWT token for authorized access. |
| **Total content**                                                 |      105 min       |                                                                                                                                                           |

## References

📖 [Reference Materials](../references/README.md)
