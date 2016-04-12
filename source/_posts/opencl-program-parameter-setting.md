---
title: 异构计算 OpenCL(CPU/GPU) 执行参数设置
date: 2016-04-08 20:00:07
tags:
---
通过 clCreateCommandQueue 创建执行命令队列。
```C
	cl_command_queue command_queue = clCreateCommandQueue(context, device_id, 0, &ret);
```
	
创建分配设备缓存空间。
```C
	cl_mem a_mem_obj = clCreateBuffer(context, CL_MEM_READ_ONLY,
		LIST_SIZE * sizeof(int), NULL, &ret);
	cl_mem b_mem_obj = clCreateBuffer(context, CL_MEM_READ_ONLY,
		LIST_SIZE * sizeof(int), NULL, &ret);
	cl_mem c_mem_obj = clCreateBuffer(context, CL_MEM_WRITE_ONLY,
		LIST_SIZE * sizeof(int), NULL, &ret);
```	
将A与B数组的数据初始化到a_mem_obj和b_mem_obj中，并放入执行队列中。
```C
	ret = clEnqueueWriteBuffer(command_queue, a_mem_obj, CL_TRUE, 0, LIST_SIZE * sizeof(int), A, 0, NULL, NULL);
	ret = clEnqueueWriteBuffer(command_queue, b_mem_obj, CL_TRUE, 0, LIST_SIZE * sizeof(int), B, 0, NULL, NULL);
```
编译Kernel文件，即OpenCL的核心程序。
```C
	cl_program program = clCreateProgramWithSource(context, 1,
		(const char **)&source_str, (const size_t *)&source_size, &ret);

	// Build the program
	ret = clBuildProgram(program, 1, &device_id, NULL, NULL, NULL);
```
如果编译失败，可以通过 clGetProgramBuildInfo 可以获取编译失败的详细信息。
```C
	if (ret != CL_SUCCESS) 
	{
		printf("Error: Building Program( clBuildProgram ) %d\n", ret);
		
		if (ret == CL_BUILD_PROGRAM_FAILURE) 
		{
			cl_int logStatus;
			size_t buildLogSize = 0;
			logStatus = clGetProgramBuildInfo(program, device_id, CL_PROGRAM_BUILD_LOG, 0, NULL, &buildLogSize);

			char * buildLog = NULL;
			buildLog = (char*)malloc(buildLogSize);
			memset(buildLog, 0, buildLogSize);
			logStatus = clGetProgramBuildInfo(program, device_id, CL_PROGRAM_BUILD_LOG, buildLogSize, buildLog, NULL);

			printf("Build Log:\n%s\n", buildLog);
			free(buildLog);
		}
		getchar(); 
		exit(1);
	}
```
通过创建成功的Program构建Kernel，这里的vector_add_kernel为Kernel调用的kernel函数名，通过clSetKernelArg设置Kernel函数需要的参数。
```C
	// Create the OpenCL kernel
	cl_kernel kernel = clCreateKernel(program, vector_add_kernel, &ret);

	// Set the arguments of the kernel
	ret = clSetKernelArg(kernel, 0, sizeof(cl_mem), (void *)&a_mem_obj);
	ret = clSetKernelArg(kernel, 1, sizeof(cl_mem), (void *)&b_mem_obj);
	ret = clSetKernelArg(kernel, 2, sizeof(cl_mem), (void *)&c_mem_obj);
	
	// Execute the OpenCL kernel on the list
	size_t global_item_size[] = { LIST_SIZE }; // Process the entire lists  
	size_t local_item_size[] = { 128 };  // Divide work items into groups of 64
	ret = clEnqueueNDRangeKernel(command_queue, kernel, 1, NULL,
		global_item_size, local_item_size, 0, NULL, NULL);

```
[clEnqueueNDRangeKernel](https://www.khronos.org/registry/cl/sdk/1.0/docs/man/xhtml/clEnqueueNDRangeKernel.html) 参数介绍

# 相关连接
[- 异构计算 OpenCL(CPU/GPU) 开发环境构建](http://blog.h2nachuang.cn/2016/03/31/opencl-environment-build/)
[- 异构计算 OpenCL(CPU/GPU) 执行参数设置](http://blog.h2nachuang.cn/2016/04/08/opencl-program-parameter-setting/)
