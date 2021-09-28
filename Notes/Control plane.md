### what is control plane?
The orchestration layer that exposes API and interfaces to define, deploy & manage lifecycle of the containers.

### Control plane components
- The control plane components make global decisions about the cluster (Ex: scheduling), as well as detecting and responding to the cluster events(Ex : Starting a new pod when deployment's replicas field is unsatisfied)
- Control plane components can be run on any machine in the cluster. However, for simplicity, setup scripts typically start all CPC on the same machine, and do not run user containers on this machine. 

#### kube-apiserver
The API server is a component of the k8s control plane that exposes the kubernetes API. The API server is the front end of the K8s Control plane. 
The kube-apiserver is designed to scale horizontally.

#### etcd
- Consistent & highly-available key value store used as k8's backing store for all cluster data. 

#### kube-scheduler
- Control plane component that watches for newly created Pods with no assigned node, and selects a node for them to run on.
- 

### Todo
- [ ] Read etcd documentation