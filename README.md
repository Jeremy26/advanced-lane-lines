## Advanced Lane Finding

The goals of this project are to detect lines on the road. To do so, we are going through few steps :

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

To understand the project, please find the following folders and files in this repository:
* camera_cal is used to calibrate the camera from chessboard images
* test_images are images used to try things like undistortion or gradients thresholding
* output_images are my results from test_images

Then,

* P4.ipynb is the jupyter notebook that will guide you through the code
* writeup.md is my report
* The mp4 files are videos used to try my program
