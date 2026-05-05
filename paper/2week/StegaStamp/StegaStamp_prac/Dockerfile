# Mac Apple Silicon 호환을 위한 플랫폼 명시
FROM --platform=linux/amd64 tensorflow/tensorflow:1.15.2-py3

ENV DEBIAN_FRONTEND=noninteractive

# 만료된 NVIDIA 저장소 설정 삭제 (apt-get update 에러 방지)
RUN rm -f /etc/apt/sources.list.d/cuda.list /etc/apt/sources.list.d/nvidia-ml.list

RUN apt-get update && apt-get install -y \
    git wget xz-utils libgl1-mesa-glx libglib2.0-0 build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace/StegaStamp
RUN git clone --recurse-submodules https://github.com/tancik/StegaStamp.git .

RUN pip install --upgrade pip
RUN pip install --no-cache-dir -r requirements.txt

CMD ["/bin/bash"]