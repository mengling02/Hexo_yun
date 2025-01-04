---
title: YOLOv8环境安装以及文件配置
date: 2025-01-02 21:45:59
tags: YOLO
type: 
---

# YOLOv8

## 环境安装

1. 下载yolov8代码

   ```
   git clone https://gitclone.com/github.com/ultralytics/ultralytics.git 
   ```

2. 进入ultralytics文件夹,安装yolov8

   ```
   pip install -e.
   ```

   

3. 安装conda软件，创建虚拟环境

   ```
   conda create -n yolov8 python=3.8
   ```

3. 激活虚拟环境

   ```
   conda activate yolov8
   ```

4. （可选）更改pip的源，下载速度会更快

   ```
   pip config set install.trusted-host mirrors.aliyun.com
   ```

5. （可选）安装必要的包

   ```
   pip install ultralytics
   ```

6. 下载训练模型

   ```
   https://github.com/ultralytics/assets/releases/download/v0.0.0/yolov8s.pt
   https://github.com/ultralytics/assets/releases/download/v0.0.0/yolov8n.pt
   ```

7. 验证（使用GPU需安装相应驱动）

   ```
   无显卡驱动
   yolo predict model=yolov8n.pt source='ultralytics/assets/bus.jpg' device=cpu
   
   有显卡驱动(看扩展的部分，安装gpu版本torch才能运行)
   yolo predict model=yolov8n.pt source='ultralytics/assets/bus.jpg' device=0
   
   ```

   

## ultralytics配置

1. 新建predict.py文件以及train.py文件，方便后续启动（省去敲命令行的步骤）

   ```python
   from ultralytics import YOLO
   
   yolo = YOLO("./yolov8n.pt",task="detect")
   
   result = yolo(source = "./ultralytics/assets",save=True,conf=0.25)#0:调用摄像头  screen：调用屏幕
   ```

   ```python
   from ultralytics import YOLO
   
   model = YOLO('yolov8n.pt')
   
   model.train(data='yolo-data.yaml')
   #workers：windows电脑设置为1
   #epochs训练50轮
   #yolo task=detect mode=train model=./yolov8n.pt data="yolo-data.yaml” workers=1 epochs=58 batch=16
   ```

   

2. 从 ultralytics-main/ultralytics/cfg 的目录中 copy一份 default.yam l文件，方便后续修改参数

   ```js
   yolo copy-cfg
   ```

3. 新建yolo-data.yaml文件 

   ```json
   # Ultralytics YOLO 🚀, AGPL-3.0 license
   # African-wildlife dataset by Ultralytics
   # Documentation: https://docs.ultralytics.com/datasets/detect/african-wildlife/
   # Example usage: yolo train data=african-wildlife.yaml
   # parent
   # ├── ultralytics
   # └── datasets
   #     └── african-wildlife  ← downloads here (100 MB)
   
   # Train/val/test sets as 1) dir: path/to/imgs, 2) file: path/to/imgs.txt, or 3) list: [path/to/imgs1, path/to/imgs2, ..]
   path: data # dataset root dir，只能在datasets目录下
   train: ./images/train # train images (relative to 'path') 1052 images
   val: ./images/val # val images (relative to 'path') 225 images
   test:  # test images (relative to 'path') 227 images
   
   # Classes
   names: #标签种类
     0: train
   #  1: ****
   ```

4. 数据文件夹结构

   ```
   datasets
   	├── data
   		 ├──images
   		 └──val
   	├── labels
   		 ├──images
   		 └──val
   	├──classes.txt
   ```

参考文档[YOLOv8 -Ultralytics YOLO 文档](https://docs.ultralytics.com/zh/models/yolov8/)