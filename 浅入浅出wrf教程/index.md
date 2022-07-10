# 浅入浅出 WRF 教程


<!--more-->

> 猴子都能懂的 WRF 教程，简称猴懂 WRF。

{{<admonition tip " 提示">}}

如有问题欢迎[与我联系](//about/##与我联系)😀

{{</admonition>}}

## 在这之前

---

在安装 WRF(The Weather Research and Forecasting Model) 之前，请先确定你的**需求**。比如要使用 WRF 来预测 PM ，这就要求你应当安装 WRF-Chem 而不仅仅是 WRF 。

本文内容包含 WRF 及 WRF-Chem 的编译。 有关 WRF-Chem 运行的部分，请参考 [WRF-Chem User's Guide](https://ruc.noaa.gov/wrf/wrf-chem/Users_guide.pdf)。

在 WRF 官网有提供一个[非常友善的 WRF 编译教程](https://www2.mmm.ucar.edu/wrf/OnLineTutorial/compilation_tutorial.php)，但是该教程编译只包含 WRF 的部分，而且使用了 tcsh，与现在比较流行的 bash 可能会有一些出入。

有繁琐工作的地方就有自动化脚本，当然包括 WRF-Chem 的安装。这里也推荐我在Gitee上发现的一个[全自动安装脚本](https://gitee.com/xuejingkai/WRFCHEM-4.3-install-script-linux-64bit)，直接免除下面的所有工作，你仅仅需要根据自己的需求对脚本做一些简单的修改即可。

## 平台准备

---

WRF 模型仅能运行在 Linux 系统上。为此，你可能需要：

1. 一台安装了 Linux 系统的电脑
2. 带有 Linux 系统的虚拟机
3. WSL(Windows Subsystem Linux)

中的其中一种方式来使用 Linux 系统。如果你的操作系统是 Windows 10 或者 Windows 11，那么 WSL 可能是一个最佳的选择。WSL 的安装可以参考[微软官方的教程](https://docs.microsoft.com/zh-cn/windows/wsl/install)。本文中使用的系统为 Windows11 下的 WSL，安装的系统为 Ubuntu 20.04。

## 文件下载及环境准备

---

首先要准备 [WRF](https://github.com/wrf-model/WRF) 和 [WPS(WRF Pre-Processing System)](https://github.com/wrf-model/WPS) 的安装文件 ，这两个程序的最新版本可以在 GitHub 很容易的下载下来，现在你需要新建一个文件夹，把 WRF 和 WPS 放在同一文件夹内（这个文件夹将会成为你放置 WRF 和 WPS 程序的文件夹），并将它们解压。对于 zip 类型的压缩包，你可以使用 `unzip` 命令来解压缩；对于 tar.gz 类型的压缩包，你需要使用 `tar xzvf`  命令来解压缩。

接下来，你可能需要安装一些编译器。编译 WRF 需要三个编译器，分别是 `gfortran` 、 `cpp` 和 `gcc`。你可以分别使用命令 `which gfortran` 、 `which cpp` 和 `which gcc` 来检查你的环境中是否包含这三个编译器，对于包含编译器的环境，`which` 命令将会返回对应地址，如果 `which` 命令没有任何反应，你可能需要手动安装这些编译器。不同的操作系统可能会有所差异，对于 Ubuntu 来说，你只需要运行 `sudo apt-get install gfortran` 来安装 `gfortran` 编译器，其它缺失的编译器同理。

除了三个编译器之外，你可能还需要安装 `csh` 和 `perl` 。你同样可以通过 `sudo apt-get install` 命令来安装它们。

## 依赖库

---

在这里，你需要安装 5 个与 WRF 运行有关的库，分别是：

- [mpich-3.0.4](https://www2.mmm.ucar.edu/wrf/OnLineTutorial/compile_tutorial/tar_files/mpich-3.0.4.tar.gz)
- [netcdf-4.1.3](https://www2.mmm.ucar.edu/wrf/OnLineTutorial/compile_tutorial/tar_files/netcdf-4.1.3.tar.gz)
- [jasper-1.900.1](https://www2.mmm.ucar.edu/wrf/OnLineTutorial/compile_tutorial/tar_files/jasper-1.900.1.tar.gz)
- [libpng-1.2.50](https://www2.mmm.ucar.edu/wrf/OnLineTutorial/compile_tutorial/tar_files/libpng-1.2.50.tar.gz)
- [zlib-1.2.7](https://www2.mmm.ucar.edu/wrf/OnLineTutorial/compile_tutorial/tar_files/zlib-1.2.7.tar.gz)

你可以通过点击上述链接，下载库的源代码。现在进入到放置 WRF 和 WPS 的文件夹中，在该文件夹内新建名为 `Bulid_WRF` 的文件夹，并在该文件夹内新建名为 `LIBRARIES` 的文件夹。将这些程序在 `LIBRARIES` 文件夹中逐一解压。

### 添加环境变量

在进行以下的操作前，请先在终端（ bash ）中运行下面的代码来添加环境变量：

```bash
export DIR=$HOME/software/Build_WRF/LIBRARIES	# 请修改此条为你的 LIBRARIES 文件夹路径
export WRF_DIR=$HOME/software/WRF-4.3.3/		# 请修改此条为你解压 WRF 后文件夹的路径
export CC=gcc
export CXX=g++
export FC=gfortran
export FCFLAGS=-m64
export F77=gfortran
export FFLAGS=-m64
export JASPERLIB=$DIR/grib2/lib
export JASPERINC=$DIR/grib2/include
export LDFLAGS=-L$DIR/grib2/lib
export CPPFLAGS=-I$DIR/grib2/include
export PATH=$DIR/mpich/bin:$PATH
export PATH=$DIR/netcdf/bin:$PATH
export NETCDF=$DIR/netcdf
```

### mpich

mpich 是并行计算所需要的库，你可以运行以下的代码来安装：

```bash
cd mpich-3.0.4								# 进入解压后的 mpich-3.0.4 文件夹
./configure --prefix=$DIR/mpich
make
make install
```

### netCDF

netCDF 是编译 WRF 所需要的数据格式库，你可以通过运行以下的代码来安装：

```bash
cd netcdf-4.1.3								# 进入解压后的 netcdf-4.1.3 文件夹
./configure --prefix=$DIR/netcdf --disable-dap --disable-netcdf-4 --disable-shared
make
make install
```

### zlib

编译 WPS 所需的库，你可以通过运行以下的代码来安装：

```bash
cd zlib-1.2.7
./configure --prefix=$DIR/grib2
make
make install
```

### libpng

编译 WPS 所需的库，你可以通过运行以下的代码来安装：

```bash
cd libpng-1.2.50
./configure --prefix=$DIR/grib2
make
make install
```

### Jasper

编译 WPS 所需的库，你可以通过运行以下的代码来安装：

```bash
cd jasper-1.900.1
./configure --prefix=$DIR/grib2
make
make install
```

请按照上述顺序来安装以避免预料之外的错误。

### WRF-Chem

对 WRF-Chem 来说，这里还需要一些额外的步骤。对于 WRF4  以下的版本，你可能需要下载单独的 WRF-Chem 编译所需的文件，并将这些文件解压到你的 WRF 文件夹内的 chem 文件夹中。对于 WRF4 以后的版本，则不需要进行此操作。

为了编译 WRF-Chem，这里需要添加一些环境变量：

```bash
export WRF_EM_CORE=1
export WRF_NMM_CORE=0  
export WRF_CHEM=1
export WRF_KPP=1 
export YACC='/usr/bin/yacc -d' 
export FLEX=/usr/bin/flex
export FLEX_LIB_DIR=/usr/lib/x86_64-linux-gnu/ 
export KPP_HOME=$HOME/software/WRF-4.3.3/chem/KPP/kpp/kpp-2.1
export WRF_SRC_ROOT_DIR=$HOME/software/WRF-4.3.3
export PATH=$KPP_HOME/bin:$PATH
export SED=/usr/bin/sed
export WRFIO_NCD_LARGE_FILE_SUPPORT=1
```

然后安装两个额外的库：

```bash
sudo apt-get install yacc flex
```

## 编译 WRF

---

进入解压好的 WRF 文件夹，然后运行：

```bash
./configure
```

在这里你将会看到非常多不同的编译选项，一般选择 34 即可，这表示你使用 gcc 分布式并行编译 WRF。接下来，对于 nesting，直接选择默认选项即可。

接下来就可以对 WRF 进行编译，在此处你可以选择你想使用的 WRF 运行类型，有以下选项可以选择：

- `em_real` (三维真实情况)
- `em_quarter_ss `(三维理想状况)
- `em_b_wave` (三维理想状况)
- `em_les` (三维理想状况)
- `em_heldsuarez` (三维理想状况)
- `em_tropical_cyclone` (三维理想状况)
- `em_hill2d_x` (二维理想状况)
- `em_squall2d_x` (二维理想状况)
- `em_squall2d_y` (二维理想状况)
- `em_grav2d_x` (二维理想状况)
- `em_seabreeze2d_x` (二维理想状况)
- `em_scm_xy` (一维理想状况)

你可以根据自己的需求来选择编译哪个选项。一般应当选择 `em_real` 进行编译，以此为例，你应当运行：

```bash
./compile em_real
```

编译需要花费一段时间，对于 WRF，这可能会花费 30 分钟的时间，对于 WRF-Chem，编译可能需要 1 个小时。编译成功后，运行：

```bash
ls -s main/*.exe
```

根据你编译选项的不同，你将会看到数个 `.exe` 文件。对于 `em_real` 选项，这四个文件分别是：

- `wrf.exe`
- `real.exe`
- `ndown.exe`
- `tc.exe`

> ⚠ 如果你编译失败，需要重新开始编译，请在重新执行本步骤前执行 `./clean`。

## 编译 WPS

---

进入解压后的 WPS 文件夹，然后执行:

```bash
./configure
```

这里你依旧会看到不同的编译选项，一般来说，此处应当选择 3 号选项。请注意，这里的编译选项应当和编译 WRF 时的选项一致，尽管序号可能有所不同。接下来就可以对 WPS 进行编译，执行：

```bash
./compile
```

这可能会花费一段时间，执行成功后，输入  `ls -s *.exe`，将会看到三个 `.exe` 文件，分别是：`geogrid.exe`，`ungrib.exe` 和 `metgrid.exe`。

至此，所有的编译工作已经完成。如果有时间，我会考虑增加：

- [ ] WPS 的运行
- [ ] WRF 的运行
- [ ] wrfout 数据的处理

等内容。

---

> 作者: Blathers  
> https://blathers23.netlify.app/%E6%B5%85%E5%85%A5%E6%B5%85%E5%87%BAwrf%E6%95%99%E7%A8%8B/
