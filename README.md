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

### 2. Voxelization
Voxel Grid Downsampling is applied to reduce the number of points, which speeds up processing and removes noise. A 3D voxel grid is created, and points are subsampled based on the centroid within each voxel.
A voxel (short for volume element) is the 3D equivalent of a pixel in 2D images. It represents a value on a regular grid in three-dimensional space. Just as a pixel is a single point of information in a 2D image, a voxel is a small cubic unit of space in a 3D structure.
In the context of point clouds or 3D data:
Voxelization is the process of dividing a 3D space into discrete, uniform cubes (voxels) to represent the volume of objects.
Each voxel can store information about the points within it, such as position, color, or density, which helps in simplifying the data or improving processing efficiency.
![redoc](https://github.com/rim373/NuScenes_Lidar_Data_Preprocessinig/blob/main/doc/voxel.png)

** Key steps for voxelization: **
1. Define a voxel size (smaller sizes for more detail, larger sizes for faster processing).
2 .Divide the 3D space into voxels.
3. Retain one point per voxel, either by centroid or other methods.
4. Remove redundant points in overlapping voxels.
The following function contributes to the filtering process.
```sh 
voxalisation (pcd)
```
This is an example from the KITTI dataset explaining how the voxel size affects the data.
![redoc](https://github.com/rim373/NuScenes_Lidar_Data_Preprocessinig/blob/main/doc/voxel_by_size.png)
### 3. Filtering the Point Cloud
-We filter the point cloud along the z-axis to remove points below a specific threshold (z<100). This step ensures we retain useful information while excluding irrelevant ground-level points.
-Horizontal filtering is avoided to preserve essential object data
The following function contributes to the filtering process.
```sh 
filter (o3d_pcd)
```
### 4. RANSAC Algorithm
RANSAC (Random Sample Consensus) is used to detect and remove planar surfaces from the point cloud, such as ground planes. This method iteratively fits a plane model to the point cloud, removing points that align with the ground.
The following function contributes to the filtering process.
```sh 
ransac(point_cloud, distance_threshold=0.1, ransac_n=3, num_iterations=100000)
```
This is an example of NuScenes data output after applying the RANSAC algorithm.
![redoc](https://github.com/rim373/NuScenes_Lidar_Data_Preprocessinig/blob/main/doc/00000.png)
### 5. DBSCAN Clustering 
DBSCAN (Density-Based Spatial Clustering of Applications with Noise) is used to group nearby points into clusters. This helps in distinguishing different objects within the point cloud after ground removal.
Points that do not belong to any dense clusters are treated as noise
The following function contributes to the filtering process.
```sh 
dbscan(outlier_cloud, eps=1.0, min_points=10)
```
This figure explains how the clustering works.
![redoc](https://github.com/rim373/NuScenes_Lidar_Data_Preprocessinig/blob/main/doc/culstur.png)
### 6. 3D Visualization and Storage
The processed point clouds can be visualized in 3D, and stored in the LiDAR data format based on a boolean control parameter.
```sh 
visualize_point_clouds(*point_clouds, show=True, save=False, output_folder='output', filename='new_lidar_point_could_file')
```
### 7. Annotation and Model Evaluation
To evaluate the quantitative performance of the model, it is necessary to annotate the ground points, as the NuScenes dataset does not provide explicit ground annotations.
We used the platform Segments.ai for manual annotation.

With Segments.ai, images are uploaded, and regions corresponding to the ground are manually annotated. These annotations are then integrated with the LiDAR data for further evaluation.

Here’s a step-by-step guide to help you get started with Segments.ai for annotating your dataset:

#### 1. Create an Account on Segments.ai
-Go to Segments.ai and sign up for an account.
-After logging in, you’ll have access to the dashboard where you can create new projects, upload datasets, and begin annotating.
#### 2. Create a New Project
-Once you're logged in, click on Create New Project.
-Choose the type of project you want to create (e.g., image segmentation, bounding boxes).
-Provide a name and description for your project, then click Create Project.
#### 3. Upload Your Dataset
-In your project dashboard, click on Datasets and then select Upload Dataset.
-You can upload images or point clouds in supported formats.
-If working with point clouds, you can export your LiDAR data as images or projections of the point cloud onto 2D planes.


#### 4. Creating Annotation Tasks
-After the dataset is uploaded, click Create Tasks.
-Tasks can be assigned to yourself or to team members for annotation.
-Choose Segmentation or Bounding Box depending on the nature of your annotation.
#### 5. Annotating the Dataset
-Once your tasks are set, go to the Annotate tab.
-The interface provides various tools like polygon selection, brush tools, and object labels to mark regions in the image.
-Select a label (e.g., “Ground”, “Vehicle”, etc.) and start drawing or brushing over the areas you want to annotate.


#### 6. Navigating the Annotation Tools
-Brush Tool: Use this tool to paint over areas you want to label as ground or other objects.
-Polygon Tool: Use this for more precise annotations, especially for defining object boundaries.
-Zoom & Pan: You can zoom in for more detailed annotations and pan around the image using the mouse or keyboard shortcuts.
-Undo/Redo: Easily correct any mistakes with undo/redo options.
#### 7. Export the Annotations
-Once the annotation is complete, you can export the annotated data.
-Segments.ai allows exporting in several formats (COCO, Pascal VOC, etc.) depending on your requirements.
-Download the labeled dataset and integrate it with your point cloud processing workflow.
#### 8. Integrating Annotations with Your Code
Once you’ve exported the annotations, you can load them into your Python script for further use.
#### 9. Advanced Features
-AI-assisted annotation: Segments.ai provides some AI-based tools that can assist in speeding up annotation tasks. These tools can help auto-detect objects or ground regions based on your initial annotations.
-Quality Assurance (QA): Use the QA tab to validate and review the accuracy of annotations, ensuring consistency and correctness.
These are pictures of sample data before annotation, after annotation, and the annotated part.
![redoc](https://github.com/rim373/NuScenes_Lidar_Data_Preprocessinig/blob/main/doc/label3.png)
![redoc](https://github.com/rim373/NuScenes_Lidar_Data_Preprocessinig/blob/main/doc/label1.png)
![redoc](https://github.com/rim373/NuScenes_Lidar_Data_Preprocessinig/blob/main/doc/label2.png)











