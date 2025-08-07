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

## Pod Disruption Budgets (PDB)

Pod Disruption Budgets (PDBs) ensure a minimum number of pods remain available during voluntary disruptions, such as node maintenance or cluster upgrades. They help maintain application availability and reliability.

### Key Features
- Prevents too many pods from being evicted simultaneously.
- Supports policies like `minAvailable` (minimum pods available) or `maxUnavailable` (maximum pods disrupted).
- Integrates with GKE's cluster autoscaler and maintenance operations.

### Example Configuration
```yaml
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: example-pdb
spec:
  minAvailable: 2
  selector:
    matchLabels:
      app: my-app
```

### Best Practices
- Align PDBs with application SLAs.
- Test PDBs to ensure expected behavior during disruptions.
- Monitor PDBs using GKE tools.

## IAM and RBAC in GKE

### IAM (Identity and Access Management)
- IAM operates at the project level and controls access to GCP resources, including GKE clusters.
- Key roles for GKE:
  - **Kubernetes Engine Admin**: Full control over GKE clusters.
  - **Kubernetes Engine Cluster Viewer**: Read-only access to cluster configurations.
  - **Kubernetes Engine Developer**: Limited access to deploy workloads without managing clusters.

### RBAC (Role-Based Access Control)
- RBAC operates at the Kubernetes cluster and namespace levels.
- Controls access to Kubernetes resources like Pods, Deployments, and Services.
- Key components:
  - **Roles**: Define permissions within a namespace.
  - **ClusterRoles**: Define permissions across the entire cluster.
  - **RoleBindings**: Grant roles to users or service accounts within a namespace.
  - **ClusterRoleBindings**: Grant cluster-wide roles to users or service accounts.

### Integration of IAM and RBAC
- IAM manages who can access the cluster, while RBAC manages what they can do within the cluster.
- Use IAM to authenticate users and RBAC to authorize actions.

### Best Practices
- Use IAM for coarse-grained access control and RBAC for fine-grained permissions.
- Regularly review and audit IAM roles and RBAC bindings.
- Follow the principle of least privilege to minimize security risks.

## GKE Sandbox

GKE Sandbox is a security feature that provides an additional layer of isolation for containerized workloads in GKE. It is based on gVisor, a container runtime sandbox.

### Key Features
- **Enhanced Isolation**: Protects workloads from potential vulnerabilities in the host kernel.
- **Compatibility**: Supports most Kubernetes workloads with minimal changes.
- **Integration**: Works seamlessly with GKE clusters.

### Use Cases
- **Multi-Tenant Environments**: Ensures stronger isolation between workloads from different tenants.
- **Untrusted Workloads**: Provides additional security for running third-party or less-trusted applications.
- **Compliance Requirements**: Helps meet strict security and compliance standards.

### Enabling GKE Sandbox
To enable GKE Sandbox, specify the `sandbox.gke.io/runtime: gvisor` annotation in the Pod specification:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: sandboxed-pod
  annotations:
    sandbox.gke.io/runtime: gvisor
spec:
  containers:
  - name: my-container
    image: my-image
```

### Best Practices
- Use GKE Sandbox for workloads requiring high security and isolation.
- Test workloads for compatibility with gVisor before enabling.
- Monitor performance, as sandboxing may introduce slight overhead.

## GKE Networking

GKE provides robust networking capabilities to manage traffic within and outside the cluster.

### Key Features
- **VPC-Native Clusters**: Use Google Cloud's Virtual Private Cloud (VPC) for pod and service networking.
- **Pod IPs**: Each pod gets a unique IP address, enabling direct communication without NAT.
- **Service IPs**: Services are assigned stable IPs for internal and external access.
- **Load Balancing**: Integrates with Google Cloud Load Balancer for external traffic management.
- **Network Policies**: Control traffic flow to and from pods using Kubernetes Network Policies.

### Use Cases
- **Internal Communication**: Use ClusterIP services for pod-to-pod communication within the cluster.
- **External Access**: Use LoadBalancer services or Ingress for exposing applications to the internet.
- **Traffic Control**: Use Network Policies to restrict access to sensitive workloads.

### Best Practices
- Use VPC-native clusters for better scalability and integration with Google Cloud networking.
- Define Network Policies to enhance security by limiting traffic to specific pods or namespaces.
- Monitor network traffic using Cloud Monitoring and Logging tools.

## GKE Workload Identity

GKE Workload Identity is a feature that allows Kubernetes workloads to securely access Google Cloud services using IAM service accounts.

### Key Features
- **Secure Access**: Eliminates the need for long-lived service account keys.
- **Identity Mapping**: Maps Kubernetes service accounts to Google Cloud IAM service accounts.
- **Granular Permissions**: Assigns least privilege permissions to workloads.

### Configuration Steps
1. **Enable Workload Identity**:
   - Enable Workload Identity on the GKE cluster during creation or update.

   ```bash
   gcloud container clusters update CLUSTER_NAME \
       --workload-pool=PROJECT_ID.svc.id.goog
   ```

2. **Create a Kubernetes Service Account (KSA)**:
   ```bash
   kubectl create serviceaccount KSA_NAME
   ```

3. **Create and Bind an IAM Service Account (IAM SA)**:
   ```bash
   gcloud iam service-accounts create IAM_SA_NAME
   gcloud projects add-iam-policy-binding PROJECT_ID \
       --member="serviceAccount:IAM_SA_NAME@PROJECT_ID.iam.gserviceaccount.com" \
       --role="ROLE"
   ```

4. **Annotate the KSA**:
   Link the KSA to the IAM SA:
   ```bash
   kubectl annotate serviceaccount KSA_NAME \
       iam.gke.io/gcp-service-account=IAM_SA_NAME@PROJECT_ID.iam.gserviceaccount.com
   ```

5. **Deploy Workloads**:
   Use the annotated KSA in your Pod specifications.

### Best Practices
- Use Workload Identity instead of service account keys for enhanced security.
- Assign least privilege roles to IAM service accounts.
- Regularly audit IAM policies and Kubernetes RBAC bindings.

## Exam Tips
- IAM and Kubernetes RBAC work together to help manage access to your cluster.
RBAC controls access on a cluster and namespace level, while IAM works on the project level