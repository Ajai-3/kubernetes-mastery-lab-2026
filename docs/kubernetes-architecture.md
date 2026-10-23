# Kubernetes Architecture

## Cluster Architecture

A Kubernetes clusture consists of the control plane and the worker mechines called nodes, that run the containerized applications.
Every clusture needs ar least one worker node in the order to run pods.

## Control Plane

The Control Plane acts as the "brain" of the Kubernetes cluster. It is responsible for making all global decisions about the cluster, such as scheduling workloads and detecting or responding to cluster events. Its primary role is to ensure the cluster maintains the desired state defined by the user.

![alt.image](/images/k8-clusture-architecture.png)

## Control plane components

**- [kube-apiserver](01-Control-Plane/kube-apiserver.md):** The front-end of the control plane. It is the gateway for developers to communicate with Kubernetes and handles all internal cluster communication.


**- [etcd](01-Control-Plane/etcd.md):** The database used in Kubernetes that stores all cluster data in key-value pairs, maintaining the current and desired states of the cluster.

**- [kube-scheduler](01-Control-Plane/kube-scheduler.md):** The component that watches for new pods and decides which node is the best place for each one to run.

**- [kube-controller-manager](01-Control-Plane/kube-controller-manager.md):** A daemon that runs control loops to ensure the cluster matches its desired state by handling tasks like node monitoring, pod replication, and service management.

**- [cloud-controll-manager](01-Control-Plane/cloud-controll-manager.md):** A component that links your cluster into your cloud provider's API, allowing you to manage cloud-specific resources like load balancers and storage volumes.

## Worker mechine (nodes) componets

**- [kubelet](01-Control-Plane/kubelet.md):** The "node agent" that runs on each worker node. It receives instructions from the kube-apiserver and ensures that the containers described in the PodSpecs are running and healthy. The node (The computer).

**- [kube-proxy](01-Control-Plane/kube-proxy.md):** A network proxy that runs on each node. It manages networking rules, ensuring that traffic reaches the correct containers and handles communication between services.

**- [container-runtime](01-Control-Plane/container-runtime.md):** The software responsible for running containers (such as containerd or CRI-O). It handles the actual pulling of images and starting/stopping of containers.

## End-to-End Pod Creation Flow

1. **The Request:** You send a command via `kubectl` to the **API Server** asking for 3 pods.
2. **Saving the Plan:** The **API Server** checks your request and saves your "goal" (3 pods) into the database called **etcd**.
3. **The Check:** The **Controller Manager** looks at your goal (3 pods) and sees we currently have 0.
4. **The Order:** The **Controller Manager** tells the **API Server** to start building those 3 new pods.
5. **Cloud Setup:** If you need special cloud tools (like a load balancer), the **Cloud Controller Manager** talks to your cloud provider to set them up.
6. **Picking the Spot:** The **Scheduler** looks at all your computers (nodes) to find the best place for each pod to run.
7. **Updating the Plan:** The **Scheduler** tells the **API Server** which node should run each pod.
8. **Getting the Order:** The **Kubelet** (the agent on your node) sees the new instructions from the **API Server**.
9. **Starting the Pod:** The **Kubelet** tells the **Container Runtime** to download the images and turn on your 3 pods.
10. **Connecting the Network:** The **Kube-proxy** sets up the network rules so your pods can send and receive traffic.
