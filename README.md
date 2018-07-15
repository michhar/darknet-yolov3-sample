# Darknet YOLO v3 Sample

Some instructions and an example of using the nice/fast ML program `darknet` for object detection with YOLO v3.

**WIP** and blog post coming soon.

## How to use this repository

1.  Clone the files from this repo as examples of a custom `darknet` sample (to be added to install repo)
2.  Install and build `darknet` (see [Setup](#setup)) and https://pjreddie.com/darknet/install/
3.  On Windows place the custom `experiment` folder into the build.... x64 folder or on MacOS place it in the base of the folder structure of the `darknet` repo to be accessed by `darknet.exe` or `darknet`, respectively.

## Sample results

By running the `calc_mAP.cmd` for the custom dataset (not great, but doing pretty good for now):

```
detections_count = 15664, unique_truth_count = 430
class_id = 0, name = helmet,     ap = 65.47 %
class_id = 1, name = no_helmet,          ap = 31.48 %
 for thresh = 0.25, precision = 0.70, recall = 0.31, F1-score = 0.43
 for thresh = 0.25, TP = 132, FP = 56, FN = 298, average IoU = 53.51 %

 mean average precision (mAP) = 0.484737, or 48.47 %
```

## Setup

### Windows Instructions for Training with Darknet (GPU version)

> TIP:  when moving any files from Windows to macOS, check for proper newlines (e.g. lack of `^M` characters in text files)

#### My System
* Windows 10
* NVIDIA GeForce GTX 1060 Graphics Card
* CUDA 9.1
* cuDNN 7.0

#### Steps

Setup - see https://github.com/AlexeyAB/darknet for details and download links
* MS Visual Studio 2017 (instructions say 2015, but 2017 worked well for me)
* OpenCV 3.4.0

1.  Built `Yolo_mark` for Windows according to instructions at https://github.com/AlexeyAB/Yolo_mark (clone and run)

    1.1 Make sure you have OpenCV 3.4.0 at it's at `C:\opencv_3.0\opencv` (Linux instructions on repo Readme)
  
    1.2 This will produce an executable at `C:\Users\<directory to solution>\Yolo_mark\x64\Release\yolo_mark.exe`
  
    1.3 Find files `opencv_world320.dll` and `opencv_ffmpeg320_64.dll` (or `opencv_world340.dll` and `opencv_ffmpeg340_64.dll`) in `C:\opencv_3.0\opencv\build\x64\vc14\bin` and put it near with `yolo_mark.exe`

2.  Label images with bouding boxes and classes according to the steps on `Yolo_mark` under "To use for labeling your custom images"
3.  Built `darknet` for Windows according to instructions at https://github.com/AlexeyAB/darknet#how-to-compile-on-windows

    3.1 Except, use VS 2017 with **Platform Toolset `Visual Studio 2015 (v140)`** (right-click solution -> Properties -> Configuration Properties (General))
    
    3.2 Make sure `cudnn.h` is in `C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v9.1\include` folder
    
    3.3 Make sure `cudnn.lib` is in `C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v9.1\lib\x64` folder
    > TIP:  when installing the NVIDIA CUDA Toolkit 9.1, you may need to do an Advanced/Custom install and "uncheck" Visual Studio Integration as this may cause install not to work properly

4.  Train a Tiny YOLO v3 model on custom images according to more instructions at https://github.com/AlexeyAB/darknet#how-to-train-tiny-yolo-to-detect-your-custom-objects

    4.1 May need to
    
        4.1.1 Place certain cuDNN libs into v9.1 CUDA directory (e.g. `cudnn64_7.dll` found in search after a cuDNN intall into `C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v9.1\bin`)
 
    4.2 Note, put your `minifig.data` (directs `darknet` on where to go for information and saving models) file in the `experiment` folder and use full paths to the `train.txt` and `minifig.names` (a file to indicate classes)
    
    4.3 Run with:
  
      `darknet.exe detector train experiment\minifig.data experiment\yolov3-tiny.cfg experiment\yolov3-tiny.conv.15`
      
    4.4 The final trained model with be in the `backup` folder.  I recommend giving it a good, unique name.
  
5.  Test with:
    
    `darknet.exe detector test experiment\minifig.data experiment\yolov3-tiny.cfg backup\yolov3-tiny_10000.weights data\test\IMG_4658.JPG`
  
### macOS Instructions for Training with Darknet (CPU)

#### My System
* macOS High Sierra 10.13.5
* MacBook Pro (15-inch, 2017)

#### Steps

1.  Build `Yolo_mark` for Linux according to instructions at https://github.com/AlexeyAB/Yolo_mark (clone and run)

2. Build `darknet` with opencv (mostly following instructions at https://pjreddie.com/darknet/install/):

    2.1  `brew install opencv@2`
    
    2.2  Clone `darknet` repo:  `git clone https://github.com/pjreddie/darknet`
    
    2.3  `cd darknet`
    
    2.4  In the `Makefile` update the opencv parameter to `OPENCV=1` (change any other pertinent parameters as well here)
    
    2.5  Run `make` in the base `darknet` directory

3. Label images with bouding boxes and classes according to the steps on `Yolo_mark` under "To use for labeling your custom images"

4. Train a Tiny YOLO v3 model on custom images according to more instructions at https://github.com/AlexeyAB/darknet#how-to-train-tiny-yolo-to-detect-your-custom-objects

    4.1 Note, put your `minifig.data` (directs `darknet` on where to go for information and saving models) file in the `experiment` folder and use full paths to the `train.txt` and `minifig.names` (a file to indicate classes)

    4.2 If doing transfer learning, place `stopbackward=1` in the `yolov3-tiny.cfg`

    4.3 Run with:

        ./darknet detector train experiment\minifig.data experiment\yolov3-tiny.cfg experiment\yolov3-tiny.conv.15
    
    4.4 The final trained model with be in the `backup` folder.
