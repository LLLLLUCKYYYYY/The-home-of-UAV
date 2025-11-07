# 无人机汇总

## 报错与解决

#### 报错1：
> Installing PX4 Python3 dependencies
Collecting argcomplete
  Using cached argcomplete-3.6.3-py3-none-any.whl (43 kB)
Collecting argparse>=1.2
  Using cached argparse-1.4.0-py2.py3-none-any.whl (23 kB)
Collecting cerberus
  Using cached Cerberus-1.3.7-py3-none-any.whl (30 kB)
ERROR: Could not find a version that satisfies the requirement coverage (from -r /home/zq/sunray_px4/Tools/setup/requirements.txt (line 4)) (from versions: none)
ERROR: No matching distribution found for coverage (from -r /home/zq/sunray_px4/Tools/setup/requirements.txt (line 4))

显示如上问题。
#### 解决1：
这是 PX4 的 Python 依赖安装问题，主要是 coverage 包找不到。让我们解决这个问题：
```
# 升级 pip
pip3 install --upgrade pip

# 更换为清华源
pip3 config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
```

#### 报错2：
>Installing PX4 Python3 dependencies
ERROR: Invalid requirement: 'matplotlib>=3.0.*': .* suffix can only be used with `==` or `!=` operators
    matplotlib>=3.0.*
              ~~~~~~^ (from line 11 of /home/zq/sunray_px4/Tools/setup/requirements.txt)
#### 解决2：
这个错误是因为 requirements.txt 中的版本语法不正确。matplotlib>=3.0.* 这种写法在较新版本的 pip 中不被支持。让我们修复这个问题：
```
# 修复版本语法
sed -i 's/matplotlib>=3.0\.\*/matplotlib>=3.0/g' /home/zq/sunray_px4/Tools/setup/requirements.txt

重新安装依赖
cd /home/zq/sunray_px4

# 使用国内镜像源安装
pip3 install -i https://pypi.tuna.tsinghua.edu.cn/simple -r Tools/setup/requirements.txt
```

#### 报错3：
出现如下错误
> Could not find a package configuration file provided by "octomap_msgs" with
  any of the following names:
    octomap_msgsConfig.cmake
    octomap_msgs-config.cmake
make: *** No targets specified and no makefile found.  Stop.
make: *** No targets specified and no makefile found.  Stop.
================================
[DEBUG] make exit code: 2
catkin构建失败: sunray_ugv_control (make failed with exit code: 2)
[错误] 模块构建失败: sunray_ugv_control
[失败] 18/21: sunray_ugv_control

#### 解决3：
```
sudo apt-get install -y \
ros-noetic-octomap-msgs \
ros-noetic-octomap-ros \
ros-noetic-octomap-server \
ros-noetic-octomap-rviz-plugins
```

#### 报错4：
![出现如下报错](/images/4.jpg)
#### 解决4：
仿真不需要，硬件需要

**硬件环境下：**

方案1：安装 RKNN Toolkit（如果你有 Rockchip 设备）
```
# 创建第三方库目录
mkdir -p ~/libs
cd ~/libs

# 下载 RKNN Toolkit（需要从官方获取）
# 由于版权原因，需要从 Rockchip 官网下载
# 或者使用 wget 下载（如果知道下载链接）
# wget https://github.com/rockchip-linux/rknn-toolkit2/archive/refs/tags/v1.5.2.tar.gz

# 解压并安装
tar -xzf rknn-toolkit2-*.tar.gz
cd rknn-toolkit2-*

# 安装 Python 包
pip3 install -r requirements.txt
pip3 install .

# 设置头文件路径
export RKNN_API_PATH=~/libs/rknn-toolkit2-*/runtime/RKNN-RT2/rknn-api/include
```
**仿真环境下：**

方案2：临时注释掉 NPU 相关代码
```
cd /home/zq/Sunray/External_Module/sunray_detection

# 备份文件
cp detection_libs/src/inference_backend/npu/rknn/rknn_runner.h detection_libs/src/inference_backend/npu/rknn/rknn_runner.h.bak

# 在文件开头添加禁用宏
echo -e "#ifndef DISABLE_NPU\n#define DISABLE_NPU\n#endif\n\n$(cat detection_libs/src/inference_backend/npu/rknn/rknn_runner.h)" > detection_libs/src/inference_backend/npu/rknn/rknn_runner.h
```

#### 报错5：
![出现如下报错](/images/5.jpg)
#### 解决5：
```
sudo apt-get install -y \
    libsfml-dev \
    libsfml-system2.5 \
    libsfml-window2.5 \
    libsfml-graphics2.5 \
    libsfml-audio2.5 \
    libsfml-network2.5
```

#### 报错6：
![出现如下报错](/images/6.jpg)

#### 解决6：
清理所有ros进程
```
pkill -f roscore
pkill -f roslaunch
pkill -f rosmaster
sleep 2
roscore
```
