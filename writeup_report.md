# Vehicle Dection

## Writeup Report
---

**Vehicle Detection Project**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector.
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[image1]: ./output_images/car_not_car.png
[image2]: ./output_images/HOG_example.jpg
[image3]: ./output_images/output_test1.jpg
[image4]: ./output_images/output_test2.jpg
[image5]: ./output_images/output_test3.jpg
[image6]: ./output_images/output_test4.jpg
[image7]: ./output_images/output_test5.jpg
[image8]: ./output_images/output_test6.jpg
[image9]: ./output_images/bboxes_and_heat.png
[image10]: ./output_images/labels_map.png
[image11]: ./output_images/output_bboxes.png
[video1]: ./project_video.mp4

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Vehicle-Detection/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Histogram of Oriented Gradients (HOG)

#### 1. Explain how (and identify where in your code) you extracted HOG features from the training images.

The code for this step is contained in the first code cell of the IPython notebook.  

I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of one of each of the `vehicle` and `non-vehicle` classes:

![alt text][image1]

I then explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I grabbed random images from each of the two classes and displayed them to get a feel for what the `skimage.hog()` output looks like.

Here is an example using the `RGB` color space and HOG parameters of `orientations=9`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`:


![alt text][image2]

#### 2. Explain how you settled on your final choice of HOG parameters.

I tried various combinations of parameters, finally I decided to use the class used 'YUV' color space, with SVM classifier, I got a very good accuracy. Also, for tuning parameters, I tried some and found out the class reference is a good base.


|   Parameters   | Values  |
|:--------------:|:-------:|
|  color_space   | 'YUV' |
|  spatial_size  | (32,32) |
|   hist_bins    |   32    |
|     orient     |    13    |
|  pix_per_cell  |    8    |
| cell_per_block |    2    |
|  hog_channel   |  'All'  |
|  spaital_feat  |  True   |
|   hist_feat    |  True   |
|    hog_feat    |  True   |

#### 3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

I trained a linear SVM using YUV HOG featurs with historgram of color and spatially binned color. The test accuracy is 98.93%.

### Sliding Window Search

#### 1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

Regarding the scaling-window search, I used the multi-scale slide method instead of a dynamic one. I hardcode ten scales to implement the search. The details is shown below. It works fine for the test video. But definitely a dynamic scaling search approach will be better.

#### 2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

Ultimately I searched on three scales using YCrCb 3-channel HOG features plus spatially binned color and histograms of color in the feature vector, which provided a nice result.  Here are some example images:

![alt text][image3]
![alt text][image4]
![alt text][image5]
![alt text][image6]
![alt text][image7]
![alt text][image8]
---

### Video Implementation

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here's a [link to my video result](./output_images/project_video_output.mp4)


#### 2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.  

Here's an example result showing the heatmap from a series of frames of video, the result of `scipy.ndimage.measurements.label()` and the bounding boxes then overlaid on the last frame of video:

### Here is an emaple of one frames and its corresponding heatmap:

![alt text][image9]

### Here is the output of `scipy.ndimage.measurements.label()` on the integrated heatmap from all six frames:
![alt text][image10]

### Here the resulting bounding boxes are drawn onto the last frame in the series:
![alt text][image11]

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

One of the issue I met was to decide the HOG parameters. I have to try different options multiple times to get the best training result. In the future, more parameter combinations could be tried to get a better predict performance.

Also, it's difficult to determine a good heat map threshold. Since lower threshold will cause false detection, higher value will filter out the real vehicles.

In addition, I am using a fixed scale method to do the window search for the current project. In the future, a dynamic scaling approach for the window search should be more robust.
