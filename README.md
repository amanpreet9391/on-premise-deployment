# On Premise Deployment
### This is a project for Social Cops for the profile of Devops Engineer.
## Problem Statement
Solve on-premise application deployments for enterprise clients. Taking an example of a containerized web application as a single microservice, say Parse Server Example, we are asked to deploy it on a cluster of machines. These can be virtual machines on the 
cloud or enterprise-level client-managed hardware. Along with this consider the points mentioned below:
* Ease of clustered enterprise-level deployments
* Incremental remotely triggered application updates
* Easy remote debugging
* Health Alerts and Monitoring
* Application Security (with source code protection)
* Disaster management
## Tools
In order to sort out foreseeable problems related to an on-premise deployment which are mentioned above, different tools are required.
###  Ease of clustered enterprise level deployments
For clustered enterprise-level deployments, the most common tools used are `Docker Swarm, Kubernetes, Mesosphere DC/OS `etc. Among these more nowadays the most widely used is Kubernetes. In this project, Docker Swarm is being used.
### Incremental remotely triggered application updates
`Jenkins` can be used for this. Integrate Jenkins with git, gitlab or bitbucket(wherever code is present). Jenkins is a software that allows continuous integration and continuous deployment. Jenkins will be installed on a server where the central build will take place. 
### Easy remote debugging
For remote debugging, we are using `Node-Inspector`.Node Inspector is a debugger interface for Node.js applications that use the Blink Developer Tools (formerly WebKit Web Inspector).
### Health Alerts and Monitoring
The most commonly used monitoring tools are Grafana, Prometheus, ELK stack. For this project, we will be using `Grafana`.Grafana is an open source visualization tool that can be used on top of a variety of different data stores but is most commonly used together with Graphite, InfluxDB, and also Elasticsearch and Logz.io.
### Application Security (with source code protection)
Git, Gitlab and Bitbucket are used. Among this, git is most widely used because of features like tight control access, security testings etc.
### Disaster Management
This can be handled by either using the NFS server or creating replicas. The tools which can be used is `Nginx`. NGINX is a free, open-source, high-performance HTTP server and reverses proxy, as well as an IMAP/POP3 proxy server. NGINX is known for its high performance, stability, rich feature set, simple configuration, and low resource consumption.
## Implementation(steps)
The steps were taken in this project for deployment while keeping in consideration the different problems related to on-premise deployment.
### 1. Containerization
All the server and applications used in this project are containerized using Docker containers. 
* Microservice mentioned in assignment i.e.parse-server-example is containerized. First, create its Dockerfile and then build that using
`docker build . -t amanpreet9391/parse-server` command. (Here amanpreet9391/parse-server is the name given to image). After build command, this has now been created as an image. To run this image use `docker run -p 1337:1337 -t amanpreet9391/parse-server` command. 
To directly run this image use `docker pull amanpreet9391/parse-server`(this container is also present in docker hub). 1337 is the
port on which this server will run. To check whether the server is running, browse for https://localhost:1337/test. 
![parse-server](https://user-images.githubusercontent.com/25201552/52538270-b82fde80-2d96-11e9-8998-db7d7ed0b98e.png)
This page implies that the server is running.
* As already mentioned for remote debugging, Node Inspector is used. For setup of node-inspector and start it, a container is created.
Its Dockerfile is being created first, which is present in the node-inspector folder. In the same way, as done for parse-server-example
first, build the Dockerfile with command ` docker build . -t amanpreet9391/node-inspector`(amanpreet9391/node-inspector is the name given
to the node-inspector image). To run this image, run `docker run -p 8080:8080 -p 5858:5858 -p 9229:9229 -t amanpreet9391/node-inspector`
This container is pushed on to the docker hub, so it can be pulled from there as well by `docker pull amanpreet9391/node-inspector` command.
![chrome-devtool](https://user-images.githubusercontent.com/25201552/52538385-f974be00-2d97-11e9-8b16-18cc8fc5b947.png)
Just copy the URL shown in the last after running this Docker Container. The dashboard will appear. Debug and perform a different task with this on your sicript.
* For remotely triggered application updates, Jenkins is used. Jenkins is also containerized.There is already a docker container available for Jenkins, with the name Jenkins. For this just run ` docker pull, Jenkins` .  Then `docker run -p 8081:8080 -t Jenkins`(Jenkins will run at port 8081). You can access Jenkins dashboard on URL https://localhost:8081.
![jenkins](https://user-images.githubusercontent.com/25201552/52538524-d1865a00-2d99-11e9-861c-548c84f6a350.png)
* In the same way running Docker container for grafana by ` docker pull grafana/grafana` (docker container available on docker hub).
Run the container by `docker run -p 3000:3000 -t grafana/grafana`.
![grafana-dashboard](https://user-images.githubusercontent.com/25201552/52538586-6c7f3400-2d9a-11e9-9324-88e35cb11ed9.png)
This is the grafana dashboard which appears after browsing https://localhost:3000.
![grafana-logs](https://user-images.githubusercontent.com/25201552/52538606-a819fe00-2d9a-11e9-9ab8-d4bb078f52fe.png)
This shows the logs.
### 2. Compose
There are containers for every service. To run these services simultaneously, Docker Compose is being used. `docker-compose.yml` file is the docker compose file to run all the above-mentioned containers or services simultaneously. By `docker-compose up` command, compose file will run resulting starting of all these services.
![compose](https://user-images.githubusercontent.com/25201552/52540635-bb849380-2db1-11e9-88ff-9e4aaaa77f3d.png)
### 3. Clustering
With the help of the docker compose file, we can run these services on a single machine. To deploy those services on more than one machine, we need to make a cluster. In this project, Docker Swarm is used. In this cluster, one node becomes manager and rest becomes workers. The manager runs commands `docker swarm init --advertise-addr <ip of manager>` and initialize docker swarm. It creates a token. After that paste that token to the worker nodes so that they can join the docker swarm as well. To deploy parse-server, a docker swarm is created with one manager(manager) and two worker nodes(worker-1 and worker-2).
`docker swarm init --advertise-addr <ip of manager>`
##### Initialising docker swarm on manager node
![docker swarm started](https://user-images.githubusercontent.com/25201552/52540250-a1e14d00-2dad-11e9-8e40-5c4f03db7bbc.png)
##### Copy the token and paste on worker-1
![docker swarm worker-1 copy the token](https://user-images.githubusercontent.com/25201552/52540268-c76e5680-2dad-11e9-870b-207f7f55017d.png)
##### Copy the token and paste it on worker-2
![copy the token to add this node as worker-2](https://user-images.githubusercontent.com/25201552/52540333-714de300-2dae-11e9-85fa-243b671d0634.png)
##### worker-1 and worker-2 has joined the docker swarm
![node is added as worker in swarm](https://user-images.githubusercontent.com/25201552/52540377-d1448980-2dae-11e9-8d01-4404b71389ee.png)
![worker-2](https://user-images.githubusercontent.com/25201552/52540380-da355b00-2dae-11e9-9007-07103709defa.png)
##### To check the connected nodes in docker swarm run `docker node ls` on manager node
![list of all nodes in swarm](https://user-images.githubusercontent.com/25201552/52540404-0c46bd00-2daf-11e9-9353-b8882ae18a4a.png)
##### To create service in manager run `docker service create --name "parse-server" -p 1337:1337 amanpreet9391/parse-server1`
![running parse-server service on swarm cluster](https://user-images.githubusercontent.com/25201552/52540425-4748f080-2daf-11e9-9770-3dfdb2067011.png)
For docker swarm commands refer to `Docker-Swarm-commands.txt`

