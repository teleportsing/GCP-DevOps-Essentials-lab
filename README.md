# Continuous Delivery with Jenkins in Kubernetes Engine
In this lab, you will learn how to set up a continuous delivery pipeline with Jenkins on Kubernetes engine. Jenkins is the go-to automation server used by developers who frequently integrate their code in a shared repository. The solution you'll build in this lab will be similar to the following diagram:

In the Cloud Architecture Center, see [Jenkins on Kubernetes Engine](https://cloud.google.com/kubernetes-engine/docs/archive/jenkins-on-kubernetes-engine) to learn more about running Jenkins on Kubernetes.

# What you'll learn

In this lab, you'll complete the following tasks to learn about running Jenkins on Kubernetes:

 * Provision a Jenkins application into a Kubernetes Engine Cluster
 * Set up your Jenkins application using Helm Package Manager
 * Explore the features of a Jenkins application
 * Create and exercise a Jenkins pipeline


#Prerequisites

This is an advanced level lab. Before taking it, you should be comfortable with at least the basics of shell programming, Kubernetes, and Jenkins. Here are some labs that can get you up to speed:

 * [Introduction to Docker](https://www.cloudskillsboost.google/focuses/1029?parent=catalog)
 * [Hello Node Kubernetes](https://www.cloudskillsboost.google/focuses/564?parent=catalog)
 * [Managing Deployments Using Kubernetes Engine](https://www.cloudskillsboost.google/focuses/639?parent=catalog)
 * [Setting up Jenkins on Kubernetes Engine](https://www.cloudskillsboost.google/focuses/1776?parent=catalog)

Once you are prepared, scroll down to learn more about Kubernetes, Jenkins, and Continuous Delivery.


# What is Kubernetes Engine?

Kubernetes Engine is Google Cloud's hosted version of Kubernetes - a powerful cluster manager and orchestration system for containers. Kubernetes is an open source project that can run on many different environments—from laptops to high-availability multi-node clusters; from virtual machines to bare metal. As mentioned before, Kubernetes apps are built on containers - these are lightweight applications bundled with all the necessary dependencies and libraries to run them. This underlying structure makes Kubernetes applications highly available, secure, and quick to deploy—an ideal framework for cloud developers.


# What is Jenkins?

[Jenkins](https://www.jenkins.io/) is an open-source automation server that lets you flexibly orchestrate your build, test, and deployment pipelines. Jenkins allows developers to iterate quickly on projects without worrying about overhead issues that can stem from continuous delivery.


What is Continuous Delivery / Continuous Deployment?

When you need to set up a continuous delivery (CD) pipeline, deploying Jenkins on Kubernetes Engine provides important benefits over a standard VM-based deployment.

When your build process uses containers, one virtual host can run jobs on multiple operating systems. Kubernetes Engine provides ephemeral build executors—these are only utilized when builds are actively running, which leaves resources for other cluster tasks such as batch processing jobs. Another benefit of ephemeral build executors is speed—they launch in a matter of seconds.

Kubernetes Engine also comes pre-equipped with Google's global load balancer, which you can use to automate web traffic routing to your instance(s). The load balancer handles SSL termination and utilizes a global IP address that's configured with Google's backbone network—coupled with your web front, this load balancer will always set your users on the fastest possible path to an application instance.

Now that you've learned a little bit about Kubernetes, Jenkins, and how the two interact in a CD pipeline, it's time to go build one.

# Task 1. Download the source code

1. To get set up, open a new session in Cloud Shell and run the following command to set your zone <filled in at lab start> :

`gcloud config set compute/zone `

2. Then copy the lab's sample code:

`gsutil cp gs://spls/gsp051/continuous-deployment-on-kubernetes.zip .`













