# GKE

## Kubernetes concepts and architecture

### Concepts
- Kubernetes is an orchestration platform for managing containerized applications across clusters.
- Automates scheduling, running, and managing containers on physical or virtual machines.
- Ensures high availability and fault tolerance for distributed systems.

### Architecture
- **Control Plane:**
  - Manages the cluster and maintains its desired state.
  - Key components:
    - **API Server:** Handles RESTful requests.
    - **Scheduler:** Assigns workloads to nodes.
    - **Controller Manager:** Manages controllers to ensure the desired state.
    - **etcd:** Stores cluster data reliably.
    - **Cloud Manager:** Integrates with the underlying cloud provider to provision and manage resources such as load balancers, storage, and networking.

- **Worker Nodes:**
  - Run workloads and communicate with the control plane.
  - Key components:
    - **Kubelet:** Ensures containers are running in Pods.
    - **Kube-proxy:** Manages network rules and load balancing.
    - **Container Runtime:** Runs containers (e.g., Docker).
 
- **Pod:**
  - The smallest deployable unit in Kubernetes.
  - Encapsulates one or more containers, storage, and network resources.
  - Represents a single instance of a running process in your cluster.

- **Deployment:**
  - Manages the deployment and scaling of Pods.
  - Ensures the desired number of Pods are running at all times.
  - Supports rolling updates and rollbacks for application changes.

- **StatefulSet:**
  - Manages stateful applications by ensuring the deployment and scaling of Pods with unique identities.
  - Maintains a stable network identity and persistent storage for each Pod.
  - Suitable for workloads requiring ordered deployment, scaling, and deletion, such as databases.

- **Service:**
  - Provides a stable network endpoint for accessing Pods.
  - Abstracts and balances traffic to a group of Pods using labels.
  - Types include ClusterIP, NodePort, LoadBalancer, and ExternalName.

- **Ingress:**
  - Manages external HTTP/S access to Services within the cluster.
  - Supports routing, load balancing, SSL termination, and name-based virtual hosting.
  - Requires an Ingress Controller to function.

## GKE modes

GKE offers two operational modes:

1. **Standard Mode:**
   - Provides full control over the Kubernetes environment.
   - Suitable for advanced use cases requiring custom configurations.
   - Users are responsible for managing node pools, scaling, and upgrades.
   - Offers flexibility for integrating with other GCP services and custom workloads.

2. **Autopilot Mode:**
   - Simplifies cluster management by automating operational tasks.
   - Google manages the underlying infrastructure, including scaling and upgrades.
   - Enforces best practices for security and resource optimization.
   - Ideal for users who want to focus on deploying applications without managing the cluster.

## Location type

GKE clusters can be deployed in the following location types:

1. **Zonal:**
   - A single zone within a region.
   - Lower cost but less resilient to zone failures.

2. **Regional:**
   - Spans multiple zones within a region.
   - Provides higher availability and fault tolerance.

## Cluster management

Cluster management in GKE involves the following key tasks:

1. **Node Pool Management:**
   - Add, delete, or resize node pools to meet workload demands.
   - Use preemptible VMs for cost savings.

2. **Upgrades:**
   - Regularly upgrade clusters and node pools to the latest GKE version for security and feature updates.
   - Supports manual and automatic upgrades.

3. **Monitoring and Logging:**
   - Use Cloud Monitoring and Cloud Logging to track cluster health and performance.

4. **Autoscaling:**
   - Enable cluster autoscaler to automatically adjust the size of the node pool based on resource demands.

## Workload deployment

1. **Deployments:**
   - Use Deployments to manage stateless applications.
   - Supports rolling updates and rollbacks.

2. **StatefulSets:**
   - Use StatefulSets for stateful applications requiring persistent storage and stable network identities.

3. **DaemonSets:**
   - Ensure a copy of a Pod runs on all or specific nodes.
   - Useful for logging, monitoring, or networking tasks.

4. **Jobs and CronJobs:**
   - Use Jobs for one-time tasks and CronJobs for recurring tasks.

5. **ConfigMaps and Secrets:**
   - Use ConfigMaps to manage configuration data.
   - Use Secrets to store sensitive information securely.

### Kubernetes Services

1. **ClusterIP**:
   - Default type of Service.
   - Exposes the Service on an internal IP within the cluster.
   - Accessible only within the cluster.
   - Suitable for internal communication between Pods.

2. **NodePort**:
   - Exposes the Service on a static port on each node's IP.
   - Allows external access to the Service using `<NodeIP>:<NodePort>`.
   - Useful for simple external access but lacks advanced features like load balancing.

3. **LoadBalancer**:
   - Integrates with cloud provider's load balancer to expose the Service externally.
   - Automatically provisions an external IP and load balancer.
   - Ideal for production workloads requiring external access.

4. **ExternalName**:
   - Maps the Service to an external DNS name.
   - Does not create a cluster IP or proxy.
   - Useful for integrating with external services.

### Service Discovery

- Kubernetes provides built-in service discovery mechanisms.
- Services are discoverable via environment variables or DNS.
- DNS is the preferred method, where each Service gets a DNS name in the format `<service-name>.<namespace>.svc.cluster.local`.

### Load Balancing

- Kubernetes Services distribute traffic across Pods using label selectors.
- Load balancing ensures high availability and efficient resource utilization.
- External load balancers (e.g., GCP Load Balancer) can be used with LoadBalancer Services for advanced traffic management.

### Network Policies

- Define rules for controlling traffic flow to and from Pods.
- Use Network Policies to enhance security by restricting access to specific Pods or namespaces.
- Policies are implemented by network plugins (e.g., Calico, Cilium).