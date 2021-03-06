OpenPose - Installation and FAQ
====================================

## Contents
1. [Operating Systems](#operating-systems)
2. [Requirements](#requirements)
3. [Clone and Update the Repository](#clone-and-update-the-repository)
4. [Installation](#ubuntu)
5. [FAQ](#faq)



## Operating Systems
**Ubuntu** 14 and 16.
OpenPose can be e Update the system kernel.
```bash
sudo apt-get update && sudo apt-get upgrade && sudo apt-get dist-upgrade && sudo apt-get autoremove
```





## Requirements
- NVIDIA graphics card with at least 1.6 GB available (the `nvidia-smi` command checks the available GPU memory in Ubuntu).
- At least 2 GB of free RAM memory.
- Highly recommended: cuDNN and a CPU with at least 8 cores.

Note: These requirements assume the default configuration (i.e. `--net_resolution "656x368"` and `scale_number 1`). You might need more (with a greater net resolution and/or number of scales) or less resources (with smaller net resolution and/or using the MPI and MPI_4 models).





## Clone and Update the Repository
The first step is to clone the OpenPose repository. It might be done with [GitHub Desktop](https://desktop.github.com/) in Windows and from the terminal in Ubuntu:
```bash
git clone https://github.com/CMU-Perceptual-Computing-Lab/openpose
```

OpenPose can be easily updated by clicking the `synchronization` button at the top-right part in GitHub Desktop in Windows, or by running `git pull origin master` in Ubuntu. After OpenPose has been updated, just run the `Reinstallation` section described below for your specific Operating System.




## Ubuntu
**Prerequisites**
Fresh the compile dependence environment of caffe
```bash
sudo apt-get install libatlas-base-dev
sudo apt-get install libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libboost-all-dev libhdf5-serial-dev
sudo apt-get install libgflags-dev libgoogle-glog-dev liblmdb-dev protobuf-compiler
sudo apt-get install python-dev python-numpy python-scipy python-matplotlib
```


### Installation - CMake (Recommended installation method) 
**Prerequisites**
CUDA, cuDNN, OpenCV and Atlas must be already installed on your machine:

    1. [CUDA](https://developer.nvidia.com/cuda-80-ga2-download-archive) must be installed. You should reboot your machine after installing CUDA.
    2. [cuDNN](https://developer.nvidia.com/cudnn): Once you have downloaded it, just unzip it and copy (merge) the contents on the CUDA folder, e.g. `/usr/local/cuda-8.0/`. Note: We found OpenPose working ~10% faster with cuDNN 5.1 compared to cuDNN 6. Otherwise, check [Compiling without cuDNN](#compiling-without-cudnn).
    3. OpenCV can be installed with `apt-get install libopencv-dev`. If you have compiled OpenCV 3 by your own, follow [Manual Compilation](#manual-compilation). After both Makefile.config files have been generated, edit them and uncomment the line `# OPENCV_VERSION := 3`. You might alternatively modify all `Makefile.config.UbuntuXX` files and then run the scripts in step 2.
    4. In addition, OpenCV 3 does not incorporate the `opencv_contrib` module by default. Assuming you have OpenCV 3 compiled with the contrib module and you want to use it, append `opencv_contrib` at the end of the line `LIBRARIES += opencv_core opencv_highgui opencv_imgproc` in the `Makefile` file.
    5. Atlas can be installed with `sudo apt-get install libatlas-base-dev`. Instead of Atlas, you can use OpenBLAS or Intel MKL by modifying the line `BLAS := atlas` in the same way as previosuly mentioned for the OpenCV version selection.

It is simpler and it offers many more customization settings. See [doc/installation_cmake.md](installation_cmake.md). Note that it is a beta version, if it fails, please post in GitHub and use [Installation - Script Compilation](#installation---script-compilation) meanwhile.

### Installation - Script Compilation
Build Caffe & the OpenPose library + download the required Caffe models for Ubuntu 14.04 or 16.04 (auto-detected for the script) and CUDA 8:
```bash
bash ./ubuntu/install_caffe_and_openpose_if_cuda8.sh
```
**Highly important**: This script only works with CUDA 8 and Ubuntu 14 or 16. Otherwise, see [doc/installation_cmake.md](installation_cmake.md) or [Installation - Manual Compilation](#installation---manual-compilation).

Or you can set up the process

Install CUDA
```bash
bash ./ubuntu/install_cuda.sh
```
Install CUDNN
```bash
bash ./ubuntu/install_cudnn.sh
```
Install CAFFE AND OpenPose
```bash
bash ./ubuntu/install_caffe_and_openpose_if_cuda8.sh
```

### Installation - Manual Compilation
Alternatively to the script installation, if you want to use CUDA 7, avoid using sh scripts, change some configuration labels (e.g. OpenCV version), etc., then:
1. Install the [Caffe prerequisites](http://caffe.berkeleyvision.org/installation.html).
2. Compile Caffe and OpenPose by running these lines:
    ```
    ### Install Caffe ###
    git submodule update --init --recursive
    cd 3rdparty/caffe/
    # Select your desired Makefile file (run only one of the next 4 commands)
    cp Makefile.config.Ubuntu14_cuda7.example Makefile.config # Ubuntu 14, cuda 7
    cp Makefile.config.Ubuntu14_cuda8.example Makefile.config # Ubuntu 14, cuda 8
    cp Makefile.config.Ubuntu16_cuda7.example Makefile.config # Ubuntu 16, cuda 7
    cp Makefile.config.Ubuntu16_cuda8.example Makefile.config # Ubuntu 16, cuda 8
    # Change any custom flag from the resulting Makefile.config (e.g. OpenCV 3, Atlas/OpenBLAS/MKL, etc.)
    # Compile Caffe
    make all -j`nproc` && make distribute -j`nproc`

    ### Install OpenPose ###
    cd ../../models/
    bash ./getModels.sh # It just downloads the Caffe trained models
    cd ..
    cp ubuntu/Makefile.example Makefile
    # Same file cp command as the one used for Caffe
    cp ubuntu/Makefile.config.Ubuntu14_cuda7.example Makefile.config
    # Change any custom flag from the resulting Makefile.config (e.g. OpenCV 3, Atlas/OpenBLAS/MKL, etc.)
    make all -j`nproc`
    ```

    NOTE: If you want to use your own Caffe distribution, follow the steps on [Custom Caffe](#custom-caffe) section and later re-compile the OpenPose library:
    ```
    bash ./install_openpose_if_cuda8.sh
    ```
    Note: These steps only need to be performed once. If you are interested in making changes to the OpenPose library, you can simply recompile it with:
    ```
    make clean
    make all -j$(NUM_CORES)
    ```
**Highly important**: There are 2 `Makefile.config.Ubuntu##.example` analogous files, one in the main folder and one in [3rdparty/caffe/](../3rdparty/caffe/), corresponding to OpenPose and Caffe configuration files respectively. Any change must be done to both files (e.g. OpenCV 3 flag, Atlab/OpenBLAS/MKL flag, etc.). E.g. for CUDA 8 and Ubuntu16: [3rdparty/caffe/Makefile.config.Ubuntu16_cuda8.example](../3rdparty/caffe/Makefile.config.Ubuntu16.example) and [ubuntu/Makefile.config.Ubuntu16_cuda8.example](../ubuntu/Makefile.config.Ubuntu16_cuda8.example).



### Reinstallation
If you updated some software that our library or 3rdparty use, or you simply want to reinstall it:
1. Clean the OpenPose and Caffe compilation folders:
```
make clean && cd 3rdparty/caffe && make clean
```
2. Repeat the [Installation](#installation) steps. You do not need to download the models again.



### Uninstallation
You just need to remove the OpenPose folder, by default called `openpose/`. E.g. `rm -rf openpose/`.





## FAQ
**Q: Out of memory error** - I get an error similar to: `Check failed: error == cudaSuccess (2 vs. 0)  out of memory`.

**A**: Most probably cuDNN is not installed/enabled, the default Caffe model uses >12 GB of GPU memory, cuDNN reduces it to ~1.5 GB.


**Q: Out of memory error** - I get an error similar to: `/sbin/ldconfig.real: /usr/lib/nvidia-375/libEGL.so.1 not a symbol link
/sbin/ldconfig.real: /usr/lib32/nvidia-375/libEGL.so.1 not a symbol link`.

**A**:

    
    sudo mv /usr/lib/nvidia-375/libEGL.so.1 /usr/lib/nvidia-375/libEGL.so.1.org
    
    sudo mv /usr/lib32/nvidia-375/libEGL.so.1 /usr/lib32/nvidia-375/libEGL.so.1.
    
    sudo ln -s /usr/lib/nvidia-375/libEGL.so.375.39 /usr/lib/nvidia-375/libEGL.so.1
    
    sudo ln -s /usr/lib32/nvidia-375/libEGL.so.375.39 /usr/lib32/nvidia-375/libEGL.so.1
    



**Q: Low speed** - OpenPose is quite slow, is it normal? How can I speed it up?

**A**: Check the [OpenPose Benchmark](https://docs.google.com/spreadsheets/d/1-DynFGvoScvfWDA1P4jDInCkbD4lg0IKOYbXgEq0sK0/edit#gid=0) to discover the approximate speed of your graphics card. Some speed tips:

    1. Use cuDNN 5.1 (cuDNN 6 is ~10% slower).
    2. Reduce the `--net_resolution` (e.g. to 320x176) (lower accuracy).
    3. For face, reduce the `--face_net_resolution`. The resolution 320x320 usually works pretty decently.
    4. Use the `MPI_4_layers` model (lower accuracy and lower number of parts).
    5. Change GPU rendering by CPU rendering to get approximately +0.5 FPS (`--render_pose 1`).



**Q: Webcam is slow** - Using a folder with images matches the speed FPS benchmarks, but the webcam has lower FPS. Note: often on Windows.

**A**: OpenCV has some issues with some camera drivers (specially on Windows). The first step should be to compile OpenCV by your own and re-compile OpenPose after that (following the `Reinstallation` section in Ubuntu or cleaning the project on Windows). If the speed is still slower, you can better debug it by running a webcam OpenCV example (e.g. [this C++ example](http://answers.opencv.org/question/1/how-can-i-get-frames-from-my-webcam/)). If you are able to get the proper FPS with the OpenCV demo but OpenPose is still low, then let us know!



**Q: Video and/or webcam are not working** - Using a folder with images does work, but the video and/or the webcam do not. Note: often on Windows.

**A**: OpenCV has some issues with some camera drivers and video codecs (specially on Windows). Follow the same steps as the `Webcam is slow` question to test the webcam is working. After re-compiling OpenCV, you can also try this [OpenCV example for video](http://docs.opencv.org/3.0-beta/modules/videoio/doc/reading_and_writing_video.html).



**Q: I am getting an error of the type: munmap_chunk()/free/invalid pointer.**

**A**: In order to run OpenCV 3.X and Caffe simultaneously, [OpenCV must be compiled without `WITH_GTK` and with `WITH_QT` flags](https://github.com/BVLC/caffe/issues/5282#issuecomment-306063718). On Ubuntu 16.04 the qt5 package is "qt5-default" and the OpenCV cmake option is WITH_QT.

