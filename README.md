Below is ControlNet 1.0

Official implementation of [Adding Conditional Control to Text-to-Image Diffusion Models](https://arxiv.org/abs/2302.05543).

ControlNet is a neural network structure to control diffusion models by adding extra conditions.

![img](github_page/he.png)

It copys the weights of neural network blocks into a "locked" copy and a "trainable" copy. 

The "trainable" one learns your condition. The "locked" one preserves your model. 

Thanks to this, training with small dataset of image pairs will not destroy the production-ready diffusion models.

The "zero convolution" is 1×1 convolution with both weight and bias initialized as zeros. 

Before training, all zero convolutions output zeros, and ControlNet will not cause any distortion.

No layer is trained from scratch. You are still fine-tuning. Your original model is safe. 

This allows training on small-scale or even personal devices.

This is also friendly to merge/replacement/offsetting of models/weights/blocks/layers.

### FAQ

**Q:** But wait, if the weight of a conv layer is zero, the gradient will also be zero, and the network will not learn anything. Why "zero convolution" works?

**A:** This is not true. [See an explanation here](docs/faq.md).

# Stable Diffusion + ControlNet

By repeating the above simple structure 14 times, we can control stable diffusion in this way:

![img](github_page/sd.png)

In this way, the ControlNet can **reuse** the SD encoder as a **deep, strong, robust, and powerful backbone** to learn diverse controls. Many evidences (like [this](https://jerryxu.net/ODISE/) and [this](https://vpd.ivg-research.xyz/)) validate that the SD encoder is an excellent backbone.

Note that the way we connect layers is computational efficient. The original SD encoder does not need to store gradients (the locked original SD Encoder Block 1234 and Middle). The required GPU memory is not much larger than original SD, although many layers are added. Great!

# Features & News

2023/0/14 - We released [ControlNet 1.1]. Those new models will be merged to this repo after we make sure that everything is good.

2023/03/03 - We released a discussion - [Precomputed ControlNet: Speed up ControlNet by 45%, but is it necessary?]

2023/02/26 - We released a blog - [Ablation Study: Why ControlNets use deep encoder? What if it was lighter? Or even an MLP?]

2023/02/20 - Implementation for non-prompt mode released. See also [Guess Mode / Non-Prompt Mode](#guess-anchor).

2023/02/12 - Now you can play with any community model by [Transferring the ControlNet].

2023/02/11 - [Low VRAM mode](docs/low_vram.md) is added. Please use this mode if you are using 8GB GPU(s) or if you want larger batch size.

# Production-Ready Pretrained Models

First create a new conda environment

    conda env create -f environment.yaml
    conda activate control

All models and detectors can be downloaded from [our Hugging Face page]. Make sure that SD models are put in "ControlNet/models" and detectors are put in "ControlNet/annotator/ckpts". Make sure that you download all necessary pretrained weights and detector models from that Hugging Face page, including HED edge detection model, Midas depth estimation model, Openpose, and so on. 

We provide 9 Gradio apps with these models.

All test images can be found at the folder "test_imgs".
