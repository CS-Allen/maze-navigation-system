# 自主迷宫导航 Turtlebot3 仿真系统

## 项目简述

本项目实现了一个基于 ROS2 和 Gazebo 模拟器的 Turtlebot3 机器人自主迷宫导航系统。系统使用 A* 算法进行路径规划，能够让机器人在复杂的迷宫环境中自主导航到目标位置。

主要功能：
- Gazebo 仿真环境中的迷宫构建与渲染
- 基于 A* 算法的路径规划
- Turtlebot3 机器人的自主导航
- RViz2 中的实时地图可视化
- 导航状态和估计到达时间的实时反馈

## 配置环境

### 系统要求
- Ubuntu 22.04 或更高版本
- ROS2 Humble Hawksbill
- Python 3.10 或更高版本

### 依赖包安装

1. **安装 ROS2 Humble**
   请参考 [ROS2 官方安装指南](https://docs.ros.org/en/humble/Installation/Ubuntu-Install-Debians.html) 安装 ROS2 Humble。

2. **安装必要的 ROS2 包**
   ```bash
   sudo apt update
   sudo apt install -y \
     ros-humble-turtlebot3-gazebo \
     ros-humble-slam-toolbox \
     ros-humble-navigation2 \
     ros-humble-nav2-bringup \
     ros-humble-rviz2
   ```

3. **安装 Python 依赖**
   ```bash
   pip3 install numpy
   ```

4. **设置 Turtlebot3 模型环境变量**
   ```bash
   echo "export TURTLEBOT3_MODEL=waffle" >> ~/.bashrc
   source ~/.bashrc
   ```

## 核心算法

### A* 路径规划算法

本项目使用 Navigation2 栈中的 A* 算法实现路径规划。A* 算法是一种高效的路径搜索算法，通过以下步骤工作：

1. **启发式搜索**：使用曼哈顿距离作为启发函数，估计从当前位置到目标位置的代价
2. **代价计算**：综合考虑路径长度和启发函数值，选择最优路径
3. **路径优化**：生成平滑的导航路径

### 导航系统架构

1. **地图构建**：使用 SLAM Toolbox 生成环境地图
2. **定位**：通过 AMCL (Adaptive Monte Carlo Localization) 实现机器人定位
3. **路径规划**：使用 A* 算法计算从起点到目标的最优路径
4. **运动控制**：通过控制器执行路径，实现机器人的自主导航

## 项目构建流程

### 1. 创建工作空间

```bash
mkdir -p ~/ros2_ws/src
cd ~/ros2_ws
```

### 2. 克隆项目代码

```bash
cd src
git clone https://github.com/CS-Allen/maze-navigation-system.git
mv maze-navigation-system maze_navigation_system
```

### 3. 构建项目

```bash
cd ~/ros2_ws
colcon build --packages-select maze_navigation_system
```

### 4. 启动仿真环境

打开第一个终端，运行：

```bash
cd ~/ros2_ws
source install/setup.bash
ros2 launch maze_navigation_system tb3_maze_navigation.launch.py
```

这将启动：
- Gazebo 模拟器（包含迷宫环境和 Turtlebot3 机器人）
- RViz2（用于可视化地图和机器人状态）
- Navigation2 导航系统

### 5. 启动自主导航

打开第二个终端，运行：

```bash
cd ~/ros2_ws
source install/setup.bash
ros2 run maze_navigation_system maze_solver.py
```

### 6. 观察导航过程

- **Gazebo 窗口**：可以看到 Turtlebot3 机器人在迷宫中移动
- **RViz2 窗口**：可以看到机器人的路径规划和实时位置
- **终端输出**：显示估计到达时间和导航状态

## 项目结构

```
maze_navigation_system/
├── src/
│   └── maze_navigation_system/
│       ├── config/             # 配置文件
│       │   ├── maze_map.yaml   # 迷宫地图配置
│       │   └── tb3_nav_params.yaml  # 导航参数配置
│       ├── launch/             # 启动文件
│       │   └── tb3_maze_navigation.launch.py  # 主启动文件
│       ├── script/             # Python 脚本
│       │   ├── maze_solver.py  # 迷宫导航主脚本
│       │   ├── entity_spawner.py  # 实体生成脚本
│       │   └── occupancy_grid_pub.py  # 占据栅格发布脚本
│       └── worlds/             # 世界文件
│           └── tb3_maze_world/  # 迷宫世界模型
├── build/                      # 构建目录
└── install/                    # 安装目录
```

## 核心文件说明

1. **tb3_maze_navigation.launch.py**：主启动文件，负责启动 Gazebo、RViz2 和导航系统
2. **maze_solver.py**：核心导航脚本，设置初始位置和目标位置，启动导航任务
3. **entity_spawner.py**：用于在 Gazebo 中生成迷宫模型
4. **occupancy_grid_pub.py**：发布占据栅格地图信息

## 故障排除

### 常见问题

1. **缺少 numpy 库**
   ```bash
   pip3 install numpy
   ```

2. **Gazebo 启动失败**
   检查是否正确安装了 Gazebo 和 Turtlebot3 相关包

3. **导航系统未就绪**
   确保地图正确加载，机器人定位成功

4. **路径规划失败**
   检查目标位置是否在可行区域内，是否有障碍物阻挡

## 扩展功能

- **添加新的迷宫环境**：修改 `worlds/tb3_maze_world` 目录下的模型文件
- **调整导航参数**：修改 `config/tb3_nav_params.yaml` 文件中的导航参数
- **实现更复杂的导航任务**：扩展 `maze_solver.py` 脚本，添加多目标点导航

## 参考资料

- [ROS2 官方文档](https://docs.ros.org/en/humble/)
- [Navigation2 文档](https://navigation.ros.org/)
- [Turtlebot3 官方文档](https://emanual.robotis.com/docs/en/platform/turtlebot3/overview/)
- [A* 算法详解](https://en.wikipedia.org/wiki/A*_search_algorithm)

## 许可证

本项目基于 MIT 许可证开源。
