# Docker commands

## Build and push the images

```bash
docker build -f ./Dockerfile-arm32v7 -t mleitz1/rpi-java8:arm32v7 .
docker build -f ./Dockerfile-arm64v8 -t mleitz1/rpi-java8:arm64v8 .

docker push mleitz1/rpi-java8:arm32v7
docker push mleitz1/rpi-java8:arm64v8
```

## Create the manifest

```bash
docker manifest create mleitz1/rpi-java8:latest mleitz1/rpi-java8:arm32v7 mleitz1/rpi-java8:arm64v8
```

## Push the manifest

```bash
docker manifest push mleitz1/rpi-java8:latest
```

## Test image

```bash
docker exec -it mleitz1/rpi-java8:latest /usr/bin/bash
```

# References

 * [docker-multi-architecture-images](https://medium.com/@mauridb/docker-multi-architecture-images-365a44c26be6)
