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

[image1]: ./output_images/chessboard_before_distortion.jpg "Chessboard"
[image2]: ./output_images/chessboard_after_distortion.jpg "Chessboard Undistorted"
[image3]: ./output_images/before_undistort_image.jpg "Orignal Image"
[image4]: ./output_images/after_undistort_image.jpg "Undistorted"
[image5]: ./output_images/before_combined_thresholds.jpg "Before Gradient"
[image6]: ./output_images/after_combined_thresholds.jpg "After Gradient"
[image7]: ./output_images/before_filter.jpg "Before Color filter"
[image8]: ./output_images/after_filter.jpg "After Color filter"
[image9]: ./output_images/before_warp.jpg "Before warp"
[image10]: ./output_images/after_warp.jpg "After warp"
[image11]: ./examples/color_fit_lines.jpg "Identify poly"
[image12]: ./output_images/before_process_image.jpg "Before Process"
[image13]: ./output_images/after_process_image.jpg "After process"


## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the first code cell of the IPython notebook located in "Lane Detection.ipynb".  

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

Before Undestortion
![alt text][image1]

After Undestortion
![alt text][image2]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:

Before 
![alt text][image3]

After
![alt text][image4]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of color and gradient thresholds to generate a binary image (thresholding steps at lines In[6] through In[8] blocks in `Lane Detection.ipynb`).  Here's an example of my output for this step. 

Before Gradient Thresholding
![alt text][image5]

After Gradient Thresholding
![alt text][image6]

Before Color Thresholding
![alt text][image7]

After Color Thresholding
![alt text][image8]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `warper()`, which appears in block In[9] in the file `Lane Detection.ipynb`.  The `warp()` function takes as inputs an image (`image`).  I chose the hardcode the source and destination points in the following manner:

```python
src = np.float32([[570,460],[image.shape[1] - 573,460],[image.shape[1] - 150,image.shape[0]],[150,image.shape[0]]])
dst = np.float32([[200,0],[image.shape[1]-200,0],[image.shape[1]-200,image.shape[0]],[200,image.shape[0]]])
```

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 570, 460      | 200, 0        | 
| 707, 460      | 1080, 0       |
| 1130, 720     | 1080, 720     |
| 150, 720      | 200, 720      |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

Before Warp
![alt text][image9]

After Warp
![alt text][image10]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

Then I did some other stuff and fit my lane lines with a 2nd order polynomial kinda like this:

![alt text][image11]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in block In[10] in my code in `Lane Detection.ipynb`

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in block In[10 in my code in `Lane Detection.ipynb` in the function `LaneDetector.draw_lane()`.  Here is an example of my result on a test image:

Before Processing
![alt text][image12]

After Processing
![alt text][image13]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video_out.mp4)

Here's a [link to my challenge video result](./challenge_video_out.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

I first calculated image distortion using some checkerboard images. By calculating the distortion I was able to undistort the images that are taken from car. On these images, I applied color thresholding and later gradient thresholding to get a good binary image of lane lines. After getting satisfied with the results I warped the image to get the birds eye view. Here I did a couple of adjustments to get the lane lines straight in straight_lines1.jpg and straight_lines2.jpg. On these images then I tried predicting lane lines. The output was good but flickering. So to make it more robust I used some averaging to smooth up the output. 
The pipeline might fail if the lane lines have the curvature is more than fitting in bird's eye view and even if there is a change in lighting condition change.

Further, I would like to tackle the issues that I mentioned above to improve on lighting condition and highly curvy road mentioned in 

[link to output of Harder challenge](./harder_challenge_video_out.mp4)
