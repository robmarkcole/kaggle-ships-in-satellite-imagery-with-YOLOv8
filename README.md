# kaggle-ships-in-Google-Earth-yolov5
Applying YOLOv5 to Kaggle Ships in Google Earth dataset.

## Dataset
The dataset used is the kaggle [Ships in Google Earth](https://www.kaggle.com/tomluther/ships-in-google-earth) which consists of images extracted from Google Earth. These images are typically 30 or 50cm resolution, and generally consist of a range of sizes of ship/boat against the blank ocean background. Some images are captured with ships close to the shoreline, or with multiple clustered boats. Each ship is annotated with a bounding box in Pascal VOC (XML) format.

<p align="center">
<img src="https://github.com/robmarkcole/kaggle-ships-in-Google-Earth-yolov5/blob/main/images/kaggle.png" width="900">
</p>

For this project I wished to use [YOLOv5](https://github.com/ultralytics/yolov5) to perform object detection, as this is a model that is easy to use and has good performance. YOLOv5 requires images in a specific annotation format, so to transform the annotations from Pascal VOC to YOLOv5 format I uploaded the dataset to [Roboflow](https://roboflow.com/), which provides a number of handy features including dataset insights & versioning, data pre and post processing (resizing & augmentations), and the ability to export datasets in the required YOLOv5 format (or many other formats).

The dataset on kaggle consist of 794 images, and on uploading to Roboflow I rebalanced the dataset into train/validation/test splits with 550/183/61 images respectively, equating to approximately 70%/23%/7%. I applied three augmentations to the training images which are appriate for aerial imager: a horizontal flip and 2x rotations. This increased the training image set size threefold to approximately 1400 images. For pre-processing I resized all images to 640x640 since this is the expected size by the YOLOv5s model, which is the smallest and fasted of the YOLOv5 models. Viewing the dataset healthcheck we can see this would result in the majority of images being stretched slightly since the median image size is 510x493 pixels.

<p align="center">
<img src="https://github.com/robmarkcole/kaggle-ships-in-Google-Earth-yolov5/blob/main/images/dataset.png" width="900">
</p>

<p align="center">
<img src="https://github.com/robmarkcole/kaggle-ships-in-Google-Earth-yolov5/blob/main/images/dataset_healthcheck.png" width="900">
</p>

Using [lightly.ai](https://www.lightly.ai/) we can visualise the data in feature space, here with embeddings generated using the default resnet18-simclr backbone. A cluster plot of the embeddings shows several discrete clusters, with a cluseter of boats by the shore highlighted. There is also a medium sized cluster to the left of the plot which are container ships, whilst the largest cluser (right) are mainly smaller ships. This inspection reveals some class imbalances in the data, i.e. imbalance across size of boats, and whether at port or at sea.

<p align="center">
<img src="https://github.com/robmarkcole/kaggle-ships-in-Google-Earth-yolov5/blob/main/images/lightly.png" width="900">
</p>

## Training
Roboflow provide ready to use training notebooks, and I used the YOLOv5 notebook as-is. The notebook is provided here in the file `training.ipynb` and the final trained model is in the `model` folder. Training was performed on Google Colab Pro using a Tesla T4 with 16GB RAM and completed in approximately 21 mins. The results of training for 100 epochs are below:

<p align="center">
<img src="https://github.com/robmarkcole/kaggle-ships-in-Google-Earth-yolov5/blob/main/images/training.png" width="400">
</p>

- Precision: 0.852
- Recall: 0.572
- mAP@.5: 0.708
- mAP@.5:.95: 0.442

Whilst we have achieved a decent precision and mAP@.5, the recall is a little low, indicating missed ships. The notebook shows the inferences performed on the hold-out test set. We see that for ships in the open water the model does well:

<p align="center">
<img src="https://github.com/robmarkcole/kaggle-ships-in-Google-Earth-yolov5/blob/main/images/good_1.png" width="500">
</p>

However the model has failed to distinguish boats that are clustered:

<p align="center">
<img src="https://github.com/robmarkcole/kaggle-ships-in-Google-Earth-yolov5/blob/main/images/error_1.png" width="500">
</p>

Also a boat that is small and has a large wake is missed:

<p align="center">
<img src="https://github.com/robmarkcole/kaggle-ships-in-Google-Earth-yolov5/blob/main/images/error_2.png" width="500">
</p>

Finally the model has mistaken the dock for a boat, which is perhaps understandable as large container ships do reseble a dock covered with containers.

<p align="center">
<img src="https://github.com/robmarkcole/kaggle-ships-in-Google-Earth-yolov5/blob/main/images/error_3.png" width="500">
</p>

## Conclusions
With relatively little time (1-2 hours including training) and effort a model was trained that provides decent performance for ships in open water. However small ships, clustered ships, and false positives close to land are an issue. Future work will evaluate the balance of the dataset with regard to these poorly performing cases. Note also that YOLOv5s is the smallest available, and better metrics could be achieved with one of the [larger models](https://github.com/ultralytics/yolov5#pretrained-checkpoints). Experimentation with alternative augmentation strategies could be explored, to help balance out some of the variations we observe due to: sea conditions (rough or calm), lighting (overcast or bright sunshine), ship density.