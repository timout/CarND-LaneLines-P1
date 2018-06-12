# **Project 1: Finding Lane Lines on the Road** 

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report

[//]: # (Image References)

[timage1]: test_images_output/processed_solidWhiteCurve.jpg 
[timage2]: test_images_output/processed_solidWhiteRight.jpg 
[timage3]: test_images_output/processed_solidYellowCurve.jpg
[timage4]: test_images_output/processed_solidYellowCurve2.jpg
[timage5]: test_images_output/processed_solidYellowLeft.jpg
[timage6]: test_images_output/processed_whiteCarLaneSwitch.jpg

[tdark]: test_images_output/processed_solidYellowLeft_dark.jpg

---

### Reflection

The pipeline was implemented in process_image method and consists of the following steps:
* Convert image to grayscale
* For better yellow line isolation convert image using HSV (Hue value saturation). Apply white mask to gray image and yellow mask to HSV image. Combine masks in result image. Yellow range was found using varios examples from google. The pipeline works actually fine without hsv, but it dramatically improved "challenge.mp4" results. Also it was found that it negatively impact results if visibility is lower. 
* Blurs an image using a Gaussian filter for noise suppression. 
* Find edges using Canny Edge Detection.
* Calculate Region of Interests (ROI). Implemented as roi_shape function. Initially tested with hardcoded coordinates, but as a very simple attemp to make it more flexible was expressed in shape fractions. 
* Apply ROI to image for scope reduction.  
    Image outside ROI is set to black.
* Apply Hough transformation for line detection.
* Using result of Hough transformation calculate and draw two (left and right) lines.
  Logic is implemented in draw_lines function.
  Flow:
  1. Calculate slope for every input line
  2. Filter out (almost) horizontal lines and lines with inf slope.
  3. Separate, base on slope, left and right lines. (we need to keep in mind that upper-left coner is a begining of coordinates. )
  4. Compute averages for slope and lines
  5. Smooth lines if they are intersected
  6. If runs against video file smooth previous and current frames and save current frame to cache.
     Different coeficieants were tested but 0.2 showed the best results.
* Overlay image with lines over unmodified image.

The pipeline was tested in 2 modes:
* Against single frame images
```python
    image = mpimg.imread(filename)
    processed = process_image(image)
```
##### solidWhiteCurve
![alt text][timage1]
##### solidWhiteRight
![alt text][timage2]
##### solidYellowCurve
![alt text][timage3]
##### solidYellowCurve2
![alt text][timage4]
##### solidYellowLeft
![alt text][timage5]
##### whiteCarLaneSwitch
![alt text][timage6]
* Against video files
```python
    clip = VideoFileClip("videofile")
    res_clip = clip.fl_image(process_image) 
    %time res_clip.write_videofile(white_output, audio=False)
```
#### solidWhiteRight
[![Video White](test_videos_output/solidWhiteRight.gif?raw=true)]
#### solidYellowLeft
[![Video White](test_videos_output/solidYellowLeft.gif?raw=true)]
#### challenge
[![Video White](test_videos_output/challenge.gif?raw=true)]
    

### 2. Identify potential shortcomings with your current pipeline

* Changing Visibility conditions. 

    Current approach is not adaptive to visibility conditions. "solidYellowLeft_dark" is an example of that
##### solidYellowLeft_dark
![alt text][tdark]

* Region of Interest calculation is not flexible.
It can be affected by more steep turns, front cars,  hills...

### 3. Suggest possible improvements to your pipeline

* Improve identification process under various visibility conditions. 
* Flexible region of interest for better processing turns, ups and downs, cars, or camera placement.
