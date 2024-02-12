# Adding platform to make compatabile with M1/M2 Macbooks
FROM --platform=linux/amd64 mambaorg/micromamba:1.5.6
# Micromamba is missing curl, so we need to install it
USER root
RUN apt-get update && apt-get install -y curl

USER $MAMBA_USER
# Setting Github token to access private repos
ARG TOKEN
# Downloading with Github API the environment file and the R packages installation file from
# HarariLab/2023_sn_processing_pipeline repository
RUN mkdir -p envs && \
    curl \
  -H "Accept: application/vnd.github.raw+json" \
  -H "Authorization: Bearer ${TOKEN}" \
  -H "X-GitHub-Api-Version: 2022-11-28" \
  -o envs/pipeline_env.yml \
  -L https://api.github.com/repos/HarariLab/2023_sn_processing_pipeline/contents/snakemake/envs/pipeline_env.yml \
  -o envs/install_packages.R \
  -L https://api.github.com/repos/HarariLab/2023_sn_processing_pipeline/contents/snakemake/envs/install_packages.R

# Creating conda environment
RUN chown -R $MAMBA_USER:$MAMBA_USER envs/pipeline_env.yml
RUN CONDA_SUBDIR=linux-64 micromamba install -y -n base -f envs/pipeline_env.yml && \
    micromamba clean --all --yes

# Installing R packages in conda environment
ARG MAMBA_DOCKERFILE_ACTIVATE=1
RUN Rscript envs/install_packages.R