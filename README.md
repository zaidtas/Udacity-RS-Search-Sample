## Project: Search and Sample Return
---


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
[rock]: ./output/rock_calibration.png
[process_image]: ./output/notebook_process_image.png
[result]: ./output/autonomous_mode_map.png

## [Rubric Points](https://review.udacity.com/#!/rubrics/916/view) 
#### I will explain how each of these Rubric points have been addressed in my implementation 

---
###  README

 
You're already reading it!

### Notebook Analysis
* **Transformation Calibration:** Added a function called `get_transform()` that takes the calibration image( shown below ), the source and destination coordinates and provides a one-time perspective transformation that can then be applied to the camera output. Also provides a transformed image of the camera FOV.

```python
get_transform(img, src, dst):
           
    M = cv2.getPerspectiveTransform(src, dst)
    
    #added a mask so that the camera fov is overlayed
    mask = cv2.warpPerspective(np.ones_like(img[:,:,0]),M,(img.shape[1], img.shape[0]))
    
    return M,mask
```

![alt text][image2]

* Changed the funtion `perspect_transform()` to simply take in an image and the already calibrated transformation matrix M. This saves the unnecessary computation of transformation matrix fir each frame

* **Stone Calibration:** Added a function callled `find_rocks()` take takes in an image and thresholds for the color yellow in the HSV domain. The calibration results are below

![alt text][rock]

* **Modifying `process_image()`:** we implement all the steps prescribed in the helper comments of the starter code. The main addition is the logic to print out the image of the obstacle(red channel), navigable terrain(blue channel) and the rocks found(all white). We provide an additional attribute of `world_cnt` to the DataBucket class where we increment the pixel values in the respective channels for obstacle and navigation terrain and then after each frame compare these counters to saturate the pixels in the respective channels. The video is attached and you can find the screenshot of test run done by you below.


![alt text][process_image]

### Autonomous Navigation and Mapping
1. **Perception Step:** Changed the function according to the changes implemented in the function `process_image()` in the Jupyter Notebook practice explained in the section above. The ctr for Navigable terrain was increased by 4 while obstacles were only increased by 1. The results were good for these settings.

2. **Decision Step:** Kept the original decision tree as it is. 


### Discussion
1. The Simulation was run at $2560\times2048$ resolution at the **fastest** graphics quality
2. We map over 60% of the environment with over 90% fidelity as shown below.

![alt text][result]

3. The robot gets stuck sometimes along small rocks that can be avoided by having a better decision tree.
4. Picking up important rock samples can be achieved by modifying the decision step by checking if your rock sampling threshold image has any rock sample pixels. If so, take mean of the angles in the polar coordinates for those pixels and direct your rover towards them until you reach a particular distance. Then execute the pickup command chain.

