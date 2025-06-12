# Project Mavlink

<!-- Please refer to https://aerostack2.github.io/_02_examples/pixhawk/project_px4_vision/index.html for more information. -->

## Installation

You need to clone and build [as2_platform_mavlink](https://github.com/aerostack2/as2_platform_mavlink.git).
<!-- refer to https://aerostack2.github.io/_03_aerial_platforms/_pixhawk/index.html for more information. -->

To install this project, clone the repository:

```bash
git clone https://github.com/aerostack2/project_mavlink.git
```

To start using this project, please go to the root folder of the project.

## Execution

### 0. Pre-requisites
For this drones, aerostack2 nodes must be launched in the onboard computer of each drone, and the ground station can be launched in both the onboard computer of the drone or in an external computer.

For **simulation**, we use a docker image for launch Gazebo Garden with the Pixhawk SITL. You need to build it with the following command:

```bash
cd sitl_config/docker && ./docker_build.bash
```

Then, configure the simulation in the *sitl_config/world.yaml* file. You can change the world, the number of drones, the initial position, etc.

Finally, you need to launch the simulation with the following command:

```bash
./launch_sitl.bash
```

At the execution ends, close tmux sessions with the following command:

```bash
./stop_tmuxinator_sitl.bash
```

### 1. Launch aerostack2 nodes for each drone

To launch aerostack2 nodes for each drone, execute once the following command:

```bash
./launch_as2.bash -n drone0
```

Re-launch the command for each drone you want to launch, changing the drone namespace with the flag `-n`.

The flags for the components launcher are:

- **-n**: select drone namespace to launch. Default is 'drone0'
- **-c**: motion controller plugin (pid_speed_controller, differential_flatness_controller), choices: [pid, df]. Default: pid
- **-r**: record rosbag. Default not launch
- **-g**: launch using gnome-terminal instead of tmux. Default not set

### 2. Launch aerostack2 nodes for the ground station
To launch aerostack2 nodes for the ground station, execute once the following command:

```bash
./launch_ground_station.bash -n drone0
```

The flags for the components launcher are:

- **-n**: drone namespaces, comma separated. Default is 'drone0'
- **-t**: launch keyboard teleoperation. Default not launch
- **-v**: open rviz. Default not launch
- **-m**: launch mocap4ros2. Default not launch
- **-r**: record rosbag. Default not launch
- **-g**: launch using gnome-terminal instead of tmux. Default not set

### 3. Launch a mission
There are several missions that can be executed:

- **AS2 keyboard teleoperation control**: You can use the keyboard teleoperation launched with the ground station, using the flag `-t`:
  ```bash
  ./launch_ground_station.bash -t
  ```
- **AS2 Python API single drone mission**: You can execute a mission that used AS2 Python API, launching the mission with:
  ```bash
  python3 mission.py -n drone0
  ```
- **AS2 Python API single drone mission using GPS**: You can execute a mission that used AS2 Python API with GPS, launching the mission with:
  ```bash
  python3 mission_gps.py -n drone0
  ```
- **AS2 Mission Interpreter single drone mission**: You can execute a mission that used AS2 Mission Interpreter, launching the mission with:
  ```bash
  python3 mission_interpreter.py -n drone0
  ```
- **AS2 Behavior Trees single drone mission**: You can execute a mission that used AS2 Behavior Trees, launching the mission with:
  ```bash
  python3 mission_behavior_tree.py -n drone0
  ```

### 4. End the execution

If you are using tmux, you can end the execution with the following command:

- **End the execution of all nodes**:
  ```bash
  ./stop_tmuxinator_as2.bash drone0
  ```
- **End the execution of all nodes of the ground station**:
  ```bash
  ./stop_tmuxinator_ground_station.bash
  ```
- **End the execution of both**:
  ```bash
  ./stop_tmuxinator.bash drone0
  ```

You can force the end of all tmux sessions with the command:
```bash
tmux kill-server
```

If you are using gnome-terminal, you can end the execution by closing the terminal.

## Developers guide

All projects in aerostack2 are structured in the same way. The project is divided into the following directories:

- **tmuxinator**: Contains the tmuxinator launch file, which is used to launch all aerostack2 nodes.
  - **aerostack2.yaml**: Tmuxinator launch file for each drone. The list of nodes to be launched is defined here.
  - **ground_station.yaml**: Tmuxinator launch file for the ground station. The list of nodes to be launched is defined here.
- **config**: Contains the configuration files for the launchers of the nodes in the drones.
- **config_ground_station**: Contains the configuration files for the launchers of the nodes in the ground station.
- **launch_as2.bash**: Script to launch nodes defined in *tmuxinator/aerostack2.yaml*.
- **launch_ground_station.bash**: Script to launch nodes defined in *tmuxinator/ground_station.yaml*.
- **mission_\*.py**: Differents python mission files that can be executed.
- **stop_tmuxinator_as2.bash**: Script to stop all nodes launched by *launch_as2.bash*.
- **stop_tmuxinator_ground_station.bash**: Script to stop all nodes launched by *launch_ground_station.bash*.
- **stop_tmuxinator.bash**: Script to stop all nodes launched by *launch_as2.bash* and *launch_ground_station.bash*.
- **rosbag/record_rosbag.bash**: Script to record a rosbag. Can be modified to record only the topics that are needed.
- **trees\***: Contains the behavior trees that can be executed. They can be selected in the *aerostack2.yaml* file.
- **utils**: Contains utils scripts for launchers.

Both python and bash scripts have a help message that can be displayed by running the script with the `-h` option. For example, `./launch_as2.bash -h` will display the help message for the `launch_as2.bash` script.

**Note**: For knowing all parameters for each launch, you can execute the following command:

```bash
ros2 launch my_package my_launch.py -s
```

Also, you can see them in the default config file of the package, in the *config* folder. If you want to modify the default parameters, you can add the parameter to the config file.