# `CUDA 12.0`, `Pytorch 2.1.0`の組み合わせにおける`__nvJitLinkAddData_12_1`が見つからないエラーと回避方法

![](https://raw.githubusercontent.com/yKesamaru/cuda120_torch_121_error/master/assets/eye_catch.png)

## はじめに
`CUDA 12.0`, `Pytorch 2.1.0`の組み合わせにおいて、以下のようなエラーが発生し、Pytorchが使用できない事があります。

https://discuss.pytorch.org/t/pytorch-for-cuda-12/169447

https://github.com/pytorch/pytorch/issues/111469

自身の備忘録ですが、本家Issue以外に情報がなさそうだったので共有します。

### エラー出力
```bash
$ python
Python 3.8.10 (default, May 26 2023, 14:05:08) 
[GCC 9.4.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import torch
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/home/user/bin/lib/python3.8/site-packages/torch/__init__.py", line 235, in <module>
    from torch._C import *  # noqa: F403
ImportError: /home/user/bin/lib/python3.8/site-packages/torch/lib/../../nvidia/cusparse/lib/libcusparse.so.12: undefined symbol: __nvJitLinkAddData_12_1, version libnvJitLink.so.12
```

`__nvJitLinkAddData_12_1`が見つからないというエラーです。

試したのはPython 3.8.10ですが、Python 3.10でも同様の問題が発生するようです。

## 結論
`libcusparse.so.12`のパスを`LD_LIBRARY_PATH`に追加することで、この問題を回避できます。
```bash
export LD_LIBRARY_PATH=/home/user/bin/lib64/python3.8/site-packages/nvidia/nvjitlink/lib:$LD_LIBRARY_PATH
```

## 環境
最後にわたしの現環境を示しておきますが、`CUDA 12.0`, `Pytorch 2.1.0`の組み合わせであれば発生する問題のようです。
一応参考までに。

```bash
(GFPGAN) 
$ nvcc --version
nvcc: NVIDIA (R) Cuda compiler driver
Copyright (c) 2005-2023 NVIDIA Corporation
Built on Fri_Jan__6_16:45:21_PST_2023
Cuda compilation tools, release 12.0, V12.0.140
Build cuda_12.0.r12.0/compiler.32267302_0
(GFPGAN) 
$ nvidia-smi
Sun Nov  5 16:10:58 2023       
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 525.85.12    Driver Version: 525.85.12    CUDA Version: 12.0     |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|                               |                      |               MIG M. |
 
$ . bin/activate
(GFPGAN) 
$ python -V
Python 3.8.10

(GFPGAN) 
$ pip list | grep torch
torch                    2.1.0
torchvision              0.16.0
```

なんらかのアップデートで、いつの間にかこのエラーは消えるかもしれません。

以上です。ありがとうございました。
