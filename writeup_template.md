## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

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

[image1]: ./examples/undistort_output.png "Undistorted"
[image2]: ./output_images/undistorted_0.jpg "Road Transformed"
[image3]: ./output_images/binary_1.jpg "Binary Example"
[image4]: ./output_images/undistorted_and_warped_1.jpg "Warp Example"
[image5]: ./output_images/lanes_polyfit.jpg "Fit Visual"
[image6]: ./output_images/output_1.jpg "Output"
[video1]: ./project_video_output.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the first code cell of the IPython notebook located in "./examples/example.ipynb" (or in lines # through # of the file called `some_file.py`).  

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![alt text][image2]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

In *pipeline() function* I have read the image in HLS and seperated the H.L.S component. Then I have taken derivative in x and y direction 
using sobel transform. Then I have set threshold in each of h,l,s paramenter and for x and y derivative. after that I have created a binary image(image with 1's 0's). Logic used for creating a binary image is if a pixel value is between the threshold value set it to 1 else set it to 0. Below is an example of binary image.
![alt text][image3]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

I have warp() function in my code which is used for perspective transform I have used below Source point as src and used used offest as 1/4th of original size.

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 585, 460      | 320, 0        | 
| 203, 720      | 320, 720      |
| 1127, 720     | 960, 720      |
| 695, 460      | 960, 0        |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image4]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

I have found lane pixel in find_lane_pixels(binary_warped) function, in this function i have calucalted left lane base position and right lane base position using the argmax of historgram on both side from the mid point. No I have created and window (9) and of 100 pixel width.
.Now I have iterated through each window and found the pixel value within the current window which are non-zero in the given window. Whenever we found there are more than 50 pixels I have shifted the base lane position to the mean position of these non-zero pixel.
After finding these nonzero pixels I have ploted a curve of order 2 in fit_polynomial function.
Below is the example image.


![alt text][image5]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I have calculated radius in measure_curvature_real(binary_warped) function. 
First I have calculated meters per pixel in x and y direction,. Then I have plot the left lane and right lane with pixel per meter bias included, after that I have calucalated radius of curve using the formula shared in videos.
 
#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I have implement this in cell no 14 in my jupyter notebook I have used weighted_img() from the last project to plot the lane.

![alt text][image6]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video_output.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

My code fialed on challenge videos, one of the reason I found is because of more bendy curves, one of the solution I thought is to decrease the are of intereset so that we can decrease the amount of curve but at the same time we have to increase the amount of processing to mkae it work in real time. Also there are many exceptions in the code which needs to be handled to make it work in any scenarios.
