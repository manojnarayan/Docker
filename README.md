# Docker
Deploying a Java War in a Docker Container

Deploy a WAR File to Tomcat
WAR (Web Application Archive) is a zipped archive file that packages all the web application-related files and their directory structure.

To make things simple, deploying a WAR file on Tomcat is nothing but copying that WAR file into the deployment directory of the Tomcat server. The deployment directory in Linux is $CATALINA_HOME/webapps. $CATALINA_HOME denotes the installation directory of the Tomcat server.

After this, we need to restart the Tomcat server, which will extract the WAR file inside the deployment directory.

Deploy WAR in Docker Container
Let’s assume that we have a WAR file for our application, ROOT.war, which we need to deploy to the Tomcat server.

To achieve our goal, we need to first create a Dockerfile. This Dockerfile will include all the dependencies necessary to run our application.

Further, we’ll create a Docker image using this Dockerfile followed by the step to launch the Docker container.

Let’s now dive deep into these steps one by one.

 Create Dockerfile
We’ll use the latest Docker image of Tomcat as the base image for our Dockerfile. The advantage of using this image is that all the necessary dependencies/packages are pre-installed. For instance, if we use the latest Ubuntu/CentOS Docker images, then we need to install Java, Tomcat, and other required packages manually.

Since all the required packages are already installed, all we need to do is copy the WAR file, ROOT.war, to the deployment directory of the Tomcat server. That’s it!

Let’s have a closer look:

$ ls
Dockerfile  ROOT.war
$ cat Dockerfile 
FROM tomcat

COPY ROOT.war /usr/local/tomcat/webapps/
$CATALINA_HOME/webapps denotes the deployment directory for Tomcat. Here, CATALINA_HOME for the official Docker image of Tomcat is /usr/local/tomcat. As a result, the complete deployment directory would turn out to be /usr/local/tomcat/webapps.

The application that we used here is very simple and does not require any other dependencies.

Build the Docker Image
Let’s now create the Docker image using the Dockerfile that we just created:

$ pwd
/baeldung
$ ls
Dockerfile  ROOT.war
$ docker build -t myapp .
Sending build context to Docker daemon  19.97kB
Step 1/2 : FROM tomcat
 ---> 710ec5c56683
Step 2/2 : COPY ROOT.war /usr/local/tomcat/webapps/
 ---> Using cache
 ---> 8b132ab37a0e
Successfully built 8b132ab37a0e
Successfully tagged myapp:latest
The docker build command will create a Docker image with a tag myapp. 

Make sure to build the Docker image from inside the directory where the Dockerfile is located. In our example above, we’re inside the /baeldung directory when we build the Docker image.

Run Docker Container
So far, we’ve created a Dockerfile and built a Docker image out of it. Let’s now run the Docker container:

$ docker run -itd -p 8080:8080 --name my_application_container myapp
e90c61fdb4ac85b198903e4d744f7b0f3c18c9499ed6e2bbe2f39da0211d42c0
$ docker ps 
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                    NAMES
e90c61fdb4ac        myapp               "catalina.sh run"   6 seconds ago       Up 5 seconds        0.0.0.0:8080->8080/tcp   my_application_container
This command will launch a Docker container with the name my_application_container using the Docker image myapp. 

The default port for the Tomcat server is 8080. Therefore, while starting the Docker container, make sure to always bind the container port 8080 with any available host port. We’ve used host port 8080 for simplicity here.

Verify the Setup
Let’s now verify everything that we’ve done so far. We’ll access the URL http://<IP>:<PORT> in the browser to view the application.

Here, the IP denotes the public IP (or private IP in some cases) of the Docker host machine. The PORT is the container port that we have exposed while running the Docker container (8080, in our case).

We can also verify the setup using the curl utility in Linux:

$ curl http://localhost:8080
!
In the command above, we’re executing the command from the Docker host machine. So, we are able to connect to the application using localhost. In response, the curl utility prints the raw HTML of the application webpage.

Conclusion
In this article, we’ve learned to deploy a Java WAR file in a Docker container. We started by creating the Dockerfile using the official Tomcat Docker image. Then, we built the Docker image and ran the application container.

At last, we verified the setup by accessing the application URL.
