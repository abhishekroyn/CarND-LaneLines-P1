# **Finding Lane Lines on the Road** 

## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file. But feel free to use some other method and submit a pdf if you prefer.

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./test_images_output/solidWhiteCurve.jpg
[image2]: ./test_images_output/solidWhiteRight.jpg
[image3]: ./test_images_output/solidYellowCurve.jpg
[image4]: ./test_images_output/solidYellowCurve2.jpg
[image5]: ./test_images_output/solidYellowLeft.jpg
[image6]: ./test_images_output/whiteCarLaneSwitch.jpg

[video1]: ./test_videos_output/solidWhiteRight.mp4
[video2]: ./test_videos_output/solidYellowLeft.mp4
[video3]: ./test_videos_output/challenge.mp4

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 5 steps. 
1) First, I converted the images to grayscale and applied Gaussian blur on it. For optional challenge, I chose to convert the video frames (images) into HSVscale in order to filter colors and retain only a specific range of white color. And applied morphological operation 'closing' on the filtered HSVscaled image to remove noise in the image.
2) Then, I converted the scaled (gray or hsv) to canny image to extract edges in the image.
3) Next, I masked the edge to have only region of interest retained which is basically a polygon (quadrilateral) region here provided by specifying coordinates of the vertices obtained and adjusted by trial and error method.
4) Thereafer, I calculated hough lines from the masked image where hough lines are obtained and drawn on a blank image. If custom flag if set then multiple hough lines are averaged/extrapolated to map out the full extent of the lane lines and provides single left and single right lines only.
5) Finally, the image with hough lines and the original image were combined to give a final weighted image having detected lane-lines superimposed in red on top of the original image.

In order to draw a single line on the left and right lanes, I modified the draw_lines() function by providing a custom flag. When custom flag was set, 4 arrays were initialized for left_lane_x_coordinate, left_lane_y_coordinate, right_lane_x_coordinate, and right_lane_y_coordinate respectively. Each of these 4 arrays were then assigned an empty list. I iterated over all the hough lines one after another. From each line, x and y coordinates of both ends of the line was used to calculate slope of that line and based on slope being positive (or negative) and above (or below) a threshold, it was considered as part of left (or right) line and both ends x and y coordinates were added to left_lane_x_coordinate (or right_lane_x_coordinate) and left_lane_y_coordinate (or right_lane_y_coordinate) respectively. From there, using all of the x and y coordinates of the left_lane (or right lane), I calculated average slope and intercept of single left line (or right line) using np.polyfit and converted it back into equation of a line with x and y coordinates being variable using np.poly1d for left line (or right line). Thus, I obtained single left line (or right line) and plotted them on a blank image using cv.line and region of interest vertices specified earlier.

The output images usinig modified draw_lines() function are shown below: 

![alt text][image1]
![alt text][image2]
![alt text][image3]
![alt text][image4]
![alt text][image5]
![alt text][image6]

The output video usinig modified draw_lines() function are shown below: 

![alt text][video1]
![alt text][video2]
![alt text][video3]


### 2. Identify potential shortcomings with your current pipeline


1) One potential shortcoming would be what would happen when there are vehicles at front in the image frame which might end up hiding major sections of lane-lines.

2) Another shortcoming could be the inability of the algorithm to handle curved lane-lines accurately.

3) Also, presence of road-bumps may offset region of interest on the image-frame.


### 3. Suggest possible improvements to your pipeline

1) A possible improvement would be to further fine-tune hough parameters and vertices of region-of-interest through additional trial-and-error method in order to keep using high-resolution for parameters to avoid very high computation time while still being able to retrieve meaningful information without much noise.

2) Also, to average and/or extrapolate the line segments here I preferred to find their slope and intercept first, and then converted them back into an average and extrapolated line segment. The same could be achieved and tested for performance by finding their slope & center first.

3) Another potential improvement could be to use clustering algorithms like K-means for extrapolation of lines and finding its center points while modifying draw_lines() function.

4) Moreover, historical data could be helpful for processing, for example, using results of past frames like past 10 frames for smoothing in order to have lane-lines change shape per frame quite smoothly.
