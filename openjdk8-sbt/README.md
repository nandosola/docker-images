### Docker Repository
This image is hosted at `graphenedb/openjdk8-sbt`

### Usage
Go to the root of your Scala project and type:
  `docker run -v $PWD:/app -ti graphenedb/openjdk8-sbt`

The project will be mounted in the container's `/app` directory. Shortly after that, the SBT prompt will appear.

