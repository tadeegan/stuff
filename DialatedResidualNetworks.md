# Paper Review Series: Dialated Residual Networks

Link: https://arxiv.org/abs/1705.09914

### Intro

I read a lot of ML papers. One of the frustrating things about the ML community is that you can feel quite isolated from others.
There is no public comments section on arxiv! Its imposible to know if a specific paper can actually deliver results without some
unexpected/unmentioned drawbacks. In this series I hope to give back insigts after reading AND implementing research papers. 
I hope to give actional real world feedback and feasibility for these papers. Note that becuase of my employer I likely will 
not be able to share code most of the time.

### Abstract

> Convolutional networks for image classification progressively reduce resolution until the image is represented by
tiny feature maps in which the spatial structure of the scene
is no longer discernible. Such loss of spatial acuity can limit
image classification accuracy and complicate the transfer
of the model to downstream applications that require detailed scene understanding. These problems can be alleviated by dilation, which increases the resolution of output
feature maps without reducing the receptive field of individual neurons. We show that dilated residual networks
(DRNs) outperform their non-dilated counterparts in image classification without increasing the model’s depth or
complexity. We then study gridding artifacts introduced by
dilation, develop an approach to removing these artifacts
(‘degridding’), and show that this further increases the performance of DRNs. In addition, we show that the accuracy
advantage of DRNs is further magnified in downstream applications such as object localization and semantic segmentation.

### 
