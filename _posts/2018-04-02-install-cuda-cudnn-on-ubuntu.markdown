---
layout: post
title:  "Ubuntu에 NVIDIA CUDA Toolkit + NVIDIA cuDNN 설치하기"
date:   2018-04-02 23:39:00 +0900
categories: mldl
---
Tensorflow 또는 Keras 등의 도구는 CPU만으로도 실행이 가능하지만, 빠른 속도의 연산을 위해서는 GPU를 사용해야한다. 이런 도구들이 GPU를 활용할 수 있도록 하려면 GPU의 명령어를 사용할 수 있도록 하는 Library? Driver?를 설치해야 한다.

한 시간 동안의 삽질을 통해 얻은 Ubuntu 16.04 LTS + Tensorflow 1.6을 사용할 때 권장하는 각각의 버전은 아래와 같다.

* NVIDIA CUDA Toolkit 9.0
* NVIDIA cuDNN 7.0.5 for CUDA 9.0

현재 시점으로 CUDA Toolkit은 9.1이 최신 버전이고 cuDNN은 7.1.2가 최신인데 무턱대고 최신버전을 설치했다가는 아래와 같은 오류를 만나게 되더라. 최신 버전은 다 좋은 줄 알았는데 그게 아니었다. 자, 이제 신나는 좌충우돌 설치기를 공유한다.

### 신나는 좌충우돌 설치기

PC엔 NVIDIA Geforce GTX 960이 설치되어있었다. 그럼 당연히 GPU를 써봐야지! 하고 `tensorflow-gpu`를 설치했다.

```sh
$ pip3 install tensorflow-gpu
```

뭔가 주르륵 설치가 되었고, 잘 설치되었는지 확인하기 위해서 python3 interpreter를 실행한 뒤 `import tensorflow`를 쳤더니 오류를 뱉었다.

```
ImportError: libcublas.so.9.0: cannot open shared object file: No such file or directory


Failed to load the native TensorFlow runtime.

See https://www.tensorflow.org/install/install_sources#common_installation_problems

for some common reasons and solutions.  Include the entire stack trace
above this error message when asking for help.
```

뭐지? 왜 안되지? stackoverflow에 물어보니까 CUDA를 설치하라고 한다. google에서 CUDA를 찾아보니 NVIDIA 홈페이지로 연결된다.
Linux > x86_64 > Ubuntu > 16.04 > dev(network) 를 차례로 선택하면 아래와 같이 다운로드 링크와 함께 설치방법이 보인다.

```
$ sudo dpkg -i cuda-repo-ubuntu1604_9.1.85-1_amd64.deb
$ sudo apt-key adv --fetch-keys http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/7fa2af80.pub
$ sudo apt-get update
$ sudo apt-get install cuda
```

순서대로 다 따라했다. 다시 python prompt에 `import tensorflow`를 쳐 봤는데 똑같은 오류를 뱉는다. -_- 왜이래;;;

stackoverflow와 google의 바다를 항해하고 나서야 답을 찾았는데, Tensorflow 1.6은 CUDA Toolkit 9.0을 사용해서 빌드되었기 때문에 발생하는 오류였다. 설치된 버전은 9.1이므로 다운그레이드 해야했다.

```
$ sudo apt-get autoremove --purge cuda
$ sudo apt-get install cuda-9-0
```

이젠 잘 되겠지...

다시 한 번 python prompt에 `import tensorflow`를 쳤더니 잠시 멈췄다가 아무 오류도 뜨지 않았다. 이제 성공인가?

...는 아니었다. 미리 다운받아 둔 tensorflow 예제를 돌려보려고 실행했더니 잘 돌아가나 싶더니 또 다른 오류를 뱉는다.

```
ImportError: libcudnn.so.7: cannot open shared object file: No such file or directory


Failed to load the native TensorFlow runtime.

See https://www.tensorflow.org/install/install_sources#common_installation_problems

for some common reasons and solutions.  Include the entire stack trace
above this error message when asking for help.
```

이건 또 뭔가... 다시 stackoverflow를 찾았다. cuDNN이라는걸 설치해야한단다. 구글에 cuDNN을 검색했다. 다시 NVIDIA 홈페이지로 연결된다. ㅎㅎ 반복되는 방문...

cuDNN을 설치하려면 NVIDIA 홈페이지에 가입을 해야했다. 가입을 하고 나서 최신버전! cuDNN 7.1.2 for CUDA 9.0 Runtime과 Developer Library를 다운로드 받아서 dpkg로 설치했다.

```
$ sudo dpkg -i libcudnn7_7.1.2.21-1+cuda9.0_amd64.deb
$ sudo dpkg -i libcudnn7-dev_7.1.2.21-1+cuda9.0_amd64.deb
```

자... 다시 예제를 실행해보자. 또 안된다... 이번엔 다른 오류를 뱉었다.

```
2018-04-02 22:39:23.193493: E tensorflow/stream_executor/cuda/cuda_dnn.cc:378] Loaded runtime CuDNN library: 7102 (compatibility version 7100) but source was compiled with 7004 (compatibility version 7000).  If using a binary install, upgrade your CuDNN library to match.  If building from sources, make sure the library loaded at runtime matches a compatible version specified during compile configuration.
2018-04-02 22:39:23.194004: F tensorflow/core/kernels/conv_ops.cc:717] Check failed: stream->parent()->GetConvolveAlgorithms( conv_parameters.ShouldIncludeWinogradNonfusedAlgo<T>(), &algorithms)
```

다시 stackoverflow에 물어봤다. -_- 이번엔 cuDNN 버전이 너무 높단다... 7.0.x를 설치해야 한다고 한다. 그래서 다시 홈페이지에 들어가서 7.0.5를 다운로드 받고나서 7.1.2를 지우고 다운그레이드했다.

```
$ sudo dpkg -i libcudnn7_7.0.5.15-1+cuda9.0_amd64.deb
$ sudo dpkg -i libcudnn7-dev_7.0.5.15-1+cuda9.0_amd64.deb
```

자... 다시 예제를 실행해보자. 아무 오류없이 잘 실행된다. 버전 의존성이 예민한 도구라는 생각이 들었다.

`nvidia-smi` 명령어로 GPU 사용률을 보니 GPU를 잘 사용하면서 돌고있었다.

```
Mon Apr  2 23:02:57 2018
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 390.30                 Driver Version: 390.30                    |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|===============================+======================+======================|
|   0  GeForce GTX 960     Off  | 00000000:01:00.0  On |                  N/A |
| 34%   58C    P2    63W / 130W |   3864MiB /  4040MiB |     69%      Default |
+-------------------------------+----------------------+----------------------+

+-----------------------------------------------------------------------------+
| Processes:                                                       GPU Memory |
|  GPU       PID   Type   Process name                             Usage      |
|=============================================================================|
|    0      1238      G   /usr/lib/xorg/Xorg                            15MiB |
|    0      1406      G   /usr/lib/xorg/Xorg                            92MiB |
|    0      1743      G   /usr/bin/gnome-shell                         130MiB |
|    0      9061      G   ...-token=BE31F4D9E02865736DA95A4B4B25A132    57MiB |
|    0     15514      C   python3                                     3521MiB |
+-----------------------------------------------------------------------------+
```

