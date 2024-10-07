
最近在部署 `SyncTalk` 虚拟数字人项目时，需要安装很多依赖项，在执行到`pip install --no-index --no-cache-dir pytorch3d -f https://dl.fbaipublicfiles.com/pytorch3d/packaging/wheels/py38_cu113_pyt1121/download.html`这一句命令时，安装 PyTorch3D 失败，输出如下信息：



```
(synctalk) C:\SyncTalk>pip install --no-index --no-cache-dir pytorch3d -f https://dl.fbaipublicfiles.com/pytorch3d/packaging/wheels/py38_cu113_pyt1121/download.html
Looking in indexes: https://mirrors.tuna.tsinghua.edu.cn/pypi/web/simple, https://mirrors.tuna.tsinghua.edu.cn/pypi/web/simple
ERROR: Could not find a version that satisfies the requirement pytorch3d (from versions: none)
ERROR: No matching distribution found for pytorch3d

```

于是，我就想着克隆 `facebookresearch/pytorch3d` 官方仓库源码，自行通过本地编译的方式进行安装。


了解到 SyncTalk 虚拟数字人项目依赖的 Pytorch 版本是 1\.12\.1，CUDA 版本是 11\.3，PyTorch3D 版本是 0\.7\.2。


而编译 PyTorch3D 除了依赖 CUDA 之外，需要另外依赖 CUB 和 Visual Studio 2017 或 Visual Studio 2019。


根据 CUB 官方建议，CUDA 11\.3 对应的 CUB 版本是 1\.11\.0，对照表地址：`https://github.com/NVIDIA/cub`。


所以从 CUB 官方仓库 下载了 [1\.11\.0 版本压缩包](https://github.com)：[https://github.com/NVIDIA/cub/archive/refs/tags/1\.11\.0\.zip](https://github.com)，将其解压到任意路径，


我是解压到 `C:\Program Files\cub-1.11.0`，然后配置 `CUB_HOME` 系统环境变量，填的也是这个路径，不需要额外添加到 `PATH` 系统环境变量。


另外，也配置了 `CUDA_HOME` 这个系统环境变量，指向 `CUDA Toolkits 11.3` 的安装路径：`C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v11.3`，


以及向系统环境变量 `PATH` 追加了 `%CUDA_HOME%\bin;%CUDA_HOME%\libnvvp;`。


需要再次强调的是，这里配的是`系统环境变量`，不是`用户环境变量`。系统全局范围生效，而不是当前用户生效。


至于 Visual Studio 2017 或 Visual Studio 2019，我电脑只安装了 Visual Studio 2022，


而VS官方下载页已经不开放 VS2019 及更早版本的下载，即便是登录 Microsoft 账号跳转到订阅服务页面，普通订阅也获取不到可用的下载地址。


经过一番搜索，发现这几个永久下载链接还没失效，大家可以下载收藏：


* 最新版本 Microsoft C\+\+ 生成工具 \- Visual Studio：[https://visualstudio.microsoft.com/zh\-hans/visual\-cpp\-build\-tools/](https://github.com)
* Visual Studio 2022 Build Tools 下载：[https://aka.ms/vs/17/release/vs\_BuildTools.exe](https://github.com):[wgetCloud机场](https://tabijibiyori.org)
* Visual Studio 2019 Build Tools 下载：[https://aka.ms/vs/16/release/vs\_BuildTools.exe](https://github.com)
* Visual Studio 2017 Build Tools 下载：[https://aka.ms/vs/15/release/vs\_BuildTools.exe](https://github.com)


Visual Studio 生成工具 安装后，需要重启电脑。


解决了前置依赖条件和编译环境后，打开 `x64 Native Tools Command Prompt for VS 2017` 或 `x64 Native Tools Command Prompt for VS 2019`，执行以下命令，开始克隆 PyTorch3D 0\.7\.2 版本，同时进行编译和安装：



```
(synctalk) C:\SyncTalk>set DISTUTILS_USE_SDK=1
pip install "git+https://github.com/facebookresearch/pytorch3d.git@v0.7.2"

```

编译并且安装成功后，我们验证一下是否可用：



```
(synctalk) C:\SyncTalk>python -c "import torch; import pytorch3d; print('PyTorch version:', torch.__version__); print('PyTorch3D version:', pytorch3d.__version__)"
PyTorch version: 1.12.1+cu113
PyTorch3D version: 0.7.2

```

PS：`CUDA Toolkits 11.3` 的下载地址为：[https://developer.nvidia.com/cuda\-11\.3\.0\-download\-archive](https://github.com)


如果要编译和安装 PyTorch3D 最新稳定版，可以把 `@v0.7.2` 改为 `@stable`。


