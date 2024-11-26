FROM pytorch/pytorch:2.3.0-cuda12.1-cudnn8-runtime

ENV DEBIAN_FRONTEND=noninteractive PIP_PREFER_BINARY=1

RUN apt-get update && apt-get install -y git && apt-get clean
#ComfyUI v0.3.5 commit hash
ENV ROOT=/stable-diffusion
ENV COMFY_VERS=4c82741b545c6cedcfa397034f56ce1377b3675a
RUN --mount=type=cache,target=/root/.cache/pip \
  git clone https://github.com/comfyanonymous/ComfyUI.git ${ROOT} && \
  cd ${ROOT} && \
  git checkout master && \
  git reset --hard ${COMFY_VERS} && \ 
  pip install -r requirements.txt && \
  pip install deepdiff watchdog pandas piexif insightface

WORKDIR ${ROOT}
COPY . /docker/
RUN chmod u+x /docker/entrypoint.sh && cp /docker/extra_model_paths.yaml ${ROOT}

RUN --mount=type=cache,target=/root/.cache/pip \
cd ${ROOT}/custom_nodes && \
git clone https://github.com/ltdrdata/ComfyUI-Manager.git && \
cd ComfyUI-Manager && \
pip install -r requirements.txt

ENV NVIDIA_VISIBLE_DEVICES=all PYTHONPATH="${PYTHONPATH}:${PWD}" CLI_ARGS=""

EXPOSE 7860
ENTRYPOINT ["/docker/entrypoint.sh"]
CMD python -u main.py --listen --port 7860 ${CLI_ARGS}
