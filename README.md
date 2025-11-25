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




# 无人机启动步骤 
## 1.前置准备
下载NoMachine远程连接(Windows,Ubuntu均可)
**下载链接：** [NoMachine - Free Remote Desktop for Everybody](URL)

## 2.打开机载电脑连接网络（初次连接和更改网络需完成该步骤）：
### 2.1
使用原装电源给机载电脑供电，插入HDMI并连接显示器
### 2.2
按下开机键（某些机载设备上电自动开机），待设备开机进入桌面后连接WiFi，连接成功后查看IP地址(指令：ip a)
P.S如果遇到上电开机很久显示器还是没有显示，但是按钮风扇都有启动的情况，尝试以下操作：
a.在上电开机的状态下按下重置按钮，此时电脑会断电关机

![机载电脑开机](images/机载电脑开机.png)

b.拔出电源重新上电，按下开机键，等待一会

![重置](images/重置.png)

c.电脑成功显示，如多次尝试还是不显示，请联系售后人员

![电脑成功显示](images/电脑成功显示.png)

## 3.使用NoMachine远程桌面连接机载电脑
### 3.1
将无人机放进试飞区域
### 3.2
将机载电脑连接至指定路由器（如果使用动捕系统，则需要连接到同一个局域网中）
### 3.3
打开地面电脑（确保已经连接到同一局域网，如果可以有线连接请使用网线连接），确保地面电脑与机载电脑够相互ping通
### 3.4
根据软件提示连接机载电脑,**系统用户名：yundrone，密码：123**

## 4.接入动捕系统
### 4.1
将动捕球粘贴到无人机上，数量要求大于3颗，注意不要贴成对称形状.贴好动捕球的无人机放置与动捕环境中，要求无人机机头朝向与动捕坐标X轴对齐, 创建刚体，刚体朝向选择X轴
### 4.2
数据广播：检查VRPN数据广播的单位是否为米，错误的数据单位会引起程序错误。勾选VRPN广播，查看数据广播地址
### 4.3
将位置信息发送到无人机，程序如下：
- 打开终端 启动ros
 ``` bash
cd ~/Sunray
roscore
  ```
- 启动mavros连接无人机
``` bash
roslaunch sunray_uav_control sunray_mavros_exp.launch
  ```
- 运行外部定位模块
``` bash
roslaunch sunray_uav_control external_fusion.launch external_source:=3
  ```
## 5.运行示例程序
示例为脚本启动,在scripts_exp中打开终端后再输入指令（Sunray/scripts_exp/）
- 起飞悬停降落：该示例会使无人机自动解锁、起飞，并悬停一段时间后降落
``` bash
./demo_takeoff_hover_land.sh
  ```
- 绘制四边形：该示例会使无人机自动解锁、起飞，然后依次使用位置控制朝着四个目标点飞行形成四边形，然后回到原地降落 
``` bash
./demo_block_pos.sh
  ```
-  绘制圆形：该示例会使无人机自动解锁、起飞，然后使用水平速度+高度控制开始以圆形的轨迹飞行，在无人机定位精度稍差的时候绘制的轨迹将不是光滑的曲线
``` bash
./demo_circle.sh
  ```
- 绘制六边形：该示例会使无人机自动解锁、起飞，然后使用机体系位置控制的形式进行六边形轨迹的移动，最后回到原地位置 
``` bash
./demo_hexayon.sh
  ```



