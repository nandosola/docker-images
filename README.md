## Docker Images
This project contains some useful Docker images:
* `openjdk8-sbt` as a base image to build and test Scala projects based on OpenJDK 8 and SBT 0.13.6; pretty much if for some reason you're still using Scala 2.10.6. 
* `pimped-alpine` is an `alpine:edge` image to build child images for CircleCI 2.0 primary images and more generic projects. `alpine:edge` is used because there are more available packages in this branch than in the stable ones.

