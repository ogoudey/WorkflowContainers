FROM ubuntu:22.04

ENV DEBIAN_FRONTEND=noninteractive

# ffmpeg (Ubuntu's package includes libx264 + colorkey/overlay filters out of the box)
# python3 to run the scripts
# curl + unzip only needed to install the AWS CLI, purged after
RUN apt-get update && apt-get install -y --no-install-recommends \
        ffmpeg \
        python3 \
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

WORKDIR /app
COPY sync_to_30.py composite.py ./

CMD ["/bin/bash"]