# Introduction

This repository cointains a simplified version of the Reinforcement Learning training environment developed by Ronja Gueldenring (which can be found [here](https://github.com/RGring/drl_local_planner_ros_stable_baselines)).

By following the instructions found in this document, you should be able to train a simple reactive agent to avoid obstacles, and is intended to serve as an introductory lesson to Reinforcement Learning. It is assumed that you have prior experience with ROS and have a workspace set up.

This document is by no means a complete guide on Reinforcement Learning with ROS, but rather a simple first contact, and should be taken as such.

# Installation

1. Standard ROS setup (Code has been tested with ROS-noetic on Ubuntu 20.04)

2. Install additional packages
    ```
    sudo apt-get update && sudo apt-get install -y \
    libopencv-dev \
    liblua5.2-dev \
    virtualenv \
    screen \
    python3-dev \
    ros-noetic-tf2-geometry-msgs \
    ros-noetic-navigation \
    ros-noetic-rviz 
    ```

3. Setup repository: 
    * First, lone this repository in your src-folder of your catkin workspace with:
    ```
    cd <path_to_catkin_ws>/src/
    git clone https://github.com/BerserkingIdiot/drl_local_planner_ros_stable_baselines.git
    ```
    * Then do the following:
    ```
    cd <path_to_catkin_ws>/src/drl_local_planner_ros_stable_baselines
    cp .rosinstall ../
    cd ..
    rosws update
    cd <path_to_catkin_ws>
    catkin_make -DCMAKE_BUILD_TYPE=Release
    ```
    (please install missing packages)

4. Setup virtual environment to be able to use python3 with ROS
   ```
    virtualenv <path_to_venv>/venv_p3 --python=python3
    source <path_to_venv>/venv_p3/bin/activate
    <path_to_venv>/venv_p3/bin/pip install \
        pyyaml \
        rospkg \
        catkin_pkg \
        exception \
        numpy \
        tensorflow \
        gym \
        pyquaternion \ 
        mpi4py \
        matplotlib
    cd <path_to_catkin_ws>/src/drl_local_planner_ros_stable_baselines/
    <path_to_venv>/venv_p3/bin/pip install -r requirements.txt
    cd <path_to_catkin_ws>/src/drl_local_planner_forks/stable_baselines/
    <path_to_venv>/venv_p3/bin/pip install -e <path_to_catkin_ws>/src/drl_local_planner_forks/stable-baselines/
    ```
5. Set system-relevant variables 
    * Modify all relevant paths in rl_bringup/config/path_config.ini

# Training and running your first agent:

In this section, you'll be able to train an agent with the default parameters, and subsequently run your agent to test its capabilities. Notice that even with the exact same parameters, there are slightly different outcomes from separate training runs!

Note: All commands from here on are formatted to be run from the root of your workspace folder, and every terminal is expected to have the ROS environment set up (sourced the setup.bash files).

1. Train agent
    * Open first terminal (roscore): 
    ```
    roscore
    ```
    * Open second terminal (simulation):
    ```
    roslaunch rl_bringup setup.launch ns:="sim1" rl_params:="rl_params_scan"
    ```
    * Open third terminal (Visualization):
     ```
    roslaunch rl_bringup rviz.launch ns:="sim1"
    ```
    * Open fourth terminal (DRL-agent):
     ```
    source <path_to_venv>/bin/activate 
    python src/drl_local_planner_ros_stable_baselines/rl_agent/scripts/train_scripts/train_ppo.py
    ```
2. Execute self-trained ppo-agent
    * Open first terminal: 
    ```
    roscore
    ```
    * Open second terminal: 
    ```
    roslaunch rl_bringup setup.launch ns:="sim1" rl_params:="rl_params_scan"
    ```
    * Open third terminal: 
    ```
    roslaunch rl_bringup rviz.launch ns:="sim1"
    ```
    * Open fourth terminal:
    ```
    source <path_to_venv>/venv_p3/bin/activate 
    python src/drl_local_planner_ros_stable_baselines/rl_agent/scripts/train_scripts/train_ppo.py ppo2_simple_reactive CNN1DPolicy_multi_input train 1 0 0 static 3
    ```
# Tuning and improving your agent:

By opening the train_ppo.py script (found in rl_agent/scripts/train_scripts/) and scrolling to the end, you can find the default parameters used by the model. Try changing the values of the following and see how it changes the learning process:
        
- gamma
- n_steps
- ent_coef
- learning_rate
- cliprange
- total_timesteps
    
Additionally, you can find the reward function being used in the reward_container.py script (found in rl_agent/src/rl_agent/env_utils/), currently in the form of _rew_func_19_. You can change some of the values of the different elements of the reward, such as the punishment for hitting obstacles and the reward for moving away from the starting position, and see how that changes what the agent learns during training.

# Feedback

Once you're finished with this introduction, please fill out the following form (select the Reinforcement Learning Introduction option):
https://forms.gle/Nfi6TKP4GBzeb3nG9
