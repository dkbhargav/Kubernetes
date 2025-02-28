# EKS Cluster Upgrade: 1.22 to 1.23

## Prerequisites
1. **Upgrade `eksctl`**:
   - Ensure `eksctl` version **0.143.0 or greater** is installed before proceeding.
   - Reference: [Set up eksctl for Amazon EKS](https://docs.aws.amazon.com/eks/latest/userguide/install-eksctl.html)

2. **API & Feature Removals in Kubernetes 1.23**:
   - Kubernetes version 1.23 introduces multiple API deprecations and removals.
   - Reference: [Kubernetes 1.23: The Next Frontier](https://kubernetes.io/blog/2021/12/07/kubernetes-1-23-release-announcement/)

## Upgrade Steps
### 1. Upgrade the EKS Cluster
Execute the following commands to upgrade the EKS cluster:
```sh
eksctl upgrade cluster --name=eksgendev-1 --region us-east-1
eksctl upgrade cluster --name=eksgendev-1 --region us-east-1 --approve
```

### 2. Upgrade Node Groups
Run the command below to upgrade the node group:
```sh
eksctl upgrade nodegroup --name core-1675688423 --cluster eksgendev-1 --kubernetes-version=1.23 --region us-east-1
```

### 3. Upgrade `kube-proxy`
- Refer to the [Manage kube-proxy in Amazon EKS clusters](https://docs.aws.amazon.com/eks/latest/userguide/managing-kube-proxy.html) to find the correct image version for Kubernetes 1.23.
- Check the current version of `kube-proxy`:
```sh
kubectl get daemonset kube-proxy --namespace kube-system -o=jsonpath='{$.spec.template.spec.containers[:1].image}'
```
- Update `kube-proxy` to the appropriate version:
```sh
kubectl set image daemonset.apps/kube-proxy -n kube-system kube-proxy=602401143452.dkr.ecr.us-east-1.amazonaws.com/eks/kube-proxy:v1.23.16-eksbuild.2
```

### 4. Upgrade CoreDNS
- Refer to [Manage CoreDNS for DNS in Amazon EKS clusters](https://docs.aws.amazon.com/eks/latest/userguide/managing-coredns.html) for the correct CoreDNS image version.
- Check the current version of CoreDNS:
```sh
kubectl describe deployment coredns --namespace kube-system | grep Image | cut -d "/" -f 3
```
- Upgrade CoreDNS:
```sh
kubectl set image --namespace kube-system deployment.apps/coredns coredns=602401143452.dkr.ecr.us-east-1.amazonaws.com/eks/coredns:v1.8.7-eksbuild.5
```

### 5. Update AWS Node
Run the following command to update the AWS node component:
```sh
eksctl utils update-aws-node --cluster eksgendev-1 --approve --region us-east-1
```

### If `eksctl utils update-aws-node` Fails
If the `eksctl utils update-aws-node` command does not work, perform the following actions:

1. **Take a backup of the aws-node DaemonSet**:
   ```sh
   kubectl get daemonset aws-node -n kube-system -o yaml > aws-node_daemonset_bkp.yaml
   ```

2. **Steps to create a Managed Add-on**:
   
   a. Get the ServiceAccount (SA) for `aws-node`:
   ```sh
   kubectl get sa aws-node -n kube-system
   ```
   
   b. Take a backup of the ServiceAccount:
   ```sh
   kubectl get sa aws-node -n kube-system -o yaml > sa-aws-node-bkp.yaml
   ```
   
   c. Delete the ServiceAccount:
   ```sh
   kubectl delete sa aws-node -n kube-system
   ```
   
   d. Verify that the ServiceAccount has been deleted:
   ```sh
   kubectl get sa -n kube-system
   ```
   
   e. Create a managed add-on:
   ```sh
   eksctl create addon --cluster eksgenprod-1 --region us-east-1 --name vpc-cni --version v1.19.0-eksbuild.1 --force
   ```
   
   f. Verify that the add-on has been created:
   ```sh
   eksctl get addons --cluster eksgenprod-1 --region us-east-1
   ```
   
   g. Check if the equivalent ServiceAccount for `aws-node` has been created:
   ```sh
   kubectl get sa -n kube-system
   ```

## Post Upgrade Steps
### 1. Update `kubectl`
- Download and install the latest version of `kubectl`:
```sh
curl -O https://s3.us-west-2.amazonaws.com/amazon-eks/1.23.17/2023-05-11/bin/linux/amd64/kubectl
chmod +x ./kubectl
```
- Move `kubectl` to `/usr/local/bin`:
```sh
sudo cp ./kubectl /usr/local/bin/kubectl
kubectl version --short --client
```
- Reference: [Set up kubectl and eksctl - Amazon EKS](https://docs.aws.amazon.com/eks/latest/userguide/install-kubectl.html)

### 2. Configure `kubectl` (For First-time Installation)
If `kubectl` is being installed for the first time, configure it as follows:
```sh
mkdir -p $HOME/bin && cp ./kubectl $HOME/bin/kubectl && export PATH=$PATH:$HOME/bin
```
- Persist the changes by adding the following line to `~/.bashrc`:
```sh
echo 'export PATH=$PATH:$HOME/bin' >> ~/.bashrc
```

## Additional Notes
- Ensure to **log in as on the **bastion servers** (`dev`, `preprod`, `prod`) before performing the upgrade.
- Verify cluster functionality post-upgrade.

**End of Document**

