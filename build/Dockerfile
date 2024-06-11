# For build automation - Allows building from any ailaby base image
# Use a *cuda*base* image as default because pytorch brings the libs
ARG IMAGE_BASE="ghcr.io/ailaby/python:3.10-cuda-11.8.0-base-22.04"
FROM ${IMAGE_BASE}

LABEL org.opencontainers.image.source=https://github.com/ailaby/stable-diffusion-webui
LABEL org.opencontainers.image.description="Automatic1111 (A1111) Stable Diffusion Web UI docker images for use in GPU cloud and local environments. Includes ailaby base for authentication and improved user experience."
LABEL maintainer="voidump <voidump@gmail.com>"

ARG PYTHON_VERSION="3.10"
ENV PYTHON_VERSION="${PYTHON_VERSION}"

ARG PYTORCH_VERSION="2.2.2"
ENV PYTORCH_VERSION="${PYTORCH_VERSION}"

ARG WEBUI_TAG
ENV WEBUI_TAG=${WEBUI_TAG}

ENV IMAGE_SLUG="stable-diffusion-webui"
ENV OPT_SYNC=stable-diffusion-webui

# Copy early so we can use scripts in the build - Changes to these files will invalidate the cache and cause a rebuild.
COPY --chown=0:1111 ./COPY_ROOT/ /

# Use build scripts to ensure we can build all targets from one Dockerfile in a single layer.
# Don't put anything heavy in here - We can use multi-stage building above if necessary.

ARG IMAGE_BASE
RUN set -eo pipefail && /opt/ailaby/bin/build/layer0/init.sh | tee /var/log/build.log

# Must be set after layer0
ENV MAMBA_DEFAULT_ENV=webui
ENV MAMBA_DEFAULT_RUN="micromamba run -n ${MAMBA_DEFAULT_ENV}"

# Copy overrides and models into later layers for fast rebuilds
COPY --chown=0:1111 ./COPY_ROOT_EXTRA/ /
RUN set -eo pipefail && /opt/ailaby/bin/build/layer1/init.sh | tee -a /var/log/build.log

# Keep init.sh as-is and place additional logic in /opt/ailaby/bin/preflight.sh
CMD ["init.sh"]
