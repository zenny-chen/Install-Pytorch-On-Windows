# Install-PyTorch-On-Windows
Install PyTorch from source on Windows

<br />

Firstly, follow **https://github.com/pytorch/pytorch** instructions to install [**Prerequisites**](https://github.com/pytorch/pytorch?tab=readme-ov-file#install-dependencies). On Windows, Python 3.9 or later, latest Visual Studio Community Edition, CMake are required. However, [**Anaconda**](https://www.anaconda.com/download) environment is recommended and can be easily installed on Windows via an executable installer.

Secondly, follow [**Install Dependencies**](https://github.com/pytorch/pytorch?tab=readme-ov-file#install-dependencies).

Then, follow [**Get the PyTorch Source**](https://github.com/pytorch/pytorch?tab=readme-ov-file#get-the-pytorch-source).

If you want to clone a specific version, "v2.5.1" for example, type the following command in the Anaconda commandline console:

```shell
git clone --recursive -b v2.5.1 https://github.com/pytorch/pytorch
```

Finally, follow [**Install PyTorch**](https://github.com/pytorch/pytorch?tab=readme-ov-file#install-pytorch) to build the source.

By default, only CPU mode is used. If you want to enable CUDA, export **`USE_CUDA`** environment variable explicitly. Refer [build_pytorch.bat](https://github.com/pytorch/pytorch/blob/main/.ci/pytorch/win-test-helpers/build_pytorch.bat) for detail.

```shell
set USE_CUDA=1
python setup.py develop
```

<br />

During building, there may be some errors generated from **`third_party/mimalloc`**:

> fatal error C1189: #error:  The C++ Standard Library forbids macroizing the keyword "bool". Enable warning C4005 to find the forbidden define.

This happens because the C source code is built with C++ compiler. Check **`pytorch/third_party/mimalloc/include/mimalloc.h`**, modify the following code:

```cpp
#include <stdbool.h>    // bool

#ifdef __cplusplus
extern "C" {
#endif
```

to:

```cpp
#ifdef __cplusplus
extern "C" {
#else
#include <stdbool.h>    // bool
#endif
```

<br />

The same as above, modify the source code in **`pytorch/third_party/XNNPACK/include/xnnpack.h`**:

```cpp
#include <stdbool.h>
#include <stddef.h>
#include <stdint.h>

#include <pthreadpool.h>

#ifdef __cplusplus
extern "C" {
#endif
```

to:

```cpp
#include <stddef.h>
#include <stdint.h>

#include <pthreadpool.h>

#ifdef __cplusplus
extern "C" {
#else
#include <stdbool.h>
#endif
```

<br />

There is another convenient way to avoid this compiling error problem. Copy the **`stdbool.h`** appended in this repository to **`pytorch/third_party/XNNPACK/include/`**, **`pytorch/third_party/mimalloc/include/`**, and **`pytorch/torch/csrc/`**.


