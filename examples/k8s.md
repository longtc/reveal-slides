# Kubernetes 101
![Kubernetes icon](https://icons.terrastruct.com/azure%2F_Companies%2FKubernetes.svg) <!-- .element: width="200" -->



## How do we run apps at NAB?

- Migration from NEF (ECS) to NEF 2.0 (EKS)
- Elastic Container Service (ECS): AWS implementation of container orchestration service
- Elastic Kubernetes Service (EKS): Kubernetes service in AWS cloud



## Kubernetes

An open source system for automating deployment, scaling, and management of containerized applications. <!-- .element: class="fragment" -->


### Features

- Zero-downtime deployment: rollout or rollback without downtime
- Self-healing: automatically replace failed instances with healthy ones
- Scaling: allow to update number of running instances manually or automatically
- Load balancing: route traffic to healthy instances (random, round robin, least connection, etc.)



## Kubernetes concepts


### Containers

- Application code packaged with dependencies (libs, runtime)
- Run in isolation: CPU, memory, storage, network
- OS agnostic


#### Virtualization vs Container

![virtualization vs container](assets/svg/vm-container.svg)



### Pods

- Smallest deployable units of computing
- Group of one or more containers


#### Pod definition

```yaml [1-4|5-17]
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: webapp
    image: nginx:1.14.2
    ports:
    - containerPort: 80
    resources:
      requests: # <-- soft limit
        cpu: 100m
        memory: 128Mi
      limits: # <-- hard limit
        cpu: 200m
        memory: 256Mi
```


![miniapp pod 1 container](assets/svg/miniapp/index.svg) <!-- .element: width="600" -->


![miniapp pod 2 containers](assets/svg/miniapp/1.svg) <!-- .element: width="600" -->


### ReplicaSets

- Ensure the right number of Pods is running


### Deployments

- Wrapper around ReplicaSet to allow doing updates to Pods


#### Deployment definition

```yaml [1-4|5-6|7-13]
apiVersion: apps/v1
kind: Deployment
metadata:
  name: webapp-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: webapp
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```


- diagram of Pod > ReplicaSet > Deployments



### Autoscaling Workloads

- HPA: automatically scale a workload horizontally (number of running Pods)
- VPA: automatically scale a workload vertically (resizing CPU and memory)


#### HPA definition

```yaml [1-17|6,18-19|20-35]
apiVersion: apps/v1
kind: Deployment
metadata:
  name: webapp-deployment
spec:
  # replicas: 3 # replicas is not defined with HPA
  # other specs...
---
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: webapp-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: webapp-deployment
  minReplicas: 2
  maxReplicas: 3
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
  - type: Resource
    resource:
      name: memory
      target:
        type: Utilization
        averageUtilization: 70
  behavior:
    scaleDown:
      stabilizationWindowSeconds: 180
```


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


#### Service definition

```yaml [10-13|19-32]
apiVersion: apps/v1
kind: Deployment
metadata:
  name: webapp-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: webapp
        image: nginx:1.14.2
        ports:
        - containerPort: 80
          name: nginx-web-port
---
apiVersion: v1
kind: Service
metadata:
  name: webapp-service
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: nginx-web-port
```


- diagram?


### Ingress

- A single resource to provide external access to services in cluster
- Can route traffic by hostname and/or path


#### Ingress definition

```yaml [4,11,14-32]
apiVersion: v1
kind: Service
metadata:
  name: webapp-service
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      name: web-svc-port
      targetPort: nginx-web-port
---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: minimal-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx
  rules:
  - host: foo.bar.com
    http:
      paths:
      - path: /testpath
        pathType: Prefix
        backend:
          service:
            name: webapp-service
            port:
              name: web-svc-port
```


- diagram


### Gateway API

- Supersede Ingress. Same as Ingress but more functionalities
- Separation of concerns between roles
- Can route traffic by protocol, hostanme, path, header, query param, method
- Can modify the header of request and response
- diagram


#### Gateway API definition

```yaml [14-23|4,10,17,25-41]
apiVersion: v1
kind: Service
metadata:
  name: webapp-service
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      name: web-svc-port
      targetPort: nginx-web-port
---
apiVersion: gateway.networking.k8s.io/v1
kind: Gateway
metadata:
  name: gateway-prod
spec:
  gatewayClassName: gke-l7-regional-external-managed
  listeners:
  - name: http-gw-prod
    protocol: HTTP
    port: 80
---
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: http-route-prod
spec:
  parentRefs:
  - name: gateway-prod
  hostnames:
  - "example.com"
  rules:
  - matches:
    - path:
        type: PathPrefix
        value: /login
    backendRefs:
    - name: webapp-service
      port: 80
```


- diagram?
