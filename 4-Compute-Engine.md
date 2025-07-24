# Compute Engine
## VM Access and Instance States
- **Accessing VMs**: Root access for creators, SSH for Linux, RDP for Windows.
- **Instance States**: Provisioning, Staging, Running, Stopping, Terminated.

## Compute Options
- **Machine Families**: General-purpose, Compute-optimized, Memory-optimized, Accelerator-optimized.
- **Custom Machine Types**: Tailored configurations for specific workloads.

## Pricing and Discounts

- **Per-second billing**: You are charged by the second, with a minimum charge of 1 minute per VM instance.
- **Sustained use discounts**: Automatically applied when you run instances for a significant portion of the month, reducing costs without any commitment.
- **Committed use discounts**: Receive deeper discounts by committing to use specific resources (vCPUs, memory) for 1 or 3 years, regardless of actual usage.
- **Preemptible VM pricing**: Substantial savings (up to 80%) for short-lived, interruptible workloads using preemptible VMs.

## Special Types of VMs
### Preemptible VMs
- **Low-cost instances**: Up to 80% savings, limited to 24 hours, no live migrations.
- **Termination Warning**: 30 seconds warning before termination, but no guarantee the termination script will run successfully.
- **Limitations**: No live migrations, no automatic restarts.
- **Best Practices**: Use monitoring and load balancers to manage preemptible instances effectively.

### Comparison of Special VM Types

| VM Type           | Base Features                                      | Use Cases                                      |
|-------------------|---------------------------------------------------|-----------------------------------------------|
| Sole-tenant Nodes | Dedicated servers for compliance and performance. | Workloads requiring physical isolation.       |
| Shielded VMs      | Enhanced security with Secure Boot and vTPM.      | Protect against rootkits and bootkits.        |
| Confidential VMs  | Data encryption using AMD SEV technology.         | Secure sensitive workloads like financial data.|

## Disks

### Persistent Disks
- **Key Features**:
  - Network-attached storage that's durable and bootable
  - Can be resized while running
  - Supports snapshots for backup
  - Can be attached read-only to multiple VMs
- **Types**:
  - `pd-standard`: HDD, for sequential I/Os, most economical
  - `pd-balanced`: SSD, good balance of performance and cost
  - `pd-ssd`: SSD, for high random IOPS workloads
  - `pd-extreme`: SSD, for highest performance needs
- **Important Limits**:
  - Max 64 TiB per disk
  - Max 16 disks for shared-core VMs
  - Max 128 disks for other VMs

### Local SSDs
- **Key Features**:
  - Physically attached to VM for highest performance
  - Ephemeral: data lost when VM stops/terminates
  - Maximum 8 SSDs per VM
  - 375 GiB per disk
- **Important Notes**:
  - Can't create snapshots
  - Can't add after VM creation
  - Data persists through VM restart but not stop

## Images
- **Definition**: Pre-configured virtual disk templates for VM creation.
- **Types**: Public, Custom, Premium.
- **Best Practices**: Update custom images regularly; use public images for general workloads.

## Snapshots
- **Definition**: Point-in-time backups of persistent disks.
- **Key Exam Points**:
  - Stored in Cloud Storage (behind the scenes)
  - Incremental: Only changed blocks are saved
  - Global resource: Can be used across regions/zones
  - Encrypted by default
  - Used for disk type conversion and VM migration
  - Not available for local SSDs
  - Automatically compressed

## Instance Groups
- **Definition**: Collections of VM instances that can be managed as a single entity
- **Primary Use Cases**:
  - High availability applications
  - Load balancing
  - Autoscaling
  - Rolling updates

### Managed Instance Groups (MIGs) - Exam Essentials
- **Key Features** (frequently tested):
  - All instances are **identical** (created from same instance template)
  - **Autohealing**: Automatic health checks and instance replacement
  - **Autoscaling**: Automatic scaling based on:
    - CPU utilization
    - Load balancing capacity
    - Monitoring metrics
  - **Regional/Zonal deployment**:
    - Zonal MIG: Single zone deployment
    - Regional MIG: Multi-zone deployment for higher availability

- **Common Exam Scenarios**:
  - When to use Regional vs Zonal MIGs
  - How autohealing works with health checks
  - Autoscaling configuration and triggers
  - Rolling update strategies

### Unmanaged Instance Groups - Exam Points
- **Key Differences** (commonly tested):
  - Can include **different** types of instances
  - **No automated features** (no autoscaling, autohealing)
  - **Manual management** required
  - Main use: Basic load balancing for diverse instances

### Important for the Exam
- No additional charge for using instance groups; costs depend on resources used.

## Common Actions
- **Metadata and Scripts**: Store instance data, automate tasks.
  - Every instance stores data about itself on a metadata server.
  - Example of metadata: external IP address.
- **Move an Instance to a New Zone**:
  - We can move a VM even if it is terminated or if it is a shielded VM.
  - We can automate moving VMs if the move is inside a region.
  - To move a VM, we have to shut down the VM, move it, and restart it.
  - If the VM is moved to another region, the following process should be applied:
    1. Snapshot all persistent disks.
    2. Create new persistent disks in the destination zone and restore the snapshots.
    3. Assign static IP to the VM.
    4. Update references to the VM.
    5. Delete the snapshots, original disks, and the original VM.
- **Snapshots**:
  - Stored in cloud storage, used to back up data and move VMs between regions.
  - Snapshots can be used to transfer data between different disk types.
  - Snapshots are not available for local SSD.
  - Snapshots are incremental and automatically compressed.
- **Resize Persistent Disks**:
  - Can be achieved even when a disk is attached to a running VM.
  - We can increase disk size but not shrink it.