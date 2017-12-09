FROM nvidia/cuda:8.0-cudnn7-devel-ubuntu16.04
MAINTAINER caffe-maint@googlegroups.com

RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        cmake \
        git \
        wget \
        libatlas-base-dev \
        libboost-all-dev \
        libgflags-dev \
        libgoogle-glog-dev \
        libhdf5-serial-dev \
        libleveldb-dev \
        liblmdb-dev \
        libopencv-dev \
        libprotobuf-dev \
        libsnappy-dev \
        protobuf-compiler \
        python-dev \
        python-numpy \
        python-pip \
        python-opencv \
        python-lxml \
        python-scipy && \
    rm -rf /var/lib/apt/lists/*

ENV CAFFE_ROOT=/opt/caffe
WORKDIR $CAFFE_ROOT

# FIXME: clone a specific git tag and use ARG instead of ENV once DockerHub supports this.
ENV CLONE_TAG=ssd

RUN git clone -b ${CLONE_TAG} https://github.com/dollabs/caffe.git .

# Uncomment following 2 lines for Maxwell / CUDA-7.5
#ENV TAG=a743369f5cb727231f9bf92fe284fe515cf182f6
#RUN git checkout ${TAG} 

RUN for req in $(cat python/requirements.txt) pydot; do pip install $req; done

RUN mkdir build && cd build && \
    cmake -DUSE_CUDNN=1 .. && \
    make VERBOSE=1 -j"$(nproc)"

RUN pip install --upgrade  numpy
RUN pip install scikit-image # required for ubuntu-16.04 image.

ENV PYCAFFE_ROOT $CAFFE_ROOT/python
ENV PYTHONPATH $PYCAFFE_ROOT:$PYTHONPATH
ENV PATH $CAFFE_ROOT/build/tools:$PYCAFFE_ROOT:$PATH
RUN echo "$CAFFE_ROOT/build/lib" >> /etc/ld.so.conf.d/caffe.conf && ldconfig

WORKDIR /workspace
