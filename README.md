# Voxel-fast-lio2

**Abstract**;

This project was proposed to address the issues inherent in the basic fastlio2 algorithm. 
The algorithm resolves computational cost problems by incorporating voxelization and pass-through filters into the fundamental fastlio2. 
In the actual experiment, utilizing the alpha prime LiDAR in an outdoor environment, it was observed that the algorithm could not sustain beyond 1km, leading to a forced CPU shutdown.
However, the proposed method successfully performs flawless map generation during a 3.2km drive. 
This achievement underscores its exceptional potential for future outdoor map generation algorithm designs.


**Related papers**: 
FAST-LIO2: Fast Direct LiDAR-inertial Odometry](doc/Fast_LIO_2.pdf)


### 2.2 Build from source
Clone the repository and catkin_make:

```
    cd ~/$A_ROS_DIR$/src
    git clone https://github.com/joonhyeok1002/Voxel-fast-lio2.git
    cd FAST_LIO
    git submodule update --init
    cd ../..
    catkin_make
    source devel/setup.bash
```
- Remember to source the livox_ros_driver before build (follow 1.3 **livox_ros_driver**)
- If you want to use a custom build of PCL, add the following line to ~/.bashrc
```export PCL_ROOT={CUSTOM_PCL_PATH}```

## 3. Preparation
Noted:

A. Please make sure the IMU and LiDAR are **Synchronized**, that's important.

B. The warning message "Failed to find match for field 'time'." means the timestamps of each LiDAR points are missed in the rosbag file. That is important for the forward propagation and backwark propagation.

C. We recommend to set the **extrinsic_est_en** to false if the extrinsic is give. As for the extrinsic initiallization, please refer to our recent work: [**Robust Real-time LiDAR-inertial Initialization**](https://github.com/hku-mars/LiDAR_IMU_Init).

### 3.3 For Velodyne or Ouster (Velodyne as an example)

Step A: Setup before run

Edit ``` config/velodyne.yaml ``` to set the below parameters:

1. LiDAR point cloud topic name: ``` lid_topic ```
2. IMU topic name: ``` imu_topic ``` (both internal and external, 6-aixes or 9-axies are fine)
3. Set the parameter ```timestamp_unit``` based on the unit of **time** (Velodyne) or **t** (Ouster) field in PoindCloud2 rostopic
4. Line number (we tested 16, 32 and 64 line, but not tested 128 or above): ``` scan_line ```
5. Translational extrinsic: ``` extrinsic_T ```
6. Rotational extrinsic: ``` extrinsic_R ``` (only support rotation matrix)
- The extrinsic parameters in FAST-LIO is defined as the LiDAR's pose (position and rotation matrix) in IMU body frame (i.e. the IMU is the base frame).

Step B: Run below
```
    cd ~/$Voxel-fast-lio2$
    source devel/setup.bash
    roslaunch pcl_tester voxel_grid_filtering.launch
    roslaunch fast_lio mapping_velodyne.launch
```

Step C: Run LiDAR's ros driver or play rosbag.
```
    rosbag play xxx-your-bagfile.bag
```


