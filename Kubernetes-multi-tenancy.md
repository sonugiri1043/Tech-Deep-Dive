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

---

The remainder of this page focuses on isolation techniques used for shared Kubernetes clusters. However, even if you are considering dedicated clusters, it may be valuable to review these recommendations, as it will give you the flexibility to shift to shared clusters in the future if your needs or capabilities change.

## Control plane isolation

### Namespaces
In Kubernetes, a Namespace provides a mechanism for isolating groups of API resources within a single cluster. This isolation has two key dimensions:

1. Object names within a namespace can overlap with names in other namespaces, similar to files in folders. This allows tenants to name their resources without having to consider what other tenants are doing.
2. Many Kubernetes security policies are scoped to namespaces. For example, RBAC Roles and Network Policies are namespace-scoped resources. Using RBAC, Users and Service Accounts can be restricted to a namespace.

In a multi-tenant environment, a Namespace helps segment a tenant's workload into a logical and distinct management unit. In fact, a common practice is to isolate every workload in its own namespace, even if multiple workloads are operated by the same tenant. This ensures that each workload has its own identity and can be configured with an appropriate security policy.

The namespace isolation model requires configuration of several other Kubernetes resources, networking plugins, and adherence to security best practices to properly isolate tenant workloads.

## Access controls
Role-based access control (RBAC) is commonly used to enforce authorization in the Kubernetes control plane, for both users and workloads (service accounts). Roles and RoleBindings are Kubernetes objects that are used at a namespace level to enforce access control in your application; similar objects exist for authorising access to cluster-level objects, though these are less useful for multi-tenant clusters.

## Quotas
Kubernetes workloads consume node resources, like CPU and memory. In a multi-tenant environment, you can use Resource Quotas to manage resource usage of tenant workloads. Resource quotas are namespaced objects. By mapping tenants to namespaces, cluster admins can use quotas to ensure that a tenant cannot monopolize a cluster's resources or overwhelm its control plane.

Quotas prevent a single tenant from consuming greater than their allocated share of resources hence minimizing the “noisy neighbor” issue, where one tenant negatively impacts the performance of other tenants' workloads.

When you apply a quota to namespace, Kubernetes requires you to also specify resource requests and limits for each container. Limits are the upper bound for the amount of resources that a container can consume. Containers that attempt to consume resources that exceed the configured limits will either be throttled or killed, based on the resource type. When resource requests are set lower than limits, each container is guaranteed the requested amount but there may still be some potential for impact across workloads.

Quotas cannot protect against all kinds of resource sharing, such as network traffic. Node isolation (described below) may be a better solution for this problem.

## Data Plane Isolation

