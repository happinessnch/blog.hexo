---
title: 异构计算 OpenCL(CPU/GPU) 开发环境构建
date: 2016-03-31 09:28:48
tags: 异构计算
---
# 环境构建
根据显卡安装相关驱动和SDK（Nvidia/ATI等）

以Win，Nvidia为例，安装完Nvidia的SDK之后（OpenCL的相关内容，可以在CUDA里找到），配置开发环境。
```C
      头文件目录 C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v7.5\include
      静态链接库目录 C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v7.5\lib\Win32
      动态链接库目录 C:\Program Files\NVIDIA Corporation\OpenCL
```
# 测试代码
```C
    cl_platform_id platform_id_list[PLATFORM_SIZE];
    cl_uint ret_num_devices;
    cl_uint ret_num_platforms;
    cl_int ret = CL_SUCCESS;
    
    ret = clGetPlatformIDs(PLATFORM_SIZE, platform_id_list, &ret_num_platforms);
    
    ret = clGetDeviceIDs(platform_id_list[use_platform_index], device_type, 1, &device_id, &ret_num_devices);
    
    context = clCreateContext(NULL, 1, &device_id, NULL, NULL, &ret);
```
通过clGetPlatformIDs获取所有可使用的平台的数量和ID。
```C
    // 获取可使用平台个数
    ret = clGetPlatformIDs(0, 0, &platform_num);
```
通过clGetPlatformInfo获取更多的平台信息。
```C
    char pff[64];
    clGetPlatformInfo(platform_id, CL_PLATFORM_VENDOR, sizeof(pff), pff, NULL);
    printf("%s\n", pff);
```
输出：
```C
    Intel(R) Corporation
    NVIDIA Corporation
```
通过clGetDeviceIDs获取可使用设备，这里只获取一个，但实际上可能存在多个设备。platform_id与device_type的类型必须对应，否则获取不到device_id。
例如，platform_id选择的NVIDIA CUDA平台，对应的device_type就应该是CL_DEVICE_TYPE_GPU。
```C
    ret = clGetDeviceIDs(platform_id[use_platform_index], device_type, 1, &device_id, &ret_num_devices);

    context = clCreateContext(NULL, 1, &device_id, NULL, NULL, &ret);
```

# 相关连接
[- 异构计算 OpenCL(CPU/GPU) 开发环境构建](http://blog.h2nachuang.cn/2016/03/31/opencl-environment-build/)
[- 异构计算 OpenCL(CPU/GPU) 执行参数设置](http://blog.h2nachuang.cn/2016/04/08/opencl-program-parameter-setting/)
