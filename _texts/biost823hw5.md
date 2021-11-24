---
layout: post
title: "Explainable Deep Learning"
date: "2021-11-15"
author: Junrong Lin
tag: 
---
Deep learning has become one of the essential parts in data science. It demonstrate tremendous performance in image classification generation and detection. To practice Deep learning with [pytorch](https://pytorch.org/), I'm going to using [A insect dataset](https://people.duke.edu/~ccc14/insects.zip). The training result is explained by [SHapley Additive exPlanations](https://github.com/slundberg/shap).

### **0. Setup**  
Dependancies we need is listed as following:
```python
import torch
from torch import nn, optim
from torch.utils.data import DataLoader
import torchvision.transforms as transforms
from torchvision.datasets import ImageFolder
import torchvision.models as models
import torch.nn.functional as F
from ignite.engine import Events, create_supervised_trainer, create_supervised_evaluator
from ignite.metrics import Accuracy, Loss
import numpy as np
import shap
```

### **1. Date preprocess**  
Before define and train the CNN model, raw image need to be transformed into Tensor data type in Pytorch and do some modifications. Function `ImageFolder` is used for reading local image data.

```python
transform = transforms.Compose([
    transforms.Resize(256),
    transforms.CenterCrop(256),
    transforms.ToTensor(),
    transforms.Normalize(mean=[0.485, 0.456, 0.406], std=[0.229, 0.224, 0.225]),
])
train_data = ImageFolder("insects\\train", transform)
test_data = ImageFolder("insects\\test", transform)
idx_to_class = {0:"beetles", 1:"cockroach", 2:"dragonflies"}
```

### **2. build CNN model** 
My neural network is defined as following, two convolution layers followed by a linear stack.
```python
class CNN(nn.Module):
    def __init__(self):
        super().__init__()
        self.convlayer1 = nn.Sequential(
            nn.Conv2d(3, 32, 3, padding=1),
            nn.ReLU(),
            nn.MaxPool2d(4)
        )
        
        self.convlayer2 = nn.Sequential(
            nn.Conv2d(32, 64, 3, padding=1),
            nn.ReLU(),
            nn.MaxPool2d(4)
        )
        self.LR = nn.Sequential(
            nn.Linear(64*16*16, 64),
            nn.ReLU(),
            nn.Linear(64, 10),
            nn.LogSoftmax(dim=1)
        )
        
    def forward(self, x):
        x = self.convlayer1(x)
        x = self.convlayer2(x)
        x = x.view(x.size(0), -1)
        logits = self.LR(x)
        
        return logits
```

### **3. model training** 
`pytorch-Ignite` is used to make the code clearer
```python
model = CNN().cuda()

optimizer = optimizer = optim.Adam(model.parameters(), lr=1e-3)
criterion = nn.NLLLoss()
metrics = {'accuracy': Accuracy(), "nll": Loss(criterion)}

trainer = create_supervised_trainer(model, optimizer, criterion, device="cuda")
evaluator = create_supervised_evaluator(model, metrics, device="cuda")

train_loader = DataLoader(train_data, batch_size=64, shuffle=True, num_workers=2)
test_loader = DataLoader(test_data, batch_size=64, shuffle=False, num_workers=2)

@trainer.on(Events.EPOCH_COMPLETED)
def log_training_results(trainer):
    evaluator.run(train_loader)
    metrics = evaluator.state.metrics
    print(f"Training Results - Epoch: {trainer.state.epoch}  Avg accuracy: {metrics['accuracy']:.3f} Avg loss: {metrics['nll']:.3f}")

@trainer.on(Events.COMPLETED)
def log_test_results(trainer):
    evaluator.run(test_loader)
    metrics = evaluator.state.metrics
    print(f"TEST Results - Avg accuracy: {metrics['accuracy']:.3f}")

trainer.run(train_loader, max_epochs=EPOCHS)
```
### **4. shap deep explainer**
The plot below shows the explanations for each class on two predictions. Note that the explanations are ordered for the classes 0-9 going left to right along the rows.
![shap.png](/assets/biost823/shap.png)
