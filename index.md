## How to: Complete DevOps Stack Deployments

When it comes to the question of how to deploy a complete DevOps stack,
many considerations focus on the wrong aspects at the wrong time.
It does not _really_ matter whether you prefer a mono-repository or not,
it does not _really_ matter whether your CI/CD tool is Jenkins or GitHub Actions,
and it does not _really_ matter whether the software you want to deploy has been built in-house or is coming as standard software from another vendor.

<!--
What is way more important are considerations like:

  1. [On-Premise vs. Cloud](#)

  2. [CI/CD Workflows](#)

  3. [Environments and Namespaces](#)
-->

...

A typical real-life scenario looks like this:
The management decided to move the complete DevOps stack from on-premise to a cloud provider.
The new opportunities in the cloud have led to the decision to perform a rearchitecture to provide new features to users and developers.
Money has been budgeted for these new features, but it is limited and no money should be wasted unnecessarily.
For example, services in non-production environments can run with less vCPU and RAM as they don't service houndreds, thousands or millions of users, but just a few.
And as another example, storage does not need to have the same size and redundancy type as storage in production.


### Distinction between Infrastructure and Services

A DevOps stack deployment can usually be split into two parts; infrastructure and services.
Services are the applications that run inside a Kubernetes cluster to serve users and that probably get deployed using Helm charts.
Infrastructure on the other hand are all components that are required by these services to fully run; networks, storage, databases, virtual machines, clusters, load balancers etc.
However, for some of the components it is not that easy to asses whether they are infrastructure or not.
One of the best examples for such components are Ingress controllers.

> What is an Ingress Controller?
> 
> An Ingress controller is a specialized load balancer for Kubernetes (and other containerized) environments. [...] An Ingress controller abstracts away the complexity of Kubernetes application traffic routing and provides a bridge between Kubernetes services and external ones.
>
> https://www.nginx.com/resources/glossary/kubernetes-ingress-controller/

Ingress controllers like the NGINX Ingress Controller can be installed using an [official Helm chart](https://docs.nginx.com/nginx-ingress-controller/installation/installation-with-helm/).
This leads to the impression that an Ingress controller is a service (based on the above explanation).
On the other hand it also belongs to the list of infrastructure components (load balancer) and it is required by other services for them to fully run.

For cases like this it is simpler to assess whether a component can be deployed _easily_ during service deployment.
If a component does not fit in there properly, it is probably infrastructure.
It <!--In section [TODO](#) it-->  will turn out that it is quite tricky to deploy an Ingress controller during a regular service deployment using this approach.
The main reason is that we will have a variable number of namespaces within one environment (one Kubernetes cluster) but only one Ingress controller for all of them.
That's why the Ingress controller and similar components like a cert manager, a (Kubernetes) backup tool etc. should be considered as infrastructure.


### Splitting Infrastructure Components

