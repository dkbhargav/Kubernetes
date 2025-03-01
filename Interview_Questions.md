**What is Kubernetes, and why is it used?**

Kubernetes is an open-source container orchestration platform used for automating application deployment, scaling, and management. It helps manage containerized workloads efficiently across multiple hosts.

**2. What are the main components of Kubernetes architecture?**

**Master Node Components:**
- kube-apiserver (Handles API requests)
- etcd (Key-value store for cluster state)
- kube-scheduler (Assigns pods to nodes)
- kube-controller-manager (Manages controllers)

**Worker Node Components:**
- kubelet (Communicates with API server & manages pods)
- kube-proxy (Handles networking)
- Container runtime (Docker, containerd, etc.)

**3. Explain the difference between a Pod, Deployment, ReplicaSet, and StatefulSet.**
- **Pod:** The smallest deployable unit in Kubernetes, which can contain one or more containers.
- **ReplicaSet:** Ensures a specified number of pod replicas are running at all times.
- **Deployment:** Manages ReplicaSets and provides rollout & rollback functionality.
- **StatefulSet:** Used for stateful applications like databases, ensuring ordered deployment and stable pod names.

**4. What is a Kubernetes Namespace? How is it useful?**

A Namespace is a logical separation within a cluster, allowing multiple teams to share the cluster without conflicts.

**5. How does Kubernetes handle service discovery and load balancing?**
Kubernetes uses Services for discovery:
- **ClusterIP:** Internal communication within the cluster.
- **NodePort:** Exposes services on each node’s IP.
- **LoadBalancer:** Integrates with cloud providers for external traffic.
- Uses CoreDNS for name resolution.

**6. What is a ConfigMap, and how is it different from a Secret?**
- **ConfigMap:** Stores non-sensitive configuration data as key-value pairs.
- **Secret:** Stores sensitive information (e.g., passwords, API keys) in an encoded format.

**7. What is the role of kube-apiserver, kube-scheduler, kube-controller-manager, and kubelet?**
- **kube-apiserver:** Handles API requests.
- **kube-scheduler:** Assigns pods to worker nodes.
- **kube-controller-manager:** Manages controllers (ReplicaSet, Node Controller, etc.).
- **kubelet:** Runs on worker nodes, ensures containers are running.

**8. What is the difference between ClusterIP, NodePort, and LoadBalancer service types?**
- **ClusterIP:** Internal-only service.
- **NodePort:** Exposes a service on each node’s IP.
- **LoadBalancer:** Uses cloud provider’s LB to expose services.

**9. What is a Persistent Volume (PV) and Persistent Volume Claim (PVC)?**
- **PV:** A cluster resource that provides storage.
- **PVC:** A request for storage by a Pod.

**10. How does a Pod communicate with another Pod in a different namespace?**

Using FQDN: `<service-name>.<namespace>.svc.cluster.local`

**11. How does Kubernetes handle rolling updates and rollbacks?**

Using Deployments:
- Rolling updates replace Pods gradually (`kubectl rollout restart deployment <name>`).
- Rollbacks revert changes (`kubectl rollout undo deployment <name>`).

**12. What is the difference between a DaemonSet and a StatefulSet?**
- **DaemonSet:** Runs one pod per node (e.g., logging, monitoring).
- **StatefulSet:** Manages stateful apps (e.g., databases) with persistent storage.

**13. What is the purpose of the etcd database in Kubernetes?**

Stores all cluster state and configurations.

**14. Explain the concept of Ingress in Kubernetes.**

Ingress provides HTTP/HTTPS routing to services using an Ingress Controller.

**15. How do you debug a failing Pod in Kubernetes?**
- `kubectl describe pod <pod-name>`
- `kubectl logs <pod-name>`
- `kubectl exec -it <pod-name> -- /bin/sh`

**16. What is a HorizontalPodAutoscaler (HPA), and how does it work?**

HPA automatically scales Pods based on CPU or memory usage.

**17. How do you implement RBAC (Role-Based Access Control) in Kubernetes?**

Using Role, RoleBinding, ClusterRole, and ClusterRoleBinding.

**18. What are InitContainers, and how are they different from normal containers?**

InitContainers run before app containers to prepare the environment.

**19. How does Kubernetes handle networking internally?**

Uses CNI (Container Network Interface) plugins like Flannel, Calico.

**20. What is the difference between kubectl exec and kubectl logs?**
- **kubectl exec:** Runs a command inside a running container.
- **kubectl logs:** Fetches logs from a container.

**21. How would you secure a Kubernetes cluster?**
- Enable RBAC
- Use Network Policies
- Restrict API access
- Scan images for vulnerabilities
- Enable TLS for communication

**22. Explain how service mesh (Istio, Linkerd) works in Kubernetes.**

A service mesh manages service-to-service communication with traffic control, security, and observability.

**23. How does Kubernetes scheduler work?**

It considers resource requirements, taints/tolerations, node affinity.

**24. What is a Pod Disruption Budget (PDB)?**

Ensures a minimum number of Pods remain available during voluntary disruptions.

**25. How would you implement a multi-cluster Kubernetes deployment?**

Using Cluster Federation or Kubernetes Gateway API.

**26. How do you configure a Kubernetes cluster to run in high availability mode?**

- Use multiple master nodes.
- Configure external etcd cluster.
- Use a Load Balancer for API server.

**27. How does Kubernetes handle stateful applications like databases?**

Using StatefulSets and Persistent Volumes.

**28. How would you monitor Kubernetes?**

Using Prometheus, Grafana, Dynatrace, ELK Stack.

**29. Explain how Custom Resource Definitions (CRDs) work in Kubernetes.**

CRDs extend Kubernetes by defining custom objects.

**30. How does Kubernetes handle node failure?**

- Kubernetes reschedules pods to healthy nodes.
- Node Controller detects and marks the node as NotReady.

**Hands-on Kubernetes Tasks (YAML Examples)**

**1. Deployment YAML**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
```

**2. Persistent Volume & PVC**
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

