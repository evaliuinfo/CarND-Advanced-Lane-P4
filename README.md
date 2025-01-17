
## Advanced Lane Finding Project
### Eva Liu

The goals / steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.


### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the first code cell of the IPython notebook.

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text](https://github.com/evaliu1/CarND-Advanced-Lane-P4/blob/master/Images/image_distortion.PNG)

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images.

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of color and gradient thresholds to generate a binary image (thresholding steps at cells: "Apply Sobel Threshold" , "Apply Magnitude of the Gradient" , "Apply Direction of the Gradient" and "Apply HLS Threshold").  

Then I created a Pipeline function to combine all the thresholds together. For the Pipeline I defined, I used:
1.s channel from HLS function
2.get the x sobel
3.get the y sobel
4.apply magnitude threshold
5.apply direct thresthold

Here's an example of my output for this step.  (note: this is not actually from one of the test images)


![alt text](https://github.com/evaliu1/CarND-Advanced-Lane-P4/blob/master/Images/image_threshold.PNG)

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `warper()`, which appears in lines 1 through 8 (in the "
Image transform" cell of the IPython notebook).  The `warper()` function takes as inputs an image (`img`), as well as source (`src`) and destination (`dst`) points.  I chose the hardcode the source and destination points in the following manner:

```python
src = np.float32([[150+430,460],[1150-440,460],[1150,720],[150,720]])
       
dst = np.float32([[offset1, offset3], 
                          [img_size[0]-offset1, offset3], 
                          [img_size[0]-offset1, img_size[1]-offset2], 
                          [offset1, img_size[1]-offset2]])
```

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 580, 460      | 200, 0        | 
| 150, 720      | 320, 720      |
| 1150, 720     | 1080, 720     |
| 710, 460      | 1080, 0       |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text](https://github.com/evaliu1/CarND-Advanced-Lane-P4/blob/master/Images/bird_view.PNG)

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

I first take a histogram along all the columns in the lower half of the image. With this histogram I am adding up the pixel values along each column in the image. In my thresholded binary image, pixels are either 0 or 1, so the two most prominent peaks in this histogram will be good indicators of the x-position of the base of the lane lines. I can use that as a starting point for where to search for the lines. From that point, I can use a sliding window, placed around the line centers, to find and follow the lines up to the top of the frame.

And my lane lines with a 2nd order polynomial kinda like this:

![alt text](https://github.com/evaliu1/CarND-Advanced-Lane-P4/blob/master/Images/Fit_line.PNG)

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I defined two functions to calculate the curvature and car position("car_distance()" and "Curvature()").

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I combined all the steps for image process and calculating curvature and car position in my code in the function `Img_Pipeline()`.  Then I called this function to process all the test images, here is an example of my result on a test image:

![Color img](https://github.com/evaliu1/CarND-Advanced-Lane-P4/blob/master/Images/Color_img.JPG)

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](https://github.com/evaliu1/CarND-Advanced-Lane-P4/blob/master/output.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

During the time I implemented this project, I mixed with the output parameters couple times. Sincer their names are very similar inside each function.
For my current pipeline, I think it might fail when there is a sharp trun. Also, the lines are not so straight at the beginning. Maybe I should adjust my threshold more to make this better.
