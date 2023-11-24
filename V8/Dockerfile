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

WORKDIR /
# WORKDIR /fuzzing/
RUN wget -O  /WebKit-7614.3.4.11.2.tar.gz https://github.com/WebKit/WebKit/archive/refs/tags/WebKit-7614.3.4.11.2.tar.gz?raw=true
RUN tar -xvzf WebKit-7614.3.4.11.2.tar.gz
RUN mv WebKit-WebKit-7614.3.4.11.2/ WebKit/
WORKDIR /WebKit
RUN apt install -y cmake ruby clang
RUN patch  -p1 < /fuzzjit/Targets/JavaScriptCore/Patches/webkit.patch 
RUN /fuzzjit/Targets/JavaScriptCore/fuzzbuild.sh
