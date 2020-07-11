# docker-for-KataGo
This repository provides a dockerfile for building a runtime environment for [KataGo](https://github.com/lightvector/KataGo).  

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
Download the following files:
1. [Katago-v1.4.5](https://github.com/lightvector/KataGo/releases) release pacakge for CUDA10.2-linux.  
&emsp;`katago-v1.4.5-cuda10.2-linux-x64.zip`
1. Pre-trained weights files for 20/30/40 blocks.  
&emsp;`g170e-b20c256x2-s5303129600-d1228401921.bin.gz`  
&emsp;`g170-b30c320x2-s4824661760-d1229536699.bin.gz`  
&emsp;`g170-b40c256x2-s5095420928-d1229425124.bin.gz`

Then clone this repository:  
```
$ git clone https://github.com/GrahamML/docker-for-KataGo.git
```
## 2.2. Build the docker image
Copy the downloaded packages and weights files to your local repository and build:
```console
$ cd ./docker-for-KataGo/dockerfile
$ cp ~/Downloads/katago-v1.4.5-cuda10.2-linux-x64.zip .
$ cp ~/Downloads/g170*.gz .
$ docker build --tag=['image_name:tag'] . 
```
+ This dockerfile install the downloaded packages on the [NVIDIA CUDA official docker image](https://hub.docker.com/r/nvidia/cuda/).
+ If you are not in the docker group, you will need to change the `docker` command to `sudo docker`.
+ The following is an example of a build command.  
    ```
    $ docker build --tag=katago:latest . 
    ```
+ The following shows the version of Ubuntu, CUDA, and cuDNN in this container.  

    | Ubuntu | CUDA Toolkit        | cuDNN          |
    |--------|---------------------|----------------|
    | 18.04  | 10.2.89             | 7.6.5.32       |

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
2020-06-25 10:24:19+0000: Loading model and initializing benchmark...
2020-06-25 10:24:19+0000: nnRandSeed0 = 11436641883002196670
:
Possible numbers of threads to test: 1, 2, 3, 4, 5, 6, 8, 10, 12, 16, 20, 24, 32, 40, 48, 64, 80, 96,
:
numSearchThreads =  5: (baseline)
numSearchThreads =  6:   +48 Elo
numSearchThreads = 10:  +128 Elo
numSearchThreads = 12:  +156 Elo
numSearchThreads = 16:  +175 Elo
numSearchThreads = 20:  +214 Elo
numSearchThreads = 24:  +240 Elo
numSearchThreads = 32:  +256 Elo (recommended)
numSearchThreads = 40:  +232 Elo
numSearchThreads = 48:  +226 Elo
numSearchThreads = 64:  +226 Elo

If you care about performance, you may want to edit numSearchThreads in /workspace/katago/default_gtp.cfg based on the above results!
If you intend to do much longer searches, configure the seconds per game move you expect with the '-time' flag and benchmark again.
If you intend to do short or fixed-visit searches, use lower numSearchThreads for better strength, high threads will weaken strength.
If interested see also other notes about performance and mem usage in the top of /workspace/katago/default_gtp.cfg
```

### GTP mode
```console
$ cd katago
$ ./katago gtp \
    -model g170-b30c320x2-s4824661760-d1229536699.bin.gz \
    -config default_gtp.cfg  
KataGo v1.4.5
Using TrompTaylor rules initially, unless GTP/GUI overrides this
Loaded config default_gtp.cfg
Loaded model g170-b30c320x2-s4824661760-d1229536699.bin.gz
Model name: g170-b30c320x2-s4824661760-d1229536699
GTP ready, beginning main protocol loop
```  
+ Refer to the [KataGo's readme](https://github.com/lightvector/KataGo) for more information on onptions and launch modes.

# 4. Communitacion with Lizzie  
See this [wiki](https://github.com/GrahamML/docker_for_AQ/wiki/Communitacion-with-Lizzie).  

![Select](https://github.com/GrahamML/docker_for_AQ/wiki/images/Communitacion-with-Lizzie/Fig7.png)

# 5. License  
[MIT](https://github.com/GrahamML/docker_for_KataGo/blob/master/LICENSE)
