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
