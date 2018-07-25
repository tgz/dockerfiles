# Android ci builde node in docker

## Before build

1. Download Android NDK
2. Download tools_r25.2.3-linux.zip 

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