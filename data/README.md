# supreme-CLIP Dataset 
 The Unsplash Dataset is made up of over 250,000+ contributing global photographers and data sourced from hundreds of millions of searches across a nearly unlimited number of uses and contexts. Due to the breadth of intent and semantics contained within the Unsplash dataset, it enables new opportunities for research and learning.
 
 Here in this `/data` directory, we are dowloading the dataset which is in `.tsv` extension, having links to download all images with thier corresponding tags and description.
 
 ## Libraries and Modules Used
 
`import pandas as pd`- pandas is a software library written for the Python programming language for data manipulation and analysis. 
 
`import glob` returns a possibly-empty list of path names that match pathname, which must be a string containing a path specification. pathname can be either absolute (like /usr/src/Python-1.5/Makefile) or relative (like ../../Tools/*/*.gif),

`from torchvision.datasets.utils import download_url` This library is part of the PyTorch project. PyTorch is an open source machine learning framework. We need this framework for downloading the unsplash dataset

`import urllib.request` Urllib module is the URL handling module for python. It is used to fetch URLs (Uniform Resource Locators). urllib.request is used for opening and reading URLs.

`from PIL import Image` Pillow or Python Imaging Library (expansion of PIL) is the de facto image processing package for Python language. It incorporates lightweight image processing tools that aids in editing, creating and saving images

`import requests` Requests library is one of the integral part of Python for making HTTP requests to a specified URL.When one makes a request to a URI, it returns a response. Python requests provides inbuilt functionalities for managing both the request and response.

`import random` Python has a built-in module that can help you choose random numbers within a range.

`import time` 'Time' module defined in Python allows us to handle various operations regarding time, its conversions and representations.


## Directory Tree
```$ tree
supreme-CLIP
├── data
│   ├── README.md
│   ├── data.json
│   ├── dataset_CLIP.pynb
│   └── embed_CLIP.pynb
│  
└── README.md

2 directories, 4 files
```

# Cell-wise description of Colab Notebook
## Cell #1
Here we have imported all the possibly required modules and libraries for this project.
```import pandas as pd
import glob
from torchvision.datasets.utils import download_url
import urllib.request
from PIL import Image
import requests
import random
import time
```
## Cell #2
Here we downloaded the zipped unsplash datasetfrom its given url by using `download_url` module in Pytorch's `torchvision.datasets.utils`. 
```dataset_url = "https://unsplash.com/data/lite/latest" 
download_url(dataset_url, '.')
```

## Cell #3
Here we unzipped the dataset `/latest` to the "content" directory in colab.
```
!unzip '/content/latest' -d '/content'
```

## Cell #4
Now, we are loading the dataset to the pandas data frame.
First we used glob.glob to return a list of path names that match pathname of `.tsv` directories in dataset.Then used read.csv to read the `.tsv` files in dataset
```path = './'
documents = ['photos', 'keywords']. 
datasets = {}

for doc in documents:
  files = glob.glob(path + doc + ".tsv*"). // used glob.glob to return a list of path names that match pathname of `.tsv` directories in dataset.

  subsets = []
  for filename in files:
    df = pd.read_csv(filename, sep='\t', header=0) // used read.csv to read the `.tsv` files in dataset
    subsets.append(df)

  datasets[doc] = pd.concat(subsets, axis=0, ignore_index=True). 
```
## Cell #5 to #9
These cells are to explore the datasets and to display its key features to understand them better.

## Cell #10
Extracting 10 photo_image_url , photo_description and photo_id from the "photos" dataset randomly from range(0,25000) using 'for' loop.
```url = []
photodesc = []
photoID = []
for i in range(0,10):
   j = random.choice(range(0,24999))  
   url.append(datasets['photos'].photo_image_url[j])
   photodesc.append(datasets['photos'].photo_description[j])
   photoID.append(datasets['photos'].photo_id[j])
```

## Cell #11
### Here we are extracting the tags from the "keyword" attribute of "keywords".
Using photo_id attribute in "keywords" datasets and the extracted photo_id from "photos" dataset i.e the photoID list to extract the keyword (tag) from the "keywords" dataset in a dictionary-list "photoID_tag". While exploring the datasets['keywords'] we notice that the same photo_id's are grouped together, hence we can exploit that fact while searching. The index() helps us find the index of the first occurrence of photoID[i], now since we know the same photo_ids are grouped together we exploit that fact by iterating through and incrementing index "ind" and appending "datasets['keywords'].keyword[ind]" to phtoID_tag (list which stores the tags i.e keyword of the 10 images) untill either the
"ind" goes out of bounds from the length of list "key_photoid" or "key_photoID[ind]" becomes not equal to "photoID[i]"
```key_photoID = list(datasets['keywords'].photo_id)
photoID_tag = {new_list: [] for new_list in range(10)}
for i in range(0,10):
    ind = key_photoID.index(photoID[i])
    while(ind!=len(key_photoID)-1 and key_photoID[ind]==photoID[i]):
      photoID_tag[i].append(datasets['keywords'].keyword[ind])
      ind =ind + 1
```
## Cell #12
This is to view our extracted photoID_tag dictionary, which is basically a collection of tags (here keyword) extracted from datsets['keywords'].keyword

## Cell #13
The final cell in this colab does the following :
 * We have timed the section to measure its execution time, any alternatives to optimize the runtime would be highly appreciated. (Right now it takes about 9 seconds, which is decent but can be improved on)
 * Display the 10 randomly selected Images(refer Cell #10) with its description, tags and no. of tags of each image. This is done with help of Python Request Library `urllib.request.urlretrieve(url[i],urlname)  ` to get the urlname then using pillow library we'll display images along with thier tags and description extracted earlier in cell #9.
 * With the help of requests library in python we are also able to check the response status code to determine whether the link is valid or not. 

```start = time.process_time() 
response = []
valid_url_count = 0
invalid_url_count = 0
for i in range(0,10): 
  response = requests.get(url[i])   
  urlname = "sample"+str(i)+".jpeg" 
  urllib.request.urlretrieve(url[i],urlname)  
  im = Image.open(urlname)
  im= im.resize((800,600),Image.ANTIALIAS)
  display(im)   
  print("Photo description:",photodesc[i])
  print("Tag:",photoID_tag[i])
  print("No. of Tags:",len(photoID_tag[i]))
  print('\n')
  if response.status_code == 200:
    valid_url_count=valid_url_count+1
  elif response.status_code == 404:
    invalid_url_count= invalid_url_count+1
elapsed = (time.process_time() - start)
print("Total Valid Url : ",valid_url_count)
print("Total Invalid Url : ",invalid_url_count)
print("Program Executed in "+ str(elapsed))
```
We'll get the number of valid and Unvalid URLs along with execution time :<img width="1439" alt="Screenshot 2021-10-02 at 11 55 16 AM" src="https://user-images.githubusercontent.com/77538183/135706090-18b5ec46-c3f3-4fff-9765-80bf6f86a538.png">

# Download the data.json file from [here](https://drive.google.com/file/d/1EvRZNlS0d41ngcSv1wxLSJCdZV27wzf2/view?usp=sharing) or do the following:
To generate the json file from the colab notebook embed_CLIP.ipynb
run code sections from "Downloading the data.p file from Gdrive" heading and run all the code blocks under it sequentially, then u will find data.json file in your colab root directory, then download it manually.
