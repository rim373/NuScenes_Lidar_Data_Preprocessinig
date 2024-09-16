Ground Floor Removal from NuScenes Dataset using RANSAC
Project Overview
This project aims to remove the ground floor from the NuScenes dataset using the RANSAC algorithm. The main goal is to process LiDAR point cloud data to eliminate unnecessary ground points while retaining valuable information for further object detection and classification tasks.

Project Workflow
The workflow of the project involves several stages:

1. Reading LiDAR Point Cloud Data
We use Python and the Open3D library to read the LiDAR point cloud data from the NuScenes dataset.
The point clouds are loaded and visualized for processing.
python
Copier le code
# Example code to read point cloud
import open3d as o3d
pcd = o3d.io.read_point_cloud("path_to_lidar_file.pcd")
o3d.visualization.draw_geometries([pcd])
2. Filtering the Point Cloud
We filter the point cloud along the z-axis to remove points below a specific threshold (height). This step ensures we retain useful information while excluding irrelevant ground-level points.
Horizontal filtering is avoided to preserve essential object data.
python
Copier le code
# Example of filtering points below a height threshold
filtered_points = [p for p in pcd.points if p[2] > threshold]
3. Voxelization
Voxel Grid Downsampling is applied to reduce the number of points, which speeds up processing and removes noise. A 3D voxel grid is created, and points are subsampled based on the centroid within each voxel.
Key steps for voxelization:
Define a voxel size (smaller sizes for more detail, larger sizes for faster processing).
Divide the 3D space into voxels.
Retain one point per voxel, either by centroid or other methods.
Remove redundant points in overlapping voxels.
python
Copier le code
voxel_grid = pcd.voxel_down_sample(voxel_size=0.05)
4. RANSAC Algorithm
RANSAC (Random Sample Consensus) is used to detect and remove planar surfaces from the point cloud, such as ground planes. This method iteratively fits a plane model to the point cloud, removing points that align with the ground.
python
Copier le code
plane_model, inliers = pcd.segment_plane(distance_threshold=0.02, ransac_n=3, num_iterations=1000)
5. DBSCAN Clustering
DBSCAN (Density-Based Spatial Clustering of Applications with Noise) is used to group nearby points into clusters. This helps in distinguishing different objects within the point cloud after ground removal.
Points that do not belong to any dense clusters are treated as noise.
python
Copier le code
labels = np.array(pcd.cluster_dbscan(eps=0.02, min_points=10, print_progress=True))
6. 3D Visualization and Storage
The processed point clouds can be visualized in 3D using Open3D, and stored in the LiDAR data format based on a boolean control parameter.
python
Copier le code
o3d.visualization.draw_geometries([processed_pcd])
7. Annotation and Model Evaluation
To evaluate the quantitative performance of the model, it is necessary to annotate the ground points, as the NuScenes dataset does not provide explicit ground annotations. We used the platform Segments.ai for manual annotation.
With Segments.ai, images are uploaded, and regions corresponding to the ground are manually annotated. These annotations are then integrated with the LiDAR data for further evaluation.
plaintext
Copier le code
1. Upload images to Segments.ai.
2. Annotate ground regions manually.
3. Export annotations and use them for evaluation.
Results
After processing the point clouds with RANSAC, voxelization, and DBSCAN clustering, ground points were successfully removed, allowing for cleaner data and more accurate object detection.
The annotated dataset was used for quantitative evaluation of the ground removal method.
Future Improvements
Automating the annotation process to speed up ground truth generation.
Optimizing the voxelization and clustering parameters for specific use cases.
