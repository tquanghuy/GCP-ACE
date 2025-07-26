# Compute Engine
## VM Access and Instance States
- **Accessing VMs**: Root access for creators, SSH for Linux, RDP for Windows.
- **Instance States**: Provisioning, Staging, Running, Stopping, Terminated.
- **Zone Dependency**: VM instances are **zonal resources** - must be created in a specific zone and cannot be moved between zones.

## Compute Options
- **Machine Families**: General-purpose, Compute-optimized, Memory-optimized, Accelerator-optimized.
- **Custom Machine Types**: Tailored configurations for specific workloads.

## Pricing and Discounts

- **Per-second billing**: You are charged by the second, with a minimum charge of 1 minute per VM instance.
- **Sustained use discounts**: Automatically applied when you run instances for a significant portion of the month, reducing costs without any commitment.
- **Committed use discounts**: Receive deeper discounts by committing to use specific resources (vCPUs, memory) for 1 or 3 years, regardless of actual usage.

## Special Types of VMs
### Preemptible VMs
- **Low-cost instances**: Up to 80% savings, limited to 24 hours, no live migrations.
- **Termination Warning**: 30 seconds warning before termination, but no guarantee the termination script will run successfully.
- **Limitations**: No live migrations, no automatic restarts.
- **Notes**: 
  - Subject to zone-specific capacity availability
  - Can be created in any zone where capacity is available
  - Higher chance of preemption during peak usage in popular zones
- **Best Practices**: Use monitoring and load balancers to manage preemptible instances effectively.

### Comparison of Special VM Types

| VM Type           | Base Features                                      | Zone/Region Scope | Use Cases                                      |
|-------------------|---------------------------------------------------|-------------------|-----------------------------------------------|
| Sole-tenant Nodes | Dedicated servers for compliance and performance. | **Zonal resource** | Workloads requiring physical isolation.       |
| Shielded VMs      | Enhanced security with Secure Boot and vTPM.      | **Zonal resource** | Protect against rootkits and bootkits.        |
| Confidential VMs  | Data encryption using AMD SEV technology.         | **Zonal resource** | Secure sensitive workloads like financial data.|

## Disks

### Persistent Disks
- **Key Features**:
  - Network-attached storage (through network interface) that's durable and bootable
  - Can be resized while running
  - Supports snapshots for backup
  - Can be attached read-only to multiple VMs
- **Zone/Region Information**:
  - **Zonal disks**: Standard persistent disks are zonal resources
  - **Regional disks**: Available for higher availability, replicated across 2 zones in a region
  - **Attachment rules**: Zonal disks can only attach to VMs in the same zone; regional disks can attach to VMs in either zone
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
  - **Zone-specific**: Local SSDs are always zonal resources, attached to specific VMs in specific zones
- **Important Notes**:
  - Can't create snapshots
  - Can't add after VM creation
  - Data persists through VM restart but not stop
  - Cannot be moved between zones or regions

## Images
- **Definition**: Pre-configured virtual disk templates for VM creation.
- **Types**: Public, Custom, Premium.
- **Zone/Region Scope**: Images are **global resources** - can be used to create VMs in any zone/region.
- **Best Practices**: Update custom images regularly; use public images for general workloads.

## Snapshots
- **Definition**: Point-in-time backups of persistent disks.
- **Key Exam Points**:
  - Stored in Cloud Storage (behind the scenes)
  - Incremental: Only changed blocks are saved
  - **Global resource**: Can be used across regions/zones to create new disks anywhere
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
- **Note**: No additional charge for using instance groups; costs depend on resources used.

### Managed Instance Groups (MIGs)
- All instances are **identical** (created from same instance template)
- **Autohealing**: Automatic health checks and instance replacement
- **Autoscaling**: Based on CPU utilization, load balancing capacity, or monitoring metrics
- **Deployment types**:
  - **Zonal MIG**: Single zone deployment
    - Lower cost and complexity
    - No protection against zone failures
    - All instances in same zone
  - **Regional MIG**: Multi-zone deployment for higher availability
    - **Higher availability**: Instances distributed across multiple zones in a region
    - **Automatic zone balancing**: Maintains equal distribution across zones
    - **Zone failure protection**: Continues operating if one zone fails
    - **Best practice for production**: Recommended for critical workloads

## Metadata and Scripts
- **Instance Metadata**: Every VM instance stores data about itself on a metadata server
  - Accessible at `http://metadata.google.internal/computeMetadata/v1/`
  - Examples: external IP address, instance name, project ID, zone
  - Can store custom metadata key-value pairs
- **Startup Scripts**: Automatically run when instance boots
  - Can be specified in metadata or Cloud Storage
  - Useful for software installation and configuration
- **Shutdown Scripts**: Run when instance is stopped or restarted
  - Limited execution time (90 seconds by default)
  - Useful for cleanup tasks and graceful shutdowns

## Common Actions

### Creating and Managing VM Instances

#### Create a VM Instance
- **Console**: Compute Engine → VM instances → Create instance
- **CLI**: `gcloud compute instances create INSTANCE_NAME --zone=ZONE`
- **Key Options**:
  - Machine type: `--machine-type=MACHINE_TYPE`
  - Boot disk: `--image-family=IMAGE_FAMILY --image-project=IMAGE_PROJECT`
  - Network tags: `--tags=TAG1,TAG2`
  - Service account: `--service-account=EMAIL`
  - Scopes: `--scopes=SCOPE1,SCOPE2`

#### Start/Stop/Restart VM Instance
- **Start**: `gcloud compute instances start INSTANCE_NAME --zone=ZONE`
- **Stop**: `gcloud compute instances stop INSTANCE_NAME --zone=ZONE`
- **Restart**: `gcloud compute instances reset INSTANCE_NAME --zone=ZONE`
- **Notes**: 
  - Stopping preserves data on persistent disks
  - Local SSD data is lost when stopping
  - External IP may change unless static

#### Delete VM Instance
- **CLI**: `gcloud compute instances delete INSTANCE_NAME --zone=ZONE`
- **Console**: Select instance → Delete
- **Notes**: 
  - Boot disk deleted by default unless `--keep-disks=boot`
  - Additional persistent disks preserved by default

### Disk Management

#### Create and Attach Persistent Disks
- **Create Zonal**: `gcloud compute disks create DISK_NAME --size=SIZE --zone=ZONE --type=DISK_TYPE`
- **Create Regional**: `gcloud compute disks create DISK_NAME --size=SIZE --region=REGION --type=DISK_TYPE`
- **Attach**: `gcloud compute instances attach-disk INSTANCE_NAME --disk=DISK_NAME --zone=ZONE`
- **Mount**: SSH to instance and use `sudo mount` commands
- **Notes**:
  - **Zonal disks**: Must be in same zone as VM
  - **Regional disks**: Can attach to VMs in either zone of the region
  - Format disk before first use: `sudo mkfs.ext4 /dev/sdb`

#### Create and Manage Snapshots
- **Create**: `gcloud compute disks snapshot DISK_NAME --snapshot-name=SNAPSHOT_NAME --zone=ZONE`
- **List**: `gcloud compute snapshots list`
- **Create disk from snapshot**: `gcloud compute disks create NEW_DISK --source-snapshot=SNAPSHOT_NAME --zone=ZONE`
- **Cross-region recovery**: `gcloud compute disks create NEW_DISK --source-snapshot=SNAPSHOT_NAME --zone=DIFFERENT_ZONE`
- **Notes**:
  - **Snapshots are global resources** - can create disks in any zone/region from snapshot
  - First snapshot is full, subsequent are incremental
  - Schedule snapshots using snapshot policies
  - **Key for disaster recovery**: Move workloads across zones/regions using snapshots

#### Resize Persistent Disks
- **Resize**: `gcloud compute disks resize DISK_NAME --size=NEW_SIZE --zone=ZONE`
- **Extend filesystem**: SSH to VM and run `sudo resize2fs /dev/sdb`
- **Notes**:
  - Can only increase size, never decrease
  - Can resize while disk is attached and in use

### Image Management

#### Create Custom Images
- **From instance**: `gcloud compute images create IMAGE_NAME --source-disk=DISK_NAME --source-disk-zone=ZONE`
- **From snapshot**: `gcloud compute images create IMAGE_NAME --source-snapshot=SNAPSHOT_NAME`
- **Notes**:
  - Stop instance before creating image for consistency
  - **Images are global resources** - can be used to create VMs in any zone/region
  - Can share images across projects
  - **Exam tip**: Use images for standardized VM deployments across multiple zones

#### Manage Image Families
- **Create with family**: `gcloud compute images create IMAGE_NAME --source-disk=DISK_NAME --family=FAMILY_NAME`
- **Use latest in family**: `--image-family=FAMILY_NAME --image-project=PROJECT_ID`
- **Notes**:
  - Family always points to latest non-deprecated image
  - Useful for automated deployments

### Instance Templates and Groups

#### Create Instance Templates
- **CLI**: `gcloud compute instance-templates create TEMPLATE_NAME --machine-type=MACHINE_TYPE --image-family=IMAGE_FAMILY`
- **Key parameters**:
  - `--boot-disk-size`, `--boot-disk-type`
  - `--network`, `--subnet`
  - `--tags`, `--service-account`
  - `--metadata` for startup scripts
- **Zone/Region Notes**: Templates are **global resources** but specify zone-specific configurations

#### Create Managed Instance Groups
- **Zonal MIG**: `gcloud compute instance-groups managed create GROUP_NAME --template=TEMPLATE_NAME --size=SIZE --zone=ZONE`
- **Regional MIG**: `gcloud compute instance-groups managed create GROUP_NAME --template=TEMPLATE_NAME --size=SIZE --region=REGION`
- **Autoscale**: `gcloud compute instance-groups managed set-autoscaling GROUP_NAME --max-num-replicas=MAX --target-cpu-utilization=0.8`
- **Update**: `gcloud compute instance-groups managed rolling-action start-update GROUP_NAME --version=template=NEW_TEMPLATE`
- **Zone/Region Considerations**:
  - **Zonal MIG**: All instances in single zone, lower cost
  - **Regional MIG**: Instances distributed across multiple zones, higher availability

### Network and Security

#### Configure Firewall Rules
- **Create**: `gcloud compute firewall-rules create RULE_NAME --allow=PROTOCOL:PORT --source-ranges=CIDR --target-tags=TAG`
- **Common examples**:
  - SSH: `--allow=tcp:22`
  - HTTP: `--allow=tcp:80`
  - HTTPS: `--allow=tcp:443`
  - Custom: `--allow=tcp:8080,udp:53`
- **Scope**: Firewall rules are **global resources** that apply to all regions but target specific instances via tags/service accounts

#### Manage Static IP Addresses  
- **Reserve Regional**: `gcloud compute addresses create ADDRESS_NAME --region=REGION`
- **Reserve Global**: `gcloud compute addresses create ADDRESS_NAME --global`
- **Assign to instance**: `gcloud compute instances add-access-config INSTANCE_NAME --access-config-name="External NAT" --address=IP_ADDRESS`
- **Release**: `gcloud compute addresses delete ADDRESS_NAME --region=REGION`
- **Zone/Region Notes**:
  - **Regional IPs**: For VM instances and regional load balancers
  - **Global IPs**: For global load balancers only

### SSH and Remote Access

#### SSH to Instances
- **Browser SSH**: Console → Compute Engine → VM instances → SSH button
- **CLI**: `gcloud compute ssh INSTANCE_NAME --zone=ZONE`
- **Custom SSH**: `gcloud compute ssh INSTANCE_NAME --zone=ZONE --ssh-key-file=PATH_TO_KEY`
- **Transfer files**: `gcloud compute scp LOCAL_FILE INSTANCE_NAME:REMOTE_PATH --zone=ZONE`

#### Configure OS Login
OS Login provides centralized SSH key management through IAM instead of managing keys manually on each VM instance.

- **Enable project-wide**: `gcloud compute project-info add-metadata --metadata enable-oslogin=TRUE`
- **Grant standard access** (regular user, no sudo): `gcloud projects add-iam-policy-binding PROJECT_ID --member="user:user@example.com" --role="roles/compute.osLogin"`
  - Allows SSH access to instances
  - Cannot run commands as root or use sudo
  - Suitable for application users and developers
- **Grant admin access** (sudo privileges): `gcloud projects add-iam-policy-binding PROJECT_ID --member="user:user@example.com" --role="roles/compute.osAdminLogin"`
  - Allows SSH access with administrative privileges
  - Can run commands as root using sudo
  - Suitable for system administrators and DevOps engineers
- **Add SSH key**: `gcloud compute os-login ssh-keys add --key-file=~/.ssh/id_rsa.pub`
- **SSH access**: `gcloud compute ssh INSTANCE_NAME --zone=ZONE`
- **Key benefit**: Centralized SSH key management via IAM with automatic key distribution

### Monitoring and Logging

#### View Instance Logs
- **Serial console**: `gcloud compute instances get-serial-port-output INSTANCE_NAME --zone=ZONE`
- **Startup script logs**: Check `/var/log/syslog` or Cloud Logging
- **Custom logs**: Use Cloud Logging agent

#### Monitor Instances
- **Metrics**: CPU, disk, network usage in Cloud Monitoring
- **Health checks**: For load balancers and instance groups
- **Alerting**: Set up alerts for resource utilization

### Troubleshooting

#### Common Issues and Solutions
- **Instance won't start**: Check quotas, disk space, firewall rules
- **Can't SSH**: Verify firewall rules, SSH keys, OS Login settings
- **Performance issues**: Check CPU/memory usage, disk I/O, network
- **Boot issues**: Check serial console output, startup script logs

#### Debug Network Connectivity
- **Internal**: Test with `ping`, `telnet`, check VPC firewall rules
- **External**: Verify Cloud NAT, external IP configuration
- **DNS**: Check `/etc/resolv.conf`, test with `nslookup`

## Zone/Region Resource Summary

| Resource Type | Scope | Key Characteristics | Migration/Movement |
|---------------|-------|-------------------|-------------------|
| **VM Instances** | Zonal | Cannot move between zones | Must recreate in new zone |
| **Persistent Disks (Zonal)** | Zonal | Default type, lower cost | Use snapshots to move |
| **Persistent Disks (Regional)** | Regional (2 zones) | Higher availability, higher cost | Can attach to VMs in either zone |
| **Local SSDs** | Zonal | Highest performance, ephemeral | Cannot move, data lost on stop |
| **Images** | Global | Can create VMs anywhere | No movement needed |
| **Snapshots** | Global | Cross-region backup/recovery | Create disks in any zone |
| **Instance Templates** | Global | Reusable VM configurations | Can deploy to any zone |
| **Managed Instance Groups** | Zonal/Regional | Zonal: single zone, Regional: multi-zone | Cannot change type after creation |
| **Firewall Rules** | Global | Apply across all regions | No movement needed |
| **Static IP Addresses** | Regional/Global | Regional: VMs, Global: Load balancers | Cannot move between regions |
