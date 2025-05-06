# Kubernetes 101
![Kubernetes icon](https://icons.terrastruct.com/azure%2F_Companies%2FKubernetes.svg) <!-- .element: width="200" -->



## NEF

- Migration from NEF (ECS) to NEF2 (EKS)
- Elastic Container Service (ECS): AWS implementation of container orchestration service
- Elastic Kubernetes Service (EKS): Kubernetes service in AWS cloud



## Kubernetes

An open source system for automating deployment, scaling, and management of containerized applications. <!-- .element: class="fragment" -->


### Features

- Zero-downtime deployment: rollout or rollback without downtime
- Self-healing: automatically replace failed instances with healthy ones
- Scaling: allow to update number of running instances manually or automatically
- Load balancing: route traffic to healthy instances (random, round-robin, etc.)



## Kubernetes concepts


### Containers

- Application code packaged with dependencies (libs, runtime)
- Run in isolation: CPU, memory, storage, network
- OS agnostic


### Pods

- Smallest deployable units of computing
- Group of one or more containers
- code
- diagram of pods and containers IPEM


### ReplicaSets

- Ensure the right number of Pods is running


### Deployments

- Wrapper around ReplicaSet to allow doing updates to Pods
- code
- diagram of Pod > ReplicaSet > Deployments


### Autoscaling Workloads

- HPA: automatically scale a workload horizontally (number of running Pods)
- VPA: automatically scale a workload vertically (resizing CPU and memory)


#### HPA definition

- code of HPA with min, max, cpuUtil, memory Util
- Deployment object, HPA object


### Cluster

- Node: a virtual or physical machine
- Cluster: set of nodes
- Master nodes: host the Kubernetes control plane and manages the cluster
- Worker nodes: host Pods
- diagram?


### Namespaces

- Provide a scope for names. Names of resources need to be unique within a namespace
- Help different teams, projects share a cluster
- example?


### Services

- Expose running application behind a single endpoint (IP address or DNS name) in a cluster
- code
- diagram?


### Ingress

- A single resource to provide external access to services in cluster
- Can route traffic by hostname and/or path
- diagram


### Gateway API

- Supersede Ingress. Same as Ingress but more functionalities
- Separation of concerns between roles
- Can route traffic by protocol, hostanme, path, header, query param, method
- Can modify the header of request and response
- diagram
- code
