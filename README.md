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

## day 3

### 3.1 学习目标

- ImageNet预训练模型
- CNN模型

### 3.2 ImageNet18

复用ImageNet18，构建模型

```python
class SVHN_Model2(nn.Module):
    def __init__(self):
        super(SVHN_Model1, self).__init__()
                
        model_conv = models.resnet18(pretrained=True)
        model_conv.avgpool = nn.AdaptiveAvgPool2d(1)
        model_conv = nn.Sequential(*list(model_conv.children())[:-1])
        self.cnn = model_conv
        
        self.fc1 = nn.Linear(512, 11)
        self.fc2 = nn.Linear(512, 11)
        self.fc3 = nn.Linear(512, 11)
        self.fc4 = nn.Linear(512, 11)
        self.fc5 = nn.Linear(512, 11)
    
    def forward(self, img):        
        feat = self.cnn(img)
        # print(feat.shape)
        feat = feat.view(feat.shape[0], -1)
        c1 = self.fc1(feat)
        c2 = self.fc2(feat)
        c3 = self.fc3(feat)
        c4 = self.fc4(feat)
        c5 = self.fc5(feat)
        return c1, c2, c3, c4, c5
```

> 注：ImageNet18()输入的图片size为(224,224)，但不知到这里为什么用(120,60)

跑了10个epoch，比baseline多使用了一些数据增强的方法，验证集上的结果比提交的成绩要高7%

```python
Epoch: 0, Train loss: 4.0019889717102055 	 Val loss: 3.6224123163223267
Val Acc 0.3282
Epoch: 1, Train loss: 2.5506652064323427 	 Val loss: 3.1023997325897215
Val Acc 0.4089
Epoch: 2, Train loss: 2.1469370821317035 	 Val loss: 2.946799901485443
Val Acc 0.4513
Epoch: 3, Train loss: 1.9218358896573384 	 Val loss: 2.760008645534515
Val Acc 0.4757
Epoch: 4, Train loss: 1.7602460599740346 	 Val loss: 2.6176762866973875
Val Acc 0.4987
Epoch: 5, Train loss: 1.6631318992773692 	 Val loss: 2.627127679824829
Val Acc 0.5059
Epoch: 6, Train loss: 1.5585278493563335 	 Val loss: 2.7410025124549864
Val Acc 0.5034
Epoch: 7, Train loss: 1.4755264279842377 	 Val loss: 2.6916711211204527
Val Acc 0.5079
Epoch: 8, Train loss: 1.4116250272591908 	 Val loss: 2.4272326512336733
Val Acc 0.5481
Epoch: 9, Train loss: 1.3362294898033142 	 Val loss: 2.4245066576004026
Val Acc 0.5441
```




### 3.3 CNN模型

使用CNN模型，这里用了两次卷积，两次池化

```python
import torch
torch.manual_seed(0)
torch.backends.cudnn.deterministic = False
torch.backends.cudnn.benchmark = True

# 定义模型
class SVHN_Model1(nn.Module):
    def __init__(self):
        super(SVHN_Model1, self).__init__()
        # CNN提取特征模块
        self.cnn = nn.Sequential(
            nn.Conv2d(3, 16, kernel_size=(3, 3), stride=(2, 2)),
            nn.ReLU(),  
            nn.MaxPool2d(2),
            nn.Conv2d(16, 32, kernel_size=(3, 3), stride=(2, 2)),
            nn.ReLU(), 
            nn.MaxPool2d(2),
        )
        # 
        self.fc1 = nn.Linear(32*3*7, 11)
        self.fc2 = nn.Linear(32*3*7, 11)
        self.fc3 = nn.Linear(32*3*7, 11)
        self.fc4 = nn.Linear(32*3*7, 11)
        self.fc5 = nn.Linear(32*3*7, 11)
        self.fc6 = nn.Linear(32*3*7, 11)
    
    def forward(self, img):        
        feat = self.cnn(img)
        feat = feat.view(feat.shape[0], -1)
        c1 = self.fc1(feat)
        c2 = self.fc2(feat)
        c3 = self.fc3(feat)
        c4 = self.fc4(feat)
        c5 = self.fc5(feat)
        c6 = self.fc6(feat)
        return c1, c2, c3, c4, c5, c6
```

> 目前使用这个CNN模型，训练结果也太低了，应该是网络的问题，正在审查中...

