# Debugging Jenkinsfile Runner

## Debugging without Docker

Jenkinsfile Runner is a common Java application.
You can debug it by passing Debug flags to JVM on the startup and then connecting a remote debugger from your IDE.

```bash
java -agentlib:jdwp=transport=dt_socket,server=y,suspend=y,address=5005 \
     -jar ../../app/target/jenkinsfile-runner-standalone.jar \
     -p ../../vanilla-package/target/plugins/ -w ../../vanilla-package/target/war/jenkins.war -f . 
```

## Debugging in Docker

In case you want to debug Jenkinsfile Runner, you need to use the "Vanilla" Docker image built following the steps mentioned in the section above.

Then, set the `DEBUG` environment variable and expose the port where to connect the remote debug. Note Jenkinsfile Runner itself
considers 5005 as debugging port but you can map such port to whatever value you prefer through Docker port mapping.

```bash
docker run --rm -e DEBUG=true -p 5005:5005 -v $PWD/test:/workspace jenkinsfile-runner:my-production-jenkins
```

In case you are having issues when the Docker image is generated in another way (for example through [Custom WAR Packager](https://github.com/jenkinsci/custom-war-packager/)),
you can directly pass `JAVA_OPTS` using the Docker run arguments:

```bash
docker run --rm -e JAVA_OPTS='-agentlib:jdwp=transport=dt_socket,server=y,suspend=y,address=5005' -p 5005:5005 -v $PWD/test:/workspace jenkinsfile-runner:my-production-jenkins
```
