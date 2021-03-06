FROM ubuntu:groovy

# For deferring setting up tzdata, avoiding interaction
ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y \
    tzdata build-essential default-jdk nasm llvm clang emacs-nox vim-nox tmux \
    curl git python3 ghc cabal-install haskell-stack flex bison unzip \
    bubblewrap locales

# Set up locale. See:
# https://stackoverflow.com/questions/28405902/how-to-set-the-locale-inside-a-debian-ubuntu-docker-container
RUN sed -i '/en_US.UTF-8/s/^# //g' /etc/locale.gen && locale-gen
ENV LANG en_US.UTF-8
ENV LANGUAGE en_US:en
ENV LC_ALL en_US.UTF-8
# Set up tzdata.
RUN ln -f -s /usr/share/zoneinfo/Europe/Stockholm /etc/localtime
RUN dpkg-reconfigure tzdata

RUN stack upgrade --binary-only
RUN install -c -m 0755 /root/.local/bin/stack /usr/bin/stack

RUN useradd -ms /bin/bash user
USER user
WORKDIR /home/user

RUN cabal update
RUN cabal install BNFC-2.8.4 alex happy
RUN stack update

RUN mkdir -p classpath/JLex/JLex
RUN cd classpath/JLex/JLex && \
    curl -O https://www.cs.princeton.edu/~appel/modern/java/JLex/current/Main.java && \
    javac Main.java

RUN mkdir -p classpath/CUP
RUN cd classpath/CUP && \
    curl -O http://www2.cs.tum.edu/projects/cup/releases/java-cup-bin-11b-20160615.tar.gz && \
    tar -xvf java-cup-bin-11b-20160615.tar.gz

RUN mkdir -p subm

ENV PATH="/home/user/.local/bin:${PATH}"
ENV PATH="/home/user/.cabal/bin:${PATH}"
ENV CLASSPATH="/home/user/classpath/JLex:${CLASSPATH}"
ENV CLASSPATH="/home/user/classpath/CUP/java-cup-11b-runtime.jar:${CLASSPATH}"
ENV CLASSPATH="/home/user/classpath/CUP/java-cup-11b.jar:${CLASSPATH}"

RUN git clone https://github.com/myreen/tda283
WORKDIR /home/user/tda283/tester

CMD /bin/bash
