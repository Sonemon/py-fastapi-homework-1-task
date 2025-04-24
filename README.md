# Movie Theater API Project

## Table of Contents
- [Project Structure Overview](#project-structure-overview)
- [Purpose of the Structure](#purpose-of-the-structure)
- [Tip: Using `get_db` for Dependency Injection in FastAPI](#tip-using-get_db-for-dependency-injection-in-fastapi)
- [How to Run the Project](#how-to-run-the-project)
  - [Running the Project with Docker Compose](#running-the-project-with-docker-compose)
  - [Manually Running the Project](#manually-running-the-project)
- [Notes](#notes)
- [Run Project with PyCharm](#run-project-with-pycharm)
- [Task Description: Extending the Cinema Application](#task-description-extending-the-cinema-application)
  - [Database Information](#database-information)
  - [Endpoints to Implement](#endpoints-to-implement)
  - [Instructions](#instructions)

---

## Movie Theater API Project

Welcome to the **Movie Theater API** project! This is an educational assignment designed to help you practice and improve your skills in building web applications using FastAPI, SQLAlchemy, and Docker. The foundation of the project has already been prepared, including:

- **Database setup**: A SQLite database is configured and ready to use.
- **Data population**: The database can be filled with data from a pre-existing dataset using a pre-written script.
- **Docker integration**: The project is pre-configured to run in Docker, simplifying the setup process.
- **Project structure**: The core project structure, including routing, database models, and utility scripts, is already in place.

### Project Structure Overview

The project is structured to facilitate modular development and ease of maintenance. Below is a detailed explanation of the directories and files:

Here is a visual representation of the project structure:

```
.
├── Dockerfile
├── README.md
├── commands
│   └── run.sh
├── docker-compose.yml
├── poetry.lock
├── pyproject.toml
├── pytest.ini
└── src
    ├── config
    │   ├── __init__.py
    │   └── settings.py
    ├── database
    │   ├── __init__.py
    │   ├── models.py
    │   ├── populate.py
    │   ├── seed_data
    │   │   └── imdb_movies.csv
    │   ├── session.py
    │   └── source
    │       └── movies.db
    ├── main.py
    ├── routes
    │   ├── __init__.py
    │   └── movies.py
    ├── schemas
    │   ├── __init__.py
    │   └── movies.py
    └── tests
        ├── __init__.py
        ├── conftest.py
        └── test_integration
            ├── __init__.py
            └── test_movies.py
```

This representation outlines the hierarchy of files and directories within the project, showing the relationships between components.

#### Root Directory
- **`Dockerfile`**: Contains the instructions to build the Docker image for the application.
- **`docker-compose.yml`**: Defines the services, networks, and volumes for Docker Compose to run the project.
- **`poetry.lock`**: Dependency lock file managed by Poetry to ensure consistent environments.
- **`pyproject.toml`**: Configuration file for Poetry, defining project metadata and dependencies.
- **`README.md`**: Placeholder for the project description and instructions (to be completed).
- **`commands/run.sh`**: A script used to initialize or run the application in a containerized environment.
- **`pytest.ini`**: Configuration file for Pytest, specifying settings for running tests.

#### `src` Directory
The main source directory for the application.

- **`src/config/`**
  - **`settings.py`**: Contains project configurations, including paths for the database and seed data. Implements environment-specific settings for testing and development.

- **`src/database/`**
  - **`models.py`**: Defines the database models using SQLAlchemy.
  - **`session.py`**: Handles the database connection and session management.
  - **`populate.py`**: Includes logic to populate the database with seed data from a CSV file.
  - **`seed_data/imdb_movies.csv`**: CSV file containing sample movie data for initial database seeding.
  - **`source/movies.db`**: SQLite database file (used in non-testing environments).

- **`src/routes/`**
  - **`movies.py`**: Defines the FastAPI routes (endpoints) for interacting with the movie database.

- **`src/schemas/`**
  - **`movies.py`**: Contains Pydantic models for request validation and response serialization.

- **`src/tests/`**
  - **`conftest.py`**: Defines reusable Pytest fixtures for tests, including database resets and test client setup.
  - **`test_integration/test_movies.py`**: Contains integration tests for the movie-related endpoints to ensure they work as expected.

- **`src/main.py`**
  - The entry point of the application. Configures FastAPI and mounts the defined routes.

### Purpose of the Structure
This structure ensures:
1. Clear separation of concerns (e.g., routing, database, configurations, testing).
2. Easy integration of Docker for development and production environments.
3. Ready-to-use seed data for database initialization during development and testing.
4. Modular and maintainable codebase for extending functionality.

### Tip: Using `get_db` for Dependency Injection in FastAPI

The `get_db` function is a generator that provides a SQLAlchemy session for interacting with the database. This function is particularly useful in FastAPI as it can be injected into route handlers using the `Depends` mechanism. Here’s how you can use it effectively:

```python
from fastapi import Depends, APIRouter
from sqlalchemy.ext.asyncio import AsyncSession
from database import get_db  # Import the get_db generator

router = APIRouter()

@router.get("/example")
async def example_route(db: AsyncSession = Depends(get_db)):
    # Use the db session here to interact with the database
```

#### Key Points:
- The `Depends` function simplifies injecting dependencies like the database session into your route handlers.
- The `get_db` function ensures proper session handling: the session is created before the route logic executes and is closed automatically afterward.
- This approach promotes cleaner, more testable code by separating dependency setup from business logic.

You can use this pattern across your application for any routes that need database access.

### How to Run the Project

This project is designed to run with **Docker Compose** for ease of setup and also supports manual setup for local development.

---

#### **Running the Project with Docker Compose**

1. Ensure Docker and Docker Compose are installed on your system.
2. Navigate to the root directory of the project (where the `docker-compose.yml` file is located).
3. Run the following command to start the services:

   ```bash
   docker-compose up --build
   ```

4. When the containers start, the application will:
   - Automatically populate the database with movie data from the provided dataset (`imdb_movies.csv`).
   - Launch the API server, which will be accessible at [http://localhost:8000](http://localhost:8000).

5. To stop the services, press `Ctrl+C` and then run:

   ```bash
   docker-compose down
   ```

---

#### **Manually Running the Project**

If you prefer to run the project without Docker, you can set it up manually by following these steps:

1. **Set up a Python virtual environment**:
   ```bash
   python -m venv .venv
   ```

2. **Activate the virtual environment**:
   - On macOS/Linux:
     ```bash
     source .venv/bin/activate
     ```
   - On Windows:
     ```bash
     .venv\Scripts\activate
     ```

3. **Install Poetry**:
   ```bash
   pip install poetry
   ```

4. **Install project dependencies**:
   ```bash
   poetry install
   ```

5. **Populate the database**:
   Run the database seeding script to populate the SQLite database:
   ```bash
   python src/database/populate.py
   ```

6. **Start the FastAPI application**:
   ```bash
   uvicorn src.main:app --host 0.0.0.0 --port 8000 --reload
   ```

   The API will be accessible at [http://localhost:8000](http://localhost:8000).

---

### Notes

- **Database Seeding**: Whether running through Docker Compose or manually, the database is automatically populated with movie data during the initial setup.
- **API Documentation**: Once the application is running, you can explore the API documentation at [http://localhost:8000/docs](http://localhost:8000/docs).
- **Testing**: Tests can be executed using `pytest` with the following command:
  ```bash
  pytest
  ```

This setup ensures flexibility, whether you prefer running the project in a containerized environment or directly on your development machine.


### Run project with PyCharm

1. Mark the `src` directory as root
    ![mark src directory as root](./assets/images/1_mark_directory_as_source_root.png)
2. Close all open code tabs
3. Restart `PyCharm`
4. Create a new Run Configuration for the `FastAPI` project
    ![create new fastapi run configuration](./assets/images/2_create_new_run_config.png)
5. Run the project via `PyCharm` and use breakpoints for debugging


### Task Description: Extending the Cinema Application

In this assignment, you are tasked with continuing the development of the cinema application. Your objective is to implement two endpoints in the `schemas/movies.py` and `routes/movies.py` files. The rest of the application, including database models and utility functions, has already been provided.

#### Database Information

The `database/models.py` file contains a `MovieModel` class that defines the schema for the `movies` table. This table has the following fields:

- `id`: Primary key
- `name`: Movie title
- `date`: Release date
- `score`: Movie rating (a float between 0 and 100)
- `genre`: Movie genres (comma-separated)
- `overview`: Short description of the movie
- `crew`: List of cast and crew
- `orig_title`: Original title of the movie
- `status`: Release status (e.g., "Released", "Post-production")
- `orig_lang`: Original language
- `budget`: Movie budget
- `revenue`: Revenue generated
- `country`: Country of production

#### Endpoints to Implement

1. **Get a Paginated List of Movies**
   - **URL**: `/movies/`
   - **Method**: `GET`
   - **Query Parameters**:
     - `page` (integer, required, default: `1`, must be >= 1): The page number to fetch.
     - `per_page` (integer, required, default: `10`, must be >= 1 and <= 20): Number of movies to fetch per page.
   - **Responses**:
     - **200 OK**: Returns a paginated list of movies.
       - **Example Response**:
         ```json
         {
           "movies": [
             {
               "id": 1,
               "name": "Creed III",
               "date": "2023-03-02",
               "score": 73,
               "genre": "Drama,Action",
               "overview": "After dominating the boxing world, Adonis Creed has been thriving in both his career and family life...",
               "crew": "Michael B. Jordan, Tessa Thompson",
               "orig_title": "Creed III",
               "status": "Released",
               "orig_lang": "English",
               "budget": 75000000,
               "revenue": 271616668,
               "country": "AU"
             }
           ],
           "prev_page": "/theater/movies/?page=1&per_page=10",
           "next_page": "/theater/movies/?page=3&per_page=10",
           "total_pages": 1000,
           "total_items": 9999
         }
         ```
     - **404 Not Found**: If no movies are found.
       - **Example Response**:
         ```json
         {
           "detail": "No movies found."
         }
         ```
     - **422 Unprocessable Entity**: If `page` or `per_page` is invalid.
       - **Example Response**:
         ```json
         {
           "detail": [
             {
               "loc": ["query", "page"],
               "msg": "ensure this value is greater than or equal to 1",
               "type": "value_error.number.not_ge"
             }
           ]
         }
         ```

2. **Get Movie Details by ID**
   - **URL**: `/movies/{movie_id}/`
   - **Method**: `GET`
   - **Path Parameters**:
     - `movie_id` (integer, required): The ID of the movie to fetch.
   - **Responses**:
     - **200 OK**: Returns detailed information about the movie.
       - **Example Response**:
         ```json
         {
           "id": 1,
           "name": "Creed III",
           "date": "2023-03-02",
           "score": 73,
           "genre": "Drama,Action",
           "overview": "After dominating the boxing world, Adonis Creed has been thriving in both his career and family life...",
           "crew": "Michael B. Jordan, Tessa Thompson",
           "orig_title": "Creed III",
           "status": "Released",
           "orig_lang": "English",
           "budget": 75000000,
           "revenue": 271616668,
           "country": "AU"
         }
         ```
     - **404 Not Found**: If no movie with the given ID exists.
       - **Example Response**:
         ```json
         {
           "detail": "Movie with the given ID was not found."
         }
         ```

#### Instructions

1. Open the `schemas/movies.py` file and define the following schemas:
   - **`MovieDetailResponseSchema`**: Contains all fields for a single movie, including `id`, `name`, `date`, etc.
   - **`MovieListResponseSchema`**: Contains a list of movies (`movies`), pagination links (`prev_page` and `next_page`), and total counts (`total_pages` and `total_items`).

2. Open the `routes/movies.py` file and implement the two endpoints:
   - Use the provided database session generator (`get_db`) to query the database.
   - Ensure proper error handling for invalid parameters and missing data.
   - Return responses that match the provided examples exactly.

3. Run the provided tests to validate your implementation. Your code must pass all tests for the task to be considered complete.
