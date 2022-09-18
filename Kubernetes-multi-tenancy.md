# Kubernetes Multi-tenancy

## Where multi-tenancy serves better?
Before even discussing multi-tenant architecture, one should know whether it is better to deploy **a single Kubernetes cluster with multi-tenant
support or should deploy multiple clusters**, different for each tenant.

There is no specific rule for this and most organisations decide this on the basis of ease of use, management etc. Deploying multiple
clusters is simple as you don’t have to worry about following additional best practices such as auditing RBAC, setting up standard practice for users.
Besides this, cost also plays an important role while making this decision. If cost is not an issue and you want simple architecture, deploy multiple
clusters. Whereas, if your compute need is far less and you want to save some extra cost, multi-tenancy could be a better option. Some other factors
also play a vital role such as the lifecycle of your cluster, if your need is only temporary, multi-cluster could be a viable option, as you don’t have
to install anything else on your cluster.

One major drawback of having multiple clusters is the increased management overhead, specially for the large environments, it requires a lot of effort
to keep every cluster updated, more security audits would be required as well.

## Use cases

### Multiple teams
A common form of multi-tenancy is to share a cluster between multiple teams within an organization, each of whom may operate one or more workloads.
These workloads frequently need to communicate with each other, and with other workloads located on the same or different clusters.

In this scenario, members of the teams often have direct access to Kubernetes resources via tools such as kubectl, or indirect access through GitOps
controllers or other types of release automation tools. There is often some level of trust between members of different teams, but Kubernetes policies
such as RBAC, quotas, and network policies are essential to safely and fairly share clusters.

### Multiple customers
This form of multi-tenancy involves a Software-as-a-Service (SaaS) vendor running multiple instances of a workload for customers.
In this scenario, the customers do not have access to the cluster; Kubernetes is invisible from their perspective and is only used
by the vendor to manage the workloads. Kubernetes policies are used to ensure that the workloads are strongly isolated from each other.
