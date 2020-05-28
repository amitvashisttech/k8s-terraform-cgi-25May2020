# Labels and Selectors

Labels are key/value pairs that are attached to objects, such as pods. Labels are intended to be used to specify identifying attributes of objects that are meaningful and relevant to users, but do not directly imply semantics to the core system. Labels can be used to organize and to select subsets of objects. Labels can be attached to objects at creation time and subsequently added and modified at any time. Each object can have a set of key/value labels defined. Each Key must be unique for a given object.

```
"metadata": {
  "labels": {
    "key1" : "value1",
    "key2" : "value2"
  }
}
```

# Motivation

Labels enable users to map their own organizational structures onto system objects in a loosely coupled fashion, without requiring clients to store these mappings.

Service deployments and batch processing pipelines are often multi-dimensional entities (e.g., multiple partitions or deployments, multiple release tracks, multiple tiers, multiple micro-services per tier). Management often requires cross-cutting operations, which breaks encapsulation of strictly hierarchical representations, especially rigid hierarchies determined by the infrastructure rather than by users

Example labels:

- ``` "release" : "stable", "release" : "canary" ```
- ``` "environment" : "dev", "environment" : "qa", "environment" : "production"```
- ```"tier" : "frontend", "tier" : "backend", "tier" : "cache"```

# Syntax and character set
Labels are key/value pairs. Valid label keys have two segments: an optional prefix and name, separated by a slash (/). The name segment is required and must be 63 characters or less, beginning and ending with an alphanumeric character ([a-z0-9A-Z]) with dashes (-), underscores (_), dots (.), and alphanumerics between. 

For example, here’s the configuration file for a Pod that has two labels environment: production and app: nginx :
```
apiVersion: v1
kind: Pod
metadata:
  name: label-demo
  labels:
    environment: production
    app: nginx
spec:
  containers:
  - name: nginx
    image: nginx:1.14.2
    ports:
    - containerPort: 80
```

# Concepts
Overview
What is Kubernetes?
Kubernetes Components
The Kubernetes API
Working with Kubernetes Objects
Understanding Kubernetes Objects
Kubernetes Object Management
Object Names and IDs
Namespaces
Labels and Selectors
Annotations
Field Selectors
Recommended Labels
Cluster Architecture
Nodes
Control Plane-Node Communication
Controllers
Cloud Controller Manager
Containers
Containers overview
Images
Container Environment
Runtime Class
Container Lifecycle Hooks
Workloads
Pods
Pod Overview
Pods
Pod Lifecycle
Init Containers
Pod Preset
Pod Topology Spread Constraints
Disruptions
Ephemeral Containers
Controllers
ReplicaSet
ReplicationController
Deployments
StatefulSets
DaemonSet
Garbage Collection
TTL Controller for Finished Resources
Jobs - Run to Completion
CronJob
Services, Load Balancing, and Networking
Service
Service Topology
EndpointSlices
DNS for Services and Pods
Connecting Applications with Services
Ingress
Ingress Controllers
Network Policies
Adding entries to Pod /etc/hosts with HostAliases
IPv4/IPv6 dual-stack
Storage
Volumes
Persistent Volumes
Volume Snapshots
CSI Volume Cloning
Storage Classes
Volume Snapshot Classes
Dynamic Volume Provisioning
Node-specific Volume Limits
Configuration
Configuration Best Practices
ConfigMaps
Secrets
Managing Resources for Containers
Pod Overhead
Resource Bin Packing for Extended Resources
Organizing Cluster Access Using kubeconfig Files
Pod Priority and Preemption
Security
Overview of Cloud Native Security
Pod Security Standards
Policies
Limit Ranges
Resource Quotas
Pod Security Policies
Scheduling and Eviction
Kubernetes Scheduler
Taints and Tolerations
Assigning Pods to Nodes
Scheduling Framework
Scheduler Performance Tuning
Cluster Administration
Cluster Administration Overview
Certificates
Cloud Providers
Managing Resources
Cluster Networking
Logging Architecture
Metrics For The Kubernetes Control Plane
Configuring kubelet Garbage Collection
Proxies in Kubernetes
API Priority and Fairness
Installing Addons
Extending Kubernetes
Extending your Kubernetes Cluster
Extending the Kubernetes API
Custom Resources
Extending the Kubernetes API with the aggregation layer
Compute, Storage, and Networking Extensions
Network Plugins
Device Plugins
Operator pattern
Service Catalog
Poseidon-Firmament Scheduler

Edit This Page
Labels and Selectors

Labels are key/value pairs that are attached to objects, such as pods. Labels are intended to be used to specify identifying attributes of objects that are meaningful and relevant to users, but do not directly imply semantics to the core system. Labels can be used to organize and to select subsets of objects. Labels can be attached to objects at creation time and subsequently added and modified at any time. Each object can have a set of key/value labels defined. Each Key must be unique for a given object.

"metadata": {
  "labels": {
    "key1" : "value1",
    "key2" : "value2"
  }
}

Labels allow for efficient queries and watches and are ideal for use in UIs and CLIs. Non-identifying information should be recorded using annotations.

    Motivation
    Syntax and character set
    Label selectors
    API

Motivation

Labels enable users to map their own organizational structures onto system objects in a loosely coupled fashion, without requiring clients to store these mappings.

Service deployments and batch processing pipelines are often multi-dimensional entities (e.g., multiple partitions or deployments, multiple release tracks, multiple tiers, multiple micro-services per tier). Management often requires cross-cutting operations, which breaks encapsulation of strictly hierarchical representations, especially rigid hierarchies determined by the infrastructure rather than by users.

Example labels:

    "release" : "stable", "release" : "canary"
    "environment" : "dev", "environment" : "qa", "environment" : "production"
    "tier" : "frontend", "tier" : "backend", "tier" : "cache"
    "partition" : "customerA", "partition" : "customerB"
    "track" : "daily", "track" : "weekly"

These are just examples of commonly used labels; you are free to develop your own conventions. Keep in mind that label Key must be unique for a given object.
Syntax and character set

Labels are key/value pairs. Valid label keys have two segments: an optional prefix and name, separated by a slash (/). The name segment is required and must be 63 characters or less, beginning and ending with an alphanumeric character ([a-z0-9A-Z]) with dashes (-), underscores (_), dots (.), and alphanumerics between. The prefix is optional. If specified, the prefix must be a DNS subdomain: a series of DNS labels separated by dots (.), not longer than 253 characters in total, followed by a slash (/).

If the prefix is omitted, the label Key is presumed to be private to the user. Automated system components (e.g. kube-scheduler, kube-controller-manager, kube-apiserver, kubectl, or other third-party automation) which add labels to end-user objects must specify a prefix.

The kubernetes.io/ and k8s.io/ prefixes are reserved for Kubernetes core components.

Valid label values must be 63 characters or less and must be empty or begin and end with an alphanumeric character ([a-z0-9A-Z]) with dashes (-), underscores (_), dots (.), and alphanumerics between.

For example, here’s the configuration file for a Pod that has two labels environment: production and app: nginx :


apiVersion: v1
kind: Pod
metadata:
  name: label-demo
  labels:
    environment: production
    app: nginx
spec:
  containers:
  - name: nginx
    image: nginx:1.14.2
    ports:
    - containerPort: 80

# Label selectors
Unlike names and UIDs, labels do not provide uniqueness. In general, we expect many objects to carry the same label(s).

Via a label selector, the client/user can identify a set of objects. The label selector is the core grouping primitive in Kubernetes.

The API currently supports two types of selectors: equality-based and set-based. A label selector can be made of multiple requirements which are comma-separated. In the case of multiple requirements, all must be satisfied so the comma separator acts as a logical AND (&&) operator.

### Equality-based requirement
Equality- or inequality-based requirements allow filtering by label keys and values. Matching objects must satisfy all of the specified label constraints, though they may have additional labels as well. Three kinds of operators are admitted =,==,!=. The first two represent equality (and are simply synonyms), while the latter represents inequality. For example:
```
environment = production
tier != frontend
```
The former selects all resources with key equal to environment and value equal to production. The latter selects all resources with key equal to tier and value distinct from frontend, and all resources with no labels with the tier key. One could filter for resources in production excluding frontend using the comma operator: environment=production,tier!=frontend

```
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: helloworld-deployment
spec:
  replicas: 3
  template:
    metadata:
      labels:
        app: helloworld
    spec:
      containers:
      - name: k8s-demo
        image: amitvashist7/k8s-tiny-web
        ports:
        - name: nodejs-port
          containerPort: 80
      nodeSelector:
        hardware: virtual
```

Mult-Node Selector

```
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: helloworld-deployment-2
spec:
  replicas: 3
  template:
    metadata:
      labels:
        app: helloworld-1
    spec:
      containers:
      - name: k8s-demo
        image: amitvashist7/k8s-tiny-web
        ports:
        - name: nodejs-port
          containerPort: 80
      nodeSelector:
        hardware: virtual
        env: prod
```
