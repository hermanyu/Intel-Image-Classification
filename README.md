<h1 align='center'> Intel Image Classification Challenge: <br>
Computer Vision for Background and Landscape Images
</h1>

---

<br>

**Technical Note:** The models, checkpoints, and data sets exceed githubs upload and storage limits. The image data set can be found here: <a href='https://www.kaggle.com/datasets/puneet6060/intel-image-classification'> The Intel Image Classification dataset </a>.

<br>


## Problem Statement

This project is the result of a 5 hour "hack-a-thon" where the author takes on the Intel Image Classification challenge originally presented to <a href='https://datahack.analyticsvidhya.com/contest/practice-problem-intel-scene-classification-challe/'> Analytics Vidhya </a> in May of 2018. The objective was to build the most accurate image classification model to correctly classify between 6 classes of landscape/background images: buildings, forests, mountains, glaciers, seas, and streets within the specified time limit.

<br>

---

## Methodology

A rudimentary EDA is conducted. We find that certain images of "streets" and "buildings" are close enough that even a human could not reasonably answer the classification question correctly. 

We essentially implement the process outlined in Francois Chollet's "Deep Learning with Python" Chapter 5, but with our custom choice of layer sizes, units, and depth at each step. We build 3 Convolutional Neural Networks in total.

We define a **Convolutional stack** to be 2 successive convolutional layers and 1 max pooling layer. Using this terminology, the archictecture of the 3 models are given by:
1. A network with 4 Convolutional "stacks" and 2 hidden layers; no regularization.
2. A network with 4 Convolutional "stacks" and 2 hidden layers; a dropout layer is used for regularization and we use Image Augmentation to artificially generate more training data.
3. Use a pre-trained VGG19 model for the "convolutional base" a custom 2 hidden layer "top-half".

The 3rd model was built off the pre-trained VGG19 model. The original VGG architecture was created by Karen Simonyan and Andrew Zisserman in 2014 and their paper can be
 found <a href='https://arxiv.org/abs/1409.1556'> at this arXiv address </a>.
>

<br>

---

<br>

## Summary  of Results

### EDA Findings

During our exploratory data analysis, we discovered that certain images classified as "street" and "building" were hard to classify even from a human perspective. For example consider the following pair of images:

<center>
    <img src='./data/seg_train/seg_train/street/10019.jpg' width=200> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; <img src='./data/seg_train/seg_train/buildings/1001.jpg' width=200>
    </center>

One of the images is labeled as "street" while the other is labeled as "building". It is rather unclear which one should be which, even for a human. Thus we cannot reasonable expect a machine to be able to correctly classify these images either.

Given this insight, we suspect that there is an upperbound on the possible accuracy that can be achieved by any model and no machine can achieve 100% accuracy. We are thus lead to consider what a reasonable estimate for such a "theoretical limit" should be. We give a table of possible "maximum accuracies" parameterized by a value "lambda" which corresponds to the proportion of street/building images which are "indistinguishable".

|lambda|Maximum Accuracy|
|---|---|
|1 | 83.75\%|
|0.75 | 87.8 \% |
|0.5 | 91.2\% |
|0.25 | 95.9 \% |
|0 | 100 \% |


Our first model consisted of 4 convolutional stacks and 2 hidden layers, with a total 30,904,390
trainable weights/parameters. No regularization was used and the model trained for 17 epochs before being stopped because of asymptotic validation loss. It achieved a 82.6% on the validation set.

Our second model consisted of 4 convolutional stacks, a dropout layer, and 2 hidden layers (same number of trainable parameters). Image Augmenation was to combat overfitting and the model trained for 30 epochs before stopping. It achieved a 86.6% accuracy.

The model which achieved the greatest accuracy on the validation set was Model 3: the VGG19 convolutional base + custom top-half hidden layers. The convolutional base pretrained so the weights were frozen, resulting in only 4,228,038 trainable parameters. The model trained for 34 epochs before stopping, achieving a validation accuracy of approximately 87.6%.