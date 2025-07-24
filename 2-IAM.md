# IAM
## Resource hierarchy
- **Organization**: The top level of the hierarchy, representing a company or entity.
- **Folders**: Used to group projects and other folders for better organization.
- **Projects**: The base level for creating and managing resources in GCP. Each project is associated with billing and quotas.
- **Resources**: Individual services and components (e.g., Compute Engine instances, Cloud Storage buckets) within a project.

## IAM roles and policies
- **Roles** define a set of permissions to access GCP resources.
- **Types of roles**:
    - **Basic roles**: Fixed, coarse-grained level of access.
        - **Owner**:
            - **Invite members**.
            - **Remove members**.
            - **Delete projects**.
            - **All Editor and Viewer permissions**.
        - **Editor**:
            - **Deploy applications**.
            - **Modify code**.
            - **Configure services**.
            - **All Viewer permissions**.
            - **Manage billing**.
        - **Viewer**:
            - **Read-only access to resources**.
            - **View billing information**.
        - **Billing Administrator**:
            - **Manage billing**.
            - **Add/remove administrators**.
            - **View billing information**.
    - **IAM predefined roles**: Roles that apply to a predefined GCP service in a project.
        - **Collection of permissions**, example **InstanceAdmin role** providing all the compute permissions.
        - **Examples of predefined roles**:
            - **Compute Admin**: Full control of compute engine resources.
            - **Network Admin**: Permissions to create, modify and delete networking resources, except for firewall rules and SSL certificates.
            - **Storage Admin**: Permissions to create, modify and delete disks, images and snapshots.
    - **Custom roles**: Let us define a precise set of permissions.
- **Basic roles are not recommended for fine-grained access control**.
- **Permissions for basic roles**:
    - **Owner**
        - **Invite members**.
        - **Remove members**.
        - **Delete projects**.
        - **All Editor and Viewer permissions**.
    - **Editor**
        - **Deploy applications**.
        - **Modify code**.
        - **Configure services**.
        - **All Viewer permissions**.
        - **Manage billing**.
    - **Viewer**
        - **Read-only access to resources**.
        - **View billing information**.
    - **Billing Administrator**
        - **Manage billing**.
        - **Add/remove administrators**.
        - **View billing information**.
- **Custom roles are created at the organization or project level**.

## Service accounts
- **Provide identities for carrying out server-to-server interactions**.
- **They are identified with an email address**. Ex: **service-account-name@project-id.iam.gserviceaccount.com**.
- **Programs running within Compute Engine instances can automatically acquire credentials of service account attached to that instance**.
## Types of Google Service Accounts
- **User-managed service accounts**: Created and managed by users for specific applications or workloads. Users control their permissions and lifecycle.
    - **Google only stores public key, user is responsible for managing private key**.
- **Default service accounts**: Automatically created by Google Cloud for each project and service (e.g., Compute Engine default service account). Used by resources when no user-managed account is specified.
    - **By default, the Compute Engine default service account is granted the Editor role at the project level**.
    - **By default, it is enabled on all instances created using gcloud Cloud Console**.
    - **Users can modify their permissions and use them for specific workloads**.
- **Google-managed service accounts**: Created and managed by Google for specific Google services (e.g., App Engine, Cloud Functions). Used internally by Google services to access resources on your behalf.
    - **These accounts are controlled entirely by Google and cannot be managed directly by users**.
    - **They are used for internal operations of Google services to interact with GCP resources**.
## Organization policies
- **Purpose**: Enforce constraints on resources to ensure compliance, security, and governance.
- **Scope**: Applied at organization, folder, or project levels.
- **Inheritance**: Policies are inherited by child resources unless overridden.
- **Constraints**:
  - **Restrict VM instance types**.
  - **Enforce encryption standards**.
  - **Limit access to external IPs**.
- **Policy Types**:
  - **Boolean constraints**: Enable/disable specific features.
  - **List constraints**: Specify allowed/denied values.
- **Best Practices**:
  - **Regularly review and update policies**.
  - **Test policies in non-production environments**.

## IAM Principals
| **Principal Type**          | **Identifier Format**                                   | **Example**                                      |
|-------------------------|----------------------------------------------------|----------------------------------------------|
| **Google Accounts**         | `user:USER_EMAIL_ADDRESS`                         | `user:alex@example.com`                      |
| **Service Accounts**        | `serviceAccount:SA_EMAIL_ADDRESS`                 | `serviceAccount:my-service-account@my-project.iam.gserviceaccount.com` |
| **Google Groups**           | `group:GROUP_EMAIL_ADDRESS`                       | `group:my-group@example.com`                 |
| **Domains**                 | `domain:DOMAIN`                                   | `domain:example.com`                         |
| **All Users**               | `allUsers`                                        | N/A                                          |
| **All Authenticated Users** | `allAuthenticatedUsers`                           | N/A                                          |

## Access security best practices
- **Least Privilege**: Assign only necessary permissions to users or service accounts.
- **IAM Predefined Roles**: Use predefined roles for fine-grained access control.
- **Two-Factor Authentication (2FA)**: Require 2FA for enhanced security.
- **Audit IAM Policies**: Regularly review IAM policies for compliance.
- **Restrict Service Account Permissions**: Limit permissions for service accounts.
- **Rotate Service Account Keys**: Regularly update keys for user-managed service accounts.
- **Organization Policies**: Enforce security constraints using policies.
- **Monitor IAM Activity**: Track IAM activity using Cloud Audit Logs.
- **Avoid AllUsers and AllAuthenticatedUsers**: Restrict access to these groups.
- **Secure API Access**: Use secure methods for API access.
- **Resource Hierarchy**: Organize resources and apply IAM policies effectively.
- **VPC Service Controls**: Define service perimeters to protect sensitive data.
