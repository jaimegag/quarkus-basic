# quarkus-basic

This project uses Quarkus, the Supersonic Subatomic Java Framework.

This simple Quarkus app has been created to test Quarkus with Cloud Native Buildpacks, Tanzu Build Service and Tanzu Application Platform

If you want to learn more about Quarkus, please visit its website: https://quarkus.io/ .

For the original quarkus-generated Readme please check the [README-original](/README-original.md)

## Packaging and running the application

The application can be packaged using:
```shell script
./mvnw package
```
It produces the `quarkus-run.jar` file in the `target/quarkus-app/` directory.
Be aware that it’s not an _über-jar_ as the dependencies are copied into the `target/quarkus-app/lib/` directory.

The application is now runnable using `java -jar target/quarkus-app/quarkus-run.jar`.

If you want to build an _über-jar_, execute the following command:
```shell script
./mvnw package -Dquarkus.package.type=uber-jar
```

The application, packaged as an _über-jar_, is now runnable using `java -jar target/*-runner.jar`.

## Building with Cloud Native Buildpacks

To build this application with the default Java and Maven Buildpacks, make sure to set these environment variables
```shell script
BP_MAVEN_BUILT_ARTIFACT: "target/quarkus-app/lib/ target/quarkus-app/*.jar target/quarkus-app/app/ target/quarkus-app/quarkus/"
BP_MAVEN_BUILD_ARGUMENTS: "package -DskipTests=true -Dmaven.javadoc.skip=true -Dquarkus.package.type=fast-jar"
```

In a cnbimage (kpack) Image, it looks like this
```shell script
spec:
  build:
    env:
    - name: BP_MAVEN_BUILT_ARTIFACT
      value: "target/quarkus-app/lib/ target/quarkus-app/*.jar target/quarkus-app/app/ target/quarkus-app/quarkus/"
    - name: BP_MAVEN_BUILD_ARGUMENTS
      value: "package -DskipTests=true -Dmaven.javadoc.skip=true -Dquarkus.package.type=fast-jar"
```

But in a TAP world you want to make sure the Variables are backed in the ClusterImageTemplate.
Luckily the `kpack-template` already gets all the workload build env vars and injects them in the `.spec.build.env` of the kpack Image that it stamps. All we need to do is define them in the `workload.yaml`.

Alternatively there is a Quarkus Buildpack prototype that sets these variables for you: https://github.com/matejvasek/quarkus-bp

## Deploy to Tanzu Application Platform

Use the provided `/config/workload.yaml` to deploy the application into TAP
```shell script
tanzu apps workload create quarkus-basic \
  -f ./config/workload.yaml \
  --yes
```

Use the provided `/config/catalog-info.yaml` to import the application into the TAP-GUI