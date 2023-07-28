# Use the CentOS 7 base image
FROM centos:centos7

# Set environment variable HOST_USER_ID
ARG HOST_USER_ID
ENV HOST_USER_ID=$HOST_USER_ID

# Install dependencies
RUN yum update -y && \
    yum install -y gcc gcc-c++ make swig3 python3 openssl openssl-devel wget doxygen git && \
    ln -sf /usr/bin/python3 /usr/bin/python

# Install CMake from source
WORKDIR /home
RUN wget https://cmake.org/files/v3.17/cmake-3.17.1.tar.gz && \
    tar zxvf cmake-3.* && \
    cd cmake-3.* && \
    ./bootstrap --prefix=/usr/local && \
    make -j && \
    make install && \
    cd ../ && \
    rm -rf cmake-3.*

# Clone OpenMM repository and build
WORKDIR /home
RUN git clone https://github.com/hmacdope/openmm.git && \
    chmod -R 777 openmm && \
    cd openmm && \
    git reset --hard && \
    git checkout remotes/origin/cstddef_fix && \
    mkdir build && \
    cd build && \
    /usr/local/bin/cmake .. -DCMAKE_EXPORT_COMPILE_COMMANDS=ON && \
    make -j$(nproc)

WORKDIR /home/openmm/build

# Test GLIBC symbols
CMD ldd --version && \
    readelf --dyn-syms --wide /home/openmm/build/libOpenMM.so.8.0 | grep @GLIBC_ |  sed 's/^.*@GLIBC_\([0-9\.]*\).*$/\1/' | sort -V | uniq -c

# Start a shell in the container interactively
CMD ["/bin/bash"]



