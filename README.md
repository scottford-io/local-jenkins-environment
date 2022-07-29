# Local Jenkins Environment
This repository provides code for provisioning a Jenkins environment using Docker and `docker-compose`. 


## Jenkins with docker-compose

The root of this repository contains a `docker-compose.yml` file that we can use to bring up a test environment comprised of a docker network, two docker volumes, Jenkins running in a docker container, and a Docker in Docker (dind) container to run our build jobs.

### docker-compose up
Make sure you are in the root directory of the project we cloned above and execute the following command:

```bash
docker-compose up -d
```

__EXAMPLE__

```bash
docker-compose up -d --build 
Creating network "local-jenkins-environment_jenkins" with the default driver
Creating volume "local-jenkins-environment_jenkins-docker-certs" with default driver
Creating volume "local-jenkins-environment_jenkins-data" with default driver
Building jenkins
[+] Building 0.8s (10/10) FINISHED                                                                                                                                                                  
 => [internal] load build definition from Dockerfile                                                                                                                                           0.0s
 => => transferring dockerfile: 37B                                                                                                                                                            0.0s
 => [internal] load .dockerignore                                                                                                                                                              0.0s
 => => transferring context: 2B                                                                                                                                                                0.0s
 => [internal] load metadata for docker.io/jenkins/jenkins:latest                                                                                                                              0.6s
 => [1/6] FROM docker.io/jenkins/jenkins@sha256:585cb556eca539224eb6bdc44fc9a766c101623cc2b2d415b4c94ad9c1a2a857                                                                               0.0s
 => CACHED [2/6] RUN apt-get update && apt-get install -y lsb-release                                                                                                                          0.0s
 => CACHED [3/6] RUN curl -fsSLo /usr/share/keyrings/docker-archive-keyring.asc   https://download.docker.com/linux/debian/gpg                                                                 0.0s
 => CACHED [4/6] RUN echo "deb [arch=$(dpkg --print-architecture)   signed-by=/usr/share/keyrings/docker-archive-keyring.asc]   https://download.docker.com/linux/debian   $(lsb_release -cs)  0.0s
 => CACHED [5/6] RUN apt-get update && apt-get install -y docker-ce-cli                                                                                                                        0.0s
 => CACHED [6/6] RUN jenkins-plugin-cli --plugins "blueocean:1.25.5 docker-workflow:1.28"                                                                                                      0.0s
 => exporting to image                                                                                                                                                                         0.0s
 => => exporting layers                                                                                                                                                                        0.0s
 => => writing image sha256:5cc1c6c839aa80b7983eeea85c5e78059eaf968309081b5eb84d3f5b043d205c                                                                                                   0.0s
 => => naming to docker.io/library/local-jenkins-environment_jenkins                                                                                                                           0.0s
Creating local-jenkins-environment_docker-dind_1 ... done
Creating local-jenkins-environment_jenkins_1     ... done
```

### Validate Jenkins is running

```bash
docker ps
```

```bash
docker ps
CONTAINER ID   IMAGE                               COMMAND                  CREATED         STATUS         PORTS                                                                                             NAMES
961362c96c4a   docker:dind                         "dockerd-entrypoint.…"   5 seconds ago   Up 4 seconds   2375/tcp, 0.0.0.0:2376->2376/tcp, :::2376->2376/tcp                                               local-jenkins-environment_docker-dind_1
7ae444aec63e   local-jenkins-environment_jenkins   "/usr/bin/tini -- /u…"   5 seconds ago   Up 4 seconds   0.0.0.0:5000->5000/tcp, :::5000->5000/tcp, 0.0.0.0:8080->8080/tcp, :::8080->8080/tcp, 50000/tcp   local-jenkins-environment_jenkins_1
```

### Retrieve administrator password

Once Jenkins starts up the first time, it automatically creates an Administrator password that we will need to log in. Run the following command to retrieve the admin password:

```bash
docker exec lacework_jenkins_1 cat /var/jenkins_home/secrets/initialAdminPassword
```

Open an web browser and go to [http://localhost:8080](http://localhost:8080) to launch Jenkins.

## Spinning Jenkins Down
Once you are ready to tear down the test environment you have two options. 

### Save the state
If you are going to continue to play around with Jenkins as it is configured and want to save the state you can run the following command:

```bash
$ docker-compose down
```

When you are ready to continue your work you just run:

```bash
$ docker-compose up -d
```

### Delete State 
If you want to complete tear down Jenkins and remove the docker volumes and network, run the following command:

```bash
$ docker-compose down --volumes
```