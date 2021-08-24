# Contextual-Adversarial-Patches

Official Implementation of the paper [Role of Spatial Context on Adversarial Robustness in Object Detection][paper]

The utilization of spatial context to improve accuracy in most fast object detection algorithms is well known. Detectors increase inference speed by doing a single forward pass per image which means they implicitly use contextual reasoning for their predictions. It can been shown that an adversary can design contextual adversarial patches - patches which do not overlap with any objects of interest in the scene - and exploit contextual reasoning to fool standard detectors. In this paper, we also study methods to fix this vulnerability. We design category specific adversarial patches which make a widely used object detector like YOLO blind to an attacker chosen object category and show that limiting the use of contextual reasoning during object detector training acts as a form of defense. We believe defending against context based adversarial attacks is not an easy task. We take a step towards that direction and urge the research community to give attention to this vulnerability.

![alt text][teaser]

## Requirements
Tested with pytorch 1.1 and python 3.6 \
An environment file has been provided.

ZB: has to install tqdm in addition to environment.yml

## Dataset creation

Download PASCAL VOC data. 

ZB: The host.robots.ox.ac.uk site will take a while to respond but there are 
alternative download sites as searched on google. All data will get extracted to VOCdevkit/
```
wget http://host.robots.ox.ac.uk/pascal/VOC/voc2012/VOCtrainval_11-May-2012.tar
wget http://host.robots.ox.ac.uk/pascal/VOC/voc2007/VOCtrainval_06-Nov-2007.tar
wget http://host.robots.ox.ac.uk/pascal/VOC/voc2007/VOCtest_06-Nov-2007.tar
tar xf VOCtrainval_11-May-2012.tar
tar xf VOCtrainval_06-Nov-2007.tar
tar xf VOCtest_06-Nov-2007.tar
wget http://pjreddie.com/media/files/voc_label.py
python voc_label.py
cat 2007_train.txt 2007_val.txt 2012_*.txt > voc_train.txt
```

Insert appropriate Devkit path. Look for occurrences of <devkit_root> in the project.

ZB: change to /projects/f_ps848_1/pascalvoc and run command below to regenerate dataset/no_class_overlap_clean_test 
with correct path

```python
python filter_PASCAL_VOC.py PASCAL_VOC_annotations.txt /projects/f_ps848_1/pascalvoc/VOCdevkit/VOC2007/ImageSets/Main/test.txt
```

This script reads the annotation txt file containing the bounding box and size information of each image in PASCAL VOC 2007 and finds images for each class where no ground truth boxes of that class overlap with our patch location.

The image sets used for the experiments in the paper are provided in dataset/no_class_overlap_clean_test

## Download Pretrained Weights
ZB:
```
mkdir weights
```

Download darknet weights
```
cd weights
wget http://pjreddie.com/media/files/darknet19_448.conv.23
```
Download YOLO VOC weights
```
wget http://pjreddie.com/media/files/yolo-voc.weights
```

## Per Image Patch
```bash
bash run_pipeline_per_image_patch.sh
```

This script trains contextual adversarial patch per image of a chosen category and evaluates YOLO on the patched images. Please change the VOC category name and the category index to run for desired category.

## Universal Patch
```bash
bash run_pipeline_universal_patch.sh
```

This script trains universal contextual adversarial patch for a chosen category and evaluates YOLO on the held out images patched with the universal patch. Please change the VOC category name and the category index to run for desired category.


## Train Grad Defense
```python
python train_defense.py cfg/voc.data cfg/yolo-defense.cfg weights/darknet19_448.conv.23 backupdir voc_train.txt
```


## Citation
If you find our paper or code useful, please cite us using
```bib
@InProceedings{Saha_2020_CVPR_Workshops,
author = {Saha, Aniruddha and Subramanya, Akshayvarun and Patil, Koninika and Pirsiavash, Hamed},
title = {Role of Spatial Context in Adversarial Robustness for Object Detection},
booktitle = {The IEEE/CVF Conference on Computer Vision and Pattern Recognition (CVPR) Workshops},
month = {June},
year = {2020}
}
```
---
#### License
MIT License (see LICENSE file).

[paper]: https://arxiv.org/abs/1910.00068
[teaser]: https://github.com/UMBCvision/Contextual-Adversarial-Patches/blob/master/Teaser_Contextual_Reasoning.PNG
