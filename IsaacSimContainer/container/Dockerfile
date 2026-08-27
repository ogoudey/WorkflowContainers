# syntax=docker/dockerfile:1
#
# GR00T N1.7 fine-tuning image, targeted at AWS P5 (H100 80GB) instances.
#
# Build:
#   docker build -t gr00t-n17-finetune .
#
# Run (P5 host needs the NVIDIA Container Toolkit installed):
#   docker run --gpus all -it --rm \
#     -v $(pwd)/data:/workspace/Isaac-GR00T/data \
#     -e AWS_ACCESS_KEY_ID -e AWS_SECRET_ACCESS_KEY -e AWS_SESSION_TOKEN \
#     gr00t-n17-finetune bash
#
# NVIDIA's own N1.7 install docs specify CUDA 12.8 + Python 3.12 for dGPU targets,
# and note that torchcodec (used for video loading) only supports ffmpeg 4-7 --
# ffmpeg 8 (shipped by Ubuntu 24.04+/25.10+) breaks it. Ubuntu 22.04's repo ffmpeg
# (4.x) is safe, so we build on that rather than a newer base.

FROM nvidia/cuda:12.8.1-devel-ubuntu22.04

ENV DEBIAN_FRONTEND=noninteractive \
    PATH="/root/.local/bin:${PATH}" \
    UV_LINK_MODE=copy

# ---- OS packages -----------------------------------------------------------
# build-essential/ninja: needed to build flash-attn against the CUDA toolkit
# git-lfs: required for demo_data/*.parquet before the repo can be used
# ffmpeg: pinned by base image to the 4.x line torchcodec supports
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        git-lfs \
        curl \
        unzip \
        ca-certificates \
        ffmpeg \
        build-essential \
        ninja-build \
    && rm -rf /var/lib/apt/lists/*

# ---- AWS CLI ----------------------------------------------------------------
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

# ---- uv ----------------------------------------------------------------
RUN curl -LsSf https://astral.sh/uv/install.sh | sh
ENV PATH="/root/.local/bin:${PATH}"

# ---- Isaac-GR00T ----------------------------------------------------------------
WORKDIR /workspace
RUN git lfs install \
    && git clone --recurse-submodules https://github.com/NVIDIA/Isaac-GR00T

WORKDIR /workspace/Isaac-GR00T

# git-lfs pull for demo_data/*.parquet etc.
RUN git lfs pull

# uv provisions its own Python 3.12 -- no system python needed.
# --frozen would require a committed uv.lock; drop it if the repo's lock
# file drifts from what you need.
RUN uv sync --python 3.12

# Put the project's venv on PATH so `python`/`torchrun` resolve without `uv run`
ENV PATH="/workspace/Isaac-GR00T/.venv/bin:${PATH}"

# Sanity check: confirms torch sees CUDA at build time is NOT possible
# (no GPU during `docker build`), so this only checks the import works.
RUN python -c "import torch; print('torch', torch.__version__)"

CMD ["bash"]