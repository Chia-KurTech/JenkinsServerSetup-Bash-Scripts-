
# Spring Boot Java App on AWS


This is a Spring Boot Java application built with Apache Maven. It is containerized, pushed to dockerhub repo with Docker and deployed on AWS EC2.


## Prerequisites

Java Development Kit (openjdk 17)

Apache Maven

Docker

Dockerhub account

AWS EC2 instance (Ubuntu)

Git


## EC2 Configuration (Ubuntu)

The following BASH script will install all of the dependencies needed for this project. There are 2 options listed below to initiate script:

Option #1

First install git manually to clone repo containing BASH script: 

    apt install git

Clone repo to the root directory:

    git clone http://whitespace-vxijer@git.codesubmit.io/whitespace/devops-assessment-jpvfzf

cd into folder containing bash script:

    cd whitespace/devops-assessment-jpvfzf/bashs

Allow permissions to execute bash script:
   
    chmod +x configf.sh

Run BASH script:

    ./configf.sh

Option #2

Create your own .sh file/copy and paste syntax below:

    vi Bash-script-name.sh

 Copy and Paste syntax:   

    #!/bin/bash

    # Declare variables
    maven_package="maven"
    jdk_package="openjdk-17-jdk"
    docker_package="docker.io"
    git_package="git"

    # Update package manager cache
    sudo apt update

    # Install JDK
    sudo apt install $jdk_package

    # Install Maven
    sudo apt install $maven_package

    # Install Docker
    sudo apt install $docker_package

    # Install Git
    sudo apt install $git_package

Save file by exiting insert mode "Esc" and entering:
 
    :wq

Allow permissions to execute bash script:

    chmod +x Bash-script-name.sh

Run BASH script:

    ./Bash-script-name.sh
## Building the Image with Maven

Clone the Spring Boot Application Repo to your root directory:

    git clone https://github.com/spring-guides/gs-spring-boot.git

Move into the `gs-spring-boot/complete` directory:

    cd gs-spring-boot/complete

Run the following command to test the Spring Boot Application:

    mvn spring-boot:run

When you have a successfull test, the next command will compile and create a JAR file:

    mvn clean package

The JAR file will be created in the target directory 'gs-spring-boot/complete/target' of your project once the build is complete.  To follow good practices test the JAR file with the following command:

    java -jar spring-boot-complete-0.0.1-SNAPSHOT.jar

Once the test is complete you can move to the next step of dockerizing the application.












# Dockerizing the Application/Create dockerfile

Create a file named "dockerfile" in the root directory of your project.

    vi dockerfile

Add the following syntax to the "dockerfile" and replace your "jar-file.jar" with the name of the JAR file that you built in the previous step:

    FROM amazoncorretto:17-alpine-jdk
    WORKDIR /app
    COPY complete/target/jar-file.jar .
    ENTRYPOINT ["java","-jar","jar-file.jar"]


Save the "dockerfile".
## Building the Docker Image

To make sure you have permission to access the Docker daemon. After running this command, you will be able to run Docker commands without having to use sudo. This can be convenient if you use Docker frequently and do not want to have to enter sudo every time you run a Docker command.

    sudo usermod -aG docker $USER

Log out of the current session and log back in to apply the group change.

Navigate to the root directory '/gs-spring-boot' of your project in a terminal.  Run the following command to build the Docker image:

    docker build -t gs-spring-boot .

Before you tag and push the image to dockerhub, test run the image with the following command:

    docker run --rm -p 8080:8080 gs-spring-boot

The test should output the same text as the tests you ran prior to building the image. Once we have a successful test now you can start the process of pushing the image to the dockerhub repo.




## Login to DockerHub

Login to dockerhub from CLI with the following syntax:

     docker login --username=yourusername

It will then prompt you to enter your password. Once the login is complete you are ready for the next step.




## Tag and Push Docker Image

Before you push your image to dockerhub you want to practice good habits by custom tagging your image:

    docker tag gs-spring-boot <username>/<repository>:<tag>

Next you will push the image to your dockerhub repository:
    
    docker push <username>/<repository>

Check your dockerhub repository to validate the push was successful by visiting the UI 'hub.docker.com' via web browser.
## Pull Docker image

Pull your docker image for a final test to validate functionality:

    docker pull <username/<repository>:<tag>

## Run Docker container

    docker run --rm -d 8080:8080 <username>/<repository>:<tag>

The command below will show you the containers that are currently running, along with some basic information about each container, such as the container ID, image name, and status.

    docker ps

You can also use various options with the docker ps command to filter or format the output. For example, the '-a' option will show all containers (running and stopped), and the '-q' option will display only the container IDs.

example:

    docker ps -a
## Test running Docker container from CLI

Run the following command to test the container from the CLI:

    curl localhost:8080

If everything is functioning correctly the output should read "Greetings from Spring Boot!"


## Test running Docker container from Web Browser

To test the application from a web browser, first, you must edit the inbound rules inside of the security groups for our EC2 instance. This will expose port:8080 for external traffic.

The following steps are within the AWS User Interface:

Open security groups and click "edit inbound rules"

Next click "add rule"

Add port:8080 to your "port range"

Modify source to "Anywhere-IPv4

The type should be "Custom TCP"

Once you have made those changes click "Save rules"

After you have saved the rules, go to your browser and input your server's IP address followed by port:8080.

You will now see "Greetings from Spring Boot!" as the output.

*Note:  You should not use this port mapping strategy for a Production environment due to the Security risks of the Application*
## Remote Registry Link/Application URL

DockerHub Repo:
    
    https://hub.docker.com/u/yc22

Application URL:

    :8080
## Summary

I choose challenge #2 because I enjoy working with docker and found it as another opportunity to showcase my skills with containerization. My assumption was that the target application or service was designed to run in a containerized environment and can be packaged as a Docker image.  My approach was to find a base image that includes the necessary dependencies for running a Java application, such as the Java runtime and other required libraries. My next approach would be to implement a plan for monitoring and maintaining the Docker container once it is deployed in a production environment.      


