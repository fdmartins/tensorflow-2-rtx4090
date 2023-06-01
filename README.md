# tensorflow-2-rtx4090

How to build tensorflow 2 for cuda 11.8 and cuDNN 8.6 for nvidia RTX4090

Linux Ubuntu 22.04 LTS

# DEPRECATED:
tensorflow versions after 2.12 is now compatible with cuda 11.8 and RTX4090. Just install using PIP. :-)


# Driver
1) for RTX4090, minimum driver compatible is 520.56.06. This driver comes with Cuda 11.8

Download here: https://www.nvidia.com/download/driverResults.aspx/193764/en-us/

chmod a+x NVIDIA-Linux-x86_64-520.56.06.run

sudo ./NVIDIA-Linux-x86_64-520.56.06.run

2) Reboot.

3) Test typing nvidia-smi in your terminal. Check driver  (520.56.06) and Cuda  (11.8) versions.

# prepare. Install essentials 

sudo apt-get install g++ freeglut3-dev build-essential libx11-dev libxmu-dev libxi-dev libglu1-mesa libglu1-mesa-dev

sudo apt install python3-dev python3-pip

pip install -U --user pip numpy wheel packaging requests opt_einsum

pip install -U --user keras_preprocessing --no-deps

sudo apt install apt-transport-https curl gnupg -y

sudo apt-get install python-is-python3


# remove all old cuda version with:

sudo apt purge cuda-*

sudo rm -rf /usr/local/cuda*

sudo apt-get autoremove && sudo apt-get autoclean


reboot.

# install cuda 11.8 (yes, again)

wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/x86_64/cuda-ubuntu2204.pin

sudo mv cuda-ubuntu2204.pin /etc/apt/preferences.d/cuda-repository-pin-600

wget https://developer.download.nvidia.com/compute/cuda/11.8.0/local_installers/cuda-repo-ubuntu2204-11-8-local_11.8.0-520.61.05-1_amd64.deb

sudo dpkg -i cuda-repo-ubuntu2204-11-8-local_11.8.0-520.61.05-1_amd64.deb

sudo cp /var/cuda-repo-ubuntu2204-11-8-local/cuda-*-keyring.gpg /usr/share/keyrings/

sudo apt-get update


# the secret: NOT install using "apt install cuda". This will replace your driver. Install only the cuda toolkit with this command:

sudo apt-get -y install cuda-toolkit-11-8


check if folder /usr/local/cuda-11.8/ exists.

# update enviroment variable

echo 'export PATH=/usr/local/cuda-11.8/bin:$PATH' >>  ~/.bashrc

echo 'export LD_LIBRARY_PATH=/usr/local/cuda-11.8/lib64:$LD_LIBRARY_PATH' >> ~/.bashrc

export PATH=$PATH:/usr/local/cuda-11.8/bin/

Reboot.

# install cuDNN 8.6

wget https://developer.nvidia.com/compute/cudnn/secure/8.6.0/local_installers/11.8/cudnn-linux-x86_64-8.6.0.163_cuda11-archive.tar.xz

tar -xzvf cudnn-linux-x86_64-8.6.0.163_cuda11-archive.tar.xz

sudo cp -P cuda/include/* /usr/local/cuda-11.8/include

sudo cp -P cuda/lib64/libcudnn* /usr/local/cuda-11.8/lib64/

sudo chmod a+r /usr/local/cuda-11.8/lib64/libcudnn*

# install bazel to build tensorflow

curl -fsSL https://bazel.build/bazel-release.pub.gpg | gpg --dearmor >bazel-archive-keyring.gpg

sudo mv bazel-archive-keyring.gpg /usr/share/keyrings

echo "deb [arch=amd64 signed-by=/usr/share/keyrings/bazel-archive-keyring.gpg] https://storage.googleapis.com/bazel-apt stable jdk1.8" | sudo tee /etc/apt/sources.list.d/bazel.list

sudo apt update && sudo apt install bazel

sudo apt install bazel=5.3.0

# Download TensorFlow 2 - TensorFlow 2.12.0-rc0 (PRE RELEASE at 15 fev 2023)

wget https://github.com/tensorflow/tensorflow/archive/refs/tags/v2.12.0-rc0.tar.gz

tar -xzvf tensorflow-2.12.0-rc0.tar.gz

cd tensorflow-2.12.0-rc0

./configure

dont forget to set yes for cuda, and NO for tensorRT.

# BUILD! and get a coffe, this will take more than one hour and a lot of memory.

bazel build --config=cuda --local_ram_resources=2048  //tensorflow/tools/pip_package:build_pip_package


# BUILDING WITH CPU OPTIMIZATIONS:

bazel build --config=cuda --copt=-march=native --copt=-DEIGEN_NO_DEBUG --cxxopt=-DEIGEN_NO_DEBUG --local_ram_resources=2048  //tensorflow/tools/pip_package:build_pip_package

if it fail with "assert" error , go to file /tensorflow/tsl/framework/fixedpoint/MatMatProductAVX2.h and replace "assert" to "eigen_assert" (this is a problem in this pre release version, but in master branch was solved

# BUILD THE PACKAGE

./bazel-bin/tensorflow/tools/pip_package/build_pip_package /tmp/tensorflow_pkg

 (you can save this file in /tmp for future use)
 
 # AND FINALLYYYY INSTALL THE PACKAGE:
 
 pip install /tmp/tensorflow_pkg/tensorflow-2.12.0rc0-cp310-cp310-linux_x86_64.whl



