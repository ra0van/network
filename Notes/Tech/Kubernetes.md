
### What is kubernetes?

Kubernetes is a portable, extensible, open-source platform for managing containerized workloads and services the has both declarative configuration and automation. 

- The name Kubernetes originates from Greek, meaning helmsman or pilot.
- K8s is an abbreviation resulting from counting the eight letters between the "K" and the "s". 
- Created by google, open sourced in 2014.

#### Back in time

![Deployment evolution](https://d33wubrfki0l68.cloudfront.net/26a177ede4d7b032362289c6fccd448fc4a91174/eb693/images/docs/container_evolution.svg)

### Why do you need kubernetes and what it can do
Kubernetes are a good way to bundle and run you applications. In a production environment, you need to manage the containers that run the applications and ensure there is no downtime. For example, if a container goes down, another container need to start. Wouldn't it be easier if this behavior was handled by a system?

That's how kubernetes comes to the rescue! Kubernetes provides you a framework to run distributed systems resiliently. It takes care of scaling and fail-over for your application, provides deployment patterns, and more. 

For example, kubernetes can easily manage a  [[DeploymentStrategies | canary deployment]] for your system. 

Kubernetes provides you with : 
- **Service discovery & load balancing** Kubernetes can expose a container using the DNS name or using their own IP address. If traffic to a container is high, Kubernetes is able to load balance and distribute the network traffic so that the deployment is stable.
- **Storage orchestration** Kubernetes allows you to automatically mount a storage system of your choice, such as local storage(s), public cloud providers, and more. 
- **Automated roll-outs and rollbacks** You can describe the desired state for your deployed containers using K8s, and it can change the actual state to the desired state at a controlled rate. For example, you can automate K8s to create new containers for your deployment, remove existing containers and adopt all their resources tot hew container. 
- **Automatic bin packing** You can describe the desired state for your deployed containers using kubernetes, and it can change the actual state to the desired state at a controlled rate. For example, you can automate Kubernetes to create new containers for your deployment, remove existing containers and adopt all their resources to the new container.
- **Self-healing** Kubernetes restarts containers that fail, replace containers, kill containers that don't respond to your user-defined health check and doesn't advertise them to clients until they are ready to serve.
- **Secret & Configuration management** Kubernetes lets you store and manage sensitive information, such as passwords, OAuth tokens, and SSH keys. You can deploy and update secrets and application configuration without rebuilding your container images, and without exposing secrets in your stack configuration.

### What kubernetes is not

Kubernetes is not a traditional PaaS system. Since Kubernetes operates at container level rather than at hardware level. Although it does provide some features such as scaling, deployment, load balancing, and lets users integrate their logging, monitoring, and alerting solutions. However, k8s is not monolithic and all these solutions are pluggable. Kubernetes provides the building blocks for building developer platforms, but preserves user choice and flexibility where it is important.

K8s: 
- Does not limit the types of applications supported. 
- Does not build or deploy source code. Kubernetes works on containers. 
- Does not dictate logging, monitoring, or alerting solutions. It provides some integrations as proof of concept, and mechanisms to collect and export metrics.
- Additionally, Kubernetes is not a mere orchestration system. In fact, it eliminates the need for orchestration. (The technical definition of orchestration is execution of a defined workflow: first do A, then B, then C).In contrast, Kubernetes comprises a set of independent, composable control processes that continuously drive the current state towards the provided desired state.

### Nomenclature 
- **Cluster**. A kubernetes cluster consists of a set of worker machines called nodes, that run containerized application. Every cluster has atleast one worker node. 
- **Node**. A worker node hosts the pods that are the components of the application workload. The **[[Control plane]]** manages the worker nodes and the pods in the cluster. (In production, control plane usually runs across multiple computers and a cluster usually runs multiple nodes, providing fault-tolerance and high availability.)
<img src="https://d33wubrfki0l68.cloudfront.net/2475489eaf20163ec0f54ddc1d92aa8d4c87c96b/e7c81/images/docs/components-of-kubernetes.svg"  width=600/>



[[SystemDesign]]