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

WORKDIR /opt/lerobot
RUN pip install -e ".[training]"

WORKDIR /workspace
CMD ["/bin/bash"]