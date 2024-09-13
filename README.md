# introfastapi
⚡ FastAPI for the Python backend API.
💾 PostgreSQL as the SQL database.



This is a project to learn APIs
To execute this project :
```sh 
pip install fastapi
```




## The Database
Next let’s incorporate a sqlite database to store our question and response list items. The workhorse package we’ll use for database operations is sqlalchemy. sqlalchemy is powerful but complex.

First, let’s install sqlalchemy with 
```sh 
pip install sqlalchemy 
```
this is the database table that i wanted to create:

Questions Table:

- **id**: A unique identifier for each question. (Primary Key)
- **Content**: The text of the question.
- **Responses**: {
  - **id**: A unique identifier for each response. (Primary Key)
  - **response_text**: The text of the response.
  - **Is_correct**: A boolean indicating if the response is correct (`TRUE`) or incorrect (`FALSE`).}


## Problem and Solution

### Problem with SQLite

**Issue:** SQLite does not support storing dictionaries directly within a table.

**Solution:** Migration to PostgreSQL, which provides more advanced relational database features.

### Problem with PostgreSQL

**Issue:** PostgreSQL also does not support storing dictionaries directly within a table.

**Solution:** Creation of two related tables to represent the data structure effectively.




## Database Schema

### Tables

#### `Questions` Table

- **id**: A unique identifier for each question. (Primary Key)
- **Content**: The text of the question.




#### `Responses` Table

- **id**: A unique identifier for each response. (Primary Key)
- **question_id**: A foreign key linking each response to a question in the `Questions` table.
- **response_text**: The text of the response.
- **Is_correct**: A boolean indicating if the response is correct (`TRUE`) or incorrect (`FALSE`).

### SQL Code

To create the tables in PostgreSQL, use the following SQL commands:

```sql
-- Create the Questions table
CREATE TABLE Questions (
    id SERIAL PRIMARY KEY,
    content TEXT 
);

-- Create the Responses table
CREATE TABLE Responses (
    id SERIAL PRIMARY KEY,
    question_id INTEGER REFERENCES Questions(id) ON DELETE CASCADE,
    response TEXT ,
    is_correct BOOLEAN 
);
```

## CRUD (Create, Read, Update, Delete):
 operations for managing questions and their associated responses. It is built using FastAPI and interacts with a PostgreSQL database.
```sh 
# Initialize app
app = FastAPI() 
```
```sh 
fastapi dev main.py
```
![redoc](https://github.com/rim373/introfastapi/blob/main/pics/redoc.png)

##Interactive API Documentation

![docs](https://github.com/rim373/introfastapi/blob/main/pics/docs.png)


Here's a brief description for each function :

---

### API Endpoints

#### Create a New Question

![docs](https://github.com/rim373/introfastapi/blob/main/pics/post.png)

**Description:** 
This endpoint creates a new question in the database. It receives a `QuestionRequest` object containing the question content and associated responses. It adds the question to the database, commits the transaction, and then associates each response with the newly created question before committing again. It returns the created question object.

---

#### Read All Questions
![docs](https://github.com/rim373/introfastapi/blob/main/pics/affichetot.png)

**Description:** 
This endpoint retrieves all questions from the database. It queries for all questions and their associated responses, then constructs a list of `QuestionRequest` objects with the content and choices. It returns this list.

---

#### Read a Single Question by ID
![docs](https://github.com/rim373/introfastapi/blob/main/pics/affiche.png)

**Description:** 
This endpoint retrieves a specific question by its ID. It queries the database for the question and its associated responses. If found, it returns the question content along with a dictionary of responses. If the question does not exist, it raises a 404 HTTPException.

---

#### Update a Question's Content
![docs](https://github.com/rim373/introfastapi/blob/main/pics/updataqueston.png)

**Description:** 
This endpoint updates the content of a question identified by its ID. It finds the question in the database, updates its content, and commits the changes. If the question does not exist, it raises a 404 HTTPException. It returns the updated question.

---

#### Update a Response for a Question
![docs](https://github.com/rim373/introfastapi/blob/main/pics/update%20response.png)

**Description:** 
This endpoint updates a specific response associated with a question. It locates the question and response in the database, updates the response text, and commits the change. If the question or response is not found, it raises a 404 HTTPException. It returns the updated response information.

---

#### Delete a Question
![docs](https://github.com/rim373/introfastapi/blob/main/pics/del%20question.png)

**Description:** 
This endpoint deletes a question by its ID. It removes the question and all associated responses from the database. If the question does not exist, it raises a 404 HTTPException. It returns a 204 No Content status upon successful deletion.

---

#### Delete a Specific Response
![docs](https://github.com/rim373/introfastapi/blob/main/pics/delate%20respnse.png)

**Description:** 
This endpoint deletes a specific response associated with a question. It locates the question and the response to be deleted, removes the response from the database, and commits the change. If the question or response is not found, it raises a 404 HTTPException. It returns a confirmation message upon successful deletion.

















