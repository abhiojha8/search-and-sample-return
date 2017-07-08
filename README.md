[//]: # (Image References)
[image_0]: ./misc/rover_image.jpg
# Search and Sample Return Project
![alt text][image_0] 

This project is modeled after the [NASA sample return challenge](https://www.nasa.gov/directorates/spacetech/centennial_challenges/sample_return_robot/index.html) and it will give you first hand experience with the three essential elements of robotics, which are perception, decision making and actuation.  You will carry out this project in a simulator environment built with the Unity game engine.  

## The Simulator
The first step is to download the simulator build that's appropriate for your operating system.  Here are the links for [Linux](https://s3-us-west-1.amazonaws.com/udacity-robotics/Rover+Unity+Sims/Linux_Roversim.zip), [Mac](	https://s3-us-west-1.amazonaws.com/udacity-robotics/Rover+Unity+Sims/Mac_Roversim.zip), or [Windows](https://s3-us-west-1.amazonaws.com/udacity-robotics/Rover+Unity+Sims/Windows_Roversim.zip).  

You can test out the simulator by opening it up and choosing "Training Mode".  Use the mouse or keyboard to navigate around the environment and see how it looks.

## Dependencies
You'll need Python 3 and Jupyter Notebooks installed to do this project.  The best way to get setup with these if you are not already is to use Anaconda following along with the [RoboND-Python-Starterkit](https://github.com/ryan-keenan/RoboND-Python-Starterkit). 


Here is a great link for learning more about [Anaconda and Jupyter Notebooks](https://classroom.udacity.com/courses/ud1111)

## Recording Data
I've saved some test data for you in the folder called `test_dataset`.  In that folder you'll find a csv file with the output data for steering, throttle position etc. and the pathnames to the images recorded in each run.  I've also saved a few images in the folder called `calibration_images` to do some of the initial calibration steps with.  

The first step of this project is to record data on your own.  To do this, you should first create a new folder to store the image data in.  Then launch the simulator and choose "Training Mode" then hit "r".  Navigate to the directory you want to store data in, select it, and then drive around collecting data.  Hit "r" again to stop data collection.

## Data Analysis
Included in the IPython notebook called `Rover_Project_Test_Notebook.ipynb` are the functions from the lesson for performing the various steps of this project.  The notebook should function as is without need for modification at this point.  To see what's in the notebook and execute the code there, start the jupyter notebook server at the command line like this:

```sh
jupyter notebook
```

This command will bring up a browser window in the current directory where you can navigate to wherever `Rover_Project_Test_Notebook.ipynb` is and select it.  Run the cells in the notebook from top to bottom to see the various data analysis steps.  

The last two cells in the notebook are for running the analysis on a folder of test images to create a map of the simulator environment and write the output to a video.  These cells should run as-is and save a video called `test_mapping.mp4` to the `output` folder.  This should give you an idea of how to go about modifying the `process_image()` function to perform mapping on your data.  

## Navigating Autonomously
The file called `drive_rover.py` is what you will use to navigate the environment in autonomous mode.  This script calls functions from within `perception.py` and `decision.py`.  The functions defined in the IPython notebook are all included in`perception.py` and it's your job to fill in the function called `perception_step()` with the appropriate processing steps and update the rover map. `decision.py` includes another function called `decision_step()`, which includes an example of a conditional statement you could use to navigate autonomously.  Here you should implement other conditionals to make driving decisions based on the rover's state and the results of the `perception_step()` analysis.

`drive_rover.py` should work as is if you have all the required Python packages installed. Call it at the command line like this: 

```sh
python drive_rover.py
```  

Then launch the simulator and choose "Autonomous Mode".  The rover should drive itself now!  It doesn't drive that well yet, but it's your job to make it better!

**The goals / steps of this project are the following:**  

**Training / Calibration**  

* Download the simulator and take data in "Training Mode"
* Test out the functions in the Jupyter Notebook provided
* Add functions to detect obstacles and samples of interest (golden rocks)
* Fill in the `process_image()` function with the appropriate image processing steps (perspective transform, color threshold etc.) to get from raw images to a map.  The `output_image` you create in this step should demonstrate that your mapping pipeline works.
* Use `moviepy` to process the images in your saved dataset with the `process_image()` function.  Include the video you produce as part of your submission.

**Autonomous Navigation / Mapping**

* Fill in the `perception_step()` function within the `perception.py` script with the appropriate image processing functions to create a map and update `Rover()` data (similar to what you did with `process_image()` in the notebook). 
* Fill in the `decision_step()` function within the `decision.py` script with conditional statements that take into consideration the outputs of the `perception_step()` in deciding how to issue throttle, brake and steering commands. 
* Iterate on your perception and decision function until your rover does a reasonable (need to define metric) job of navigating and mapping.  

[//]: # (Image References)

[image1]: ./misc/rover_image.jpg
[image2]: ./calibration_images/example_grid1.jpg
[image3]: ./calibration_images/example_rock1.jpg 
[image4]: ./output/navigable.png

## [Rubric](https://review.udacity.com/#!/rubrics/916/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

## Object, Rocks and Navigable Terrain Identification:

### Navigable Terrain Detection:
I have first converted the given image (recorded by the camera into Rover) into birds eye view by using `perspect_transform()`, which can be seen in 5th code cell of the ipython notebook. After getting the birds-eye view image I use color thresholding, which filters out the mountains and detects the navigable terrain. Color thresholding is done via function `color_thresh()` in the 6th code cell of Ipython notebook.

A sample binary image for navigable terrain is as shown:

![alt-text][image4]

Once we have the binary image of navigable terrain, we can calculate the navigable (x,y) pixels in Rover space using function `rover_coords()`. These Rover space (x,y) pixels are then coverted into worlspace using `pix_to_world()`. Finally, we use the (x,y) navigable pixels in world space to generate a worldmap.
The functions `rover_coords()` and `pix_to_world()` can be found in the 7th code cell of Ipython notebook.

### Obstacle detection:
Object detection is done using the function `identify_obstacle()` provided in 8th code cell of the Ipython notebook. In order to detect objects I used color thresholding, where I restricted the RGB values in between a lower and upper threshold. A low rgb threshold (1, 1, 1) and high rgb threshold of (130, 140, 140) works well here. Similar to the process used in navigable terrain detection, we make use of the functions `rover_coords()` and `pix_to_world` to map the obstacles on worlmap.

### Rock detection:
Rock detection is done using the functions `color_thresh_yellow()` and `identify_rock` provided in the 8th code cell of Ipython notebook. Inspired by the color of the rock(yellow) we decided to use color thresholding to check for yellow color in the image. Once we filter out all the other colors except yellow, we end up with a binary image of yellow rock. The process is fairly similar to object detection, just different thresholds for RGB are used. Similar to the steps performed in navigable terrain detection and Obstacle detection, we finally map Rocks into the worldmap.

## Process Image:
The `process_image()` function can be seen in the 10th code cell of Ipython notebook. Here we use Obstacle detection, Rock detection and Navigable Terrain detection to generate a worldmap. The Rover records images and each image is processed by the function `process_image()`. The pixels corresponding to navigable terrain are colored in red using `data.worldmap[nav_xpix,nav_ypix,0] += 1`, where nav_xpix and nav_pix are the navigable (x,y) coordinates in worldspace. Similarly, pixels corresponding to rocks are colored white, and those corresponding to obstacles are colored blue. The output image is a collage of 4 images, where the top left image is the image as captured by Rover , top right image is the birds-eye view, bottom left contains the worldmap. The bottom right part of image displays information regarding other images.

Here's a [link to my video result](./output/test_mapping.mp4)

## Perception Step:
The function `perception_step()` is implemented in the lines 114 through 152 in the file `perception.py`. First source and destination points are specified to perform a perspective transform on the image recieved by Rover. This warped image is then thresholded using `color_thresh()` to generate a binary image where the white pixels represent navigable terrain. The (x,y) pixels representing navigable terrain are then converted to worldspace using `pix_to_world()`. For object and rock identification we proceed as explained in the points Obstacle detection and Rock detection. Once we get the pixels corresponding to Rocks and Obstacles, we highlight rocks, obstacles and navigable terrain in the worlmap as follows:
    Rover.worldmap[y_obstacle_world, x_obstacle_world, 0] += 1
    Rover.worldmap[y_rock_world, x_rock_world, 1] += 1
    Rover.worldmap[y_world, x_world, 2] += 1
For updating the Rover State, the distance and angles of navigable pixels are calculated using the function `to_polar_coords()`. The Rover state is updated as follows:
	Rover.nav_dists, Rover.nav_angles = to_polar_coords(xpix, ypix)
For the purpose of displaying various visual information regarding navigable terrain, rocks and obstacles on the left side window of the output, we do the following:
	Rover.vision_image[:, :, 0] = obstacle * 128
    Rover.vision_image[:, :, 1] = rock * 128
    Rover.vision_image[:, :, 2] = navigable * 128

## Decision Step:
Perhaps the most crucial part of this project is writing the AI for decision making. The logic goes as follows:
First the Rover detects whether there is any vision data to proceed forward. If there is vision data, it checks whether its mode is in forward or stop. If the Rover mode is forward, it checks the distance and direction of navigable pixels, and steers in the area of most navigable terrain. If there is no more navigable terrain, Rover stops and sets it mode to stop mode. On reaching a dead end, the Rover makes a U-turn and again starts moving towards the most navigable way.

The `decision_step()` function is implemented in the `file decision.py()`.

## Results:
The Rover does a decent job of mapping 83% of the navigable terrain with around 65% fidelity. 4 rocks are succesfully identified.