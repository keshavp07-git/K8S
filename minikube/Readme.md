```markdown
To install Chocolatey using PowerShell, run the following command as an administrator:

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force; `
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; `
iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
```
```install minikube using Chocolatey
To install Minikube using Chocolatey, run the following command in an elevated PowerShell session
```powershell
choco install minikube
```
```markdown
This command will download and install the latest version of Minikube available in the Chocolatey repository
and set it up on your system. After the installation is complete, you can verify the installation by running:

```powershell       
    minikube start --driver=virtualbox --no-vtx-check # This command starts Minikube with VirtualBox as the driver , And the --no-vtx-check flag is used to skip the VT-x check.
```
```markdown
To create a Kubernetes deployment named `hello-node` using the `k8s.gcr` image, run the following command:

```powershell
kubectl create deployment hello-node --image=k8s.gcr.io/echoserver:1.10
```
```

This command will create a deployment that runs the `k8s.gcr` container with the specified arguments.
```
```markdown
To check the status of your deployment and cluster, you can use the following commands:

```powershell
kubectl get deployments
```
This command lists all deployments in the current namespace, showing their status and number of replicas.

```powershell
kubectl get pods
```
This command lists all pods, allowing you to see if your application pods are running as expected.

```powershell
kubectl get nodes
```
This command displays all nodes in your Kubernetes cluster, showing their status and roles.

You can also inspect your Kubernetes configuration file using:

```powershell
cat $HOME\.kube\config
```
This command outputs the contents of your kubeconfig file, which contains cluster connection details and credentials , containing information about clusters, users, and contexts.
```
```markdown
To expose your deployment and interact with your application, use the following commands:

```powershell
kubectl expose deployment hello-node --type=LoadBalancer --port=8080
```
This command creates a service of type `LoadBalancer` for the `hello-node` deployment, exposing it on port 8080. This allows external access to your application.

```powershell
kubectl get services
```
Lists all services in your cluster, showing their types, cluster IPs, external IPs, and ports.

```powershell
minikube service hello-node
```
Opens your default web browser to access the `hello-node` service via Minikube's service tunnel, making it easy to test your application locally.

```powershell
kubectl get events
```
Displays recent events in your cluster, which can help you troubleshoot issues by showing resource changes and errors.

```powershell
kubectl config view
```
Shows the current Kubernetes configuration, including clusters, users, and contexts, helping you verify or debug your kubeconfig setup.

```powershell
kubectl logs hello-node-65854f4947-fb6r6
```
Retrieves the logs from the specified pod, which is useful for debugging application behavior or errors.
```
```markdown
To manage and clean up your services and deployments, use the following commands:

```powershell
kubectl get svc
```
Lists all services in your cluster, providing details such as name, type, cluster IP, external IP, and ports.

```powershell
kubectl delete svc hello-node
```
Deletes the `hello-node` service, removing its external access point.

```powershell
kubectl delete deploy hello-node
```
Deletes the `hello-node` deployment, terminating all associated pods and resources.
```