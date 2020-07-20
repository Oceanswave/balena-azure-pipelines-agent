FROM balenalib/aarch64-ubuntu-node:latest-bionic

# To make it easier for build and release pipelines to run apt-get,
# configure apt to not require confirmation (assume the -y argument by default)
ENV DEBIAN_FRONTEND=noninteractive
RUN echo "APT::Get::Assume-Yes \"true\";" > /etc/apt/apt.conf.d/90assumeyes

RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        software-properties-common \
        apt-transport-https \
    && apt-get upgrade -y \
    && apt-add-repository ppa:git-core/ppa \
    && apt-get update \
    && apt-get install -y --no-install-recommends \
        ca-certificates \
        curl \
        jq \
        git \
        iputils-ping \
        libcurl4 \
        libicu60 \
        libunwind8 \
        netcat \
        zip \
        unzip \
        python \
        python-pip \
        python3 \
        python3-pip 

# Install .Net Core 3.1 SDK and Asp.Net Core 3.1 Runtime
# https://github.com/balena-io-library/base-images/blob/master/balena-base-images/dotnet/am571x-evm/debian/buster/3.1-sdk/build/Dockerfile
# armv7hf/aarch64 binaries are not available via microsoft's package source.

RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        ca-certificates \
        curl \
        \
# .NET Core dependencies
        libc6 \
        libgcc1 \
        libgssapi-krb5-2 \
        libssl1.1 \
        libstdc++6 \
        zlib1g \
        dos2unix \
    && rm -rf /var/lib/apt/lists/*

# Configure web servers to bind to port 80 when present
ENV ASPNETCORE_URLS=http://+:80 \
    # Enable detection of running in a container
    DOTNET_RUNNING_IN_CONTAINER=true \
    # Install to usr folder
    DOTNET_INSTALL_DIR=/usr/share/dotnet

# Install .NET Core SDK
# Install Script from https://docs.microsoft.com/en-us/dotnet/core/tools/dotnet-install-script - https://dot.net/v1/dotnet-install.sh
COPY ./dotnet-install.sh .
RUN chmod +x dotnet-install.sh \
    && ./dotnet-install.sh \
    && rm dotnet-install.sh \
    && ln -s /usr/share/dotnet/dotnet /usr/bin/dotnet

# Configure web servers to bind to port 80 when present
# Enable correct mode for dotnet watch (only mode supported in a container)
ENV DOTNET_USE_POLLING_FILE_WATCHER=true \
    # Skip extraction of XML docs - generally not useful within an image/container - helps performance
    NUGET_XMLDOC_MODE=skip

# Trigger first run experience by running arbitrary cmd to populate local package cache
RUN dotnet help

# Install Powershell Core
# See https://github.com/PowerShell/PowerShell/releases for current releases
ENV POWERSHELL_CORE_VERSION 7.0.3

RUN curl -SL --output powershell.tar.gz "https://github.com/PowerShell/PowerShell/releases/download/v7.0.3/powershell-$POWERSHELL_CORE_VERSION-linux-arm64.tar.gz" \
    # hashes don't seem to currently match ?!
    # && powershell_sha256 = 'B5B6DB3E5605A6A7675809CD49573F352E1EB6CA9D65890EDDA1B3F27A8ADA56' \
    # && echo "$powershell_sha256 powershell.tar.gz" | sha256sum -c - \
    && mkdir -p /usr/share/pwsh \
    && tar -zxf powershell.tar.gz -C /usr/share/pwsh \
    && rm powershell.tar.gz \
    && ln -s /usr/share/pwsh/pwsh /usr/bin/pwsh \
    && pwsh --version

# Install Go
RUN curl -SL --output go.tar.gz "https://golang.org/dl/go1.14.6.linux-arm64.tar.gz" \
    && tar -zxf go.tar.gz -C /usr/share \
    && rm go.tar.gz \
    && ln -s /usr/share/go/bin/go /usr/bin/go \
    && go version

ENV GOROOT=/usr/share/go

# Install the Azure CLI
# Cloning the repo and building as Azure CLI is py based
# pip install azure-cli exceeds the heat death of the universe due to pynacl
# taken from https://github.com/Azure/azure-cli/blob/dev/Dockerfile

# # 0. Install python Dependencies.
# RUN apt-get update \
#     && apt-get install -y --no-install-recommends \
#         build-essential \
#         python-dev \
#         python-setuptools \
#         python3-dev \
#         python3-setuptools \
#         libssl-dev  \
#         libffi-dev \
#         musl-dev \
#         python3-wheel \
#         pax-utils

# RUN pip install wheel && pip3 install wheel
# RUN pip install azdev

# # Build jmespath - this is the dependency that requires Go

# RUN git clone git://github.com/jmespath/jp
# WORKDIR /jp
# RUN scripts/build-self-contained \
#     && cp jp /usr/local/bin \
#     && chmod +x /usr/local/bin/jp \
#     && pip install --no-cache-dir --upgrade jmespath-terminal

# WORKDIR /

# RUN rm -rf ./jp

# RUN git clone https://github.com/Azure/azure-cli.git /azure-cli
# WORKDIR /azure-cli

# # 1. Build packages and store in tmp dir
# # 2. Install the cli and the other command modules that weren't included
# # 3. Temporary fix - install azure-nspkg to remove import of pkg_resources in azure/__init__.py (to improve performance)
# RUN ./scripts/install_full.sh 

# WORKDIR /

# # Remove CLI source code from the final image and normalize line endings.
# RUN rm -rf ./azure-cli && \
#     dos2unix /root/.bashrc /usr/local/bin/az

# RUN az --version

# Create an Agent User
RUN adduser --disabled-password --gecos '' azp && \
    adduser azp sudo && \
    echo '%sudo ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers

RUN mkdir /azp
WORKDIR /azp

# define the entrypoint script
COPY ./start.sh .
RUN chmod +x start.sh

RUN chown -R azp:azp /azp
USER azp
STOPSIGNAL SIGINT

CMD ["./start.sh"]