# GeoGuessr.ai Coach - A Modern Approach to GeoGuessr AI

<p align="center">
  <a href="https://geoguessr.ai" target="_blank">
    <img src="https://geoguessr.ai/og-image.webp" alt="GeoGuessr.ai Banner - The AI Coach for GeoGuessr">
  </a>
</p>

<p align="center">
  This repository serves as the technical companion to <a href="https://geoguessr.ai" target="_blank"><strong>GeoGuessr.ai</strong></a>, our modern AI Coach for GeoGuessr. It is forked from the foundational <strong><a href="https://github.com/Stelath/geoguessr-ai">geoguessr-ai</a></strong> project by Stelath, and this document has been updated to showcase the evolution of this technology.
</p>

---

## The Evolution: From a DIY Model to a Powerful AI Coach

The original `geoguessr-ai` project (preserved in full below) was a fantastic exploration into building a custom CNN model to identify one of five US cities. It represents a classic, hands-on approach to machine learning and was an inspiration for many.

However, the AI landscape has evolved dramatically. The challenge is no longer just about building a model that can *guess*, but about creating a tool that can ***teach***.

This is the philosophy behind **[GeoGuessr.ai](https://geoguessr.ai)**. We've shifted the focus from "solving" to "coaching." Instead of training a limited model from scratch, we leverage advanced **prompt engineering** on state-of-the-art vision models (like Google's Gemini 2.5 Pro and OpenAI's o3) to teach them how to think like a world-champion player across the **entire globe**.

Our AI Coach can:
*   Identify hundreds of subtle "meta clues" (like specific bollards, road lines, and car parts).
*   Explain its reasoning step-by-step, turning every analysis into a lesson.
*   Help you train your own brain to spot these patterns.

### Try the Live AI Coach Now

This new, coaching-focused philosophy is live and accessible to everyone.

**➡️ [Experience the AI Coach at GeoGuessr.ai](https://geoguessr.ai)**
*(3 free analyses per day, no signup required)*

---

## Original `geoguessr-ai` Project README (A Nod to the Foundation)

The following sections are the original, preserved `README.md` from Stelath's project. It's a valuable look into the foundational challenges and methodologies that paved the way for modern tools. We are immensely grateful for this foundational open-source contribution.

---

## Project Overview

### Creating a Dataset

In order to train the model I first had to create a reasonably large dataset of Google Street View images to train it on. To do this I wrote a python script ([get_images.py](https://github.com/Stelath/geoguessr-ai/blob/main/get_images.py "get_images.py")) to download a large set of photographs from 5 cities in the US from Google Street View API. In order to download images from the Google Street View API latitude and longitude coordinates were needed, to solve this I utilized an address book from [Open Addresses](https://openaddresses.io/) to get the latitude and longitude data of random street addresses for each of the 5 cities.

### Formatting the Images

Before training the model I decided to format the targets in order to create a way for the AI to better guess the location by turning the GPS coordinates into multi class targets, through formatting each number in the coordinate as a one hot array with numbers ranging one through ten.

### Training the Model

After using a couple different model architectures I settled on a wideresnet with 50 layers which gave comparable results to a wideresnet with 100 layers but took far less GPU memory. The model was trained on a dataset of 50,000 images and had the best performance 20 epochs in and then began to slowly overfit.

### How Could it be Improved?

Using a custom model that is better suited to guessing locations rather than just image classification. Adding far more layers so the model can pick up more complexity however this would require a larger GPU. Arguably the best performance improver - and something that would more accurately follow the GeoGuessr - idea would be to train a 3D CNN on an array of images from the location giving the model far more data to work with and make accurate predictions.

### Takeaway

While the model is by no means perfect, it is suprisingly accurate given the limited input it recives. This interestingly enough reveals that while many would regard American cities as similar there are clearly significant differences in their landscapes so much so that the AI was able to take advantage of them to at least correctly predict the city it was in the majority of hte time.

## Instructions

### Run Pretrained Model

In order to run a pretrained model you can download it from Google Drive: [![Open In Drive](https://img.shields.io/badge/Google%20Drive-5383ec?style=flat&logo=googledrive&logoColor=5383ec&label=%E2%80%8B)](https://drive.google.com/file/d/1VJpeLJp6jC8IUfKy6cAtZ9WZcX1TTutW/view?usp=sharing) or you can use the Google Colab: [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/Stelath/geoguessr-ai/blob/main/notebook/GeoGuessr_AI_Demo.ipynb).

### Train a Model
You can use the [get_images.py](https://github.com/Stelath/geoguessr-ai/blob/main/get_images.py "get_images.py") script to download a database of images through Google Cloud, they allow 28,500 free Google Street View API calls each month so keep that in mind (anything more and you will be charged $0.007 per image), you will also have to set up a Google Cloud account. You can also download the database of Google Street View images I created [here](https://www.kaggle.com/stelath/city-street-view-dataset).

After you have a database of images running the [dataset_builder_multi_label.py](https://github.com/Stelath/geoguessr-ai/blob/main/dataset_builder_multi_label.py) script will preprocess all of the images, then running [main.py](https://github.com/Stelath/geoguessr-ai/blob/main/main.py) will begin training the model.

Here is a set of commands that would be used to train a model on 25,000 images (keep in mind you will need a cities folder containing `.geojson` files from [Open Addresses](https://openaddresses.io/)):
```
python -m get_images --cities cities/ --output images/ --icount 25000 --key (YOUR GSV API KEY HERE)
python -m dataset_builder_multi_label --file images/picture_coords.csv --images images/ --output geoguessr_dataset/
python -m main geoguessr_dataset/ -a wide_resnet50_2 -b 16 --lr 0.0001 -j 6 --checkpoint-step 1
```
