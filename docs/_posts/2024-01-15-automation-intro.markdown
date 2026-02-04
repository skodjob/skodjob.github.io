---
layout: post
title: What does automation-hub mean?
date: 2024-01-15 16:00:00 +0100
categories: automation
image: /assets/automation.jpg
---

## What is the automation-hub
Simple answer is that [automation-hub](https://github.com/skodjob/automation-hub) is a collection of [Ansible](https://www.ansible.com/) scripts, [Kubernetes](https://kubernetes.io/) deployment and configuration files, [Tekton](https://tekton.dev/) pipelines, and [ArgoCD](https://argo-cd.readthedocs.io/en/stable/) applications and application-sets.
That's quite a very long list of used tools within the repository, and we don't even mention jinja2 templates for example!
However, there is a good reason for it and we will try to take a closer look on all of them.

### Kubernetes
Kubernetes is known as one of the best way to orchestrate containerized applications in a large scale.
It provides scalability, reliability, monitoring and many more features for applications running on top of the Kubernetes cluster.
In our case we are using enterprise implementation of Kubernetes called [Openshift](https://www.redhat.com/en/technologies/cloud-computing/openshift/container-platform).
That was just a brief introduction of Kubernetes - it is a platform for running containerized applications.
We don't want to do any deep dive into Kubernetes in this blog post so if you are interested in it, we suggest to read any other blogs, you will find plenty of them.
Now let's try to answer the question "Why we use it in the scope of our experiments"?
Answer is simple "We use Kubernetes, because we work as **QA** and the applications, which we put under test, are targeted to Kubernetes."

### Ansible
Do you know the situations when you have many automation scripts written in `bash` or `powershell` and you start loosing your mind in a bunch of `$` or `sed` expressions?
We know how frustrating can be debugging shell script or running them on the remote machine.
Thankfully, there is a tool that can make your life much easier - `Ansible`.
Ansible is a declarative way how to write automation scrips in yaml format, which also allows you to use libraries with already implemented steps and tasks that interact with many components, so you do not need to re-invent the wheel.
Because the initial installation path of our environment requires a lot of steps, Ansible is the correct way to deduplicate steps, create a templates in `jinja` and drive execution based on tags.
We are able to run Ansible playbook on provided Kubernetes cluster, and deploy the whole testing infrastructure including multi-cluster environment with all needed operators and tools (we will talk about all of these stuff in upcoming posts). 

### Tekton
There are many ways where and how to run CI/CD pipelines.
You surely know systems like Gitlab-CI, Jenkins or TeamCity.
Tekton is also CI/CD system, but it has an advantage which is really important for us - it is `cloud-native`.
That means it is implemented to run on top of Kubernetes, and it is defined by resources written in yaml format.
Tekton pipeline resource allows you to split steps you want to run between different containers with different base images.
It can save you resources needed for running pipeline, and also you do not need to build large container image with all required tools installed.
Let me explain it on a simple example.
Imagine pipeline, which does an API request using `curl` and then executes `maven` command.
The first step can be done in a simple small container just with `curl` installed and `maven` step could run in different container with Java and maven installed.

We use Tekton CI/CD mainly for continuous check of deployed environment and system under test, also for triggering Kubernetes cluster upgrades, and many more day-to-day operations.

### ArgoCD
All previous technologies were mostly about platform and DevOps practices, but now let's move to another buzzword `GitOps`.
ArgoCD is GitOps tool for deploying Kubernetes applications from declarations stored in SCM.
Basically if someone puts changes of application configuration into git repository, ArgoCD gets this changes and applies them to target Kubernetes cluster.
All our systems under tests definitions are stored in [deployment-hub](https://github.com/skodjob/deployment-hub) repository and ArgoCD takes care about distributing them to multi-cluster environment.

### Stay tuned!
In our future posts, we'll be sharing our experiences with these technologies. We'll discuss the challenges we faced and how we tackled them along the way.
