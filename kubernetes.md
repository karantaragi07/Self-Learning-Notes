# ![Kubernetes](https://img.shields.io/badge/Kubernetes-Container%20Orchestration-blue?style=for-the-badge)

##  Kubernetes — Self Learning Notes

Kubernetes is an open-source system for automating deployment, scaling, and management of containerized applications.

Kubernetes (also known as **K8s**) is an **open-source container orchestration platform** developed by Google, derived from their internal project **Borg**.  
In 2016, it was donated to the **Cloud Native Computing Foundation (CNCF)**.

It helps automate:
- Container deployment  
- Scaling  
- Load balancing  
- Self-healing  
- Configuration management  

But Kubernetes is **much more than an orchestrator** — it is the backbone for building **cloud-native applications**.

---

##  Why Kubernetes Is More Than Just an Orchestrator

Traditional orchestrators only handle **container scheduling and deployment**, but Kubernetes provides a full ecosystem.

| Capability | Description | Cloud-Native Purpose                                              |
|---------------------------|--------------|---------------------------------------------------|
| **Dynamic Deployment**    | Deploy and manage containers automatically | Continuous delivery |
| **Zero Downtime Updates** | Rolling and blue-green deployments | Resiliency                  |
| **Auto Scaling**          | Scales applications up or down | Elasticity                      |
| **Self-Healing**          | Restarts failed containers | Fault tolerance                     |
| **Load Balancing**        | Routes traffic to healthy Pods | High availability               |
| **Service Discovery**     | Uses internal DNS for communication | Microservice connectivity  |

>  **Cloud-Native Applications** are designed to be modular, scalable, and resilient — Kubernetes provides the foundation to build them.  
> Cloud-native ≠ Cloud computing; cloud-native focuses on *how* apps are designed, not *where* they run.

---

##  Kubernetes Architecture Overview

A Kubernetes cluster is a group of machines (physical or virtual) that work together to run containerized applications or we can say something which manages the control plane and worker node.

A **Kubernetes Cluster** = **Control Plane** + **Worker Nodes**

- **Control Plane** → The brain that makes global decisions and manage your worker nodes.  
- **Worker Nodes** → Machines that actually run your applications (Pods).

- kubectl (which communicates with control plane) ----> control plane ----> worker nodes----> pod ---> scheduling unit

kubectl is the command-line tool used to interact with your Kubernetes cluster.

- There are two ways to interact with Kubernetes — Imperative and Declarative. The imperative approach uses direct commands like kubectl create or kubectl delete, while the declarative approach uses YAML manifests to define the desired state, which Kubernetes continuously maintains. Declarative is preferred for production and GitOps workflows.

# Full Kubernetes Architecture (with Hardware & OS)

    |------------------------------------------------------------------|
    |                  Kubernetes Control Plane                        |
    |------------------------------------------------------------------|
    |       kube-apiserver, etcd, scheduler, controller-manager        |
    |------------------------------------------------------------------|
    |                  Kubernetes Worker Nodes                         |
    |------------------------------------------------------------------|
    |       kubelet, kube-proxy, container runtime (Docker, CRI-O)     |
    |                     Pods (containers)                            |
    |------------------------------------------------------------------|
    |                  Operating System (Linux)                        |
    |------------------------------------------------------------------|
    |                  Physical or Virtual Hardware                    |
    |                       CPU, RAM, Disk                             |
    |------------------------------------------------------------------|



In Simple Terms

- Hardware → provides raw compute resources.

- Linux OS → provides the environment & kernel-level features for containers.

- Kubernetes → manages and orchestrates containers across many Linux-based nodes.

---

## Kubernetes Cluster

Control Plane + Worker Nodes

The Control Plane is the brain of the Kubernetes cluster — it makes global decisions about the cluster (like scheduling, scaling, and maintaining desired state).
It manages what should run and where it should run across worker nodes.

---

##  Control Plane Components

               --------------------------------
               |       Control Plane          |
               |------------------------------|
               |   kube-apiserver             |
               |   etcd                       |
               |   kube-scheduler             |
               |   kube-controller-manager    |
               |   cloud-controller-manager   |
               --------------------------------
                           |
                           | (API Communication)
                           v
                --------------------------
                |      Worker Nodes      |
                |------------------------|
                | kubelet  | kube-proxy  |
                | Containers (Pods)      |
                --------------------------


|          Component                       |                            Description                                                     |
|------------------------------------------|--------------------------------------------------------------------------------------------|
| **kube-apiserver**                       | The entry point for all commands and API calls; exposes Kubernetes API.                    |
| **etcd**                                 | A distributed key-value store holding the entire cluster state and configurations.         |
| **kube-scheduler**                       | Decides which Node should run a newly created Pod based on available resources.            |
| **kube-controller-manager**              | Ensures the actual state matches the desired state (e.g., restarts Pods).                  |
| **cloud-controller-manager**             | Connects Kubernetes to cloud providers (AWS, GCP, Azure).                                  |

**Example:**  
If you tell Kubernetes to run 5 Pods and one fails — the **controller-manager** and **scheduler** work together to restart it automatically.

---

## Worker Node Architecture Diagram

                    ┌──────────────────────────────────────────────┐
                    │                Worker Node                   │
                    │----------------------------------------------│
                    │                                              │
                    │   ┌──────────────────────────────────────┐   │
                    │   │              Pods                    │   │
                    │   │  (1 or more containers)              │   │
                    │   │--------------------------------------│   │
                    │   │  Container Runtime (Docker/CRI-O)    │   │
                    │   └──────────────────────────────────────┘   │
                    │                                              │
                    │   ┌──────────────────────────────────────┐   │
                    │   │             Kubelet                  │   │
                    │   │  - Communicates with API Server      │   │
                    │   │  - Ensures desired state of pods     │   │
                    │   │  - Reports node & pod status         │   │
                    │   └──────────────────────────────────────┘   │
                    │                                              │
                    │   ┌──────────────────────────────────────┐   │
                    │   │             Kube-Proxy               │   │
                    │   │  - Maintains network rules           │   │
                    │   │  - Handles service discovery         │   │
                    │   │  - Manages load balancing            │   │
                    │   └──────────────────────────────────────┘   │
                    │                                              │
                    └──────────────────────────────────────────────┘

## Interaction with Control Plane   

          ┌───────────────────────────────┐
          │        Control Plane          │
          │  (API Server, Scheduler, etc) │
          └──────────────┬────────────────┘
                         │
                         │
               Communicates via REST API
                         │
          ┌──────────────┴────────────────┐
          │         Worker Node           │
          │   (Kubelet, Kube-Proxy, etc.) │
          └───────────────────────────────┘


##  Worker Node Components

|          Component                       |                            Description                                                       |
|------------------------------------------|----------------------------------------------------------------------------------------------|
| **kubelet**                              | Agent running on each node; communicates with API server and ensures containers are running. |
| **kube-proxy**                           | Manages networking and routing; forwards traffic to the correct Pod.                         |
| **Container Runtime**                    | Software that runs containers (e.g., Docker, containerd, CRI-O).                             |



---

##  Pods

- **Pod** is the **smallest deployable unit** in Kubernetes or we can say just a scheduling unit.  
- It wraps one or more containers that share the same **network namespace**, **storage volumes**, and **lifecycle**.  
- Usually, one Pod = one application container.

> Pods are ephemeral — when a Pod dies, a new one is created automatically by a higher-level controller (like a Deployment).

worker node --> container runtime --> pod --> container

---

##  Kubernetes Workflow

1. Create your **microservice**  
2. **Containerize** it (e.g., using Docker)  
3. Place container inside a **Pod** and then deploy these pods to controller.
4. Deploy using a **Deployment** (which manages Pods) 

Microservice → Container → Pod → Deployment → Cluster

Controller are control loops that watch the state of your cluster, then make changes where needed.  fro example:- deployment controller

---

##  Kubernetes Objects (Workload Resources)

| Object | Description |
|---------|-------------|
| **Pod** | The basic unit of deployment |
| **ReplicaSet** | Ensures a specific number of Pods are running at all times |
| **Deployment** | Manages ReplicaSets and supports rolling updates & rollbacks |
| **StatefulSet** | Used for stateful applications (databases, queues) |
| **DaemonSet** | Ensures one Pod runs on every node (used for monitoring/logging agents) |
| **Job / CronJob** | Used for batch and scheduled tasks |

---

##  Services (Networking Layer)

Services expose Pods to other Pods or external users.  
They provide a **stable network endpoint** even if underlying Pods change.

| Service Type | Description |
|---------------|-------------|
| **ClusterIP** | Default — internal communication within the cluster |
| **NodePort** | Exposes service on a static port on each node |
| **LoadBalancer** | Integrates with cloud provider load balancer |
| **ExternalName** | Maps a service to an external DNS name |

> Services + DNS enable communication between microservices via names rather than changing IPs.

---

##  Kubernetes DNS (CoreDNS)

Pods communicate with each other using **CoreDNS**, which provides name-based service discovery.

**How it works:**
1. **CoreDNS** runs as a Deployment in the `kube-system` namespace.  
2. It listens to Kubernetes API for new services and updates DNS records automatically.  
3. Each Pod gets a `/etc/resolv.conf` pointing to CoreDNS.  
4. When a Pod queries another service (e.g., `ping myservice`), CoreDNS resolves it to a ClusterIP.

> CoreDNS replaced kube-dns as the default DNS server in modern Kubernetes.

---

##  ConfigMaps and Secrets

| Resource | Purpose |
|-----------|----------|
| **ConfigMap** | Stores non-sensitive configuration data in key-value pairs |
| **Secret** | Stores sensitive data like passwords, tokens, and certificates (base64 encoded) |

These help externalize configuration from application code.

---

##  Volumes and Persistent Storage

Pods are temporary — when they die, data inside them is lost.  
**Volumes** solve this by providing persistent storage.

| Volume Type                     | Description                                     |
|---------------------------------|-------------------------------------------------|
| **emptyDir**                    | Temporary storage (deleted when Pod is removed) |
| **hostPath**                    | Mounts a directory from the Node’s filesystem   |
| **PersistentVolume (PV)**       | Abstract storage resource in the cluster        |
| **PersistentVolumeClaim (PVC)** | Request for PV from a Pod                       |

> Persistent volumes enable stateful workloads like databases to survive restarts.

---

##  Namespaces

Namespaces logically divide a cluster into multiple virtual clusters.  
They help isolate environments (e.g., dev, staging, prod).

- Default namespaces: `default`, `kube-system`, `kube-public`, `kube-node-lease`
- You can create custom namespaces for projects or teams.

---

##  Ingress

Ingress exposes HTTP and HTTPS routes to Services within a cluster.

It acts like a smart **Layer 7 load balancer**, routing traffic based on hostnames or URLs.

Example use:
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: example-ingress
spec:
  rules:
  - host: myapp.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: myapp-service
            port:
              number: 80
```
---

## Scaling and Self-Healing

Kubernetes supports:

Horizontal Pod Autoscaler (HPA) → Scales Pods based on CPU/memory usage.

Vertical Pod Autoscaler (VPA) → Adjusts Pod resource requests automatically.

Cluster Autoscaler → Adds/removes nodes automatically in cloud environments.

If a Pod or Node fails, Kubernetes automatically reschedules workloads — this is self-healing.

---

## Security in Kubernetes

| Feature                              | Description                                 |
| ------------------------------------ | ------------------------------------------- |
| **RBAC (Role-Based Access Control)** | Controls who can do what within the cluster |
| **Service Accounts**                 | Identity for processes running in Pods      |
| **Network Policies**                 | Define allowed communication between Pods   |
| **Secrets Management**               | Protect sensitive information               |
| **Admission Controllers**            | Enforce security and compliance policies    |

---

## CLI Tools

| Tool         | Purpose                                                |
| ------------ | ------------------------------------------------------ |
| **kubectl**  | Main CLI for interacting with the cluster              |
| **minikube** | Local single-node cluster for learning and testing     |
| **kubeadm**  | Tool for bootstrapping and configuring clusters        |
| **Helm**     | Package manager for Kubernetes (like apt/yum for apps) |
| **K9s**      | Terminal UI for managing clusters                      |

---

## Common commands

```
kubectl get pods
kubectl get nodes
kubectl describe pod <pod-name>
kubectl logs <pod-name>
kubectl apply -f deployment.yaml
kubectl delete -f service.yaml
```
---

## Observability and Monitoring

- Metrics Server → Collects cluster resource metrics.

- Prometheus + Grafana → Monitoring and visualization.

- Fluentd / Loki / ELK Stack → Centralized logging.

- Kube-state-metrics → Exposes detailed Kubernetes object metrics.

Observability helps detect performance issues, resource bottlenecks, and unhealthy states early.

---

## Additional Concepts

| Concept                               | Description                                                        |
| ------------------------------------- | ------------------------------------------------------------------ |
| **CRI (Container Runtime Interface)** | Standard interface allowing Kubernetes to use different runtimes   |
| **CNI (Container Network Interface)** | Manages Pod networking (e.g., Calico, Flannel)                     |
| **CSI (Container Storage Interface)** | Standard for integrating storage providers                         |
| **Taints & Tolerations**              | Control which Pods can be scheduled on specific nodes              |
| **Node Affinity / Anti-Affinity**     | Rules for placing Pods on certain nodes                            |
| **Labels & Selectors**                | Key-value pairs used for grouping and selecting Kubernetes objects |
| **Annotations**                       | Store metadata for tools and automation                            |
| **Resource Requests & Limits**        | Define minimum and maximum CPU/memory for containers               |

---

## Final Recap

- Kubernetes = Automation + Reliability + Scalability

- It ensures your applications:

      - Stay running even when something fails

      - Scale automatically based on demand

      - Support zero downtime updates

      - Remain portable across environments

“You told me to run 5 applications. If one goes down, I’ll restart it.”
— The Kubernetes Scheduler

## End-to-End Kubernetes Workflow Summary

- User Interaction → A developer interacts with the cluster using kubectl or YAML manifests to define the desired state (e.g., Deployments, Pods, Services).

- API Server → The request first hits the Kube-API Server, which validates it and stores the desired state in etcd (the cluster’s key-value store).

- Scheduler → The Kube-Scheduler watches for unscheduled Pods and selects an appropriate Worker Node based on resource availability, affinity rules, and constraints.

- Kubelet on Worker Node → Once assigned, the Kubelet (agent on that node) pulls container images via the Container Runtime (Docker/containerd) and starts the containers inside Pods.

- Container Runtime → The runtime runs the containers and manages their lifecycle, ensuring the application runs as expected.

- Kube-Proxy → Handles networking rules, assigns IPs to Pods, and enables communication within and across nodes while exposing Services to the outside world.

- Controller Manager → Continuously monitors the cluster, comparing the desired state (from etcd) with the current state. If something fails (e.g., a Pod crash), it triggers corrective actions such as recreating or rescheduling Pods.

- Auto Healing & Scaling → Kubernetes automatically replaces failed Pods, scales deployments up or down, and maintains overall health to ensure system stability.

- Service Exposure → The application becomes accessible via Services (ClusterIP, NodePort, LoadBalancer) or Ingress, enabling users to reach it from within or outside the cluster.

- Continuous Reconciliation Loop → Kubernetes constantly checks and ensures that the actual cluster state always matches the desired state, providing self-healing, scalability, and high availability.

```
  [User / DevOps Engineer]
          │
          ▼
   ┌───────────────────────┐
   │   Kube-API Server     │
   └─────────┬─────────────┘
             │ (stores desired state)
             ▼
          [etcd Database]
             │
             ▼
   ┌───────────────────────┐
   │   Scheduler assigns   │
   │   Pod to Worker Node  │
   └─────────┬─────────────┘
             ▼
   ┌──────────────────────────────┐
   │       Worker Node            │
   │  ┌────────────────────────┐  │
   │  │       Kubelet          │  │
   │  │  Starts & monitors Pod │  │
   │  └──────────┬─────────────┘  │
   │             ▼                │
   │  Container Runtime (Docker)  │
   │  Kube-Proxy (Networking)     │
   └──────────────────────────────┘
             │
             ▼
      [Pods running app]
             │
             ▼
     [Services expose it]

```
---
