## Writeup Template

**Advanced Lane Finding Project**

The goals / steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

[//]: # (Image References)

[image1]: ./test_images/calibration1.jpg "Original image"
[image2]: ./output_images/undistort_calibration1.jpg "Undistorted image"
[image3]: ./output_images/point_location.png "Source Point for perspetive transformation"

[image4]: ./test_images/straight_lines1.jp "Original image"
[image5]: ./output_images/pt_straight_lines1.jpg "Warped image"
[image6]: ./output_images/pt_straight_lines1.jpg "Restore Image from Warped Images"

[image7]: ./test_images/straight_lines1.jpg "Original Strianght line Image"
[image8]: ./output_images/st_line_undistort.jpg "Undistorted Strianght line Image"

[image9]: ./output_images/st_line_filter.jpg "Straight line Image Filter"
[image10]: ./output_images/st_line_filter_rst.jpg "Straight line Image Filter restore"
[image11]: ./output_images/lane_straight_lines1.jpg "Straight line detection"


## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README


### Camera Calibration


#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the first code cell of the IPython notebook located in cell 1 and cell in P4_Solution-V2.ipynb.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 
Before starting the implementation of the lane detection pipeline, the first thing that should be done is camera calibration.
I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  Then the calibration paramters can be caculated according to  calibration images(`camera_cal/calibration*.jpg`). Use Undistort images can improve the performance of measurement.

The images in `test_images` are for testing your pipeline on single frames.  If you want to extract more test images from the videos, you can simply use an image writing method like `cv2.imwrite()`, i.e., you can read the video in frame by frame as usual, and for frames you want to save for later you can write to an image file.  


| Original image      | Undistorted image  |
|-------------------- |--------------------|
|![alt text][image1]  |![alt text][image2] |


#### Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform are located in cell 11 of   I chose the hardcode the source and destination points in the following manner:

Next step is to find the projective transform so the original images can be warped so that it looks like the images are taken from directly above the road. To find perspective transformation matrix, src points and dst point should be picked up. The following src points((229,600),(1051,600),(1180,666),(100,666) ) are picked up and dst points ((0,648),(1280,648),(1280,720),(0,720)) are choosen. 
Here is Source Points for perspective transformation.
![alt text][image3] 

Here is example code of 
```python
    img_size = (img.shape[1], img.shape[0])
    src = np.float32(
          [[229,600],
           [1051,600],
           [1180,666],
           [100,666]])
    
    dst = np.float32(
          [[img.shape[1]*0  ,img.shape[0]*0.9],
           [img.shape[1]*1  ,img.shape[0]*0.9],
           [img.shape[1]*1  ,img.shape[0]*1  ],
           [img.shape[1]*0  ,img.shape[0]*1  ]])
```
Then using cv2.getPerspectiveTransform(src,dst) to cacluate the transformation matrix.
The following operation will be done against warped image.

Here is the result between warpped 

| Warped lines         |     Final result    |
|----------------------|----------------------|
|![alt text][image4]  |![alt text][image5]  |


### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:

|  Original Images     |  Undistort result    |
|----------------------|----------------------|
|![alt text][image7]  |![alt text][image7]    |

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of color and gradient thresholds to generate a binary image. First transform color space from RGB to HLS.  Apply gadient on L space and filter on S space, then combine together to get final pipeline.

![alt text][image9]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

|  Warped Images       |  Restored Warped     |
|----------------------|----------------------|
|![alt text][image9]  |![alt text][image10]   |

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

Then I did some other stuff and fit my lane lines with a 2nd order polynomial kinda like this:
* use histogram to find starting point of left lane line and right lane line
* Find the following points by scroll window up
* For video, record all lane left fit and right fit, use np.mean to replace current left_fit and right_fit to avoid fluctuation 
* If the curvature is less 1600, ignore the left_fit and right_fit data


#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in this method calc_radius of file(P4_Solution-V2.ipynb).
Assume that lane width is 3.7 and lane length is 9 meter. X and y scalar can be caculated through this caculation( ym_per_pix = 9/720  and  xm_per_pix = 3.7/980 ). The use radius of curvature formation to caculate the real curvature.

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.


![alt text][image11]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./processed_video_project.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Currently, src and dst points in the perspective transformation are picked up manually. Maybe it can be done automatically through candy edge and hough transformation. 
To smooth the lane, np.mean is applied on ploynomial coffient and it is inappropriated for curve lane.

