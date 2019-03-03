---
layout: post
title:  "Docker for implementing immutable infrastructures"
date:   2019-03-03 10:00:00 +0000
categories: docker swarm devops
---
# Context
Today I would like to share the experience we had in the way of moving our services to a cloud-like paradigm and the process of changing our mindset in order to embrace the immutable infrastructure approach.

This has been a period of nearly 3 years in which we moved in small baby steps, from manual compilations, builds and deployments to fully automated deployment to an on-premises cloud-like architecture. 

Our final pending step is to move to an actual external cloud provider as is Amazon, Azure or Google. 

This post is **not intended to be an exhaustive step by step guide**, this is not the purpose, but a general roadmap that hopefully can help you walk the same path towards immutable infrastructures and the cloud.

# Start dockerizing the services
The first step we took was to dockerize all our services. Dockerizing the services was an important step in achieving automatic deployments since it makes way easy to create a script or a Jenkins job that runs a container within the target server.

Basically we had to create a Jenkins pipe that watches the git repository for new code pushes and performs the following steps:

 - Compile the code, run the tests and build the Docker image with latest tag, all this with the help of Maven
 - Pushes the Docker image to the private Registry
 - Run the docker command that stops the current running container and starts the new one based on the latest version

That way, we achieved an easy and automatic process of continually deploying our services in the desired target environment, but at this point, we were still mapping the environments to actual physical machines.

# From servers to clusters and logical infrastructure
We had achieved an automatic way of deployment, but still had the problem of not being fault tolerant and scalable. Moreover, when we needed a new environment, i.e. Demo environment with limited access to our sales people, we had to ask for new servers, and start doing an "artesanal" configuration until we were able to automatically deploy the containers in the new environment.

## Being fault tolerant and scalable
The answer for achieving fault tolerance and scalability was Docker Swarm, which allows you to easily create a cluster of nodes (servers) acting as if they were a unit of computing power.

Docker gives you the concept of service, that is, a set of one or more containers replicated across the nodes of the cluster. That way, is easy to achieve both scalability and fault tolerance, scaling up or down the replicas of the containers as required. Docker Swarm takes care of keeping the services up and running and load balancing the work between the replicas.


**Note**: Of course, there are some programming considerations that you have to take into account in order to be able to scale a service up and down as for example avoiding keeping state.


## Easily creating environments with the Stack concept
Once we had the problem of replication and fault tolerance solved, we wanted to find an easy way to create or destroy environments with minimum effort. The answer was the Stack concept that Docker offers.

In essence, a Stack is a programmatic description of all the services you need to have up and running in order to run your business. The Stack can be sent to Docker Swarm in order to create and run the services it describes. 

We took advantage of this concept and created one Stack per environment, so we were able to easily create new environments on demand, and just add more server to the cluster if more computing power was required.

## The importance of monitoring
Once having your cluster running, is extremely important to know how it is behaving. Unless your cluster is made up of 5 server at most, it is impracticable to inspect one by one under an error situation.

There are stacks already prepared that you can launch into your cluster and starts giving you good information about what is going on in your cluster.

TODO: Add link to the stack ...

Another story is monitoring what is going on inside the logic of your applications, it is another facet of monitoring that you will have to incorporate when you start having a considerable number of replicas.

# Conclusions
It is not a secret that containers in general and Docker in particular is already a revolution in how we are deploying our applications. Sooner or later, a company will have to adopt this new paradigm and take advantage of the cloud.
