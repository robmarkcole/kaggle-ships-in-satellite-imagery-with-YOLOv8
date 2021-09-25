# kaggle-ships-in-Google-Earth-yolov5
Applying yolov5 to Kaggle Ships in Google Earth dataset

## Dataset
The dataset used is the kaggle [Ships in Google Earth](https://www.kaggle.com/tomluther/ships-in-google-earth) which consists of images extracted from Google Earth. These images are typically 30 or 50cm resolution, and generally consist of a range of sizes of ship/boat against the blank ocean background. Some images are captured with ships close to the shoreline, or with multiple clustered boats. Each ship is annotated with a bounding box in Pascal VOC (XML) format.

<p align="center">
<img src="https://github.com/robmarkcole/kaggle-ships-in-Google-Earth-yolov5/blob/main/images/kaggle.png" width="900">
</p>

For this project I wished to use [Yolov5](https://github.com/ultralytics/yolov5) to perform object detection, as this is a model that is easy to use and has good performance. Yolov5 requires images in a specific annotation format, so to transform the annotations from Pascal VOC to Yolov5 format I uploaded the dataset to [Roboflow](https://roboflow.com/), which provides a number of handy features including dataset insights & versioning, data pre and post processing (resizing & augmentations), and the ability to export datasets in the required Yolov5 format (or many other formats).

<p align="center">
<img src="https://github.com/robmarkcole/kaggle-ships-in-Google-Earth-yolov5/blob/main/images/dataset.png" width="750">
</p>

<p align="center">
<img src="https://github.com/robmarkcole/kaggle-ships-in-Google-Earth-yolov5/blob/main/images/dataset_healthcheck.png" width="750">
</p>