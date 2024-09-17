# Ground Floor Removal from NuScenes Dataset using RANSAC
## Project Overview
This project aims to remove the ground floor from the NuScenes dataset using the RANSAC algorithm. The main goal is to process LiDAR point cloud data to eliminate unnecessary ground points while retaining valuable information for further object detection and classification tasks.

## Project Workflow
The workflow of the project involves several stages:

### 1. Reading LiDAR Point Cloud Data
-We use Python and the Open3D library to read the LiDAR point cloud data from the NuScenes dataset.

```sh 
import open3d as o3d

```
-The point clouds are loaded with this function
```sh 
get_point_cloud(directory,filname)
```
###2. Voxelization
Voxel Grid Downsampling is applied to reduce the number of points, which speeds up processing and removes noise. A 3D voxel grid is created, and points are subsampled based on the centroid within each voxel.
Key steps for voxelization:
1. Define a voxel size (smaller sizes for more detail, larger sizes for faster processing).
2 .Divide the 3D space into voxels.
3. Retain one point per voxel, either by centroid or other methods.
4. Remove redundant points in overlapping voxels.
The following function contributes to the filtering process.
```sh 
voxalisation (pcd)
```
###3. Filtering the Point Cloud
-We filter the point cloud along the z-axis to remove points below a specific threshold (z<100). This step ensures we retain useful information while excluding irrelevant ground-level points.
-Horizontal filtering is avoided to preserve essential object data
The following function contributes to the filtering process.
```sh 
filter (o3d_pcd)
```
###4. RANSAC Algorithm
RANSAC (Random Sample Consensus) is used to detect and remove planar surfaces from the point cloud, such as ground planes. This method iteratively fits a plane model to the point cloud, removing points that align with the ground.
The following function contributes to the filtering process.
```sh 
ransac(point_cloud, distance_threshold=0.1, ransac_n=3, num_iterations=100000)
```
###5. DBSCAN Clustering 
DBSCAN (Density-Based Spatial Clustering of Applications with Noise) is used to group nearby points into clusters. This helps in distinguishing different objects within the point cloud after ground removal.
Points that do not belong to any dense clusters are treated as noise
The following function contributes to the filtering process.
```sh 
dbscan(outlier_cloud, eps=1.0, min_points=10)
```
###6. 3D Visualization and Storage
The processed point clouds can be visualized in 3D, and stored in the LiDAR data format based on a boolean control parameter.
```sh 
visualize_point_clouds(*point_clouds, show=True, save=False, output_folder='output', filename='new_lidar_point_could_file')
```
###7. Annotation and Model Evaluation
To evaluate the quantitative performance of the model, it is necessary to annotate the ground points, as the NuScenes dataset does not provide explicit ground annotations.
We used the platform Segments.ai for manual annotation.
![redoc](https://github.com/rim373/introfastapi/blob/main/pics/redoc.png)
With Segments.ai, images are uploaded, and regions corresponding to the ground are manually annotated. These annotations are then integrated with the LiDAR data for further evaluation.
⚡ FastAPI for the Python backend API.
💾 PostgreSQL as the SQL database.




```sh 
pip install fastapi
```





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

















