# FastAPI-CRUD-SQLite

This project demonstrates a basic CRUD application using FastAPI with SQLite as the database. The project is structured to help beginners understand how to set up and use FastAPI with SQLAlchemy and Pydantic.

## Project Structure

```
FastAPI-CRUD-SQLite
├── sql_app
│   ├── __init__.py
│   ├── crud.py
│   ├── database.py
│   ├── models.py
│   ├── schemas.py
│   └── main.py
└── README.md
```

### File Descriptions

1. **`__init__.py`**: This file is usually empty but necessary to mark the directory as a Python package. It allows you to import files from the `sql_app` directory.

2. **`crud.py`**: Contains functions for CRUD (Create, Read, Update, Delete) operations. These functions interact with the database to perform the necessary operations. For example, the function `get_user` retrieves a user by their ID, and `create_user` adds a new user to the database.

3. **`database.py`**: Sets up the database connection using SQLAlchemy. It defines the database URL and creates the engine and sessionmaker. The `Base` variable is the base class for all our models.

   ```python
   SQLALCHEMY_DATABASE_URL = "sqlite:///./sql_app.db"
   engine = create_engine(SQLALCHEMY_DATABASE_URL, connect_args={"check_same_thread": False})
   SessionLocal = sessionmaker(autoflush=False, autocommit=False, bind=engine)
   Base = declarative_base()
   ```

4. **`models.py`**: Defines the database models using SQLAlchemy. It includes the `User` and `Item` models, which represent the database tables. Each model class is a subclass of `Base` and includes columns and relationships.

   ```python
   class User(Base):
       __tablename__ = "users"
       id = Column(Integer, primary_key=True)
       email = Column(String, unique=True, index=True)
       hashed_password = Column(String)
       is_active = Column(Boolean, default=True)
       items = relationship("Item", back_populates="owner", cascade="all, delete-orphan")
   ```

5. **`schemas.py`**: Defines the Pydantic models for data validation. These models specify the structure of the request and response data. For example, `UserCreate` is used to validate data when creating a new user, while `User` is used to format the response data.

   ```python
   class UserCreate(BaseModel):
       email: str
       password: str
   ```

6. **`main.py`**: The entry point of the application. It sets up the FastAPI app and includes the routes. It defines the API endpoints and links them to the corresponding CRUD functions. For example, the endpoint `POST /users/` calls the `create_user` function to add a new user.

   ```python
   models.Base.metadata.create_all(bind=engine)
   ```

   This line creates all the database tables defined by the SQLAlchemy models. The `metadata.create_all(bind=engine)` method binds the metadata to the engine, creating the tables in the database specified by the engine.

   ```python
   app = FastAPI()
   ```

   This line initializes a FastAPI application. `FastAPI()` creates an instance of the FastAPI class, which is used to define the API routes and handle incoming requests.

   ```python
   @app.post("/users/", response_model=schemas.User)
   def create_user(user: schemas.UserCreate, db: Session = Depends(get_db)):
       db_user = crud.get_user_by_email(db, email=user.email)
       if db_user:
           raise HTTPException(status_code=400, detail="Email already registered")
       return crud.create_user(db=db, user=user)
   ```

## Setup and Installation

1. **Clone the repository:**

   ```bash
   git clone https://github.com/hurairaz/FastAPI-CRUD-SQLite.git
   cd FastAPI-CRUD-SQLite
   ```

2. **Create a virtual environment(IF NOT EXISTS):**

   ```bash
   python -m venv venv
   source venv/bin/activate  # On Windows use `venv\Scripts\activate`
   ```

3. **Install the dependencies:**

   ```bash
   pip install -r requirements.txt
   ```

4. **Run the application:**

   ```bash
   uvicorn sql_app.main:app --reload
   ```

5. **Access the API documentation:**

   Open your browser and go to `http://127.0.0.1:8000/docs` to see the interactive API documentation provided by Swagger UI.

---

## Troubleshooting

### In case you are not accessing anything on your application at `http://127.0.0.1:8000`, follow these steps to resolve it:

1. **Ensure that port 8000 is not already in use:**

   ```bash
   netstat -ano | findstr :8000
   ```

2. **If port 8000 is in use, terminate the process (replace `PID` with the appropriate process ID):**

   ```bash
   taskkill /PID <PID> /F
   ```

3. **Check for any remaining Python processes:**

   ```bash
   tasklist | findstr python
   ```

4. **If Python processes are still active, terminate them:**

   ```bash
   taskkill /PID <PID> /F
   ```

5. **Run the FastAPI application using uvicorn:**

   ```bash
   uvicorn main:app --reload
   ```

6. **Access the API at`http://127.0.0.1:8000` in your browser or API client.**

