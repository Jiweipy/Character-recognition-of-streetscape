# Character-recognition-of-streetscape

## 1 Day1

### 1.1 学习目标

- 下载数据集
- 理解数据集
- 选定识别思路

### 1.2 学习过程

- 认识图片
- 了解json数据代表的含义

## 2 Day2

### 2.1 学习目标

- 数据扩增的方法
- OpenCV-Python库的应用
- pytorchd加载数据

### 2.2 数据扩增

**数据扩增目的：**扩展样本空间、缓解模型过拟合

**数据扩增方法：**一般会从图像颜色、尺寸、形态、空间和像素等角度进行变换

**OpenCV样例：（待补充）**

```python
import cv2
img = cv2.imread('cat.jpg')
# 转换为RGB模式
img1 = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)
# 转换为GRAY模式
img1 = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
```

### 2.3 torchvision

```python
transforms.CenterCrop       对图片中心进行裁剪      
transforms.ColorJitter      对图像颜色的对比度、饱和度和零度进行变换      
transforms.FiveCrop         对图像四个角和中心进行裁剪得到五分图像     
transforms.Grayscale        对图像进行灰度变换    
ransforms.Pad               使用固定值进行像素填充     
transforms.RandomAffine     随机仿射变换    
transforms.RandomCrop       随机区域裁剪     
transforms.RandomHorizontalFlip      随机水平翻转     
transforms.RandomRotation            随机旋转     
transforms.RandomVerticalFlip        随机垂直翻转 
```

> 字符的话需要注意旋转问题

### 2.4 Pytorch读取数据  

> Pytorch中数据是通过Dataset进行封装，并通过DataLoder进行并行读取

**示例代码**

```python
import os, sys, glob, shutil, json
import cv2

from PIL import Image
import numpy as np

import torch
from torch.utils.data.dataset import Dataset
import torchvision.transforms as transforms

class SVHNDataset(Dataset):
    def __init__(self, img_path, img_label, transform=None):
        self.img_path = img_path
        self.img_label = img_label 
        if transform is not None:
            self.transform = transform
        else:
            self.transform = None

    def __getitem__(self, index):
        img = Image.open(self.img_path[index]).convert('RGB')

        if self.transform is not None:
            img = self.transform(img)
        
        # 原始SVHN中类别10为数字0
        lbl = np.array(self.img_label[index], dtype=np.int)
        lbl = list(lbl)  + (5 - len(lbl)) * [10]
        
        return img, torch.from_numpy(np.array(lbl[:5]))

    def __len__(self):
        return len(self.img_path)

train_path = glob.glob('../input/train/*.png')
train_path.sort()
train_json = json.load(open('../input/train.json'))
train_label = [train_json[x]['label'] for x in train_json]

train_loader = torch.utils.data.DataLoader(
        SVHNDataset(train_path, train_label,
                   transforms.Compose([
                       transforms.Resize((64, 128)),
                       transforms.ColorJitter(0.3, 0.3, 0.2),
                       transforms.RandomRotation(5),
                       transforms.ToTensor(),
                       transforms.Normalize([0.485, 0.456, 0.406], [0.229, 0.224, 0.225])
            ])), 
    batch_size=10, # 每批样本个数
    shuffle=False, # 是否打乱顺序
    num_workers=0, # 读取的线程个数
)

for data in train_loader:
    break
    

"""Output:
	torch.Size([10, 3, 64, 128]), torch.Size([10, 6]
"""
```

> - num_workers=10 这个在ubuntu上可以运行，但是在我的mac上就得调成0，不知道为啥

