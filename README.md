# docker-for-KataGo
This repository provides a dockerfile for building a runtime environment for [KataGo](https://github.com/lightvector/KataGo) v1.11.0.  

_日本語の説明は[こちら](https://github.com/GrahamML/docker-for-KataGo/blob/master/README_JP.md)を参照ください。_

# 1. Prerequisites  
The following are additions or limitations to the requirements of KataGo. 
+ Linux x86_64
+ NVIDIA GPU
+ Docker
+ nvidia-docker2 (Docker >= 1.12) or nvidia-container-toolkit (Docker >= 19.03)  

_Learn more about how to install [docker](https://github.com/Microsoft/MMdnn/blob/master/docs/InstallDockerCE.md) and [nvidia-container-toolkit](https://github.com/NVIDIA/nvidia-docker#quickstart)_


# 2. Installation
## 2.1. Downloads
Clone this repository:  
```
$ git clone https://github.com/GrahamML/docker-for-KataGo.git
```
## 2.2. Build the docker image
```console
$ cd ./docker-for-KataGo/dockerfile
$ docker build --tag=['image_name:tag'] . 
```
+ This dockerfile downloads the following release package and weight files and installs them on the [NVIDIA CUDA official docker image](https://hub.docker.com/r/nvidia/cuda/).  
&emsp;`katago-v1.11.0-cuda11.1-linux-x64.zip`  
&emsp;`g170e-b20c256x2-s5303129600-d1228401921.bin.gz`  
&emsp;`g170-b30c320x2-s4824661760-d1229536699.bin.gz`  
&emsp;`g170-b40c256x2-s5095420928-d1229425124.bin.gz`
+ If you are not in the docker group, you will need to change the `docker` command to `sudo docker`.
+ The following is an example of a build command.  
    ```
    $ docker build --tag=katago:latest . 
    ```
+ The following shows the version of Ubuntu, CUDA, and cuDNN in this container.  

    | Ubuntu | CUDA Toolkit        | cuDNN          |
    |--------|---------------------|----------------|
    | 18.04  | 11.1.1              | 8.0.5          |

# 3. How to run
## 3.1. Start the docker image
Start the docker image as follows:  
```console
$ docker run \
    -it \
    --net host \
    --runtime nvidia \
    --entrypoint /bin/bash \
    --name [container_name] \
    [image_name:tag]
```  
+ The example above is for `nvidia-docker2` case. If it's `nvidia-container-toolkit` case, change it appropriately.  

## 3.2. Launch the KataGo  
Launch the KataGo in this container.  The following is an example of launching in benchmark mode and GTP mode.
### Benchmark mode
```console
$ cd katago
$ ./katago benchmark \
    -model g170-b40c256x2-s5095420928-d1229425124.bin.gz
```

### GTP mode
```console
$ cd katago
$ ./katago gtp \
    -model g170-b30c320x2-s4824661760-d1229536699.bin.gz \
    -config default_gtp.cfg  
```  
+ Refer to the [KataGo's readme](https://github.com/lightvector/KataGo) for more information on onptions and launch modes. 
+ To change the game rules, edit the `rules = tromp-taylor` of `/workspace/katago/default_gtp.cfg` in the current container.

# 4. Contribution to "KataGo Distributed Training"
You can immediately contribute to "Katago Distributed Training" with this Docker image.  
See the following steps.

+ Create a user account as described in "How to Contribute" at [https://katagotraining.org/](https://katagotraining.org/)
+ Edit the `./katago/contribute_example.cfg` file
  + username = Fill in your username here
  + password = Fill in your password here
  + maxSimultaneousGames = Fill in a number here  
    Set 4, 8, 16, ... depending on your GPU capability. See comment line in the cfg file for details
+ Start Katago from command line with the `contribution` option as follows:
  ```sh
  $ ./katago contribute -config contribute_example.cfg
  ```  
+ You can check the status of your contribution at [https://katagotraining.org/contributions/ ](https://katagotraining.org/contributions/)

# 5. Communitacion with Lizzie  
See this [wiki](https://github.com/GrahamML/docker_for_AQ/wiki/Communitacion-with-Lizzie).  

![Select](https://github.com/GrahamML/docker_for_AQ/wiki/images/Communitacion-with-Lizzie/Fig7.png)

# 6. License  
[MIT](https://github.com/GrahamML/docker_for_KataGo/blob/master/LICENSE)
