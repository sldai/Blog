## Ubuntu Reinforcement Learning Environment Installation

Running environment:
* dell XPS 9570
* Ubuntu 18.04 LTS
* GTX 1650
* cuda 10.0
* mujoco200
* conda python 3.7

First disable secure boot in bios, since it really complicates the installation of drivers.

---------

### Install cuda

Follow the instruction from [tensorflow gpu](https://www.tensorflow.org/install/gpu):

``` shell
# Add NVIDIA package repositories
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-repo-ubuntu1804_10.0.130-1_amd64.deb
sudo dpkg -i cuda-repo-ubuntu1804_10.0.130-1_amd64.deb
sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/7fa2af80.pub
sudo apt-get update
wget http://developer.download.nvidia.com/compute/machine-learning/repos/ubuntu1804/x86_64/nvidia-machine-learning-repo-ubuntu1804_1.0.0-1_amd64.deb
sudo apt install ./nvidia-machine-learning-repo-ubuntu1804_1.0.0-1_amd64.deb
sudo apt-get update

# Install NVIDIA driver
sudo apt-get install --no-install-recommends nvidia-driver-418
# Reboot. Check that GPUs are visible using the command: nvidia-smi

# Install development and runtime libraries (~4GB)
sudo apt-get install --no-install-recommends \
    cuda-10-0 \
    libcudnn7=7.6.2.24-1+cuda10.0  \
    libcudnn7-dev=7.6.2.24-1+cuda10.0


# Install TensorRT. Requires that libcudnn7 is installed above.
sudo apt-get install -y --no-install-recommends libnvinfer5=5.1.5-1+cuda10.0 \
    libnvinfer-dev=5.1.5-1+cuda10.0
```

------------

### Install mujoco

Get the [license](https://www.roboti.us/license.html). Download mujoco200 linux from [product](https://www.roboti.us/index.html).

``` shell
mkdir ~/.mujoco
unzip mujoco200_linux.zip -d ~/.mujoco
cd ~/.mujoco
mv mujoco200_linux mujoco200
```

Copy mjkey.txt to ~/.mujoco

``` shell
cp mjkey.txt ~/.mujoco
```

Add the environment variables to ~/.bashrc

``` shell
gedit ~/.bashrc
export LD_LIBRARY_PATH=${HOME}/.mujoco/mujoco200/bin${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
export MUJOCO_KEY_PATH=${HOME}/.mujoco${MUJOCO_KEY_PATH}
```

Test mujoco

``` shell
cp mjkey.txt ~/.mujoco/mujoco200/bin
cd ~/.mujoco/mujoco200/bin
./simulate ../model/humanoid.xml
```

----------

### Install mujoco-py

Create a virtual environment use Anaconda and activate the environment. 

``` shell
conda create --name mujoco --clone base
conda activate mujoco
```

Install libraries and mujoco-py

``` shell
sudo apt install libosmesa6-dev libgl1-mesa-glx libglfw3 
sudo ln -s /usr/lib/x86_64-linux-gnu/libGL.so.1 /usr/lib/x86_64-linux-gnu/libGL.so
pip install -U 'mujoco-py<2.1,>=2.0'
```

Install gym

``` shell
pip install gym
```

Test gym and mujoco-py, run the following python code

``` python
import gym
env = gym.make('Humanoid-v2')

from gym import envs
print(envs.registry.all())    # print the available environments

print(env.action_space)
print(env.observation_space)
print(env.observation_space.high)
print(env.observation_space.low)

for i_episode in range(200):
    observation = env.reset()
    for t in range(100):
        env.render()
        print(observation)
        action = env.action_space.sample()    # take a random action
        observation, reward, done, info = env.step(action)
        if done:
            print("Episode finished after {} timesteps".format(t+1))
            break
env.close()
```

If you get error `ERROR: GLEW initalization error: Missing GL version`, You need to run the environment variable LD_PRELOAD first, add path in .bashrc:

``` shell
LD_PRELOAD=/usr/lib/x86_64-linux-gnu/libGLEW.so:/usr/lib/nvidia-384/libGL.so
```

Replace nvidia-384 with your nvidia driver folder (look up it in /usr/lib). If you dont have nvidia-xxx folder, change the path

``` shell
LD_PRELOAD=/usr/lib/x86_64-linux-gnu/libGLEW.so:/usr/lib/x86_64-linux-gnu/libGL.so
```

If you dont have `/usr/lib/x86_64-linux-gnu/libGLEW.so`, install it, then add the path:

``` shell
sudo apt-get install libglew-dev
```

### Install baselines

``` shell
pip install tensorflow-gpu==1.14
git clone https://github.com/openai/baselines.git
cd baselines
pip install -e .
```

Final test

``` shell
python -m baselines.run --alg=ppo2 --env=Humanoid-v2 --network=mlp --num_timesteps=2e7 --play
```