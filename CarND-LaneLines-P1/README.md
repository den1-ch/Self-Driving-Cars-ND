# **Finding Lane Lines on the Road** 


The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: test_images/solidWhiteRight.jpg
[image2]: test_images_output/edges.jpg

---

### Reflection


### 1. Road lines detection pipeline
![image example][image1]
Here the pipeline that I had to find road line on the images and even video. It consists of 3 major steps: 
* First, detect edges in the image. For that, I converted image to grayscale and after applied gaussian blur to reduce noise. To detect edges on gray blured image I used [Canny algorithm](https://en.wikipedia.org/wiki/Canny_edge_detector). I tried to use diffrent combination of pairs of low_threshold and high_threshold but 40 for low and 120 high seems works the best on tested images.
![canny_edges][image2]
* Second, Find lines in area of interested. For that, first I filtered not usefful parts of the image, I created polygon mask for the area that I expect to see lanes, and applied to image with edges. After I used "Hough transform" to detect lines with next configuration:     
    ** rho = 1 # distance in Hough space in units of pixels
** theta = np.pi / 180 # angular resolution in units of radians (2p rad = 360)
** threshold = 10 # specifies the minimum number of votes (intersections in a given grid cell) 
** min_line_length = 90 # is the minimum length of a line (in pixels) that you will accept in the output
** max_line_gap = 30

* Third, Draw left and right lines. I anylysed outhput of "Hough transform" to figure out position of left and right lines. All lines with negative slope I used as data to detect left line, and with positive slope for right line. Collected data point I used as input for least-squares regression. 


### 2. Identify potential shortcomings with your current pipeline

Typical issue with current approach that I have static mask and analyse only one particular area in the image, but images could be made with diffrent angle. Another problem would be with curved road, my algorith always assume that line is straight. Also shadows on the road are big issue with current approach.


### 3. Suggest possible improvements to your pipeline

On the begging to extropulate left and right lines, I simply tries to get avarage of points, but applying linear regresssion seems improved results. I think it worth to try non linear function such as [curve fit] (https://docs.scipy.org/doc/scipy/reference/generated/scipy.optimize.curve_fit.html#scipy.optimize.curve_fit) it should better work on curves.
Other, minor improvement would be to not apply gausian blur as seems Canny already doing that. 
I added filter by slope by counting lines with very small slope (abs(slope) = 0.1) that hepled reduce eror of line detection for cases like
** shadows 
** perpendicular lines on side of road
** top of the car hood
