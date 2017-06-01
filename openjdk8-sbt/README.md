### Docker Repository
This image is hosted at [`nandosola/openjdk8-sbt`](https://hub.docker.com/r/graphenedb/openjdk8-sbt/)

### Versions
* `Alpine Linux, edge branch`
* `OpenJDK 64-Bit build 1.8.0_111-b14`
* `SBT 0.13.6`

### Usage
Go to the root of your Scala project and type:
  `docker run -v $PWD:/app -ti graphenedb/openjdk8-sbt`

The project will be mounted in the container's `/app` directory. Shortly after that, the SBT prompt will appear.

