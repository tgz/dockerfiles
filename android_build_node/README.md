# Android ci builde node in docker

## Before build

1. [Download Android NDK r14b-linux](https://dl.google.com/android/repository/android-ndk-r14b-linux-x86_64.zip)
2. [Download tools_r25.2.3-linux](https://dl.google.com/android/repository/tools_r25.2.3-linux.zip) 

put file in resources folder

## Build

```bash
docker build -t name .
```

## Run 

```bash
docker run -it -v ~/workspace/path:/home/jenkins/workspace --name=docker.zerozero.cn/android_build -p 1399:22 -d android_node
```

## Useage

```bash
ssh jenkins@1.2.3.4 -p 1399

# password is jenkins
```
