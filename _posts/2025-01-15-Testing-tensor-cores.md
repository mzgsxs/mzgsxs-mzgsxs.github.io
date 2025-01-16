# Testing-tensor-core
Altough tensor-core was introduced in the RTX20s series, I have only tried it very breifly after the launch but never used it in practice apart from deloying tensorRT models. It only supports FP16 initially but it's now supporting more data types.
With siginificant boost of tensor cores in RTX50s series, and disappointing increase in its shader cores performance, it's worth spending some time investigating this feature. 
I want to test if the performance of the tensor cores are comparable to odinary cuda/shader cores and wether I should upgrade to RTX5090.

I'm runing all tests on my RTX3080ti, which have 320 tensor cores and [offers 136 dense and 273 sparse FP16 TFLOPS](https://en.wikipedia.org/wiki/GeForce_30_series). 
For higher precision, tensor core typically use TF32 data type, it only have 10 bits for the significand (reduced from FP32's 23 bits), this reduction in significand bits lowers precision to about 3 decimal digits, 
but with siginificant speedup (500 TFLOPS compares to 60 TFLOPS	for TF32 with Nvidia H100). Server GPUs have a better support for [higher precision tensorcore computations](https://www.anandtech.com/show/17327/nvidia-hopper-gpu-architecture-and-h100-accelerator-announced).

Does my RTX3080ti support TF32? It does according to [this](https://www.nvidia.com/content/PDF/nvidia-ampere-ga-102-gpu-architecture-whitepaper-v2.1.pdf), 
but is this performance a result from the regular shader cores? I need test it to find out, Monitor it's utilization requires more effort than `nvidia-smi`.


## Monitoring
The easiest way is to compare the numerical result:
```python
import torch
import torch.profiler

matrix_size = 2<<12  # Large enough to utilize Tensor Cores optimally

A = torch.randn((matrix_size, matrix_size), device='cuda', dtype=torch.float32)
B = torch.randn((matrix_size, matrix_size), device='cuda', dtype=torch.float32)

def test():
    with torch.profiler.profile(
        activities=[torch.profiler.ProfilerActivity.CPU, torch.profiler.ProfilerActivity.CUDA],
        on_trace_ready=torch.profiler.tensorboard_trace_handler('./log'),
        record_shapes=True,
        with_stack=True
    ) as prof:
        # Run your training code here
        # Generate two large random matrices on the GPU with FP32 precision
        C_old = torch.zeros((matrix_size, matrix_size), device='cuda', dtype=torch.float32)
        for _ in range(100):
            C = torch.matmul(A, B)  # Matrix multiplication using TF32 on Tensor Cores

    print(prof.key_averages().table(sort_by="cuda_time_total"))
    return C


torch.backends.cuda.matmul.allow_tf32 = True  # Enable TF32 for matmul
torch.backends.cudnn.allow_tf32 = True       # Enable TF32 for cuDNN operations
C_tf32 = test()

torch.backends.cuda.matmul.allow_tf32 = False  # Enable TF32 for matmul
torch.backends.cudnn.allow_tf32 = False       # Enable TF32 for cuDNN operations
C_fp32 = test()

error = (C_tf32 - C_fp32).abs().max()
print(f"Max absolute error between TF32 and FP32: {error}")

```
I do observe a speedup and difference in the result matrix:
```
---------------------------  ------------  ------------  ------------  ------------  ------------  ------------
                       Name    Self CPU %      Self CPU   CPU total %     CPU total  CPU time avg    # of Calls
---------------------------  ------------  ------------  ------------  ------------  ------------  ------------
                aten::zeros         0.06%       1.711ms         0.77%      23.881ms      23.881ms             1
                aten::empty         0.03%       1.076ms         0.07%       2.163ms       2.163ms             1
      cudaStreamIsCapturing         0.00%       5.746us         0.00%       5.746us       1.436us             4
                 cudaMalloc         0.14%       4.196ms         0.14%       4.196ms     599.480us             7
                aten::zero_         0.06%       1.934ms         0.65%      20.008ms      20.008ms             1
                aten::fill_         0.03%     944.903us         0.58%      18.074ms      18.074ms             1
           cudaLaunchKernel         0.55%      17.129ms         0.55%      17.129ms      17.129ms             1
               aten::matmul         0.02%     632.346us         2.81%      86.796ms     867.963us           100
                   aten::mm         1.91%      58.923ms         2.79%      86.164ms     861.639us           100
                   cudaFree         0.10%       3.219ms         0.10%       3.219ms       3.219ms             1
     cudaDeviceGetAttribute         0.00%      25.599us         0.00%      25.599us       0.217us           118
    cudaGetDriverEntryPoint         0.00%       0.879us         0.00%       0.879us       0.440us             2
       cudaGetSymbolAddress         0.63%      19.461ms         0.63%      19.461ms      19.461ms             1
             cuLaunchKernel         0.05%       1.419ms         0.05%       1.419ms      14.191us           100
      cudaDeviceSynchronize        96.42%        2.982s        96.42%        2.982s        2.982s             1
---------------------------  ------------  ------------  ------------  ------------  ------------  ------------
Self CPU time total: 3.093s

--------------------------  ------------  ------------  ------------  ------------  ------------  ------------
                      Name    Self CPU %      Self CPU   CPU total %     CPU total  CPU time avg    # of Calls
--------------------------  ------------  ------------  ------------  ------------  ------------  ------------
               aten::zeros         0.00%      33.837us         0.03%       1.406ms       1.406ms             1
               aten::empty         0.02%       1.304ms         0.02%       1.304ms       1.304ms             1
               aten::zero_         0.00%       6.321us         0.00%      67.731us      67.731us             1
               aten::fill_         0.00%      20.899us         0.00%      61.410us      61.410us             1
          cudaLaunchKernel         0.00%      40.511us         0.00%      40.511us      40.511us             1
              aten::matmul         0.00%     220.170us         0.17%       9.472ms      94.718us           100
                  aten::mm         0.09%       4.829ms         0.17%       9.252ms      92.516us           100
    cudaDeviceGetAttribute         0.00%      31.641us         0.00%      31.641us       0.316us           100
           cudaMemsetAsync         0.03%       1.775ms         0.03%       1.775ms      17.752us           100
            cuLaunchKernel         0.02%       1.246ms         0.02%       1.246ms      12.456us           100
     cudaStreamIsCapturing         0.00%       2.410us         0.00%       2.410us       2.410us             1
                cudaMalloc         0.03%       1.367ms         0.03%       1.367ms       1.367ms             1
     cudaDeviceSynchronize        99.80%        5.408s        99.80%        5.408s        5.408s             1
--------------------------  ------------  ------------  ------------  ------------  ------------  ------------
Self CPU time total: 5.419s

Max absolute error between TF32 and FP32: 0.15386199951171875
```

## Automatic Mixed Precision package
This is the easiest way to leverage tensor core, to allow some ops to [automatically run in lower precision](https://pytorch.org/docs/stable/amp.html).
I will use a standard [tutorial](https://pytorch.org/tutorials/beginner/basics/quickstart_tutorial.html) for testing. 

Include this to import `amp`
```python
from torch.amp import autocast, GradScaler
```
And modify the following inference and backprop code
```
scaler = GradScaler('cuda')
...
    with autocast('cuda'):  # Enable mixed precision
        pred = model(data)
        loss = loss_fn(pred, y)

    # Backpropagation
    scaler.scale(loss).backward()  # Scale the loss for stability
    scaler.step(optimizer)
    scaler.update()
...
```
