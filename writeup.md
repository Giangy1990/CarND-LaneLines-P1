# **Finding Lane Lines on the Road**

### Reflection

### 1. Pipeline description.

The function *lane_line_finder()* contains my pipeline to detect the lane lines on the provided images/videos. It takes in input the frame to be processed and returns a new frame with the detected line printed out.\
This pipeline has a work flow composed by five core steps:
* Gray scale image conversion;
* Gaussian blur filtering;
* Canny edge detection;
* Region of interaction selection;
* Hough transform.

To develop my pipeline I customized the helper function provided by Udacity.\
In particular:
1. I renamed the *draw_lines()* function in *draw_lines_org()* to preserve a backup version of the provided one;
2. I wrote my own *draw_line()* function;
3. I wrote an outliers filter function named *outliers_filter()*;
4. I modified the *hough_lines()* function to manage the introduced features.

The *draw_line()* function has the task to identify the two lane line starting from the lines list provided by Hough algorithm. To do that I created two support lists separating the input lines according two criteria:
- left or right side of the image;
- line slope.

Here there is an extraction of the code:
```
for line in lines:
    for x1,y1,x2,y2 in line:
        # compute the slope of the current line
        slope = ((y2-y1)/(x2-x1))
        # check if the current line is on the left or right side of the image
        if (x1 <= side_threshold) & (x2 <= side_threshold):
            # check if the slope is coherent with the left side
            if slope < 0:
                left_lines.append((x1,y1,x2,y2))
        else:
            # check if the slope is coherent with the right side
            if slope > 0:
                right_lines.append((x1,y1,x2,y2))
```

The *outliers_filter()* function is used to erase the outliers from the line lists.
It takes in input the line list and the filter parameter. The output is the line list without outliers.
It is an implementation of the quantile filtering applied to the line slope. This is useful to avoid displacement in the computation of the final lane lines.

The function *hough_lines()* has been improved introducing two adding parameters:
- mode: it selects which draw line function must be used;
- filter_parameter: it is used by the filter to compute the quantile.
The step added to this function is the call to *outliers_filter()* before the call to the chosen draw line function.

The parameters that must be tuned for the pipeline are:

|    Algorithm    |   Parameter     | Value   |
| :-------------: | :-------------: | :-----: |
| Gaussian Blur   | kernel size     | 5       |
| Canny Detector  | low threshold   | 80      |
| Canny Detector  | high threshold  | 240     |
| Hough Transform | outliers filter | 0.45    |
| Hough Transform | rho             | 2       |
| Hough Transform | theta           | 1 rad   |
| Hough Transform | threshold       | 50      |
| Hough Transform | min line len    | 20      |
| Hough Transform | max line gap    | 20      |

### 2. Identify potential shortcomings with your current pipeline

Some potential shortcoming would be the management of the curves and the imprecision due to colored line and shadow.

### 3. Suggest possible improvements to your pipeline

A possible improvement would be the management of the curves using spline and a new tuning able to manage the shadow.
