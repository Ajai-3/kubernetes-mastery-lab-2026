# Component: etcd

`etcd` is the consistent and highly-available key-value store used as Kubernetes' backing store for all cluster data. It serves as the "source of truth" by maintaining the entire state of the cluster.

## Key Responsibilities

* **Source of Truth:** Stores all cluster data, including configuration, pod status, secrets, and system state.
* **Desired State:** Stores the configuration you define (e.g., "I want 3 pods running").
* **Current State:** Stores the actual status of the cluster as reported by the system (e.g., "Currently, 0 pods are running").
* **Comparison Engine:** Acts as the reference point for the control plane to constantly compare the **Desired State** against the **Current State**.
* **Consistency:** Ensures that all cluster members see the same data at the same time, preventing conflicts during state updates.
* **Watch Mechanism:** Provides a critical feature that allows other control plane components (like the API server) to watch for changes in the data, triggering updates automatically when the current state deviates from the desired state.

## Why it is Critical

* **Cluster Reliability:** If `etcd` fails, the API server cannot read or write data, effectively rendering the cluster unresponsive.
* **State Reconciliation:** Kubernetes is built on the concept of control loops that compare the "Current State" to the "Desired State." `etcd` holds both of these values, making it the heart of the reconciliation process.

## Interaction Flow

1. **API Requests:** The `kube-apiserver` receives your command and stores the **Desired State** in `etcd`.
2. **Reconciliation Loop:** The Controller Manager continuously watches `etcd` and detects that the **Current State** (0) does not match the **Desired State** (3).
3. **Storage Updates:** As the system works to create the pods, it constantly updates `etcd` with the progress and the final **Current State**.
4. **Notification:** Whenever the state changes, `etcd` notifies the `kube-apiserver`, which then alerts the controllers to act on the update.b