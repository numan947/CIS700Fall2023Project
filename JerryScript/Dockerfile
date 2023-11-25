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
RUN git clone https://github.com/numan947/fuzzjit.git
WORKDIR /fuzzjit
RUN swift build -c release
RUN swift run FuzzilliCli --help
RUN apt install -y cmake ruby clang rustc cargo llvm pkg-config m4


WORKDIR /
# WORKDIR /fuzzing/
RUN git clone https://github.com/jerryscript-project/jerryscript.git /JerryScript
RUN apt install -y cmake ruby python2
RUN ln -s /usr/bin/python2 /usr/bin/python
RUN apt-get install libglib2.0-dev -y
RUN apt-get install libgtk-3-dev -y
RUN apt-get install libatk-bridge2.0-dev -y
WORKDIR /JerryScript
RUN git checkout 8ba0d1b6ee5a065a42f3b306771ad8e3c0d819bc
RUN patch  -p1 < /fuzzjit/Targets/Jerryscript/Patches/jerryscript.patch
RUN chmod +x /fuzzjit/Targets/Jerryscript/fuzzbuild.sh
RUN /fuzzjit/Targets/Jerryscript/fuzzbuild.sh

WORKDIR /fuzzjit
CMD swift run -c release FuzzilliCli --profile=jerryscript --timeout=500 --storagePath=/JerryResults /JerryScript/build/bin/jerry
# CMD swift run -c release FuzzilliCli --profile=jerryscript --timeout=500 --storagePath=./jerryscript /JerryScript/