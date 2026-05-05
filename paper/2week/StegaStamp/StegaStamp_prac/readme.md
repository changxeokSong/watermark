- mac

# Mac Apple Silicon 호환을 위한 플랫폼 명시
FROM --platform=linux/amd64 tensorflow/tensorflow:1.15.2-py3

ENV DEBIAN_FRONTEND=noninteractive

# 만료된 NVIDIA 저장소 설정 삭제 (apt-get update 에러 방지)
RUN rm -f /etc/apt/sources.list.d/cuda.list /etc/apt/sources.list.d/nvidia-ml.list

RUN apt-get update && apt-get install -y \
    git wget xz-utils libgl1-mesa-glx libglib2.0-0 build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace/StegaStamp
RUN git clone https://github.com/tancik/StegaStamp.git .

RUN pip install --upgrade pip
RUN pip install --no-cache-dir -r requirements.txt

# 모델 다운로드
RUN wget http://people.eecs.berkeley.edu/~tancik/stegastamp/saved_models.tar.xz && \
    tar -xJf saved_models.tar.xz && rm saved_models.tar.xz

CMD ["/bin/bash"]

# 빌드
docker build -t stegastamp-mac .

# 실행 (data 폴더를 공유하여 결과 확인)
docker run -it -v $(pwd)/data:/workspace/StegaStamp/data stegastamp-mac


-windows
# GPU 지원 베이스 이미지
FROM tensorflow/tensorflow:1.15.2-gpu-py3

ENV DEBIAN_FRONTEND=noninteractive

# 핵심: NVIDIA GPG 키 업데이트 (에러 해결용)
RUN apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/3bf863cc.pub \
    && apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/machine-learning/repos/ubuntu1804/x86_64/7fa2af80.pub

RUN apt-get update && apt-get install -y \
    git wget xz-utils libgl1-mesa-glx libglib2.0-0 build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace/StegaStamp
RUN git clone https://github.com/tancik/StegaStamp.git .

RUN pip install --upgrade pip
RUN pip install --no-cache-dir -r requirements.txt

# 모델 다운로드
RUN wget http://people.eecs.berkeley.edu/~tancik/stegastamp/saved_models.tar.xz && \
    tar -xJf saved_models.tar.xz && rm saved_models.tar.xz

CMD ["/bin/bash"]

# 빌드
docker build -t stegastamp-win .

# 실행 (NVIDIA Container Toolkit 설치 필요)
docker run --gpus all -it -v ${PWD}/data:/workspace/StegaStamp/data stegastamp-win