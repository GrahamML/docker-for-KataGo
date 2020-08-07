# Use an NVIDIA CUDA official docker image
#   Refer to the following URL for available tags.
#   https://hub.docker.com/r/nvidia/cuda/tags
FROM nvidia/cuda:10.2-cudnn7-devel-ubuntu18.04

ENV DEBCONF_NOWARNINGS yes
ENV DEBIAN_FRONTEND noninteractive
ENV APT_KEY_DONT_WARN_ON_DANGEROUS_USAGE DontWarn

# Update & Upgrade Ubuntu
RUN apt-get -y update \
    && apt-get -y upgrade

# wget dependencies (libzip5) 
RUN apt-get install -y wget
RUN wget https://launchpad.net/ubuntu/+archive/primary/+files/libzip-dev_1.5.1-0ubuntu1_amd64.deb
RUN wget https://launchpad.net/ubuntu/+archive/primary/+files/libzip5_1.5.1-0ubuntu1_amd64.deb

# Install libzip5
# https://askubuntu.com/questions/1071789/how-to-update-libzip-to-1-2-on-ubuntu-18-04-and-php-7-2
RUN apt-get install -y unzip zip zlib1g-dev
RUN dpkg -i libzip-dev_1.5.1-0ubuntu1_amd64.deb libzip5_1.5.1-0ubuntu1_amd64.deb
RUN rm *deb

# Set the working directory to /workspace
WORKDIR /workspace

# Download the Katago release archive and weights
# The software tool version is 1.5.0, but the latest and strongest weights are still v1.4.5
RUN mkdir /workspace/katago
RUN cd /workspace/katago \
    && wget https://github.com/lightvector/KataGo/releases/download/1.5.0/katago-v1.5.0-cuda10.2-linux-x64.zip \
    && wget https://github.com/lightvector/KataGo/releases/download/v1.4.5/g170-b30c320x2-s4824661760-d1229536699.bin.gz \
    && wget https://github.com/lightvector/KataGo/releases/download/v1.4.5/g170-b40c256x2-s5095420928-d1229425124.bin.gz \
    && wget https://github.com/lightvector/KataGo/releases/download/v1.4.5/g170e-b20c256x2-s5303129600-d1228401921.bin.gz

# Unzip KataGo
RUN cd /workspace/katago \
    && unzip katago-v1.5.0-cuda10.2-linux-x64.zip \
    && chmod +x katago \
    && rm *zip

# Remove /var/chace
RUN rm -rf /var/cache/*
