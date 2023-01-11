# kaggle-ships-in-Google-Earth
Applying YOLOv8 to Kaggle Ships in Google Earth dataset.

## Dataset
The dataset used is hosted on Kaggle at [Ships in Google Earth](https://www.kaggle.com/tomluther/ships-in-google-earth). This dataset consists of images extracted from Google Earth, and images are typically 30 or 50cm resolution. Images generally consist of a range of sizes of ship/boat against the blank ocean background, but some images are captured with ships close to the shoreline or with multiple clustered boats. Each ship is annotated with a bounding box in Pascal VOC (XML) format.

<p align="center">
<img src="images/kaggle.png" width="900">
</p>

For this project I use [YOLOv8](https://github.com/ultralytics/ultralytics) to perform object detection, as this is a model that is easy to use and has state of the art performance. YOLOv8 requires images in a specific annotation format, so to transform the annotations from Pascal VOC to YOLOv8 format  I uploaded the dataset to [Roboflow](https://roboflow.com/) for reformatting. Roboflow provides a number of handy features including dataset insights & versioning, data pre and post processing (resizing & augmentations), and the ability to export datasets in the required format.

The dataset on kaggle consist of 794 images, with 694 in a training set and 100 in test set. I uploading these to Roboflow and rebalanced the dataset into train/validation/test splits with 70%/20%/10% weighting (note these % change after augmenting the test images). I applied three augmentations to the training images which are appriate for aerial imager: a horizontal flip and 2x rotations. This increased the training set size threefold to approximately 1400 images. For pre-processing I resized all images to 640x640 since this is the expected size by the YOLOv8m model.

<p align="center">
<img src="images/dataset.png" width="700">
</p>

## Training YOLOv8
Roboflow provide ready to use training notebooks, and I used the [YOLOv8 notebook](https://github.com/roboflow/notebooks/blob/main/notebooks/train-yolov8-object-detection-on-custom-dataset.ipynb). The notebook is provided here in the file `yolov8-kaggle-ships.ipynb` and the final trained model is in the `model` folder. Training was performed on Google Colab Pro using a Tesla T4. The results of training for 100 epochs are below (yolov8n):
<p align="center">
<img src="images/training.png" width="700">
</p>

## Validation metrics
Metrics for yolov8m model:
- Precision: 0.923
- Recall: 0.936
- mAP@.5: 0.959
- mAP@.5:.95: 0.729

Analysing the predictions on the validation set we observe some errors particularly for small boats. Overall the results are encouraging

<p align="center">
<img src="images/val-preds.jpeg" width="700">
</p>

Reviewing the test predictions (see notebook), in general ships are accurately detected with high confidence but some small and very long ships are missed.

## Conclusions
With relatively little time and effort I trained a lightweight YOLOv8 that provides useful performance for ships detection. Note also that YOLOv8n is the smallest available YOLOv8 model, and better metrics may be achieved with one of the larger models. Experimentation with alternative augmentation strategies could be explored to help balance out some of the variations due to sea conditions (rough or calm), lighting (overcast or bright sunshine), ship density.