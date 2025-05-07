---
layout: post
title:  "Ryan Challenge Problem"
date:   2024-1-17
categories: diary
published: false
---

My experience in a challenge problem.

<!-- more -->

Getting this challenge problem is a surprise. I have never thought about this problem before. I have never finetune a model before, so this is a good opportunity to learn something new. I have to read the paper and understand the problem first.

The model is totally a disaster. The misclassification rate is 0.99, almost as bad as a random guess.

First I changed the resolution from (1024, 1024) to (224, 224). This is a common practice in EfficientNet algorithm according to [the website](https://arxiv.org/pdf/1905.11946.pdf). The model is still bad.

Then I freeze most of the layers and only train the last layer. This comes from the idea of transfer learning, especially when the dataset is small. [this website](https://keras.io/examples/vision/image_classification_efficientnet_fine_tuning/).

The problem is, the learning rate is too high. Adjusting the learning rate dynamically is a good idea. I will try to use the ReduceLROnPlateau callback.

To speed up the training, I will use the with torch.no_grad() and torch.cuda.empty_cache() to reduce the memory usage. I also change the batch size to 64 to get a overall view of the dataset.

All of these efforts makes the model more accurate. The validation accuracy rate is elevated to 0.4.

The model is not at all overfitting. The accuracy needs to be improved. Therefore I retrained the model with all parameter unfreezed, with a very low learning rate. The accuracy is improved to 0.83.

The model is overfitting because of the overall training. I will use the early stopping callback to stop the training when the validation accuracy is not improved for 10 epochs.
