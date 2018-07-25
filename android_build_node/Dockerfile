FROM ubuntu:16.04

SHELL ["/bin/bash", "-c"]
ENV DEBIAN_FRONTEND noninteractive

# Update apt-get
#COPY ./resources/sources.list /etc/apt/sources.list

# Installing packages
RUN apt-get update && apt-get -f -y --no-install-recommends install \
    locales \
    curl \
    git \
    openssh-client \
    pkg-config \
    wget \
    zip \
    unzip \
    openssh-server \
    python-software-properties \
    software-properties-common \
    apt-utils

RUN locale-gen en_US.UTF-8
ENV LANG=en_US.UTF-8 LANGUAGE=en_US.UTF-8 LC_ALL=en_US.UTF-8

# Java
RUN apt-add-repository ppa:openjdk-r/ppa \
    && apt-get update \
    && apt-get install -y openjdk-8-jdk 
# Export JAVA_HOME variable
ENV JAVA_HOME /usr/lib/jvm/java-8-openjdk-amd64/

#agent
RUN useradd -d /home/jenkins -m -s /bin/bash jenkins
RUN echo "jenkins:jenkins" | chpasswd
RUN mkdir -p /home/jenkins/workspace
RUN chown -R jenkins:jenkins /home/jenkins
#relief --privileged necessity while run container
RUN sed -i 's/\(session[[:blank:]]+required[[:blank:]]+pam_loginuid.so\)/#\1/' /etc/pam.d/sshd

WORKDIR /home/jenkins
USER jenkins

ENV ANDROID_HOME /home/jenkins/android-sdk
ENV ANDROID_SDK_HOME $ANDROID_HOME
ENV ANDROID_NDK_HOME /home/jenkins/android-ndk
ENV PATH $PATH:$ANDROID_SDK_HOME/tools:$ANDROID_SDK_HOME/platform-tools
ENV PATH $PATH:$ANDROID_NDK_HOME

# Install Android SDK Command Line Tools
RUN mkdir android-sdk
COPY ./resources/tools_r25.2.3-linux.zip android-sdk/
COPY ./resources/android-ndk-r14b-linux-x86_64.zip android-sdk/
RUN cd android-sdk \
    && unzip tools_r25.2.3-linux.zip \
    && rm tools_r25.2.3-linux.zip

# Install Android NDK
RUN cd android-sdk \
    && unzip android-ndk-r14b-linux-x86_64.zip \
    && rm android-ndk-r14b-linux-x86_64.zip \
    && mv android-ndk-* android-ndk

# Android Lincense
# android-sdk-license-2742d1c5
RUN mkdir "$ANDROID_HOME/licenses" || true \
    && echo "8933bad161af4178b1185d1a37fbf41ea5269c55" \
        > "$ANDROID_HOME/licenses/android-sdk-license" \
    && echo "84831b9409646a918e30573bab4c9c91346d8abd" \
        > "$ANDROID_HOME/licenses/android-sdk-preview-license"
# Install Android SDK
RUN cd android-sdk && yes | tools/bin/sdkmanager \
    "platform-tools" \
    "platforms;android-27"\
    "platforms;android-26" \
    "platforms;android-25" \
    "platforms;android-24" \
    "platforms;android-23" \
    "platforms;android-22" \
    "platforms;android-21" \
    "build-tools;28.0.0" \
    "build-tools;27.0.3" \
    "build-tools;27.0.2" \
    "build-tools;27.0.1" \
    "build-tools;27.0.0" \
    "build-tools;26.0.2" \
    "build-tools;26.0.1" \
    "build-tools;25.0.3" \
    "build-tools;25.0.0" \
    "build-tools;24.0.3" \
    "build-tools;23.0.3" \
    "build-tools;22.0.1" \
    "build-tools;21.1.2"

ENV PATH $PATH:$ANDROID_SDK_HOME/build-tools/27.0.3:$ANDROID_SDK_HOME/build-tools/28.0.0
# Gradle Configs
ENV JAVA_OPTS="-Xms512m -Xmx512m" \
    GRADLE_OPTS="-XX:+UseG1GC -XX:MaxGCPauseMillis=1000"

USER root

EXPOSE 22
COPY ./resources/startup.sh /usr/local/bin/startup.sh

RUN curl -sL https://deb.nodesource.com/setup_8.x | bash -
RUN apt-get install -y nodejs
RUN npm config set registry https://registry.npm.taobao.org --global \
  && npm config set disturl https://npm.taobao.org/dist --global \
  && npm install -g yarn react-native-cli \
  && yarn config set registry https://registry.npm.taobao.org --global \
  && yarn config set disturl https://npm.taobao.org/dist --global

COPY ./resources/id_rsa /home/jenkins/.ssh/id_rsa
COPY ./resources/id_rsa.pub /home/jenkins/.ssh/id_rsa.pub
COPY ./resources/known_hosts /home/jenkins/.ssh/known_hosts
RUN cd /home/jenkins && mkdir .gradle
COPY ./resources/gradle.properties /home/jenkins/.gradle/gradle.properties

RUN chown -R jenkins:jenkins /home/jenkins/.ssh
RUN sed -i '1i\ANDROID_HOME=/home/jenkins/android-sdk' /home/jenkins/.bashrc
RUN chown -R jenkins:jenkins /home/jenkins/.gradle

ENTRYPOINT ["/bin/bash","/usr/local/bin/startup.sh" ]
