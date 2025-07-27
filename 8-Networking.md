# Networking

## Projects and Networks
- **Projects**: Projects are the key organizer of infrastructure resources in Google Cloud.
- **Network Quota**: The default quota for each project is 5 networks.
- **Network Sharing and Peering**: Networks can be shared or peered with other networks.
- **Global Networks**: Google Cloud networks are global and span across all available regions. These networks do not have IP ranges.

## Virtual Private Cloud (VPC)
- **VPC Networks**: Global resources that contain subnetworks in regions.
- **Subnets**: Regional resources within a VPC network. Inside networks, resources can be segregated into subnetworks.
- **Firewall Rules**: Control traffic to and from instances.
- **Routes**: Define paths for traffic leaving the instances.

### Types of Networks
1. **Default Network**:
   - Every project is provided with a default network.
   - This network has a subnet in each region with non-overlapping CIDR ranges.
   - Default firewall rules include SSH, RDP, ICMP from anywhere, and egress traffic to anywhere.

2. **Auto Mode Network**:
   - Automatically creates one subnet for each region.
   - Subnets use a /20 mask, expandable up to a /16 mask.
   - All subnets fit within the 10.0.0.0/9 CIDR block.
   - If new regions become available, new subnets are added to the network.
   - An auto-mode network can be converted to custom mode, but not vice-versa.

3. **Custom Mode Network**:
   - Does not automatically create subnets.
   - Provides full control over IP ranges for subnets and the regions where they are created.

## Subnet Characteristics
- **Regional Scale**: Subnets work at a regional scale and can cross zones.
- **IP Range Assignment**: Subnets are assigned an IP range. The first and second addresses from the range are reserved for the network and subnet gateway.
- **Reserved Addresses**: The second-to-last and last addresses are reserved as broadcast IP addresses.
- **Subnet Extension**:
   - Subnets can be extended without any downtime.
   - The new subnet mask should not overlap with any other subnet range in the network.
   - Auto mode subnets can be extended from /20 to /16.

## Load Balancing
- **Global Load Balancers**: HTTP(S), SSL Proxy, TCP Proxy.
- **Regional Load Balancers**: Internal TCP/UDP Load Balancing.

## Cloud DNS
- Managed DNS service for high availability and low latency.
- It is a scalable, reliable, managed authoritative domain name system (DNS service).
- Uses Google’s global network of anycast nameservers to serve DNS zones.
- GCP offers a 100% update SLA for Cloud DNS.
- Allows creation and update of millions of DNS records without the burden of managing own DNS servers.

### DNS Resolution
#### DNS Resolution for Internal Addresses
- Each instance has a hostname that can be resolved to an internal IP address:
  - The hostname is the same as the instance name.
  - FQDN is `[hostname].[zone].c.[project-id].internal`.
- Name resolution is handled by an internal DNS resolver:
  - Provided as part of the Compute Engine (169.254.169.254).
  - Configured for use on instances via DHCP.
  - Provides answers for internal and external addresses.

#### DNS Resolution for External Addresses
- Instances with external IP addresses can connect to hosts outside the project.
- DNS records for external addresses can be published using existing DNS servers (outside of Google Cloud).
- DNS zones can be hosted using Cloud DNS.


## Hybrid Connectivity
- **Cloud VPN**: Securely connects on-premises networks to GCP.
- **Cloud Interconnect**: High-bandwidth, low-latency connection to GCP.

## Network Security
- **Cloud Armor**: Protects against DDoS attacks.
- **Private Google Access**: Allows instances without external IPs to reach Google APIs and services.

## Cloud NAT
- **Cloud NAT (Network Address Translation)**: Provides outbound internet access for resources in a private VPC network without exposing them to external IPs.
- **Key Features**:
  - Supports both manual and automatic NAT IP address allocation.
  - Scales automatically to handle traffic spikes.
  - Provides high availability without user intervention.
- **Use Cases**:
  - Allowing private instances to access the internet for updates or API calls.
  - Ensuring compliance by keeping resources private while enabling outbound connectivity.
- **Configuration**:
  - Can be configured using the GCP Console, gcloud CLI, or Terraform.
  - Requires a NAT gateway and a Cloud Router.

## Routes
- **Definition**: Routes define paths for network traffic to take within a VPC.
- **Types of Routes**:
  1. **System-generated routes**:
     - Default route to the internet (0.0.0.0/0) via the default internet gateway.
     - Routes for each subnet to enable internal communication.
  2. **Custom static routes**:
     - User-defined routes for specific traffic patterns.
  3. **Dynamic routes**:
     - Created by Cloud Router for hybrid connectivity (e.g., VPN, Interconnect).
- **Key Features**:
  - Routes are evaluated in order of priority (lower number = higher priority).
  - Support for next-hop options like instance, VPN tunnel, or Cloud Router.
- **Use Cases**:
  - Directing traffic between subnets.
  - Enabling hybrid connectivity with on-premises networks.
  - Controlling egress traffic to specific destinations.
