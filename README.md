# InfoGAN-CR: Disentangling Generative Adversarial Networks with Contrastive Regularizers

**[[paper (arXiv)]()]**
**[[code](https://github.com/fjxmlzn/InfoGAN-CR)]**


**Authors:** [Zinan Lin](http://www.andrew.cmu.edu/user/zinanl/), [Kiran Koshy Thekumparampil](https://scholar.google.com/citations?user=0gJQCIgAAAAJ&hl=en), [Giulia Fanti](https://www.andrew.cmu.edu/user/gfanti/), [Sewoong Oh](http://web.engr.illinois.edu/~swoh/)

**Abstract:** Training disentangled representations with generative adversarial networks (GANs) remains challenging, with leading implementations failing to achieve comparable performance to Variational Autoencoder (VAE)-based methods. After beta-VAE and FactorVAE discovered that regularizing the total correlation of the latent vectors promotes disentanglement, numerous VAE-based methods emerged. Such a discovery has yet to be made for GANs, and reported disentanglement scores of GAN-based methods are significantly inferior to VAE-based methods on benchmark datasets. To this end, we propose a novel regularizer that achieves higher disentanglement scores than state-of-the-art VAE- and GAN-based approaches. The proposed contrastive regularizer is inspired by a natural notion of disentanglement: latent traversal. Latent traversal refers to generating images by varying one latent code while fixing the rest. We turn this intuition into a regularizer by adding a discriminator that detects how the latent codes are coupled together, in paired examples. Numerical experiments show that this approach improves upon competing state-of-the-art approaches on benchmark datasets. 


## Preparing datasets

### 3D Teapot
3D Teapot dataset was originally proposed by [Eastwood et al.](https://openreview.net/pdf?id=By-7dz-AZ). However, the code for generating this dataset was not published, thus we cannot calculate [Kim & Mnih disentanglement metric](https://arxiv.org/pdf/1802.05983) which requires additional data. We try to reproduce the dataset, and make several extensions to it, based on [this renderer](https://github.com/polmorenoc/opendr) by Pol Moreno. We provide all our codes for generating the datasets. 

The datasets can be downloaded [here](https://drive.google.com/drive/folders/1tyTgGZ4Yb2hXCLoXxvUZPJ-y3pFd4I_E?usp=sharing).

The steps of running 3D Teapot dataset generator by your own are as follows:

* ` git clone https://github.com/polmorenoc/opendr `. (Or use my forked version: https://github.com/fjxmlzn/opendr)
* Copy all files in `3D_teapot` folder in this repo to `opendr` folder.
* Follow the instructions on [https://github.com/polmorenoc/opendr](https://github.com/polmorenoc/opendr) to install the required library, and download `teapotModel.pkl` to `opendr/data` folder.
* `cd opendr`. Then you can run:
	* `python generate_teapots_all_color_incomplete_rotation_up_light.py`: this code tries to reproduce [the original 3D Teapot dataset](https://github.com/cianeastwood/qedr), which contains teapots with different colors, incomplete rotations, and the light source is from the top. Note that since we do not know the parameters (e.g., teapot size, shape translation) the original dataset use, the data generated by this code may be different from the original one. 
	* `python generate_teapots_fixed_color_complete_rotation_up_light.py`: this code generates teapots with a fixed color, all possible rotations (uniformly), and the light source is from the top.
	* `python generate_teapots_fixed_color_complete_rotation_uniform_light.py`: this code generates teapots with a fixed color, all possible rotations (uniformly), and the direction of the light source is also uniformly random among all possibilities.
* After running the above codes, you will get the following files in a corresponding folder

```
train_data/
├── data.npz
metric_data/
├── data.npz
```
`train_data/data.npz` contains samples for training, and `metric_data/data.npz` contains samples for evaluating [Kim & Mnih disentanglement metric](https://arxiv.org/pdf/1802.05983). Please copy them (keep directory structure) to the `data` folder in code (see below).



### CelebA
Download `img_align_celeba.zip` from [https://www.kaggle.com/jessicali9530/celeba-dataset](https://www.kaggle.com/jessicali9530/celeba-dataset) (or other source), and put it in `CelebA` folder. Then run `python process_celeba.py`. This code will crop and resize the images into 32x32x3 format. Please copy the generated `data.npz` to the `data` folder in code (see below).


### dSprites
Download `dsprites_ndarray_co1sh3sc6or40x32y32_64x64.npz` from [https://github.com/deepmind/dsprites-dataset](https://github.com/deepmind/dsprites-dataset) and put it in the `data` folder in code (see below).

## Train models
The codes are based on [GPUTaskScheduler](https://github.com/fjxmlzn/GPUTaskScheduler) library, which helps you automatically schedule jobs among GPU nodes. Please install it first. You may need to change GPU configurations according to the devices you have. The configurations are set in `config.py` in each directory. Please refer to [GPUTaskScheduler's GitHub page](https://github.com/fjxmlzn/GPUTaskScheduler) for details of how to make proper configurations.

> You can also run these codes without GPUTaskScheduler. Just run `python infogan_cr.py` or `python factorVAE.py` in `gan` subfolders.

### InfoGAN-CR, dSprites dataset
```
cd InfoGAN-CR_dSprites
```
Copy dSprites data files to `data/dSprites`.

```
cd gan
python train_DSpritesInceptionScore.py # train network for evaluating inception score on dSrpites dataset
cd ..
python main.py
```

### FactorVAE, dSprites dataset
```
cd FactorVAE_dSprites
```
Copy dSprites data files to `data/dSprites`.

```
cd gan
python train_DSpritesInceptionScore.py # train network for evaluating inception score on dSrpites dataset
cd ..
python main.py
```

### InfoGAN-CR, 3D teapot
```
cd InfoGAN-CR_3D_teapot
```
Copy 3D Teapot data files to `data/3Dpots`.

```
python main.py
```

### FactorVAE, 3D teapot
```
cd FactorVAE_3D_teapot
```
Copy 3D Teapot data files to `data/3Dpots`.

```
python main.py
```

### InfoGAN-CR, CelebA
```
cd InfoGAN-CR_CelebA
```
Copy CelebA data files to `data/celeba`.

```
python main.py
```
