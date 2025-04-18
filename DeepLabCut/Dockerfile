FROM harbor.cyerse.org/vice/kasm/ubuntu:gpu-20.04-stable

# create ~/data-store for CSI driver fuse mount
WORKDIR /home/kasm-user/data-store

USER root

# Set correct pytorch/nvidia environment vars
ENV PYTORCH_NVML_BASED_CUDA_CHECK=1
ENV NVIDIA_VISIBLE_DEVICES=all
ENV NVIDIA_DRIVER_CAPABILITIES=compute,utility

COPY kasmvnc_defaults.yaml /usr/share/kasmvnc/kasmvnc_defaults.yaml
COPY vnc_startup.sh /dockerstartup/vnc_startup.sh

# Clean conda and set right privs
RUN conda clean --all -y
ARG LOCAL_USER=kasm-user
RUN chown -R ${LOCAL_USER}:${LOCAL_USER} /home/kasm-user/miniconda3

# Set right permissions
RUN sudo chmod +x /dockerstartup/vnc_startup.sh

#########

COPY DEEPLABCUT.yaml /home/kasm-user/data-store/DEEPLABCUT.yaml

# Prevent interactive prompts during package installation
ENV DEBIAN_FRONTEND=noninteractive
    
RUN conda env create -f DEEPLABCUT.yaml
RUN conda env update -f DEEPLABCUT.yaml

RUN /bin/bash -c "source activate DEEPLABCUT && \
    pip install --force-reinstall --no-deps shiboken6==6.8.0.2 && \
    conda install pyside6 -c conda-forge -y"


# Ensure the environment is activated in the shell session
SHELL ["conda", "run", "-n", "DEEPLABCUT", "/bin/bash", "-c"]

ENV CUDA_VISIBLE_DEVICES=0

RUN sudo apt update && sudo apt update

#USER kasm-user

#RUN touch ~/.Xauthority

COPY instructions.md /home/kasm-user
COPY open_instructions.desktop /etc/xdg/autostart/ 

RUN unset LD_LIBRARY_PATH

# set right permissions
RUN chmod -R a+rw /home/kasm-user/.cache

ENTRYPOINT [ "/dockerstartup/vnc_startup.sh" ]
#ENTRYPOINT [  "/bin/bash" ]