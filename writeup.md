## Project: Perception Pick & Place
### Writeup Template: You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

---


# Required Steps for a Passing Submission:
1. Extract features and train an SVM model on new objects (see `pick_list_*.yaml` in `/pr2_robot/config/` for the list of models you'll be trying to identify). 
2. Write a ROS node and subscribe to `/pr2/world/points` topic. This topic contains noisy point cloud data that you must work with.
3. Use filtering and RANSAC plane fitting to isolate the objects of interest from the rest of the scene.
4. Apply Euclidean clustering to create separate clusters for individual items.
5. Perform object recognition on these objects and assign them labels (markers in RViz).
6. Calculate the centroid (average in x, y and z) of the set of points belonging to that each object.
7. Create ROS messages containing the details of each object (name, pick_pose, etc.) and write these messages out to `.yaml` files, one for each of the 3 scenarios (`test1-3.world` in `/pr2_robot/worlds/`).  [See the example `output.yaml` for details on what the output should look like.](https://github.com/udacity/RoboND-Perception-Project/blob/master/pr2_robot/config/output.yaml)  
8. Submit a link to your GitHub repo for the project or the Python code for your perception pipeline and your output `.yaml` files (3 `.yaml` files, one for each test world).  You must have correctly identified 100% of objects from `pick_list_1.yaml` for `test1.world`, 80% of items from `pick_list_2.yaml` for `test2.world` and 75% of items from `pick_list_3.yaml` in `test3.world`.
9. Congratulations!  Your Done!

# Extra Challenges: Complete the Pick & Place
7. To create a collision map, publish a point cloud to the `/pr2/3d_map/points` topic and make sure you change the `point_cloud_topic` to `/pr2/3d_map/points` in `sensors.yaml` in the `/pr2_robot/config/` directory. This topic is read by Moveit!, which uses this point cloud input to generate a collision map, allowing the robot to plan its trajectory.  Keep in mind that later when you go to pick up an object, you must first remove it from this point cloud so it is removed from the collision map!
8. Rotate the robot to generate collision map of table sides. This can be accomplished by publishing joint angle value(in radians) to `/pr2/world_joint_controller/command`
9. Rotate the robot back to its original state.
10. Create a ROS Client for the “pick_place_routine” rosservice.  In the required steps above, you already created the messages you need to use this service. Checkout the [PickPlace.srv](https://github.com/udacity/RoboND-Perception-Project/tree/master/pr2_robot/srv) file to find out what arguments you must pass to this service.
11. If everything was done correctly, when you pass the appropriate messages to the `pick_place_routine` service, the selected arm will perform pick and place operation and display trajectory in the RViz window
12. Place all the objects from your pick list in their respective dropoff box and you have completed the challenge!
13. Looking for a bigger challenge?  Load up the `challenge.world` scenario and see if you can get your perception pipeline working there!

## [Rubric](https://review.udacity.com/#!/rubrics/1067/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  

You're reading it!

### Exercise 1, 2 and 3 pipeline implemented
#### 1. Complete Exercise 1 steps. Pipeline for filtering and RANSAC plane fitting implemented.
![image1](misc-image/image10.PNG)

#### 2. Complete Exercise 2 steps: Pipeline including clustering for segmentation implemented.  
![image2](misc-image/image11.PNG)

#### 3. Complete Exercise 3 Steps.  Features extracted and SVM trained.  Object recognition implemented.
![image3](misc-image/image12.PNG)  
The confusion matrix of my svm model is below;  
![image13](misc-image/image13.PNG)
![image14](misc-image/image14.PNG)

#### Result of detection
![image15](misc-image/image15.PNG)
![image16](misc-image/image16.PNG)
![image17](misc-image/image17.PNG)


### Pick and Place Setup

#### 1. For all three tabletop setups (`test*.world`), perform object recognition, then read in respective pick list (`pick_list_*.yaml`). Next construct the messages that would comprise a valid `PickPlace` request output them to `.yaml` format.
Here is the snapshot of my PR2 doing.
![image4](misc-image/image1.PNG)
![image5](misc-image/image2.PNG)
![image6](misc-image/image3.PNG)
![image7](misc-image/image4.PNG)
![image8](misc-image/image5.PNG)
![image9](misc-image/image6.PNG)
![image10](misc-image/image7.PNG)
![image11](misc-image/image8.PNG)
![image12](misc-image/image9.PNG)

### Discussion
#### What we worked
In this project, I learned to extract feature quantities from point cloud data and cluster them.
Specifically, we filtered the point cloud data to remove the noise, and separated the desk and the object using the RANSAC algorithm. 
Next, we clustered the point cloud data of the object for each object and applied each cluster to the SVM to identify what the object is. 
Through these, I learned how to identify objects in 3 - D space, and I was able to experiment how to actually implement them.

#### Future improvement
 * SVM identification accuracy is not yet perfect, so further training is required. For example, you need to consider using other features and using it.
 * Although I added a noise filter this time, it is not possible to consider the robustness that can correspond to various noise environments. In order to make a more robust system, it is necessary to think about other noise factors. For example, I think that it is necessary to consider irregular reflection from the surface of the object and the influence of surrounding scattered light.
 * This time I used the RANSAC algorithm to detect the desk, but I need to consider what to do if the desk is slightly slanted or the field of view is diagonal. In order to consider such inclination, it may be good to adjust parameters so that some inclination can be ignored, or coordinate conversion of point cloud data if it is within a certain range. Also, it may be good to insert a parallel sensor in the vicinity of the camera and perform processing while considering the tilt of the main unit.
 