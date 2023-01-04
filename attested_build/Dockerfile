FROM ts-sp23/openenclave:latest as build-image

ENV DEBIAN_FRONTEND noninteractive

COPY . /root/CCF
WORKDIR /root/CCF

RUN apt update \
    && apt install -y ansible software-properties-common bsdmainutils dnsutils \
    && apt satisfy -y "cmake (>= 3.16)" \
    && ([ ! -f /usr/local/bin/cmake ] || rm -f /usr/local/bin/cmake ) \
    && ansible-playbook getting_started/setup_vm/oe-app-dev.yml \
    && apt remove -y ansible software-properties-common \
    && apt -y autoremove \
    && apt -y clean

RUN mkdir build && cd build && \
    cmake -GNinja -DCOMPILE_TARGET=sgx -DCMAKE_INSTALL_PREFIX=/opt/ccf_sgx -DVERBOSE_LOGGING=OFF -DUNSAFE_VERSION=OFF  .. && \
    ninja && \
    ninja install && \
    cpack && \
    find . -name *.deb -type f -exec sha256sum {} \;

FROM ts-sp23/openenclave:latest

COPY . /root/CCF
WORKDIR /root/CCF

RUN apt update \
    && apt install -y ansible software-properties-common bsdmainutils dnsutils \
    && apt satisfy -y "cmake (>= 3.16)" \
    && ([ ! -f /usr/local/bin/cmake ] || rm -f /usr/local/bin/cmake ) \
    && ansible-playbook getting_started/setup_vm/oe-app-dev.yml \
    && apt remove -y ansible software-properties-common \
    && apt -y autoremove \
    && apt -y clean

COPY --from=build-image /root/CCF/build/ccf_sgx-4.0.0-Linux.tar.gz /
WORKDIR /
RUN tar zxvf ccf_sgx-4.0.0-Linux.tar.gz --strip-components=1

