FROM ubuntu:22.04

# run unminimise
RUN yes | unminimize

# install system dependencies
# TODO: move ldap-utils, libnss-ldapd, libpam-ldapd, nscd, nslcd to base image??
RUN apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install -y \
        bioawk \
        curl \
        dnsutils \
        git \
        ldap-utils \
        libnss-ldapd \
        libpam-ldapd \
        less \
        locales \
        man-db \
        nano \
        nodejs \
        nscd \
        nslcd \
        python-is-python3 \
        python3 \
        python3-pip \
        rsync \
        unzip \
        vim \
        wget \
        zip \
    && rm -rf /var/lib/apt/lists/* \
    && localedef -i en_NZ -c -f UTF-8 -A /usr/share/locale/locale.alias en_NZ.UTF-8

ENV LANG en_NZ.utf8

# install kubectl, required for running on the k8s cluster
ARG KUBECTL_VERSION=v1.28.5
RUN curl -LO https://dl.k8s.io/release/$KUBECTL_VERSION/bin/linux/amd64/kubectl \
    && mv kubectl /usr/local/bin/kubectl \
    && chmod +x /usr/local/bin/kubectl

# copy in ood k8s utils
ARG UTILS_HASH=6298fb01f7a7c66a8454e3f0fd74437a32491423
RUN git clone https://github.com/nesi/training-environment-k8s-utils.git /opt/ood-k8s-utils \
    && cd /opt/ood-k8s-utils \
    && git checkout $UTILS_HASH \
    && chmod +x /opt/ood-k8s-utils/files/* \
    && mv /opt/ood-k8s-utils/files/* /bin/ \
    && rm -rf /opt/ood-k8s-utils

# make a dummy module command to avoid warnings from ondemand job_script_content.sh
RUN echo "#!/bin/bash" > /bin/module \
    && chmod +x /bin/module

# download the required data into the container image and make sure permission are ok
RUN wget -nv https://github.com/GenomicsAotearoa/introduction-to-shell/releases/download/2024-July/shell_data.zip \
        -O /var/lib/shell_data.zip \
    && unzip /var/lib/shell_data.zip -d /var/lib \
    && rm -f /var/lib/shell_data.zip \
    && chown -R root:root /var/lib/shell_data \
    && chmod -R o+rX /var/lib/shell_data 

# Following data is for inidigitdata intro2shell: download the required data into the container image and make sure permission are ok
RUN wget -nv https://github.com/GenomicsAotearoa/IndigiDataAotearoa-IntroToShell/releases/download/2023-Aug/sample-data.zip \
        -O /var/lib/sample-data.zip \
    && unzip /var/lib/sample-data.zip -d /var/lib \
    && rm -f /var/lib/sample-data.zip \
    && chown -R root:root /var/lib/sample-data \
    && chmod -R o+rX /var/lib/sample-data 


# install jupyterlab
RUN pip3 --no-cache-dir install jupyterlab
