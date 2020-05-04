# SSH Honeypot

## Description of the scenario

A "honeypot" is used to detect or deflect unauthorized access to a system. The SSH Honeypot application demonstrates how 
SSH attacks against a server can be deflected and logged for further analysis. Such analysis reveals the IP address that 
the attack originated from  and the set of usernames and passwords being employed, which may help uncover leaks. This 
specific SSH Honeypot demonstration uses code developed by the [LongTail project](https://github.com/wedaa/LongTail-Log-Analysis). 
The actual SSH port (22) and port 2222 are used to trick the attacker, while SSH access is provided through a non-standard high 
number port, 49000 in this case. The rsyslog system log processing utility is used to determine the IP address, username, 
and password employed by the attacker when trying to attack the standard SSH ports 22 and 2222. 

This repository contains a basic demonstration of the SSH Honeypot, showing how SSH attempts from a potential attacker who 
is unaware of the port change can be logged. The demonstration is based on our interpretation of a small portion of the 
LongTail research project and is not intended as a complete representation of the authors' research. For more details on 
this project, please visit the [LongTail project](https://github.com/wedaa/LongTail-Log-Analysis) page. 

## Target Audience

### Instructors

If you are an instructor teaching cybersecurity concepts, you can use this example to provide hands-on experience with a 
honeypot, demonstrating how the use of non-standard ports along with logging can provide some measure of security and attack 
deflection.

### Students

If you are a student in a cybersecurity class, you can get further practical experience with how a honeypot can be used to 
prevent attacks and log the attack information for further analysis.

## Design and Architecture

This demonstration is designed using two Docker containers, one each for the server, and client. The server functions as an SSH 
server and hosts the modified SSH library and SSH config that logs all incoming SSH requests to ports 22 and 2222, while allowing 
SSH as usual on the higher port 49000. The client is a simple Ubuntu container that has the SSH client and attempts to SSH to the 
server at each of these ports. 

## Installation and Usage

The recommended approach to running this set of containers is on CHEESEHub, a web platform for cybersecurity demonstrations. CHEESEHub 
provides the necessary resources for orchestrating and running containers on demand. In order to set up this application to be 
run on CHEESEHub, an *application specification* needs to be created that configures the Docker image to be used, memory and 
CPU requirements, and, the ports to be exposed for each of the three containers. The JSON *spec* for this SSH Honeypot demonstration can be 
found [here](https://github.com/rkalyanapurdue/catalog/tree/ssh-honeypot/honeypot).

CHEESEHub uses Kubernetes to orchestrate its application containers. You can also run this application on your own Kubernetes 
installation. For instructions on setting up a minimal Kubernetes cluster on your local machine, refer to [Minikube](https://github.com/kubernetes/minikube). 

Before being able to run on either CHEESEHub or Kubernetes, Docker images needs to be built for the two application containers. 
Container definitions for the server and client can be found in the *server* and *client* directories respectively.
To build these containers, run:

```bash
cd server
docker build -t <server image tag of your choice> .

cd client
docker build -t <client image tag of your choice> .
```

Once the Docker images have been built, you can run the containers using just the Docker engine.

```bash
docker run -d <server image tag from above>

docker run -it <client image tag from above> /bin/bash
```
We run the client interactively with a terminal so we can get a shell in the client container. Since we will only attempt to SSH to the 
server from inside the client container, we do not bother to map any of the exposed ports from the server container.

### Usage
First we determine the IP address of the server container. Get a shell into the running server container by typing the following:

```bash
docker run -it <server container ID> /bin/bash
```

The container ID for the server can be determined by using *docker ps*. Once at the terminal on the server container, determine its IP by running:

```bash
ifconfig
```

Next, monitor the log messages on the server by typing:

```bash
tail -f /var/log/messages
```

At the client container's terminal, attempt to SSH to the server using the user *term*:

```bash
ssh term@<server IP address>
```

SSH should fail irrespective of the password used. Now attempt to use a different port (2222):

```bash
ssh -p 2222 term@<server IP address>
```

This should fail as well for each of the three tries. Finally, use the modified port (49000) and the right password *term*:

```bash
ssh -p 49000 term@<server IP address>
```

You should be dropped into a shell on the server container. 

When running on CHEESEHub, you can use the console for the server and client to run these respective commands.

## How to Contribute

To report issues or contribute enhancements to this application, open a GitHub issue. 

