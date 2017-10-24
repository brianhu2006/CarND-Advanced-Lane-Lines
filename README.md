## Advanced Lane Finding
[![Udacity - Self-Driving Car NanoDegree](https://s3.amazonaws.com/udacity-sdc/github/shield-carnd.svg)](http://www.udacity.com/drive)


In this project, your goal is to write a software pipeline to identify the lane boundaries in a video, but the main output or product we want you to create is a detailed writeup of the project.  Check out the [writeup template](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) for this project and use it as a starting point for creating your own writeup.  

---

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
[image2]: ./output_images/calibration1.jpg "Undistorted image"
[image3]: ./output_images/point_location.png "Source Point for perspetive transformation"

[image4]: ./test_images/straight_lines1.jp "Original image"
[image5]: ./output_images/pt_straight_lines1.jpg "Warped image"
[image8]: ./output_images/pt_straight_lines1.jpg "Restore Image from Warped Images"


##Camera Calibration
Before starting the implementation of the lane detection pipeline, the first thing that should be done is camera calibration. That would help us:
* calculate the calibration paramters from calibration images(`camera_cal/calibration*.jpg`)
* Undistort images can improve the performance of measurement.

The images in `test_images` are for testing your pipeline on single frames.  If you want to extract more test images from the videos, you can simply use an image writing method like `cv2.imwrite()`, i.e., you can read the video in frame by frame as usual, and for frames you want to save for later you can write to an image file.  


| Original image      | Undistorted image  |
|-------------------- |--------------------|
|![alt text][image1]  |![alt text][image2] |

##Finding projective transformation
Next step is to find the projective transform so the original images can be warped so that it looks like the images are taken from directly above the road. To find perspective transformation matrix, src points and dst point should be picked up. The following src points((229,600),(1051,600),(1180,666),(100,666) ) are picked up and dst points ((0,648),(1280,648),(1280,720),(0,720)) are choosen. 
Here is Source Points for perspective transformation.
![alt text][image3] 
Then using cv2.getPerspectiveTransform(src,dst) to cacluate the transformation matrix.
The following operation will be done against warped image.

Here is the result between warpped and 
| Warped lines         |     Final result    |
|----------------------|----------------------|
|![alt text][image4]  |![alt text][image5]  |



##Estimating pixel resolution
Next important step is to estimate resolution in pixels per meter of the warped image. It also can be done by hand, but as previously we'll create a script that does that for us. In the course materials, it was stated that width of the lane is no less than 12 feet. In order to estimate the resolution in pixels per meter, the images with the straight lines will be used. They will be unwarped and the distance between the lines will be measured. The lower of two distances will be assumed to be 12 feet or 3.6576 meters. 




To help the reviewer examine your work, please save examples of the output from each stage of your pipeline in the folder called `ouput_images`, and include a description in your writeup for the project of what each image shows.    The video called `project_video.mp4` is the video your pipeline should work well on.  

The `challenge_video.mp4` video is an extra (and optional) challenge for you if you want to test your pipeline under somewhat trickier conditions.  The `harder_challenge.mp4` video is another optional challenge and is brutal!

If you're feeling ambitious (again, totally optional though), don't stop there!  We encourage you to go out and take video of your own, calibrate your camera and show us how you would implement this project from scratch!

## How to write a README
A well written README file can enhance your project and portfolio.  Develop your abilities to create professional README files by completing [this free course](https://www.udacity.com/course/writing-readmes--ud777).

