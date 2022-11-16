# Scan 'N Plan Workshop

Framework for developing and operating simple Scan 'N Plan applications in which a robot:

- Executes a scan path with a depth camera
- Reconstructs the surface of the objects observed by the camera
- Plans a tool path on the reconstructed surface
- Plans robot motions to execute the tool path
- Executes the process path

![Block diagram](docs/block_diagram.png)

## Tool path planning

See also [snp_tpp/README.md](snp_tpp/README.md)

As seen in the diagram,  uses the [GenerateToolPaths.srv](snp_msgs/srv/GenerateToolPaths.srv) service to wrap noether. Served by  the snp_app in [snp_tpp/src/tpp_widget.cpp](snp_tpp/src/tpp_widget.cpp#L77), which uses `noether::ToolPathPlannerPipeline` with the params defined in the gui. The pipeline feature of noether is not very well documented, only in [noether/noether_tpp/README.md](../noether/noether_tpp/README.md).

## Generate Motion Planning

As seen in the diagram,  uses the [GenerateMotionPlan.srv](snp_msgs/srv/GenerateMotionPlan.srv) service to wrap tesseract. Served by snp_motion_planner in [snp_motion_planning/src/planning_server.cpp](snp_motion_planning/src/planning_server.cpp#L282). This in turn calls a `tesseract_planning::ProcessPlanningServer` with a `tesseract_planning::CompositeInstruction` that basically iterates through all rasters with cartesian linear moves and goes between them with joint moves. Under the hood, tesseract uses trajopt and descartes to optimize this trajectory.

## Build Setup

1. Install the prerequisite packages:
    - `taskflow` (from the ROS-I PPA)

      ```bash
      sudo add-apt-repository ppa:ros-industrial/ppa
      sudo apt-get update
      sudo apt-get install taskflow
      ```

2. Install the ROS2 dependencies

    ```bash
    cd <snpd_workspace>
    vcs import < src/scan_n_plan_workshop/dependencies_tesseract.repos
    vcs import < src/scan_n_plan_workshop/dependencies.repos
    rosdep install --from-paths src --ignore-src -r -y
    ```

3. Follow the ROS2 build setup instructions for the application-specific implementations
    - Alternatively, add `COLCON_IGNORE` files to the application-specific implementation packages and skip their builds

## Build

```bash
colcon build --cmake-args -DTESSERACT_BUILD_FCL=OFF
```

## Application-specific Implementations

- [Automate 2022](snp_automate_2022)
