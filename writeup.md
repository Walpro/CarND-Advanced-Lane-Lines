
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

[image1]: ./img1.png "Undistorted"
[image2]: ./img2.png "Road Transformed"
[image3]: ./img3.png  "Binary Example"
[image4]: ./img4.png  "Warp Example"
[image5]: ./img5.png  "Fit Visual"
[image6]: ./img6.png  "Output"
[video1]: ./output_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.

### Camera Calibration

The code for this step is contained in the first and second code cells of the IPython notebook located in "project.ipynb". 

In the first code cell, I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objps` is just a replicated array of coordinates, and `obj_points` will be appended with a copy of it every time I successfully detect all chessboard corners for all the calibration images. `img_points` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `obj_points` and `img_points` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

### Pipeline (single images)

#### 1. Distortion-corrected image.

To undistort an image I used the camera calibration and distortion coefficients I computed in the first step with the `cv2.undistort()` function, here is an example on a test image.

![alt text][image2]

#### 2. thresholded binary image.

In `2- Thresholded binary images`, I used a combination of color and gradient thresholds to generate a binary image . 
I converted the image colors to HLS color space and used the Soebel operator to compute the image x gradiant after that I used thresholds to extract the importnant image features Here's an example of my output for this step.

![alt text][image3]

#### 3. Perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `Pers_transform()`, which appears in `3- Perspective transform`lines 1 through 10.  This function takes as inputs an image (`img`), as well as source (`src`) and destination (`dst`) points. After tests on the images, I selected the following points:

| Source        | Destination   | 
|:-------------:|:-------------:|  
| 275, 675      | 400, 720      | 
| 1035, 675     | 950, 720      |
| 700, 460      | 950, 1        |
| 580, 460      | 400, 1        |

I verified that my perspective transform was working as expected by drawing the `src_points` and `dst_points` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image4]

#### 4. identifiying lane-line pixels and fit their positions with a polynomial

Then I calculated the histogram of the image and searched for the peak of the left and right halves of the histogram which I used as a starting point then i did a sliding window search to find the points on the lane. After that I fitted these points to a second order polynom and plotted it on the image using `cv2.fillPoly`.

![alt text][image5]

#### 5.  Radius of curvature of the lane and the position of the vehicle with respect to center.

To compute the raduis of curvature I fitted in the function `calc_curve(4- Detecting lanes and fitting curve)` the right and left
lane points to a second order polynom which I used to compute the right and left radius of the lane curvature. 
I computed the raduis of curvature as the mean value of the two radiuses
computed previously.
The lane position is the difference between the camera position and the lane center computed in `warp_back(5- warping back to the original image)`.
The code to compute 

#### 6. Example image of the plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in `5- warping back to the original image` Here is an example of my result on a test image:

![alt text][image6]

---

### Pipeline (video)

Here's a [link to my video result](./output_video.mp4)

---

### Discussion

I had the problem that my pipeline failed to detect the lanes correctly in some images where shadows exists or some objects near the car. I tried to avoid this porblem by using a mask on the binary thresholded images to avoid any noise on the right or left of the lane.
My video processing pipiline would mostly fail if any important noise exists in the lane where the car is driving.
A second mask that erase the noise in the middle of the road could make the pipeline more robust.
