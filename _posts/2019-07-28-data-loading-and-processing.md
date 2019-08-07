---
layout: post
current: post
cover: assets/images/cover-img/c15.jpg
navigation: True
title: Data Loading and Processing with Pytorch
date: 2019-07-30
comments: true
---


**This post serves as a note after reading this <a href="https://pytorch.org/tutorials/beginner/data_loading_tutorial.html" target="_blank"  style="color: #0074D9;">Pytorch official tutorial</a>.**

------------------

The training of a model comes with a lot of work in data preparation, thus knowing how to load data properly and how to process the data before ready to train is a vital step. Below is a typical way of manipulating data and I am using facial pose dataset as an example. Basically, 68 different landmark points are annotated for each face.

<img src="assets/images/Data_L_P/L_P-1.jpg" alt="L_P-1" style="width: 50%;">

<br><br>
**Data Loading**

To begin, Download the dataset from <a href="https://download.pytorch.org/tutorial/faces.zip">here</a> so that the images are in a directory named ‘data/faces/’.

The first thing to do is to import all the packages that we need for the task:
```python
from __future__ import print_function, division
%matplotlib inline
import os
import torch
from torchvision import untils, transforms
import pandas ad pd
import numpy as np
import matplotlib.pyplot as plt
from torch.utils.data import Dataset, DataLoader
from skimage import io, transform
```

**Get dataframe fron .csv**

Once we have them imported, we can start to read the ```.csv``` file to check what's in the dataframe:
```python
landmarks_frame = pd.read_csv('data/faces/face_landmarks.csv')   

# let's say we want to look at the $$65^th$$ data 
n = 65
img_name = landmarks_frame[n,0]

# reshape landmarks to (x, y) form
landmarks = landmarks_frame[n, 1:].as_matrix()
landmarks = landmarks.astyope('float').reshape(-1, 2)
```
<br><br>
**Visualization**

Then, having the dataframe and reshaped data, we can visualize the image to get a sense of what does it look like:
```python
def show_landmarks(image, landmarks):
    plt.imshow(image)
    plt.scatter(landmarks[:, 0], landmarks[:, 1], s=10, c='r', marker='.')

plt.figure()
show_landmarks(io.imread(os.path.join('data/faces/', img_name)), landmarks)
plt.show()
```
<img src="assets/images/Data_L_P/L_P-2.jpg" alt="L_P-2" style="width: 60%;">

**Customized dataset**

Now, we want to create a dataset class to represent our data.
```python
class FaceLandmarkData(Dataset):
    def __int__(self, csv_path, root_dir, transform=None):
        self.landmarks_frame = pd.read_csv(csv_path)
        self.root_dir = root_dir
        self.transform = transform
    
    def __len__(self ):
        return len(self.landmarks_frame)

    def __getitem__(self, idx):
        image_path = os.path.join(self.root_dir, self.landmarks_frame.iloc[idx, 0])
        image = io.imread(image_path)
        landmarks = landmarks_frame.iloc[idx, 1:].as_matrix()
        landarks = landmarks.astype('float').reshape(-1, 2)
        sample = {'image': image, 'landmarks': landmarks}
        if transform:
            sample = self.transform(sample)

        return sample
```

Notice that ```torch.utils.data.Dataset``` is an abstract class representing a dataset. Your custom dataset should inherit ```Dataset``` and override the following methods:
- ```__len__``` so that ```len(dataset)``` returns the size of the dataset.
- ```__getitem__``` to support the indexing such that ```dataset[i]``` can be used to get ith sample

We will read the csv in __init__ but leave the reading of images to __getitem__. This is memory efficient because all the images are not stored in the memory at once but read as required. Sample of our dataset will be a ```dict {'image': image, 'landmarks': landmarks}```. The dataset will take an optional argument transform so that any required processing can be applied on the sample. I will talk in details about ```transform``` later in this article. 

<br><br>
**Play with dataset**

Now that we have created our own dataset, let's see if we can play around for a bit to get familiar with it:
```python
face_data = FaceLandmarkData('data/faces/face_landmarks.csv', 'data/faces/')

plt.figure()
for i in range(len(face_data)):
    sample = face_data[i]

    image = sample['image']
    landmarks = sample['landmarks]

    ax = plt.subplot(1,4,i+1)
    plt.tight_layout()
    ax.set_title('Sample #{}'.format(i))
    ax.axis('off')
    show_landmarks(**sample)

    if i == 3:
        plt.show()
        break
```
<img src="assets/images/Data_L_P/L_P-3.jpg" alt="L_P-3" style="width: 80%;">

<br><br>
**Transforms**

One issue we can see from the above is that the samples are not of the same size. Most neural networks expect the images of a fixed size. Therefore, we will need to write some prepocessing code. Let’s create three transforms:
- ```Rescale```: to scale the image
- ```RandomCrop```: to crop from image randomly. This is data augmentation
- ```ToTensor```: to convert the numpy images to torch images (we need to swap axes)

We will write them as callable classes instead of simple functions so that parameters of the transform need not be passed everytime it’s called. For this, we just need to implement ```__call__``` method and if required, ```__init__``` method. We can then use a transform like this:

```python
class Rescale():
    def __init__(self, output_size):
        assert isinstance(output_size, (int, tuple))
        self.output_size = output_size
        
    def __call__(self, sample):
        img, landmarks = sample['image'], sample['landmarks']
        h, w = img.shape[2:]
        if isinstance(self.output_size, int):
            if(h > w):
                new_h, new_w = self.output_size * h/w, self.output_size
            else:
                new_h, new_w = self.output_size, self.output_size * w/h
        else:
            new_h, new_w = self.output_size
        
        new_h,  = (int)new_h, (int)new_w
        img = transform.resize(img, (new_h, new_w))
        landmarks = landmarks * [new_w/w, new_h/h]

        return {'image': img, 'landmarks': landmarks}

class RandomCrop():
    def __init__(self, output_size):
        assert isinstance(self.output_size, (int, tuple))
        if isinstance(output_size, int):
            self.output_size = (output_size, output_size)
        else:
            assert len(output_size) == 2
            self.output_size = output_size

    def __call__(self, sample):
        img, landmarks = sample['image'], sample['landmarks']
        h, w = img.shape[:2]
        new_h, new_w = self.output_size
        top, left = np.random.randint(0, h-new_h), np.random.randint(0, w-new_w)

        img = img[top:top+new_h, left:left+new_w]
        landmarks = landmarks - [left, top]

        return {'image': img, 'landmarks': landmarks}

class ToTensor():
    __call__(self, sample):
        img, landmarks = sample['image'], sample['landmarks']

        img = img.transpose((2,0,1))
        
        return {'image': torch.from_numpy(img), 'landmarks': torch.from_numpy(landmarks)}
```

There is also a way to combine several transforms by ```torhcvision.transforms.Compose```. Let's say we want to show ```Rescale```, ```Crop``` and ```Rescale+Crop``` sperately.
```python
scale = Rescale(256)
crop = RandomCrop(128)
composed = transforms.Compose([
    Rescale(256),
    RandomeCrop(224)
])

plt.figure()
sample = face_data[65]

for i, trfms in enumerate([scale, crop, composed]):
    transformed_sample = trfms(sample)

    ax = plt.subplot(1, 3, i + 1)
    plt.tight_layout()
    ax.set_title(type(tsfrm).__name__)
    show_landmarks(**transformed_sample)
plt.show()
```
<img src="assets/images/Data_L_P/L_P-4.jpg" alt="L_P-4" style="width: 80%;">

<br><br>
**Iterating through the dataset**

Now it's time to leverage the ```transform``` attribute in datasetset initiation. 
```python
transformed_data = FaceLandmarkDataset('data/faces/face_landmarks.csv',
                                    'data/faces', 
                                    transform=transforms.Compose([
                                        Rescale(256),
                                        RandomeCrop(224),
                                        ToTensor()
                                    ]))
```

However, ther is a downside of using ```for``` loop, that is, we are missing out:
- Batching the data
- Shuffling the data
- Load the data in parallel using ```multiprocessing``` workers.

fortunitely, ```torch.utils.data.DataLoader``` is an iterator which provides all these features. One parameter of interest is ```collate_fn```. You can specify how exactly the samples need to be batched using ```collate_fn```. However, default collate should work fine for most use cases.

```python
dataloader = DataLoader(transformed_dataset, batch_size=4,
                        shuffle=True, num_workers=4)


# Helper function to show a batch
def show_landmarks_batch(sample_batched):
    """Show image with landmarks for a batch of samples."""
    images_batch, landmarks_batch = \
            sample_batched['image'], sample_batched['landmarks']
    batch_size = len(images_batch)
    im_size = images_batch.size(2)
    grid_border_size = 2

    grid = utils.make_grid(images_batch)
    plt.imshow(grid.numpy().transpose((1, 2, 0)))

    for i in range(batch_size):
        plt.scatter(landmarks_batch[i, :, 0].numpy() + i * im_size + (i + 1) * grid_border_size,
                    landmarks_batch[i, :, 1].numpy() + grid_border_size,
                    s=10, marker='.', c='r')

        plt.title('Batch from dataloader')

for i_batch, sample_batched in enumerate(dataloader):
    print(i_batch, sample_batched['image'].size(),
          sample_batched['landmarks'].size())

    # observe 4th batch and stop.
    if i_batch == 3:
        plt.figure()
        show_landmarks_batch(sample_batched)
        plt.axis('off')
        plt.ioff()
        plt.show()
        break
```

<br><br>
**torchvision**

 we have seen how to write and use datasets, transforms and dataloader. ```torchvision``` package provides some common datasets and transforms. You might not even have to write custom classes. One of the more generic datasets available in ```torchvision``` is ImageFolder. 

 ```python
import torch
from torchvision import transforms, datasets

data_transform = transforms.Compose([
        transforms.RandomSizedCrop(224),
        transforms.RandomHorizontalFlip(),
        transforms.ToTensor(),
        transforms.Normalize(mean=[0.485, 0.456, 0.406],
                             std=[0.229, 0.224, 0.225])
    ])
hymenoptera_dataset = datasets.ImageFolder(root='data/',
                                           transform=data_transform)

dataset_loader = torch.utils.data.DataLoader(hymenoptera_dataset,
                                             batch_size=4, shuffle=True,
                                             num_workers=4)
 ```
