# Hydra in ROS Noetic
This repo uses Docker to set up and run the implementation at this link: https://github.com/MIT-SPARK/Hydra/tree/main?tab=readme-ov-file

Owners and collaborators of this repo are not claiming to have developed anything there, but are soley (at the time of this commit, anyway) responsible for developing everything needed to use the implementation in Docker. Containerizing this seemed useful since the developers state that it is tested on ROS Noetic and Ubuntu 20.04.

### Prerequisites
Install docker.

### Configure development environment
We first want to configure a development environment in which we can build the docker image. This will also create a docker volume that will mount to the workspace. After cloning this repo, navigate to `ros_hydra_in_docker`. To create the environment profile, do

```bash
./docker_scripts/create_environment_profile.sh
```

We now want to build the docker image. We could probably pull the docker image down from my public repository on docker hub, but I haven't tested that so just build it locally for now.

```bash
./docker_scripts/docker_build.sh
```

Navigate to the `ros_hydra_in_docker` directory. You should now be able to run the docker image using

```bash
./docker_scripts/docker_run.sh
```

### Build and run the example
Within the same terminal instance in which the docker container is running, source the ROS setup. First navigate to the workspace directory in the docker container.
```bash
cd /workspace
```
This workspace should contain the contents of the `ros_hydra_in_docker` directory.

Source noetic:
```bash
source /opt/ros/noetic/setup.bash
```
Initalize your catkin workspace and set build configs:
```bash
catkin init
catkin config -DCMAKE_BUILD_TYPE=Release
```
You can now clone hydra, initialize the submodules, and install ros dependencies:
```bash
cd src
git clone git@github.com:MIT-SPARK/Hydra.git hydra
vcs import . < hydra/install/hydra.rosinstall
rosdep update
rosdep install --from-paths . --ignore-src -r -y
```
During installation of ROS dependencies (the last command in the above list), it is possible the container will ask you for the password; it is "guest". You may also be prompted for other configuration, like keyboard layout. Just choose whatever you usually use.

Finally, build:
```bash
cd ..
catkin build
```

You can then follow the instructions given at https://github.com/MIT-SPARK/Hydra/tree/main?tab=readme-ov-file for running the node. This will require that you download a test data set and run it using rosbag run.
