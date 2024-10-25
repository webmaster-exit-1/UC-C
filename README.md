## Overview
Welcome to the Command and Control Center (C2C) for the Ukrainian IT military. This project aims to provide a centralized command and control system for coordinating distributed cyber attacks against the enemy.

## Prerequisites
Before proceeding, ensure you have the following prerequisites:

- A Kubernetes cluster set up and accessible.
- `kubectl` installed and configured to interact with the Kubernetes cluster.
- Necessary permissions to create and manage resources in the Kubernetes cluster.

## Deployment
Follow the steps below to deploy the C2C and attack machines.

### Step 1: Create a Kubernetes Namespace
Create a dedicated namespace for the C2C and attack machines to isolate resources and configurations.

```bash
kubectl create namespace <namespace_name>
```

### Step 2: Deploy the Command and Control Center
Create a Kubernetes deployment manifest file (`command-center-deployment.yaml`) for the C2C, specifying the container image, replicas, and resource limits.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: command-center
  namespace: <namespace_name>
spec:
  replicas: 1
  selector:
    matchLabels:
      app: command-center
  template:
    metadata:
      labels:
        app: command-center
    spec:
      containers:
      - name: command-center
        image: <command-center-image>
        ports:
        - containerPort: 80
        env:
        - name: COMMAND_CENTER_CONFIG
          value: <command-center-config>
```

Apply the deployment manifest to the Kubernetes cluster.

```bash
kubectl apply -f command-center-deployment.yaml -n <namespace_name>
```

### Step 3: Expose the Command and Control Center
Create a Kubernetes service manifest file (`command-center-service.yaml`) to expose the C2C externally or internally.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: command-center
  namespace: <namespace_name>
spec:
  selector:
    app: command-center
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
  type: LoadBalancer
```

Apply the service manifest to the Kubernetes cluster.

```bash
kubectl apply -f command-center-service.yaml -n <namespace_name>
```

### Step 4: Deploy the Attack Machines
Create a Kubernetes deployment manifest file (`attack-machine-deployment.yaml`) for the attack machines, specifying the container image, replicas, and resource limits.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: attack-machine
  namespace: <namespace_name>
spec:
  replicas: 3
  selector:
    matchLabels:
      app: attack-machine
  template:
    metadata:
      labels:
        app: attack-machine
    spec:
      containers:
      - name: attack-machine
        image: <attack-machine-image>
        ports:
        - containerPort: 80
        resources:
          limits:
            memory: "512Mi"
            cpu: "500m"
          requests:
            memory: "256Mi"
            cpu: "250m"
        env:
        - name: ATTACK_MACHINE_CONFIG
          value: <attack-machine-config>
```

Apply the deployment manifest to the Kubernetes cluster.

```bash
kubectl apply -f attack-machine-deployment.yaml -n <namespace_name>
```

### Step 5: Configure Attack Machine Communication
Set up the necessary configurations for the attack machines to communicate with the C2C.

1. Configure environment variables in the attack machine deployment manifest file (`attack-machine-deployment.yaml`) to provide necessary information for communication with the C2C.

2. Create network policies to control the communication between the attack machines and the C2C. Use the `NetworkPolicy` resource in Kubernetes to define rules for allowing or denying traffic between pods based on labels or IP addresses.

3. Use Kubernetes services to enable service discovery between the attack machines and the C2C. Create a service for the C2C (`command-center-service.yaml`) and ensure that the attack machines can resolve the service name to the correct IP address.

4. Update the code of the attack machines to use the configured environment variables, network policies, and service discovery mechanisms for communication with the C2C.

## Monitoring and Management
Use Kubernetes tools like `kubectl` or a web-based dashboard (e.g., Kubernetes Dashboard) to monitor the status of the C2C, attack machines, and other resources. Manage the cluster by scaling deployments, updating configurations, or troubleshooting issues.

---
