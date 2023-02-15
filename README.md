# tensorflow-2-rtx4090
How to build tensorflow 2 for cuda 11.8 and cuDNN 8.6 for nvidia RTX4090
Linux Ubuntu 22.04 LTS


# Driver
1) for RTX4090, minimum driver compatible is 520.56.06. This driver comes with Cuda 11.8
Download here: https://www.nvidia.com/download/driverResults.aspx/193764/en-us/

2)Reboot.

3) Test typing nvidia-smi in your terminal.

# install essentials for build tensorflow
sudo apt-get install g++ freeglut3-dev build-essential libx11-dev libxmu-dev libxi-dev libglu1-mesa libglu1-mesa-dev

# remove all old cuda version with:
sudo apt purge cuda-*
sudo rm -rf /usr/local/cuda*
sudo apt-get autoremove && sudo apt-get autoclean

reboot.







