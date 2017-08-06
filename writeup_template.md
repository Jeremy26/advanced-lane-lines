## Writeup Template

---
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

[image0]: ./examples/calibration.png "Calibration"
[image1]: ./examples/undistort_output.png "Undistorted"
[image2]: ./examples/undistorted.png "Undistorted Test Images"
[image3]: ./test_images/gradient1.png "Gradient 1"
[image4]: ./examples/gradient2.png "Gradient 2"
[image5]: ./examples/hls.png "HLS"
[image6]: ./examples/hls_select.png "HLS Select"
[image7]: ./examples/color_gradient.png "CCG"
[image8]: ./examples/warped.png "Warped"
[image9]: ./examples/sliding.png "Sliding"
[image10]: ./examples/new_sliding.png "New Sliding"
[image11]: ./examples/roc.png "Radius Of Curvature"
[image12]: ./examples/color_fit_lines.jpg "Color Fit Lines"
[image13]: ./examples/final.png "Final"

[video1]: ./project_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

Link to my [Project](https://github.com/Jeremy26/advanced-lane-lines/blob/master/P4.ipynb)

### Camera Calibration

The first thing needed here is to calibrate the camera. Most cameras today use lenses that have an effect on the image. We are converting 3D objects to 2D image points. As you can see in the following image, which is also an output from my jupyter notebook file, the first corner on the top-left, which is (0,0,0) in 3D, is (545,343) in 2D. This is the first step to calibration.

![alt text][image0]

This is the creation of object points and images points inside the function `getpoints()` used for calibration.

### Pipeline (single images)

#### 1. DISTORTION CORRECTION

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![alt text][image1]

To do this, I created a function called `cal_undistort(img, objpoints, imgpoints)` that takes the image and the generated object and image points. This function uses `cv2.calibrateCamera` and `cv2.undistort` after the calibration. In other words, we create 3D --> 2D conversion and pass them to calibrateCamera function that generates matrix and other parameters from an image to send to undistort function that returns an undistorted image.

We now have the undistortion working ! We will use it as a reference image in our pipeline.
![alt text][image2]


#### 2. COLOR AND GRADIENT

Color and Gradient are part of computer vision as the main factors to use information out of an image.

##### GRADIENT
To get gradient working, I used three main functions :
* `abs_sobel_thresh`, to calculate gradx and grady
* `mag_thresh` , to calculate the magnitude of the gradient
* `dir_threshold` , to calculate the direction of the gradient

These functions contain a lot of hyperparameters to tune. We use these calculations independantly and then combine them, as you can see in the following two pictures.

![alt text][image3]
![alt text][image4]

I tuned the hypeparameters to get the best out of these functions and to have the best gradient detection possible. The parameters I am talking about are threshold. It means we are not taking all possible data but only data that are part of the given threshold. While GradX is used to detect vertical lines, GradY performs better on horizontal lines. Mag Binary looks like canny-edge detection and dir_threshold gives the general direction of the lines. We combined them all together to get the combined image.

##### COLOR

Color Thresholding is the other main thing. We first convert from RGB space to HLS space. This means we are not in RGB space anymore. HLS stands for Hue, Lightning, Saturation; we could also have picked HSV which stands for Hue, Saturation, Value. HLS has been stated to be more efficient under certain circumstances. 
![alt text][image5]

We apply thresholding for Saturation to (170,255) and get the following binary output.
![alt text][image6]

##### COLOR AND GRADIENT

Finally, we combine Color thresholding and Gradient thresholding and get the following output.
![alt text][image7]

This image will now be used to perspective transform.

#### 3. PERSPECTIVE TRANSFORM

My `perspective_transform(img)` function takes in an image that has been treated with color and gradient threshold and returns the image from a birds eye view. I picked source points that could correspond to a region of interest and put them into a rectangle destination.
```
    src = np.float32([[575,450],[700,450],[1200,720],[0,720]])
    dst = np.float32([[350, 0], [950, 0],[950, 720],[350,720]])
```
This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 575, 450      | 350, 0        | 
| 0, 720        | 350, 720      |
| 1200, 720     | 950, 720      |
| 700, 450      | 950, 0        |

In the end, we get the following result.
![alt text][image8]

On the left, the undistorted image, on the right, the binary result.

#### 4. SLIDING WINDOW METHOD TO FIND THE LANES

We now have the lanes clearly visible and the trickiest part is done ! We now have two functions that will calculate the positions for the left and right points, as follows.

![alt text][image9]
![alt text][image10]

These functions helps detect lane lines by detecting peaks in a histogram. Thanks to this, we can now have a lot of variables that will be used to know exactly where the lanes are.


#### 5. RADIUS OF CURVATURE & POSITION TO CENTER

* Radius of curvature is a way to detect how the road is going and ultimately where the car will go. It is not needed in this project since we dont't have any effect on the car behavior. Once we know where the lines are, we can use the equation in the picture below to work on.

![alt text][image12]

This equation allows us to calculate Rcurve which is the radius of curvature, we calculate this by using the calculus from the course. We pass to the function the position of left and right points found before and use them as a start.
The lines are again detected and used. 

![alt text][image11]

We do a final conversion from pixel space to meter space as you can see in the code.

* Position to Center is a simple calculation where we assume the center of the image is the center of the road. We take the middle of the two lanes calculated and compare it to the center of the camera.

#### 6. FINAL OUTPUT

Finally, I use the draw lines function that plots the inverse of the matrix calculated from perspective transform into the undistorted image.

![alt text][image13]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./result.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Here I'll talk about the approach I took, what techniques I used, what worked and why, where the pipeline might fail and how I might improve it if I were going to pursue this project further.  
