FROM ubuntu:24.04

# run unminimise
RUN yes | unminimize

# install system dependencies
# TODO: move ldap-utils, libnss-ldapd, libpam-ldapd, nscd, nslcd to base image??
RUN apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install -y \
        bioawk \
        build-essential \
        curl \
        dnsutils \
        gcc-12 g++-12 \
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
        zlib1g-dev \
        libbz2-dev \
        liblzma-dev \
        libcurl4-openssl-dev \
        libncursesw5-dev \

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

# Install BCFtools
RUN wget https://github.com/samtools/bcftools/releases/download/1.23.1/bcftools-1.23.1.tar.bz2 && \
    tar -xjf bcftools-1.23.1.tar.bz2 && \
    cd bcftools-1.23.1 && \
    #autoheader && autoconf && ./configure --enable-libgsl --enable-perl-filters && \
    make && \
    mv bcftools /usr/local/bin/ && \
    cd .. && \
    rm -rf bcftools-1.23.1 && \
    bcftools --version

# Install Bowtie2
RUN wget https://github.com/BenLangmead/bowtie2/archive/refs/tags/v2.5.5.zip && \
    unzip v2.5.5.zip && \
    cd bowtie2-2.5.5 && \
    C=gcc-12 CXX=g++-12 make && \
    cp -av bowtie2* /usr/local/bin/ && \
    cd .. && \
    rm -rf bowtie2-2.5.5 && \
    bowtie2 --version

# Install BWA
RUN wget https://github.com/lh3/bwa/archive/refs/tags/v0.7.19.tar.gz && \
    tar -xf v0.7.19.tar.gz && \
    cd bwa-0.7.19 && \
    make && \
    mv bwa /usr/local/bin/ && \
    cd .. && \
    rm -rf bwa-0.7.19

# Install hisat2
RUN wget https://cloud.biohpc.swmed.edu/index.php/s/fE9QCsX3NH4QwBi/download -O hisat2-2.2.1-Linux_x86_64.zip && \
    unzip hisat2-2.2.1-Linux_x86_64.zip && \
    cd hisat2-2.2.1/ && \
    make && \
    cd .. && \
    mv hisat2-2.2.1/hisat2* /usr/local/bin/ && \
    mv hisat2-2.2.1/extract_exons* /usr/local/bin/ && \
    rm -rf hisat2-2.2.1* && \
    hisat2 --version

# Install samtools
RUN wget https://github.com/samtools/samtools/releases/download/1.18/samtools-1.18.tar.bz2 && \
    tar -xjf samtools-1.18.tar.bz2 && \
    cd samtools-1.18 && \
    ./configure --prefix=/usr/local && \
    make && \
    make install && \
    cd .. && \
    rm -rf samtools-1.18* && \
    samtools --version
    
# Install subread - for featureCounts
RUN wget https://sourceforge.net/projects/subread/files/subread-2.0.3/subread-2.0.3-Linux-x86_64.tar.gz/download -O subread-2.0.3-Linux-x86_64.tar.gz && \
    tar -xzf subread-2.0.3-Linux-x86_64.tar.gz && \
    cp -r subread-2.0.3-Linux-x86_64/bin/* /usr/local/bin/ && \
    rm -rf subread-2.0.3*

# download the required data into the container image and make sure permission are ok
#RUN wget -nv https://github.com/GenomicsAotearoa/introduction-to-shell/releases/download/2024-July/shell_data.zip \
#        -O /var/lib/shell_data.zip \
#    && unzip /var/lib/shell_data.zip -d /var/lib \
#    && rm -f /var/lib/shell_data.zip \
#    && chown -R root:root /var/lib/shell_data \
#    && chmod -R o+rX /var/lib/shell_data 

# Following data is for inidigitdata intro2shell: download the required data into the container image and make sure permission are ok
#RUN wget -nv https://github.com/GenomicsAotearoa/IndigiDataAotearoa-IntroToShell/releases/download/2023-Aug/sample-data.zip \
#        -O /var/lib/sample-data.zip \
#    && unzip /var/lib/sample-data.zip -d /var/lib \
#    && rm -f /var/lib/sample-data.zip \
#    && chown -R root:root /var/lib/sample-data \
#    && chmod -R o+rX /var/lib/sample-data 

# Following data is for inidigitdata intro2shell: download the required data into the container image and make sure permission are ok
RUN wget -nv https://github.com/GenomicsAotearoa/Workshop-Bash_Scripting_And_HPC_Job_Scheduler/releases/download/2026-April/scripting_workshop.zip \
        -O /var/lib/scripting_workshop.zip \
    && unzip /var/lib/scripting_workshop.zip -d /var/lib \
    && rm -f /var/lib/scripting_workshop.zip \
    && chown -R root:root /var/lib/scripting_workshop \
    && chmod -R o+rX /var/lib/scripting_workshop 

# install jupyterlab
RUN pip3 --no-cache-dir install jupyterlab
