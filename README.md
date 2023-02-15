# tensorflow-2-rtx4090
How to build tensorflow 2 for cuda 11.8 and cuDNN 8.6 for nvidia RTX4090
Linux Ubuntu 22.04 LTS


# Driver
1) for RTX4090, minimum driver compatible is 520.56.06. This driver comes with Cuda 11.8
Download here: https://www.nvidia.com/download/driverResults.aspx/193764/en-us/

chmod a+x NVIDIA-Linux-x86_64-520.56.06.run
sudo ./NVIDIA-Linux-x86_64-520.56.06.run

2) Reboot.

3) Test typing nvidia-smi in your terminal. Check driver  (520.56.06) and Cuda  (11.8) versions.

# install essentials for build tensorflow

sudo apt-get install g++ freeglut3-dev build-essential libx11-dev libxmu-dev libxi-dev libglu1-mesa libglu1-mesa-dev
sudo apt install python3-dev python3-pip
pip install -U --user pip numpy wheel packaging requests opt_einsum
pip install -U --user keras_preprocessing --no-deps

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

Reboot.

# install cuDNN 8.6

wget https://developer.nvidia.com/compute/cudnn/secure/8.6.0/local_installers/11.8/cudnn-linux-x86_64-8.6.0.163_cuda11-archive.tar.xz
tar -xzvf cudnn-linux-x86_64-8.6.0.163_cuda11-archive.tar.xz

sudo cp -P cuda/include/* /usr/local/cuda-11.8/include
sudo cp -P cuda/lib64/libcudnn* /usr/local/cuda-11.8/lib64/
sudo chmod a+r /usr/local/cuda-11.8/lib64/libcudnn*






