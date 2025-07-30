# Helm Installation

To install Helm on your system, run the following commands:

```bash
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
```

These commands will:
1. Download the Helm installation script
2. Make the script executable
3. Run the installation script to install Helm 3

## Creating a Helm Chart

After installing Helm, you can create a new chart using the following commands:

```bash
# Navigate to your project directory
cd ~/kubes-hands-on

# Create a new Helm chart
helm create apache-helm

# Explore the chart structure
cd apache-helm
tree
```

The `helm create` command generates a complete chart structure with the following components:

```
.
├── Chart.yaml
├── charts
├── templates
│   ├── NOTES.txt
│   ├── _helpers.tpl
│   ├── deployment.yaml
│   ├── hpa.yaml
│   ├── ingress.yaml
│   ├── service.yaml
│   ├── serviceaccount.yaml
│   └── tests
│       └── test-connection.yaml
└── values.yaml
```

### Chart Components:
- **Chart.yaml**: Contains metadata about the chart
- **values.yaml**: Default configuration values
- **templates/**: Directory containing Kubernetes manifest templates
- **charts/**: Directory for chart dependencies
- **templates/tests/**: Test templates for the chart

## Helm Workflow

### 1. Customizing the Chart

```bash
# Navigate to the chart directory
cd apache-helm

# Edit service template
vim templates/service.yaml

# Edit values file
vim values.yaml
```

### 2. Packaging the Chart

```bash
# Package from within the chart directory
helm package .

# Or package from parent directory
cd ..
helm package apache-helm/
```

This creates a `.tgz` file (e.g., `apache-helm-0.1.0.tgz`)

### 3. Deploying to Different Environments

```bash
# Deploy to development environment
helm install dev-apache apache-helm -n dev-apache --create-namespace

# Deploy to production environment
helm install prod-apache apache-helm -n prod-apache --create-namespace

# Deploy using packaged chart file
helm install stage-apache apache-helm-0.1.0.tgz -n stage-apache --create-namespace
```

### 4. Verifying Deployments

```bash
# Check pods in different namespaces
kubectl get pods -n dev-apache
kubectl get pods -n prod-apache
kubectl get pods -n stage-apache

# List Helm releases
helm list -A
```

### 5. Accessing the Application

After successful deployment, you can access the application using port-forwarding:

```bash
# Get pod name and port-forward
export POD_NAME=$(kubectl get pods --namespace <namespace> -l "app.kubernetes.io/name=apache-helm,app.kubernetes.io/instance=<release-name>" -o jsonpath="{.items[0].metadata.name}")
export CONTAINER_PORT=$(kubectl get pod --namespace <namespace> $POD_NAME -o jsonpath="{.spec.containers[0].ports[0].containerPort}")
kubectl --namespace <namespace> port-forward $POD_NAME 8080:$CONTAINER_PORT
```

Then visit `http://127.0.0.1:8080` to access your application.
