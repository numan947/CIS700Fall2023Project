FROM --platform=linux/amd64 ubuntu:22.04
RUN apt-get update -y
RUN apt-get upgrade -y

RUN apt-get install -y zip unzip python3.10-venv python3-pip  build-essential gdb apt-transport-https curl lsb-release wget software-properties-common tree tmux net-tools iputils-ping git
RUN pip3 install toml twisted psutil

RUN apt-get autoremove -y


# Setting up the environment for fuzzing campaign
# we need to mount config to /Config, and Results to /Results
ENV USEDPYTHON=python3.10

RUN wget https://download.swift.org/swift-5.7-release/ubuntu2204/swift-5.7-RELEASE/swift-5.7-RELEASE-ubuntu22.04.tar.gz
RUN tar -xvzf swift-5.7-RELEASE-ubuntu22.04.tar.gz
ENV PATH="/swift-5.7-RELEASE-ubuntu22.04/usr/bin:${PATH}"
RUN git clone https://github.com/SpaceNaN/fuzzjit.git
WORKDIR /fuzzjit
RUN swift build -c release
RUN swift run FuzzilliCli --help
RUN apt install -y cmake ruby clang
WORKDIR /
RUN git clone https://chromium.googlesource.com/chromium/tools/depot_tools.git
ENV PATH="/depot_tools:${PATH}"
RUN mkdir /v8
WORKDIR /v8
RUN gclient config https://chromium.googlesource.com/v8/v8
RUN gclient sync
RUN apt-get install libglib2.0-dev -y
RUN apt-get install libgtk-3-dev -y
RUN apt-get install libatk-bridge2.0-dev -y
WORKDIR /v8/v8
RUN /fuzzjit/Targets/V8/fuzzbuild.sh
