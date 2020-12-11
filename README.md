# StyleGAN Pytorch Implementation
This is a Pytorch implementation of StyleGAN (https://arxiv.org/abs/1812.04948), with the capability of generating 1024x1024 pictures. Training to grow to 1024x1024 for training is also supported. A 1080 Ti is recommended for faster training speed.

## Prerequisites
### Dependencies
See conda.yaml. Please note that I have cuda 10.0 installed. Change your conda.yaml accordingly if you use different cuda version.
### Image generation using official implementation's TensorFlow checkpoints

**This step is not needed if running the generation command succeeds for downloading.** If downloading fails for Google Drive, manual download is required:

* [ffhq-1024x1024](https://drive.google.com/uc?id=1MEGjdvVpUsu1jB4zrXZN7Y4kBBOzizDQ)
* [bedrooms-256x256](https://drive.google.com/open?id=1MOSKeGF0FJcivpBI7s63V9YHloUTORiF)
* [cats-256x256](https://drive.google.com/uc?id=1MQywl0FNt6lHu8E_EUqnRbviagS7fbiJ)

And place them in ./pretrained directory.

### Training prerequisites on CelebA dataset
Download the [celeba](https://drive.google.com/drive/folders/0B7EVK8r0v71pTUZsaXdaSnZBZzg) dataset. Unzip the .zip file into ./data/celeba directory.

## Image Generation
### Image generation using official checkpoints
Run the command:
```bash
mlflow -e generate . -P dataset=cats
```
The default random seed is 77. To generate different images with different image grid (note that the number of images you can generate is limited by your GPU).
```bash
mlflow -e generate -P dataset=cats -P random-seed=777 -P nrow=2 -P ncol=5
```
This will generate 10 images at once.
### Image generation using checkpoints generated by this code
```bash
mlflow -e genearte . \
       -P use_official_checkpoints=False \
       -P g_checkpoint=[path_to_generator_checkpoint] \
       -P target_resolution=128 \
       -P nrow=2 \
       -P ncol=2
```
This will generate images using checkpoints trained by this code.

## Training on CelebA dataset
Run the command to start from scratch:
```bash
mlflow -e train . -P resume=False
```
This will kick off the training for 128x128 resolution on CelebA dataset. During training, the model checkpoints are stored under ./checkpoints, and the fake images are generated for checking under ./checks/fake\_imgs. Note that this is a progressive process starting from 8x8, so you will see 8x8 images in the begining and 128x128 images in the end of the training process. 

To resume training:
```bash
mlflow -e train .\
       -P resume=True \
       -P g_checkpoint=[path_to_generator_checkpoint] \
       -P d_checkpoint=[path_to_discriminator_checkpoint]
```
For other training options, please check the MLproject file. For hyperparameters, please check train.py and NVidia's official implementation.

## TODO
1. Add truncation trick
2. Add and experiment with other loss functions (some are in the repo but not tried)
3. Add tensorboard support
4. Add moving average of generator's weight

Multi-GPU support is added but not experimented due to hardware limitation.

## License
This project is under BSD-3 license.
