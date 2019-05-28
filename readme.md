# Fastai Initial Setup

Fast.ai is the best coder's guide to practical deep learning. This is a guide to its environment setup on a Linux server with NVIDIA GPU.

## Server setup: GPU driver and CUDA

If you have an Nvidia GPU on your Linux machine, great! Let's install the necessary components for deep learning.

### Preparation

Install some useful packages

```
sudo apt-get update
sudo apt-get install \
  aptitude \
  freeglut3-dev \
  g++-4.8 \
  gcc-4.8 \
  libglu1-mesa-dev \
  libx11-dev \
  libxi-dev \
  libxmu-dev \
  nvidia-modprobe \
  python-dev \
  python-pip \
  python-virtualenv \
  vim
```

### Install CUDA

Download CUDA installation file: https://developer.nvidia.com/cuda-downloads

Choose Linux -> x86_64 -> Ubuntu -> 14.04 -> deb (local)  -> Download

Install CUDA in terminal (use the specific .deb file you've downloaded):

```
cd ~/Downloads
sudo dpkg -i cuda-repo-ubuntu1404-8-0-local-ga2_8.0.61-1_amd64.deb
sudo apt-get update
sudo apt-get install cuda
```

Restart the computer to activate CUDA driver. Now your screen resolution should be automatically changed to highest resolution for the display!

### Install cuDNN

The NVIDIA CUDA® Deep Neural Network library (cuDNN) is aGPU-accelerated library of primitives for deep neural networks with optimizations for convolutions etc.

Register an (free) acount on NVIDIA website and login to download the latest cuDNN library: https://developer.nvidia.com/cudnn

Choose the specific version of cuDNN (denpending on support of your prefered deep learning framework)

Choose Download cuDNN v5.1 (Jan 20, 2017), for CUDA 8.0 -> cuDNN v5.1 Library for Linux

Install cuDNN (by copying files :) in terminal:

```
cd ~/Downloads
tar xvf cudnn-8.0-linux-x64-v5.1.tgz
cd cuda
sudo cp lib64/* /usr/local/cuda/lib64/
sudo cp include/cudnn.h /usr/local/cuda/include/
sudo chmod a+r /usr/local/cuda/lib64/libcudnn*
```

### Update your .bashrc

Add the following lines to your ~/.bashrc file (you can open it by gedit ~/.bashrc in terminal)

```
export PATH=/usr/local/cuda/bin:$PATH
export MANPATH=/usr/local/cuda/man:$MANPATH
export LD_LIBRARY_PATH=/usr/local/cuda/lib64:$LD_LIBRARY_PATH
source ~/.bashrc
```

To check the installation, print some GPU and driver information by:

```
nvidia-smi
nvcc --version
```

## Server Setup: Conda

Install Anaconda

```
cd /tmp
curl -O https://repo.anaconda.com/archive/Anaconda3-2019.03-Linux-x86_64.sh
sha256sum Anaconda3-2019.03-Linux-x86_64.sh
# output
# 45c851b7497cc14d5ca060064394569f724b67d9b5f98a926ed49b834a6bb73a  Anaconda3-2019.03-Linux-x86_64.sh

bash Anaconda3-2019.03-Linux-x86_64.sh
```

You’ll receive the following output to review the license agreement by pressing `ENTER` until you reach the end.

```
# Output

Welcome to Anaconda3 2019.03

In order to continue the installation process, please review the license
agreement.
Please, press ENTER to continue
>>>
...
Do you approve the license terms? [yes|no]
```

When you get to the end of the license, type yes as long as you agree to the license to complete installation.

Once you agree to the license, you will be prompted to choose the location of the installation. You can press `ENTER` to accept the default location, or specify a different location.

Once installation is complete and ran `conda init`, the following code is added to `.bashrc`. If you use `zsh`, add it to `.zshrc`.

```
# >>> conda initialize >>>
# !! Contents within this block are managed by 'conda init' !!
__conda_setup="$('/home/loganyc/anaconda3/bin/conda' 'shell.bash' 'hook' 2> /dev/null)"
if [ $? -eq 0 ]; then
    eval "$__conda_setup"
else
    if [ -f "/home/loganyc/anaconda3/etc/profile.d/conda.sh" ]; then
        . "/home/loganyc/anaconda3/etc/profile.d/conda.sh"
    else
        export PATH="/home/loganyc/anaconda3/bin:$PATH"
    fi
fi
unset __conda_setup
# <<< conda initialize <<<
```

Use the conda command to test the installation and activation:

```
conda list
```

To setup conda environment,

```
conda create --name my_env python=3
conda activate my_env
```

To deactivate conda env

```
conda deactivate
```

(Note if you use zsh, there can be a `(base)` shown as the default conda environment.)

## Server Setup: fast.ai

```
git clone https://github.com/fastai/course-v3
conda update conda
conda install -c pytorch -c fastai fastai pytorch torchvision cuda92 jupyter
cd course-v3/nbs/dl1
jupyter notebook
```

In your terminal, and you can access the notebook at localhost:8888.

If going to localhost:8888 doesn’t work, or asks for a password/token return to your terminal window and look for this message after you typed ‘jupyter notebook’: “Copy/paste this URL into your browser when you connect for the first time, to login with a token:”

Copy and paste that URL into your browser, and this should connect you to your jupyter notebook.

Go back to the first page to see how to use this jupyter notebook and run the jupyter notebook tutorial. Come back here once you’re finished and don’t forget to stop your instance with the next step.

If you have any problem while using the fastai library try running

```
conda install -c fastai fastai
```

Note that in Ubuntu terminal, use `ctrl+\` to stop the notebook server.

## Port Forwarding to Acess Jupyter Notebook Remotely

To access the Jupyter Notebook on the Linux GPU server from a client machine's browser, run

```
ssh -fNL 8888:local:8888 <username_on_server>@<server_ip>
```

and go to `localhost:8888/tree` in your browser.

## GPU vs. CPU for Deep Learning Test

Try running this inside a Jupyter Notebook:

Cell [1]:

```
import torch
t_cpu = torch.rand(500,500,500)
%timeit t_cpu @ t_cpu
# Output
# 785 ms ± 14.6 ms per loop (mean ± std. dev. of 7 runs, 1 loop each)
```

Cell [2]:

```
t_gpu = torch.rand(500,500,500).cuda()
%timeit t_gpu @ t_gpu
# Output
# 18.7 ms ± 376 µs per loop (mean ± std. dev. of 7 runs, 1000 loops each)
```

If you would like to train anything meaningful in deep learning, a GPU is what you need - specifically an NVIDIA GPU.

## Why NVIDIA?

We recommend you to use an NVIDIA GPU since they are currently the best out there for a few reasons:

- Currently the fastest
- Native Pytorch support for CUDA
- Highly optimized for deep learning with cuDNN

## References

- https://course.fast.ai/start_aws.html
- https://github.com/charlesq34/DIY-Deep-Learning-Workstation