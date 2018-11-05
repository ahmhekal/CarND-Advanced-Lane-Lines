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

[image1]: ./output_images/undestorted_test.jpg "Undistorted"
[image2]: ./test_images/test1.jpg "Road Transformed"
[image3]: ./output_images/binary.jpg "Binary Example"
[image4]: ./output_images/warped_color.jpg "Warp Example straight lines color"
[image9]: ./output_images/warped.jpg "Warped Binary"

[image5]: ./output_images/lanes.png "Fit Visual"
[image6]: ./output_images/final.jpg "Output"
[image7]: ./camera_cal/calibration1.jpg "original distorted"
[image8]: ./straight_lines1.jpg "Road with straight lines"


[video1]: ./output_videos/project_video.mp4 "Output Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is at the beginning of the third cell of the IPython notebook located in "Source.ipynb"  

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection. 

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function which I wrote it in another function called (Calibrate_Cam) and obtained this result: 

![alt text][image1]



the original distorted image :
![alt text][image7]


### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![alt text][image2]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of color and gradient thresholds to generate a binary image. (I used a combination of thresholds for S and H in HLS color space, and for gradient threshold I used only Sobel x)

I wrote the code in a function called 'Color_Gradient_Threshold()' to do this step. you can find it in the same "Source.ipynb" file in the second cell (all the other used functions are in the same cell).  Here's an example of my output for this step.

![alt text][image3]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform is in a function called `Bird_Eye()`, which appears in lines 38 through 45 in the same file "Source.ipynb" and the second cell. 

The `Bird_Eye()` function takes as inputs an image (`img`) and inside it I chose a suitable values for src and dst points from the image (1280px x 720px).
I chose the hardcode the source and destination points in the following manner:
I chose the pixels by 

    src=np.float32([ [575, 463], [280, 660], [1000,660], [705,463] ]) #source points
    dst=np.float32([ [290, 5], [290, 700], [990,700], [990,5]])       #distination points 
    
    
```python
src = np.float32(
    [[575, 463],
    [280, 660],
    [1000,660],
    [705,463]])
dst = np.float32(
    [[290, 5],
     [290, 700],
     [990,700],
     [990,5]])
```


I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.
- original image
![alt text][image8]


- warped image
![alt text][image4]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?
I passed the binary thresholded image to the function called 'Detect_Lanes()' which starts from line 142 in the second cell of the "Source.ipynb" file.

this is the binary thresholded image
[image3]: ./output_images/binary.jpg "Binary Example"

Then I did some other stuff and fit my lane lines with a 2nd order (polynomial one polunomial for each line), then I used it to fill a polygon in the area between the two lines with green color.

I came with this output for the above image:

![alt text][image5]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I implemented the step of calculation the resadius of caurvature in lines 192 through 209 in my code in the second cell of the "Source.ipynb" file.
by scaling the points to the world space
then using the formula to calculate the raduis:


And I calculating  the position of the vehicle with respect to center 58 through 70
and in lines 19 through 28 in my code in the Fourth cell of the "Source.ipynb" file,
by calculating difference between the middle of the image and the middle of the lane lines then converting it to the world space.

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I wrote the function 'unwarp()' to return the image to the original perspective in lines 186 through 189 in my code in the second cell of the "Source.ipynb" file.

and I used it later in the code

Here is an example of my result on a test image:

![alt text][image6]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

some of the problems / issues I faced :
- choosing proper distination points for the perspictive transform. I handled it by :
First I watched the image (1280 * 720 px) and calculated the points manually, then I saw the warped image. I repetedly changed the points and watching the warped image untill I came with the best result for the points.

- The algorithm at first was deteting the shadows of trees or a near car as a line which I don't want.
I get rid of these fake lines by taking H threshold Not only S in the HLS space Color thresholding; the thresholded image became much more clearer.

My pipeline may fail if a fake line appears that the thresholding step is not able to eliminate (like in the challenging video)

Also if the lanes are very curved (has a very low radius of curveture). the histogram will not detect the starting points of the lanes correctly.

Also my pipeline may fail if at any frame, no enough points was founded for the lanes; it will fail to make a polynomial to fit the points of the lanes. (possible solution to take the data of the previous frame)

To improve the project we can fire a warning (ALARM) when the car is not within the lane lines.
