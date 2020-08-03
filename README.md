# **Finding Lane Lines on the Road** 

## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file. But feel free to use some other method and submit a pdf if you prefer.

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 5 steps. First, I converted the images to grayscale.
Then I defined parameters for Canny and apply smoothing to the image. 
After that, to find Region of Interest, I created a masked edges image using cv2.fillPoly() and defined a four sided polygon to mask.
Finally, I defined the Hough transform parameters, made a blank the same size as our image to draw on and ran Hough on edge detected image outputing "lines", which is an array containing endpoints of detected line segments


In order to draw a single line on the left and right lanes, I modified the draw_lines() function by drawing the average lines from segments of lines.
A continuous line like Y = aX + b consists of two things: a (slope) and b (coefficient)
From segments from Hough Transform, we can calculate slope of each segment: 
- Positive slope means left line.
- Negative slope means right line. 
The maximum Y-coordinate of all points on both lines is the number of rows in the image matrix while minimum Y-coordinate is the minimum Y value of all points
After that, I calculated the slope of each segment. Then average value of them is the average slope of this lane.
The average position of this lane is the average of x and y coordinate of all points.
From that, average b coefficient is calculated by b = avg_y -a*avg_x
To draw lane line on the image, we need to know the start and end points, which are determined by 
top_x = (minY -b)/a
bottom_x=(maxY -b)/a 

The output of solidYellowLeft video has glitch, where two lane lines are crossed. This happened because some of the segments noise interfere with lane line drawing function.
By filter out segments which are smaller than 40 pixels by applying euclidean distance and offset the slope by 0.5 to counter the noise

If you'd like to include images to show how the pipeline works, here is how to include an image: 

![alt text][image1]


### 2. Identify potential shortcomings with your current pipeline


One potential shortcoming would be what would happen when the car facing curved or obstructed lanes since our approach can only find the straight line.
 

Another shortcoming could be that all parameters are fixed or hard-coded. This can lead to dangerous situations because the algorithm doesn't adapt to the camera's point of view to the road, where the region of interest will be completely different from settings


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to build the model by using higher order polynominals to better respresent the lane in multiple cases (curve or straight).

Another potential improvement could be to use neural network to tune the parameters according to the training.
