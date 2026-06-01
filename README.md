# autonomy_stack_go2 fork

This repository is a fork of the original CMU Go2 autonomy stack:

<https://github.com/jizhang-cmu/autonomy_stack_go2>

This fork keeps the original project structure and adds a Humble-oriented setup for the Unitree Go2 L1 lidar workflow used here:

- L1 IMU calibration data is installed as `imu_calib_data.yaml` in the `calibrate_imu` package share directory.
- L1 IMU acceleration z-axis handling is fixed for this robot setup.
- Velocity estimation and SLAM are split into separate launch files.

## Build

```bash
colcon build --symlink-install --cmake-args -DCMAKE_BUILD_TYPE=Release
source install/setup.bash
```

If using an external computer with Unitree DDS, source the Unitree ROS2 setup script before launching nodes.

## IMU Calibration

Run calibration once for the L1 IMU:

```bash
ros2 run calibrate_imu calibrate_imu
```

The calibration result is loaded by `transform_sensors` from:

```bash
install/calibrate_imu/share/calibrate_imu/imu_calib_data.yaml
```

The default calibration file in this fork is:

```bash
src/utilities/calibrate_imu/imu_calib_data.yaml
```

## Velocity Estimation

Launch velocity estimation only:

```bash
ros2 launch point_lio_unilidar velocity_estimation.launch
```

Main output:

```bash
/sportmodestate_lin_vel
```

This launch disables SLAM point cloud publishing and RViz.

## SLAM

Launch SLAM with RViz and registered point cloud publishing:

```bash
ros2 launch point_lio_unilidar mapping_utlidar_slam.launch
```

Main outputs:

```bash
/registered_scan
/state_estimation
/Laser_map
```

Use `camera_init` as the RViz fixed frame to inspect the world-frame SLAM result.

## Notes

- `mapping_utlidar_slam.launch` disables velocity publishing.
- `velocity_estimation.launch` enables velocity publishing and disables scan publishing.
- The tested stable SLAM downsample settings use `filter_size_surf` and `filter_size_map` at `0.5`.
- For full original documentation, refer to the upstream repository linked above.
