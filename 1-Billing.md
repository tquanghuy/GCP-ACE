# Billing
## Billing accounts and projects
- **Billing is setup in project level**, when we setup a project, a billing account is linked to it.
- **Billing account can be linked to one or more projects**, a project only link to 1 billing account.
- **Project without billing account can only use free GCP services**.
- **Billing account can be charged monthly or at a set threshold**.
    - **Monthly**: You are charged once per month, regardless of how much you've spent.
    - **Threshold**: GCP charges your payment method as soon as your account reaches a certain spending threshold, which is dynamically managed.
- **Subaccounts can be used for separate billing for projects**.
    - Example: Project A and B link to billing account X, project C and D link to account Y.
- **Roles associate with billing**:
    - **Billing Account Creator**: Can create new self-serve billing accounts.
    - **Billing Account Administrator**: Manage billing accounts but cannot create them.
    - **Billing Account User**: Enable a user to link project to billing account, enable APIs that require billing to be enabled.
    - **Billing Account Viewer**: Enable a user to view billing account cost and transactions.

## Budget
- **Budget is used to manage cost ($)**.
- **Budget is set as project level**.
- **When exceed budget limit, we can configure billing account to send an alert**.
- **Budget alert can be sent to email or Pub/Sub**.
## Quota
- **Quota is used to limit and control resource usage**, calculated by service unit: Service units (e.g., CPUs, API calls, storage).
- **Quota is managed in many scopes**: API/service/project level (technical scope).
- **Quota**:
    - **Rate quote**: reset after period of time.
    - **Allocation quota**: do not reset at interval, we have to free up resources of adhere to these quotas. These quotas can be increased after requesting.
## Billing export to BigQuery
- **Billing can be export to BigQuery and visualize in Data Studio**.
- Docs: https://cloud.google.com/billing/docs/how-to/export-data-bigquery-setup.