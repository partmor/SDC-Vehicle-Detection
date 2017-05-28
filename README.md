# **CarND: Vehicle Detection and Tracking**  [![Udacity - Self-Driving Car NanoDegree](https://s3.amazonaws.com/udacity-sdc/github/shield-carnd.svg)](http://www.udacity.com/drive)
[//]: # (Image References)

[two_samples]: ./output_images/two_samples.png
[pos_hog_sample]: ./output_images/pos_hog_sample.png
[pos_3ch]: ./output_images/pos_3ch.png
[car_hist]: ./output_images/car_hist.png

The goal of this project is to build a software pipeline to **detect and track surrounding vehicles** in a video stream generated by a front-facing camera mounted on a car.

At its core, this pipeline uses a machine learning classifier model, to classify video-frame patches as *vehicle* or *non-vehicle*.

The project is separated into two well-differentiated blocks:
+ **Training stage**: feature extraction pipeline for the training dataset, and model training.
+ **Prediction stage**: feature extraction pipeline for the video stream frames.

It is possible to create a unique feature extraction pipeline that suits both training and prediction stages. However, this is not optimal since this would require a computationally expensive sliding window search implementation to predict on the new input video frames. This will be discussed later in more detail. 

### Project structure

The project workflow is built in the notebook `p5_solution.ipynb`, linked [here](.p5_solution.ipynb). To improve the readability of the notebook and make it less bulkier, the basic core methods and other helpers have been extracted to the `aux_fun.py` script. These functions are cited as `aux_fun.method_name()`, whereas those that are defined in the actual project notebook are simply cited as `method_name()`.

## Training set construction

To train the classifier, a combination of the [GTI vehicle image database](http://www.gti.ssr.upm.es/data/Vehicle_database.html) and the [KITTI vision benchmark suite](http://www.cvlibs.net/datasets/kitti/) has been used in this project.

The merged database contains labeled 64 x 64 RGB images of *vehicles* and *non-vehicles*, in a near-50% proportion. 

| Positive        | Negative   |  Total |
|:-------------:|:-------------:| :-------------:|
| 8792     | 8968       |  17760  |

Here is an example of each class:

![two_samples]

Three types of features are extracted from the database images in this project:
+ Histogram of Oriented Gradients (**HOG**), using `aux_fun.extract_hog_features()`.
+ **Color histogram** features, using `aux_fun.extract_hist_features()`.
+ **Spatial features**, i.e. resized image raw pixel values, using `aux_fun.extract_spatial_features()`.

For the initial car example, the HOG features extracted from the Y channel are:

![pos_hog_sample]

The pixel values in YCrCb space:

![pos_3ch]

And finally the histograms for each of the YCrCb channels: 

![car_hist]

To be consumable by the classifier model, the feature arrays must be flattened and concatenated together. 

For a single image, the total feature vector extraction is encapsulated in `extract_single_img_features()`. This processed is looped for each image in the database with the help of `extract_features_from_img_file_list()`. Finally `build_dataset()` merges and shuffles all the data into the final training set, and provides the label vector populated with zeros and ones. 
