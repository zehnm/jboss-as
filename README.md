# JBoss Application Server Docker image

This is an example Dockerfile with the legacy [JBoss application server 6.1.0.Final](http://jbossas.jboss.org/) based on the [WildFly Dockerfile from Red Hat](https://github.com/jboss-dockerfiles/wildfly).
This Dockerfile has been created to support a legacy JBoss AS migration to Wildfly. 

## Usage

To boot in standalone mode

    docker run -it --name jboss-as zehnm/jboss-as

## Application deployment

With the JBoss application server you can [deploy your application in multiple ways](https://docs.jboss.org/author/display/AS71/Application+deployment):

1. You can use CLI
2. You can use the web console
3. You can use the management API directly
4. You can use the deployment scanner

The most popular way of deploying an application is using the deployment scanner. In JBoss AS this method is enabled by default and the only thing you need to do is to place your application inside of the `deploy/` directory. By default it is `/opt/jboss/as/server/default/deploy/`.

The simplest and cleanest way to deploy an application to JBoss AS running in a container started from the `zehnm/jboss-as` image is to use the deployment scanner method mentioned above.

To do this you just need to extend the `zehnm/jboss-as` image by creating a new one. Place your application inside the `deploy/` directory with the `ADD` command (but make sure to include the trailing slash on the deployment folder path, [more info](https://docs.docker.com/reference/builder/#add)). You can also do the changes to the configuration (if any) as additional steps (`RUN` command).  

[A simple example](https://github.com/goldmann/wildfly-docker-deployment-example) was prepared to show how to do it, but the steps are following:

1. Create `Dockerfile` with following content:

        FROM zehnm/jboss-as
        ADD your-awesome-app.war /opt/jboss/as/server/default/deploy/
2. Place your `your-awesome-app.war` file in the same directory as your `Dockerfile`.
3. Run the build with `docker build --tag=jbossas-app .`
4. Run the container with `docker run -it jbossas-app`. Application will be deployed on the container boot.

This way of deployment is great because of a few things:

1. It utilizes Docker as the build tool providing stable builds
2. Rebuilding image this way is very fast (once again: Docker)
3. You only need to do changes to the base JBoss AS image that are required to run your application

## Logging

Logging can be done in many ways. [This blog post](https://goldmann.pl/blog/2014/07/18/logging-with-the-wildfly-docker-image/) describes a lot of them.

## Customizing configuration

Sometimes you need to customize the application server configuration. There are many ways to do it and [this blog post](https://goldmann.pl/blog/2014/07/23/customizing-the-configuration-of-the-wildfly-docker-image/) tries to summarize it.

## Image internals

This image extends the [`jboss/base-jdk:7`](https://github.com/jboss-dockerfiles/base-jdk/tree/jdk7) image which adds the OpenJDK distribution on top of the [`jboss/base`](https://github.com/jboss-dockerfiles/base) image. Please refer to the README.md for selected images for more info.

The server is run as the `jboss` user which has the uid/gid set to `1000`.

JBoss AS is installed in the `/opt/jboss/as` directory.

## Source

The source is [available on GitHub](https://github.com/zehnm/jboss-as).
