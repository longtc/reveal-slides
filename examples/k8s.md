# Kubernetes 101
![Kubernetes icon](https://icons.terrastruct.com/azure%2F_Companies%2FKubernetes.svg) <!-- .element: width="200" -->



## NEF

- ECS: AWS implementation of container orchestration service
- EKS: Kubernetes service in AWS cloud



## Kubernetes

An open source system for automating deployment, scaling, and management of containerized applications. <!-- .element: class="fragment" -->


### Features

- Zero-downtime deployment
- Self-healing
- Scaling
- Load balancing



## Kubernetes concepts


### Containers

- Application code packaged with dependencies (libs, runtime)
- Run in isolation: CPU, memory, storage, network
- OS agnostic


### Pods

- Smallest deployable units of computing
- Group of one or more containers


### ReplicaSets

- Ensure the right number of Pods is running


### Deployments

- Wrapper around ReplicaSet to allow doing updates to Pods


### Autoscaling Workloads

- HPA: automatically scale a workload horizontally (number of running Pods)
- VPA: automatically scale a workload vertically (resizing CPU and memory)


#### HPA definition


### Services

- Expose running application behind a single endpoint (IP address or DNS name)


### Nodes

- Master Node (Control plane)
- Worker Node: run Pods


### Ingress


### Gateway API
