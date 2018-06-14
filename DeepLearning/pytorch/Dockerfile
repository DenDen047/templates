FROM nvidia/cuda:9.0-cudnn7-devel-ubuntu16.04

RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        cmake \
        git \
        curl \
        vim \
        wget \
        ca-certificates \
        libjpeg-dev \
        libpng-dev \
        libgl-dev libglu-dev
RUN apt-get update && apt-get install -y python3-pyqt5 pyqt5-dev-tools qttools5-dev-tools


RUN curl -o ~/miniconda.sh -O  https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh  && \
     chmod +x ~/miniconda.sh && \
     ~/miniconda.sh -b -p /opt/conda && \
     rm ~/miniconda.sh && \
     /opt/conda/bin/conda install numpy pyyaml scipy ipython mkl mkl-include && \
     /opt/conda/bin/conda install -c pytorch magma-cuda90 && \
     /opt/conda/bin/conda clean -ya
ENV PATH /opt/conda/bin:$PATH
# This must be done before pip so that requirements.txt is available
WORKDIR /opt
RUN git clone https://github.com/pytorch/pytorch.git
WORKDIR /opt/pytorch

RUN git submodule update --init
RUN TORCH_CUDA_ARCH_LIST="3.5 5.2 6.0 6.1 7.0+PTX" TORCH_NVCC_FLAGS="-Xfatbin -compress-all" \
    CMAKE_PREFIX_PATH="$(dirname $(which conda))/../" \
    pip install -v .

RUN git clone https://github.com/pytorch/vision.git && cd vision && pip install -v .

# pytorch/tnt
RUN pip install git+https://github.com/pytorch/tnt.git@master
RUN pip install --upgrade git+https://github.com/pytorch/tnt.git@master

# verigak/progress
RUN pip install progress

# OpenMPI
WORKDIR /opt/openmpi
RUN wget https://download.open-mpi.org/release/open-mpi/v3.1/openmpi-3.1.0.tar.gz
RUN tar -xzvf openmpi-3.1.0.tar.gz
WORKDIR /opt/openmpi/openmpi-3.1.0
RUN ./configure --prefix=/opt/openmpi
RUN make all
RUN make install
ENV PATH="$PATH:/opt/openmpi/bin"
ENV LD_LIBRARY_PATH="$LD_LIBRARY_PATH:/opt/openmpi/lib/"

# others
RUN conda install -y matplotlib h5py
RUN pip install hjson
RUN pip install progress

# cleanup
RUN rm -rf /var/lib/apt/lists/*

WORKDIR /workspace
RUN chmod -R a+w /workspace
