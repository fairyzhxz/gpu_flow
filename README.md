GPU based optical flow extraction in OpenCV
====================
### Features:
* OpenCV wrapper for Real-Time optical flow extraction on GPU
* Automatic directory handling using Qt
* Allows saving of optical flow to disk, 
** either with clipping large displacements 
** or by adaptively scaling the displacements to the radiometric resolution of the output image

### Dependencies
* [OpenCV 2.4] (http://opencv.org/downloads.html) (if you want OpenCV 3.1, tell me, I'll do the port)
* [Qt 5.4] (https://www.qt.io/qt5-4/)
* [cmake] (https://cmake.org/)

### Installation
1. `mkdir -p build`
2. `cd build`
3. `cmake ..`
4. `make`

* The order to installation: cuda-cudnn-cmake-opencv-qt...

- Install and Unistall cuda 7.5

  https://blog.csdn.net/autocyz/article/details/51841157


the way to cuda driver and run driver:
```
cd /usr/local/cuda-7.5/samples/1_Utilities/deviceQuery 
make 
sudo ./deviceQuery
```


- Install and Uninstall qt: 

  https://blog.csdn.net/AAA123524457/article/details/80546957

  https://blog.csdn.net/cddchina/article/details/50764791



### Configuration:
You should adjust the input and output directories by passing in `vid_path` and `out_path`. Note that vid_path must exist, Qt will create out_path. Use -h option t for more.
In the CMakeLists.txt there is an option called WARP. This selects if you want warped optical flow or not. The warped optical flow file also outputs optical flows as a single BGR image (red is the flow magnitude). In the compute_flow_si_warp file itself there is a warp variable that you can set to false to just compute normal flow. If you want grayscale for images (x and y) use compute_flow.

### Usage:


```
sudo rm -rf build

mkdir build&& cd build

cmake -D CUDA_USE_STATIC_CUDA_RUNTIME=OFF -D CMAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=/usr/local -DCUDA_CUDA_LIBRARY=/usr/local/cuda/lib64/stubs/libcuda.so -D CUDA_ARCH_BIN=5.2 -D CUDA_ARCH_PTX="" -D WITH_CUDA=ON -D WITH_TBB=ON -D BUILD_NEW_PYTHON_SUPPORT=ON -D WITH_V4L=ON -D INSTALL_C_EXAMPLES=ON -D INSTALL_PYTHON_EXAMPLES=ON -D BUILD_EXAMPLES=ON -D WITH_QT=ON -D WITH_OPENGL=ON -D ENABLE_FAST_MATH=1 -D CUDA_FAST_MATH=1 -D WITH_CUBLAS=1 -D WITH_NVCUVID:BOOL="1" ..

make

./compute_flow --type=1 --gpuID=0 --out_path=/home/fairy/workspace/dataset/flow --skip=1 --start_video=1 --vid_path=/home/fairy/workspace/dataset/clip

```

When you meet error 'lib/libstdc++.so.6: version GLIBCXX_3.4.20 not found (required by /usr/local/lib/libopencv_gpu.so.2.4)',
```
conda update conda  
conda update --all 
strings /home/root/anaconda3/lib/libstdc++.so.6 | grep GLIBCXX 
```


```
./compute_flow [OPTION]...
```
```
./compute_flow_si_warp [OPTION] ..
```

Available options:
* `start_video`: start with video number in `vid_path` directory structure [1]
* `gpuID`: use this GPU ID [0]
* `type`: use this flow method Brox = 0, TVL1 = 1 [1] 
* `skip`: the number of frames that are skipped between flow calcuation [1]
* `vid_path`: folder with input videos
* `out_path`: folder where a folder per video containing optical flow frames will be created

Additional features in `compute_flow.cpp`:
* `float MIN_SZ = 256`: defines the smallest side of the frame for optical flow computation
* `float OUT_SZ = 256`: defines the smallest side of the frame for saving as .jpeg 
* `bool clipFlow = true;`: defines whether to clip the optical flow larger than [-20 20] pixels and maps the interval [-20 20] to  [0 255] in grayscale image space. If no clipping is performed the mapping to the image space is achieved by finding the frame-wise minimum and maximum displacement and mapping to [0 255] via an adaptive scaling, where the scale factors are saved as a binary file to `out_path`.

### Example:
```
./compute_flow --gpuID=0 --type=1 --vid_path=test --vid_path=test_out --stride=2
```


