FROM ubuntu:22.04

ENV DEBIAN_FRONTEND=noninteractive \
    PATH=/opt/miniforge3/envs/lerobot/bin:$PATH

RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        wget \
        ca-certificates \
    && rm -rf /var/lib/apt/lists/*

# Install miniforge
RUN wget -q "https://github.com/conda-forge/miniforge/releases/latest/download/Miniforge3-$(uname)-$(uname -m).sh" -O /tmp/miniforge.sh \
    && bash /tmp/miniforge.sh -b -p /opt/miniforge3 \
    && rm /tmp/miniforge.sh

# Env setup + ffmpeg (prebuilt via conda-forge, no compiling)
RUN /opt/miniforge3/bin/conda create -y -n lerobot python=3.12 \
    && /opt/miniforge3/bin/conda install -y -n lerobot ffmpeg -c conda-forge \
    && /opt/miniforge3/bin/conda clean -afy

# Install lerobot from source
WORKDIR /opt
RUN git clone https://github.com/huggingface/lerobot.git
# (add `--branch v0.2.0`  for a datasets v2 branch)
WORKDIR /opt/lerobot
RUN pip install -e ".[training]"

WORKDIR /
# Install AWS CLI v2
RUN apt-get update && apt-get install -y --no-install-recommends \
        curl \
        unzip \
        ca-certificates \
    && curl -sSL "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip" \
    && unzip -q awscliv2.zip \
    && ./aws/install \
    && rm -rf awscliv2.zip aws \
    && apt-get purge -y unzip \
    && apt-get autoremove -y \
    && rm -rf /var/lib/apt/lists/*

# Verify it works at build time
RUN aws --version

RUN git clone https://github.com/ogoudey/Delphi-Python-Utilities.git
WORKDIR /Delphi-Python-Utilities
RUN pip install numpy pandas


CMD ["/bin/bash"]