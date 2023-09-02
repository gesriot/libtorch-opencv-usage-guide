## 1. Устанавливаем или удостоверяемся, что установлен [драйвер NVIDIA](https://www.nvidia.com/Download/index.aspx?lang=en-us)
После установки перезагружаем систему
## 2. Устанавливаем [CUDA Toolkit for Windows](https://developer.nvidia.com/cuda-11-8-0-download-archive)
### 2.1 Скачиваем и куда-нибудь разархивируем вот этот [набор dll](http://www.winimage.com/zLibDll/zlib123dllx64.zip)
Добавляем путь к _zlibwapi.dll_ в переменную среды PATH
### 2.2 Скачиваем [cuDNN v8.9.1](https://developer.nvidia.com/downloads/compute/cudnn/secure/8.9.1/local_installers/11.8/cudnn-windows-x86_64-8.9.1.23_cuda11-archive.zip/) (May 5th, 2023), for CUDA 11.x

Создаем папку _C:\Program Files\NVIDIA\CUDNN\v8.x_  и закидываем в нее папки _bin_, _include_, _lib_ из скачанного архива

Добавляем путь _C:\Program Files\NVIDIA\CUDNN\v8.x\bin_  в переменную среды PATH.
Проверяем установку CUDA и cuDNN (в cmd вводим команды):
> nvcc –version<br>
> where cudnn*.dll<br>
## 3. Устанавливаем OpenCV из исходников по этим инструкциям: https://youtu.be/10Ni2gv4R7A, https://habr.com/ru/articles/722918/ 
## 4. Устанавливаем LibTorch. По сути нужно просто скачать zip-архив с [оф.сайта](https://download.pytorch.org/libtorch/cu118/libtorch-win-shared-with-deps-2.0.1%2Bcu118.zip) и добавить 2 переменные среды:
> $Env:LIBTORCH = "C:\libtorch"<br> 
> $Env:Path += ";C:\libtorch\lib"<br>
## 5. Создаем файл проекта (например, _cudnn_check.cpp_)
```
#include <torch/torch.h>
#include <iostream>

int main() {
    if (torch::cuda::cudnn_is_available()) {
        std::cout << "cuDNN is available" << std::endl;
    } else {
        std::cout << "cuDNN is not available" << std::endl;
    }
    return 0;
}
```
## 6. Пишем CMake-файл (_CMakeLists.txt_)
```
cmake_minimum_required(VERSION 3.27)
project(cudnn_check)

find_package(Torch REQUIRED)
set(OpenCV_DIR ../cv_build) #opencv root
find_package(OpenCV REQUIRED)

add_executable(cudnn_check cudnn_check.cpp)

target_link_libraries(cudnn_check "${TORCH_LIBRARIES}" ${OpenCV_LIBS})

set_property(TARGET cudnn_check PROPERTY CXX_STANDARD 14)
```
Здесь надо подкорректировать путь к папке куда собиралось OpenCV (у меня это ..\cv_build)
## 7. Создаем папку build, заходим в нее и запускам генерацию проекта
> cmake ..<br>
## 8. Открываем решение (_cudnn_check.sln_) в Visual Studio и собираем
## 9. В папку Release со скомпилированным бинарником добавляем следующие dll'ки:
- opencv_core480.dll
- opencv_dnn480.dll
- opencv_highgui480.dll
- opencv_imgcodecs480.dll
- opencv_imgproc480.dll
- opencv_videoio480.dll	