## My Debugged Version of SA-SSD

Here is my **debugged version** of the [SA-SSD](https://github.com/skyhehe123/SA-SSD) model. I greatly referenced [this CSDN blog](https://blog.csdn.net/qq_38316300/article/details/110161110) to debug the model and successfully set up the environment for training. Note that there are some paths that you may need to change in certain files.

### Required environment (I followed this precisely):

```
ubuntu 18.04 (cannot be newer version)

cuda 10.0

cudnn 7.6.4

python 3.6.2 (use anaconda)

pytorch 1.1.0 (use anaconda)

torchvision 0.3.0 (use anaconda)
```

### Required packages:

```
 pip install shapely
 pip install opencv (maybe it should be opencv-python)
 pip install scikit-image
 pip install mayavi (not sure if this is necessary)
 pip install numba
 pip install matplotlib
 pip install Cython
 pip install terminaltables
 pip install tqdm
 pip install pybind11 
 pip install pycocotools
```

### Supplementary files for training:

FYI, I included some useful files that are essential to the training of SA-SSD.

1. PKL files for training: [here](https://drive.google.com/drive/folders/1_bNBcKVOpBdupAE_yVBbncb8NLVrkAiU?usp=sharing). This should be produced after running the create_data.py (which takes some time). You can just download it here if you want to skip the create_data process.

2. I included the ImageSet file in the directory, which specifies the training and validation set of the SA-SSD.

3. I have trained the SA-SSD with car-classfication's configuration file for 12 epochs, and have uploaded the weights (pkl file). You can dowload it from [here](https://drive.google.com/file/d/1qTlYLasaLSkAbjKNkE_Qg-JNMwN6OxCI/view?usp=sharing).

4. I have also trained the SA-SSD with multi-class-classification's configuration file for 6 epochs, and have uploaded the weights (pkl file). You can dowload it from [here](https://drive.google.com/file/d/1G2CRRR32d3Jwq9U4SCO96dculCAt3yWi/view?usp=sharing).

5. If you installed all environment exactly as listed above, you can directly install **spconv** using the wheel that I provided:

```
sudo ./spconv-1.1-cp36-cp36m-linux_x86_64.whl
```

Hopefully, this debugged repository of SA-SSD will be of help.

### Bugs fixed:

1. ModuleNotFoundError: No module named 'mmcv._ext'
2. Cannot find kitti_dbinfos_train.pkl
3. RuntimeError: Expected object of backend CUDA but got backend CPU for sequence element 1 in sequence argument at position
4. TypeError: grid_sample() got an unexpected keyword argument 'align_corners'
5. Many other bugs related to spconv

### Demo screenshot of trainning:

![screenshot](screenshot.png)

### Links to download the relevant KITTI dataset:

```
# Images
!wget https://s3.eu-central-1.amazonaws.com/avg-kitti/data_object_image_2.zip

# Velodyne
!wget  https://s3.eu-central-1.amazonaws.com/avg-kitti/data_object_velodyne.zip

# Calibration
!wget  https://s3.eu-central-1.amazonaws.com/avg-kitti/data_object_calib.zip

# Labels
!wget  https://s3.eu-central-1.amazonaws.com/avg-kitti/data_object_label_2.zip
```

---

## SA-SSD: Structure Aware Single-stage 3D Object Detection from Point Cloud (CVPR 2020) [\[paper\]](https://www4.comp.polyu.edu.hk/~cslzhang/paper/SA-SSD.pdf)
Currently 1st place in KITTI BEV and 3rd in KITTI 3D. The detector can run at 25 FPS. 

**Authors**: [Chenhang He](https://github.com/skyhehe123), [Zeng Hui](https://github.com/HuiZeng), Jianqiang Huang, Xiansheng Hua, [Lei Zhang](https://www4.comp.polyu.edu.hk/~cslzhang/).

## Updates
2020-04-13: Add one_cycle (with Adam) training as default scheduler.

2020-08-04: Multi-class training is supported. (The multi-class traning is not well tuned and will slightly deteriote the performance of model with single class training (i.e. each class has a individual model), please find the bellow AP@(11 recall points) for your reference.)
```
Car AP@0.70, 0.70, 0.70:
bbox AP:98.96, 90.06, 89.52
bev  AP:90.59, 88.43, 87.49
3d   AP:89.69, 79.41, 78.33
aos  AP:98.94, 89.89, 89.19
Car AP@0.70, 0.50, 0.50:
bbox AP:98.96, 90.06, 89.52
bev  AP:98.99, 90.13, 89.68
3d   AP:98.97, 90.10, 89.63
aos  AP:98.94, 89.89, 89.19

Pedestrian AP@0.50, 0.50, 0.50:
bbox AP:62.88, 60.26, 53.58
bev  AP:58.52, 50.29, 44.10
3d   AP:55.75, 48.01, 41.94
aos  AP:58.57, 55.19, 49.07
Pedestrian AP@0.50, 0.25, 0.25:
bbox AP:62.88, 60.26, 53.58
bev  AP:71.34, 62.80, 55.64
3d   AP:71.33, 62.76, 55.60
aos  AP:58.57, 55.19, 49.07

Cyclist AP@0.50, 0.50, 0.50:
bbox AP:87.25, 73.74, 67.84
bev  AP:85.40, 70.48, 64.59
3d   AP:82.80, 63.37, 61.60
aos  AP:86.93, 73.26, 67.41
Cyclist AP@0.50, 0.25, 0.25:
bbox AP:87.25, 73.74, 67.84
bev  AP:86.78, 71.55, 65.85
3d   AP:86.78, 71.54, 65.85
aos  AP:86.93, 73.26, 67.41
```

## Demo
[![Demo](https://github.com/skyhehe123/SA-SSD/blob/master/doc/hqdefault.jpg)](https://www.youtube.com/watch?v=jrAb3ts4tAs)

# Introduction
![model](https://github.com/skyhehe123/SA-SSD/blob/master/doc/model.png)
Current single-stage detectors are efficient by progressively downscaling the 3D point clouds in a fully convolutional manner. However, the downscaled features inevitably lose spatial information and cannot make full use of the structure information of 3D point cloud, degrading their localization precision. In this work, we propose to improve the localization precision of single-stage detectors by explicitly leveraging the structure information of 3D point cloud. Specifically, we design an auxiliary network which converts the convolutional features in the backbone network back to point-level representations. The auxiliary network is jointly optimized, by two point-level supervisions, to guide the convolutional features in the backbone network to be aware of the object structure. The auxiliary network can be detached after training and therefore introduces no extra computation in the inference stage. Besides, considering that single-stage detectors suffer from the discordance between the predicted bounding boxes and corresponding classification confidences, we develop an efficient part-sensitive warping operation to align the confidences to the predicted bounding boxes.

# Dependencies
- `python3.5+`
- `pytorch` (tested on 1.1.0)
- `opencv`
- `shapely`
- `mayavi`
- `spconv` (v1.0)

# Installation
1. Clone this repository.
2. Compile C++/CUDA modules in mmdet/ops by running the following command at each directory, e.g.
```bash
$ cd mmdet/ops/points_op
$ python3 setup.py build_ext --inplace
```
3. Setup following Environment variables, you may add them to ~/.bashrc:
```bash
export NUMBAPRO_CUDA_DRIVER=/usr/lib/x86_64-linux-gnu/libcuda.so
export NUMBAPRO_NVVM=/usr/local/cuda/nvvm/lib64/libnvvm.so
export NUMBAPRO_LIBDEVICE=/usr/local/cuda/nvvm/libdevice
export LD_LIBRARY_PATH=/home/billyhe/anaconda3/lib/python3.7/site-packages/spconv;
```

# Data Preparation
1. Download the 3D KITTI detection dataset from [here](http://www.cvlibs.net/datasets/kitti/eval_object.php?obj_benchmark=3d). Data to download include:
    * Velodyne point clouds (29 GB): input data to VoxelNet
    * Training labels of object data set (5 MB): input label to VoxelNet
    * Camera calibration matrices of object data set (16 MB): for visualization of predictions
    * Left color images of object data set (12 GB): for visualization of predictions

2. Create cropped point cloud and sample pool for data augmentation, please refer to [SECOND](https://github.com/traveller59/second.pytorch).
```bash
$ python3 tools/create_data.py
```

3. Split the training set into training and validation set according to the protocol [here](https://xiaozhichen.github.io/files/mv3d/imagesets.tar.gz).
```plain
└── DATA_DIR
       ├── training   <-- training data
       |   ├── image_2
       |   ├── label_2
       |   ├── velodyne
       |   └── velodyne_reduced
       └── testing  <--- testing data
       |   ├── image_2
       |   ├── label_2
       |   ├── velodyne
       |   └── velodyne_reduced
```

# Pretrained Model
You can download the pretrained model [here](https://drive.google.com/file/d/1WJnJDMOeNKszdZH3P077wKXcoty7XOUb/view?usp=sharing), 
which is trained on the train split (3712 samples) and evaluated on the val split (3769 samples) and test split (7518 samples). 
The performance (using 40 recall poisitions) on validation set is as follows:
```
Car  AP@0.70, 0.70, 0.70:
bbox AP:99.12, 96.09, 93.61
bev  AP:96.55, 92.79, 90.32
3d   AP:93.13, 84.54, 81.71
```
# Train
To train the SA-SSD with single GPU, run the following command:
```
cd mmdet/tools
python3 train.py ../configs/car_cfg.py
```
To train the SA-SSD with multiple GPUs, run the following command:
```
bash dist_train.sh
```
# Eval
To evaluate the model, run the following command:
```
cd mmdet/tools
python3 test.py ../configs/car_cfg.py ../saved_model_vehicle/epoch_50.pth
```
## Citation
If you find this work useful in your research, please consider cite:
```
@inproceedings{he2020sassd,
title={Structure Aware Single-stage 3D Object Detection from Point Cloud},
author={He, Chenhang and Zeng, Hui and Huang, Jianqiang and Hua, Xian-Sheng and Zhang, Lei},
  booktitle={Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition},
  year={2020}
}
```

## Acknowledgement
The code is devloped based on mmdetection, some part of codes are borrowed from SECOND and PointRCNN.
* [mmdetection](https://github.com/open-mmlab/mmdetection) 
* [mmcv](https://github.com/open-mmlab/mmcv)
* [second.pytorch](https://github.com/traveller59/second.pytorch)
* [PointRCNN](https://github.com/sshaoshuai/PointRCNN)


