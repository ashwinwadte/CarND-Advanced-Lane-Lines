## Writeup

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

[image1]: ./output_images/chess_board_output.png "Undistorted"
[image2]: ./output_images/test2_undistorted.jpg "Road Transformed"
[image3]: ./output_images/test2_thresholded.jpg "Binary Example"
[image4]: ./output_images/test2_warped.jpg "Warp Example"
[image5]: ./output_images/test2_detected_lines.jpg "Fit Visual"
[image6]: ./output_images/test2_final_img.jpg "Output"
[video1]: ./output_project_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the first code cell of the IPython notebook located in "./P2.ipynb". 

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![alt text][image2]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used color thresholding on the s channel to generate a binary image (thresholding steps at cell 5). Color threshold using HLS space is useful to detect lane lines of different colors and under different lighting conditions.

Here's an example of my output for this step.

![alt text][image3]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

It is important to perform perspective transform. Due to perspective, objects appear smaller the farther they are from the viewpoint. Thus, even parallel lines to appear to converge at a point. A perspective transorm warps the image and effectively drags point towards or away from the viewpoint in order to change the apparent perspective.

In order to do this in the image, first the region of interest is exctracted, identifying four source points. Then, the four destination points are defined and the tranformation Matrix M is calculated. The region of interested is an isosceles trapezoid, warped into a rectangle. (cells 7-8)

![alt text][image4]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

Next step is to decide which pixels belong to lines and decide which belongs to left and right lines among these pixels.

By plotting a histogram of where the binary activations occur across the image is one of the solution. The two highest peaks from the histogram are used a starting point for determining where the lane lines are, and then use sliding windows moving upward in the image (further along the road) to determine where the lane lines go. (cells 9-10)

![alt text][image5]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in cells 11 to 13.

After two lines are detected, next is to calculate the radius of the curvature and the distance from the center of the road. This is a measure of the quality of the steps followed and it can give feedback thinking about real data. Knowing the shape of our image we can extract the correspondence between pixels and meters in the image.

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this in cells 14-18. 

Now I put all the steps done so far back on the original image. Radius of the curve, distance from the center of the lane and the two lines are mentioned. An area representing the lane is displayed together with the indications of Radius and distance.
Here is an example of my result on a test image:

![alt text][image6]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Sudden changes in brightness, weather conditions may result in difficulty in line detection. Video processing took around 23mins due to heavy calculations. I can try to improve performance by leveraging other thresholding methods.