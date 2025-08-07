# Storage

## Cloud Storage (GCS)
- GCS is object storage, not a file system
- Object storage: key-value pairing between unique object names and binary data
- Fully managed, scalable, and globally accessible
- Objects are immutable - editing creates new versions
- Access control: IAM policies (recommended) or ACLs (legacy)

### Key Features
- **Object versioning**: Maintains history of object changes (enabled per bucket)
- **Lifecycle management**: Automated policies to transition or delete objects based on age, storage class, etc.
- **Uniform bucket-level access**: Simplifies permissions by using only IAM (disables ACLs)
- **Retention policies**: Prevents object deletion for compliance
- **Bucket lock**: Makes retention policies immutable

### Storage Classes
- **Standard Storage**:
  - **Multi-region**: Stored across multiple regions (>= 2 regions, 160km+ apart). SLA: 99.95%
  - **Dual-region**: Stored in 2 specific regions for compliance. SLA: 99.95%  
  - **Regional**: Stored in single region for lowest latency. SLA: 99.9%
  - Use case: Frequently accessed data, websites, content distribution

- **Nearline Storage**: 
  - Infrequently accessed data (once per month or less)
  - 30-day minimum storage duration
  - Higher retrieval costs than Standard
  - SLA: 99.0%

- **Coldline Storage**:
  - Rarely accessed data (once per quarter or less)  
  - 90-day minimum storage duration
  - Higher retrieval costs than Nearline
  - SLA: 99.0%

- **Archive Storage**:
  - Long-term archival (once per year or less)
  - 365-day minimum storage duration
  - Highest retrieval costs
  - SLA: 99.0%

### Data Transfer Options
- **Online transfer**: gsutil command-line tool for uploads/downloads
- **Storage Transfer Service**: Scheduled batch transfers from other clouds, on-premises, or HTTP endpoints
- **Transfer Appliance**: Physical device for large data migrations (up to 1PB)

### Bucket Naming
- Globally unique names (3-63 characters)
- Lowercase letters, numbers, hyphens, underscores only
- Cannot start/end with hyphen or contain consecutive periods

### Object lifecycle
- **Retention policy**:
  - Enforces a minimum retention period for objects in a bucket.
  - Objects cannot be deleted or overwritten until the retention period expires.
  - Retention period can range from 1 day to 3650 days (10 years).
  - Useful for compliance and regulatory requirements.
  - **Bucket retention**:
    - Applies to all objects within the bucket.
    - Retention period is defined at the bucket level and applies uniformly.
    - Can be used with **bucket lock** to enforce compliance.
  - **Object retention**:
    - Individual objects can have retention policies independent of the bucket.
    - Useful for granular control over specific objects.
    - Retention policies can be overridden by bucket-level policies if not locked.
  - **Bucket lock**:
    - Makes the retention policy permanent and unchangeable.
    - Once locked, the retention policy cannot be removed or reduced.
- **Object Hold**:
  - Prevents an object from being deleted or overwritten.
  - Two types of holds:
    - **Event-based hold**:
      - Retention starts after a specific event occurs (e.g., loan repayment).
      - Releasing the hold resets the retention period.
    - **Temporary hold**:
      - Used for regulatory or legal purposes.
      - Does not affect the retention period.
  - Holds can be applied individually to objects or set as a default for a bucket.
  - Objects with holds can have their metadata edited but cannot be deleted or replaced.
- **Object Versioning**:
  - Preserves deleted or overwritten objects as noncurrent versions.
  - Noncurrent versions retain the same object name but are uniquely identified by a generation number.
  - Allows recovery of specific object versions.
  - Can be combined with Object Lifecycle Management to automatically delete older versions.
  - Noncurrent versions are charged at the same rate as live objects.
- **Object Lifecycle Management**:
  - Automates actions on objects based on specified conditions.
  - Common actions:
    - **Delete**: Removes objects meeting the conditions.
    - **SetStorageClass**: Changes the storage class of objects (e.g., Standard to Coldline).
    - **AbortIncompleteMultipartUpload**: Cancels incomplete multipart uploads.
  - Common conditions:
    - **age**: Object age in days.
    - **createdBefore**: Objects created before a specific date.
    - **isLive**: Whether the object is live or noncurrent.
    - **matchesStorageClass**: Objects in a specific storage class.
    - **numNewerVersions**: Number of newer versions for versioned objects.

### Access Control List (ACL)

Access Control Lists (ACLs) in Google Cloud Storage (GCS) allow you to define access to buckets and objects. ACLs consist of entries specifying permissions and scopes.

#### Key Concepts
- **Permissions**:
  - `READER`: List bucket contents, read object data.
  - `WRITER`: Includes `READER` and allows creating, replacing, deleting objects.
  - `OWNER`: Includes `WRITER` and allows managing metadata and ACLs.

- **Scopes**:
  - `allUsers`: Public access.
  - `allAuthenticatedUsers`: All authenticated accounts.
  - Specific users, groups, or domains.

#### Predefined ACLs
- `private`: Grants `OWNER` to the bucket or object owner.
- `projectPrivate`: Default for new buckets/objects, grants access based on project roles.
- `publicRead`: Grants `OWNER` to the owner and `READER` to all users.

#### Best Practices
- Prefer IAM policies over ACLs for simpler, secure access control.
- Enable uniform bucket-level access to disable ACLs.
- Avoid `publicRead` unless necessary.

For more details, refer to the [official documentation](https://cloud.google.com/storage/docs/access-control/lists).

## Cloud SQL
- Fully managed relational databases (MySQL, PostgreSQL, SQL Server).
  - Google Cloud handles provisioning, patching, backups, and monitoring.
  - Ideal for applications requiring ACID compliance and SQL queries.

### Key Features
- **Security**: IAM integration, encryption at rest and in transit.
  - Data encrypted using Google-managed keys or customer-managed keys (CMEK).
- **High availability**: Regional HA with automatic failover (60-120 seconds).
  - Synchronous replication ensures zero data loss (RPO = 0).
- **Scaling**: Read replicas for scaling read-heavy workloads.
  - Cross-region replicas for disaster recovery.

### Connection Options
- **Private IP**: VPC-native connectivity, no internet exposure.
- **Cloud SQL Proxy**: Secure tunnel with IAM authentication.
- **SSL/TLS**: Encrypted connections for all connection types.

### Backup and Recovery
- **Automatic backups**: Daily backups with configurable retention.
- **Point-in-time recovery**: Recover to a specific timestamp using binary logs.
- **Export/Import**: SQL dump or CSV for data migration.

## Cloud Spanner
- Globally distributed, strongly consistent relational database
- Horizontal scaling with ACID transactions across regions
- Combines relational database features (ACID, SQL) with NoSQL scalability
- Minimum 3 nodes for production instances

### Key Features
- **Strong consistency**: ACID transactions across globally distributed data
- **Horizontal scaling**: Linear performance scaling with added nodes
- **SQL support**: Standard SQL with distributed computing extensions
- **Automatic sharding**: Data automatically partitioned across nodes
- **Multi-regional**: Can span multiple regions for global applications

### Instance Types
- **Regional**: Single region deployment for lower latency
- **Multi-regional**: Spans 3+ regions with leader region for writes
- **Processing units**: Fractional nodes (1000 processing units = 1 node)

### Use Cases
- Global applications requiring strong consistency
- Financial systems (trading, payments)
- Gaming leaderboards with global synchronization
- Applications needing both SQL and horizontal scaling

## Firestore
- Fully managed NoSQL document database
- Real-time synchronization and offline support
- Multi-regional replication with strong consistency
- Serverless with automatic scaling

### Key Features
- **Document-oriented**: Data stored as documents in collections (similar to JSON)
- **Real-time updates**: Automatic synchronization across clients
- **Offline support**: Local caching with automatic sync when online
- **ACID transactions**: Multi-document transactions with strong consistency
- **Security rules**: Declarative security model for access control
- **Indexes**: Automatic indexing with support for composite indexes

### Database Modes
- **Native mode**: 
  - New Firestore databases
  - Full feature set including real-time updates
  - Mobile and web client libraries
  - Recommended for new applications

- **Datastore mode**:
  - Compatible with Cloud Datastore
  - Server-side applications only
  - No real-time updates or mobile SDKs
  - For migrating from Datastore

### Data Model
- **Documents**: Key-value pairs (similar to JSON objects)
- **Collections**: Groups of documents
- **Subcollections**: Collections within documents for hierarchical data
- **References**: Pointers to other documents
- **Array and map fields**: Support for complex data structures

### Scaling and Performance
- **Automatic scaling**: Handles traffic spikes without configuration
- **Multi-regional**: Data replicated across multiple regions
- **Strong consistency**: Immediately consistent reads after writes
- **1 MB document size limit**
- **20,000 writes/second per database** (soft limit, can be increased)

### Security
- **Firebase Authentication**: Integration with user authentication
- **Security rules**: Define access control at document/field level
- **IAM integration**: Server-side access control for admin operations
- **Encryption**: Data encrypted at rest and in transit

### Use Cases
- Mobile and web applications requiring real-time updates
- Collaborative applications (chat, document editing)
- Social media platforms
- Gaming applications with user profiles and leaderboards
- Content management systems
- IoT applications with real-time data synchronization

## Cloud Bigtable
- Fully managed, wide-column NoSQL database service
- Petabyte-scale with single-digit millisecond latency
- HBase API compatible
- Optimized for time-series and analytical workloads
- Linear scaling with cluster size

### Key Features
- **High performance**: Single-digit millisecond latency at any scale
- **Massive scale**: Handles petabytes of data and millions of operations per second
- **Wide column**: Sparse, distributed, persistent multidimensional sorted map
- **Fully managed**: Automatic scaling, patching, and monitoring

### Data Model
- **Row key**: Unique identifier for each row (up to 4KB)
- **Column families**: Groups of related columns defined at table creation
- **Cells**: Store multiple timestamped versions of data
- **Timestamps**: Automatic or custom timestamps for versioning

### Instance Types
- **Production instances**: 
  - Minimum 3 nodes per cluster
  - SLA-backed performance guarantees
  - Multi-zone clusters for high availability
  
- **Development instances**:
  - Single-node clusters for testing
  - No SLA guarantees
  - Lower cost for development/testing

### Integration
- **BigQuery**: Direct integration for analytics queries
- **Dataflow**: Stream and batch processing integration
- **APIs**: HBase API, gRPC, REST API

### Security
- **IAM integration**: Role-based access control
- **Encryption**: Data encrypted at rest and in transit
- **VPC connectivity**: Private network access

### Use Cases
- Time-series data (IoT sensors, monitoring metrics)
- High-throughput applications (ad serving, gaming)
- Real-time analytics and data warehousing supplements
- Machine learning feature stores

## Memorystore
- Fully managed in-memory data store service (Redis and Memcached)
- Sub-millisecond latency for high-performance applications
- VPC-native with private IP addresses

### Service Tiers
- **Standard Tier**: High availability with automatic failover
- **Basic Tier**: Single instance for development/testing (no HA)

### Key Features
- **Scaling**: Up to 300GB memory capacity
- **Security**: VPC integration, encryption at rest and in transit
- **Backup**: Redis RDB snapshots for data persistence

### Use Cases
- Application caching and session storage
- Real-time analytics and gaming leaderboards

## Database Types Comparison Table

| Feature | Cloud SQL | Cloud Spanner | Firestore | Cloud Bigtable | Memorystore |
|---------|-----------|---------------|-----------|----------------|-------------|
| **Database Type** | Relational (RDBMS) | Globally distributed relational | NoSQL document | NoSQL wide-column | In-memory cache |
| **Query Language** | Standard SQL | Standard SQL | Document queries | HBase API | Redis/Memcached |
| **Scaling** | Vertical + read replicas | Horizontal (linear) | Automatic | Linear with nodes | Vertical scaling |
| **Global Distribution** | Regional only | Multi-regional | Multi-regional | Regional/Multi-zone | Regional only |
| **Consistency** | ACID | Strong ACID globally | Strong consistency | Eventually consistent | Key-value consistency |
| **Best Use Cases** | Traditional web apps, existing SQL apps | Global apps needing consistency | Mobile/web apps with real-time needs | Time-series, IoT, analytics | Caching, sessions, real-time |
| **Maximum Storage** | 30TB | Petabyte scale | Unlimited | Petabyte scale | 300GB |
| **Key Features** | Managed MySQL/PostgreSQL/SQL Server | Global transactions | Real-time sync, offline support | High-throughput, low latency | Sub-millisecond latency |
| **Minimum Setup** | Single instance | 3 nodes (production) | Serverless | 3 nodes (production) | Single instance |

## Exam Tips
- Dual-region buckets are only available for selected regions.
- Depending on size and throughput, use gsutil / Transfer Service (low cost) / Transfer
Appliance.
- Storage Transfer Service can be set up one-off (e.g., move bucket to new
location) and recurring (e.g., back up from S3 to GCS with rsync-like semantics).