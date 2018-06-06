---
layout: post
title: Creating a virtual GPU instance in the cloud for TensorFlow applications
comments: true
img_excerpt: imgs/tflogo.png
---

In this tutorial, you will learn to create a GPU virtual instance with TensorFlow installed on the cloud for all your deep learning applications. There are a few open source startup-scripts out there for a few hardware configurations, if you do not want to do it manually.

I assume that you have created a VM with `NVIDIA K-80` GPU and with `Ubuntu 16.04` OS. But this procedure can be applied to any other CUDA compatible GPU instances as well. Since I use Google Cloud to create VM, I use some commands from the gcloud SDK that you can easily swap with similar terminal commands.

### Step 1: Creating a virtual environment
Not all projects use the exact same libraries for all the projects. In order to make sure changes made to the environment does not affect other existing projects (and vice-versa), it is recommended that you create a virtual environment, where you will install TensorFlow.

You need `virtualenv` installed. The best way to do it is using `pip`. So you will need to install `pip`. 
```
sudo apt-get install python-pip python-dev build-essential
sudo pip install virtualenv virtualenvwrapper
```

Creating a virtual environment:
``` 
virtualenv <your_env_name>
```

Activating the virtual environment:
```
source <your_env_name>/bin/activate
```

Now that you have created a virtual environment, you can install any package you want. But before installing TensorFlow for GPUs, we need to make sure we have CUDA and CUDNN (which accelerates deep learning algorithms). CUDA and CUDNN installation will happen in the root directory, so this is not specific to your virtual environment.

### Step 2: Installing CUDA
CUDA is available on [NVIDIA website](https://developer.nvidia.com/cuda-downloads). I have an ubuntu 16.04 instance (probably best for deep learning applications).

Downloading CUDA:
```
wget https://developer.nvidia.com/compute/cuda/8.0/Prod2/local_installers/cuda-repo-ubuntu1604-8-0-local-ga2_8.0.61-1_amd64-deb
```
You will need to change the link above, depending on the OS you have and the processor specifications.

Installing the kernel:
```
sudo dpkg -i cuda-repo-ubuntu1604-8-0-local-ga2_8.0.61-1_amd64-deb
sudo apt-get update
```

Installing CUDA:
```
sudo apt-get install cuda
```

(CAUTION) I had a conflict between different versions of libEGL. This was apparently a bug (as mentioned here)

Move the existing libEGL library:
```
sudo mv /usr/lib/nvidia-375/libEGL.so.1 /usr/lib/nvidia-375/libEGL.so.1.org
sudo mv /usr/lib32/nvidia-375/libEGL.so.1 /usr/lib32/nvidia-375/libEGL.so.1.org
```

Create a symlink for the new version to point to the old version of libEGL:
```
sudo ln -s /usr/lib/nvidia-375/libEGL.so.375.39 /usr/lib/nvidia-375/libEGL.so.1
sudo ln -s /usr/lib32/nvidia-375/libEGL.so.375.39 /usr/lib32/nvidia-375/libEGL.so.1
```

Adding CUDA path to your `.bashrc` file:
```
export LD_LIBRARY_PATH=/usr/local/cuda/lib64/
```

Unfortunately, I also had CUDA-8, so I had to do this as well: 
```
export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64/
```

### Step 3: Installing CUDNN
CUDNN is an accelerator library specifically designed for neural networks applications. While it should be technically possible to run TensorFlow without CUDNN, I do not know how to bypass this step. So, I list it here as a requirement to run TensorFlow on GPUs.

Downloading CUDNN:
```
wget https://developer.nvidia.com/compute/machine-learning/cudnn/secure/v5.1/prod_20161129/8.0/cudnn-8.0-linux-x64-v5.1-tgz
```
NOTE: This might not work. If that is the case, you can download the .tgz file from [NVIDIA directly](https://developer.nvidia.com/rdp/cudnn-download)

The last time I checked, CUDNN version 5.1 worked perfectly with CUDA version 8. So, it is probably best to use `CUDNN version 5.1`.

Untar the tgz file:
```
tar -xvzf /path/to/yourfile.tgz
```

We now need to copy a few files to the CUDA path used for TensorFlow (which is usually `/usr/local/cuda/`):
```
sudo cp -r <unzipped cuda path>/lib64/ /usr/local/cuda/
sudo cp -r <unzipped cuda path>/include/ /usr/local/cuda/
```

### Step 4: If it still doesn't work

It might be the case that your cuda binaries are not in the list of default directories used by your shell to access shared libraries to be linked to your program dynamically during runtime. Then, you will need to add the path to cuda binaries to that. One way to do it temporarily is to do the following:

```
sudo ldconfig /usr/local/cuda/lib64
```

You can permanently add this path by adding the following line in your bash_profile:
```
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/cuda/lib64​​
```

### Step 5: Installing TensorFlow GPU version
If you are not already in the virtual environment, make sure you activate it by following activation instructions in step 1. Once in the virtual environment, you can directly install TensorFlow with the following pip command (this used to be so hard before they made TensorFlow pip installable. All hail Jeff Dean and his team):

```
pip install --upgrade tensorflow-gpu
```