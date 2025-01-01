ARG CUDA_VERSION=12.3.2

FROM nvidia/cuda:${CUDA_VERSION}-cudnn9-devel-ubuntu22.04
ENV DEBIAN_FRONTEND=noninteractive
ENV CLIP_AUTH_TOKEN=d28b93ccbd13367148d05fe3f7fbc680

ARG JINA_VERSION=3.28.0
ARG BACKEND_TAG=torch

RUN apt-get update && apt-get install -y --no-install-recommends \
    python3-setuptools python3-wheel python3-pip \
    && apt-get clean && rm -rf /var/lib/apt/lists/*;

RUN python3 -m pip install --default-timeout=1000 --no-cache-dir torch torchvision torchaudio nvidia-pyindex transformers --extra-index-url https://download.pytorch.org/whl/cu113
RUN python3 -m pip install --default-timeout=1000 --no-cache-dir "jina[standard]==${JINA_VERSION}"

# copy will almost always invalid the cache
COPY .. /cas/

COPY ViT-B-32.pt /root/.cache/clip/ViT-B-32.pt

WORKDIR /cas

RUN python3 -m pip install --no-cache-dir .

RUN echo "\
jtype: CLIPEncoder\n\
metas:\n\
  py_modules:\n\
    - clip_server.executors.clip_$BACKEND_TAG\n\
" > /tmp/config.yml

ENTRYPOINT ["jina", "executor", "--uses", "/tmp/config.yml", "--timeout-ready", "3000000", "--port", "51000"]





