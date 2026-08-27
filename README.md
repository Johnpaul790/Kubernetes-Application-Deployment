# Deploying MongoDB and Mongo Express on Kubernetes

This project is a simple Kubernetes demo that deploys MongoDB and Mongo Express. It is intended for learning how Kubernetes resources such as Deployments, ConfigMaps, and Secrets work together to run a small application.

## Architecture

The application consists of:

- **MongoDB** — the database.
- **Mongo Express** — a browser-based interface for managing MongoDB.
- **ConfigMap** — stores non-sensitive configuration such as the MongoDB service address.
- **Secret** — stores the MongoDB username and password.

## Project Structure

```text
.
├── mongo.yaml             # MongoDB Deployment
├── mongo-express.yaml     # Mongo Express Deployment
├── mongo-configmap.yaml   # MongoDB connection configuration
├── mongo-secret.yaml      # MongoDB credentials
└── README.md
```

## Prerequisites

Before using this project, install:

- [Docker](https://docs.docker.com/get-docker/)
- [kubectl](https://kubernetes.io/docs/tasks/tools/)
- A Kubernetes cluster, such as [Minikube](https://minikube.sigs.k8s.io/docs/start/), Docker Desktop Kubernetes, or Kind

Confirm that the cluster is available:

```bash
kubectl cluster-info
kubectl get nodes
```

## Current Project Status

The manifests provide the initial resource definitions, but the application is not fully deployable yet. Before deployment, complete the following:

1. Add Base64-encoded credentials to `mongo-secret.yaml`.
2. Add the MongoDB service address to `mongo-configmap.yaml`.
3. Pass the Secret values to the MongoDB and Mongo Express containers as environment variables.
4. Pass the ConfigMap value to Mongo Express.
5. Add a ClusterIP Service for MongoDB.
6. Add a Service for Mongo Express so it can be accessed from a browser.

## Configure the Secret

Kubernetes Secret values in the `data` field must be Base64 encoded. Generate example values with:

```bash
printf 'admin' | base64
printf 'password' | base64
```

Copy the output into `mongo-secret.yaml`:

```yaml
data:
  username: <base64-encoded-username>
  password: <base64-encoded-password>
```

> Base64 encoding is not encryption. Do not commit real production credentials to source control.

## Deploy the Resources

After completing the manifests, apply the configuration in dependency order:

```bash
kubectl apply -f mongo-secret.yaml
kubectl apply -f mongo-configmap.yaml
kubectl apply -f mongo.yaml
kubectl apply -f mongo-express.yaml
```

Alternatively, apply every manifest in the directory:

```bash
kubectl apply -f .
```

## Verify the Deployment

Check that the Kubernetes resources were created successfully:

```bash
kubectl get deployments
kubectl get pods
kubectl get services
kubectl get configmaps
kubectl get secrets
```

For more detail about a pod or deployment, run:

```bash
kubectl describe pod <pod-name>
kubectl logs <pod-name>
```

## Access Mongo Express

Once a Service has been added for Mongo Express, access it according to the Service type. With Minikube and a NodePort Service, run:

```bash
minikube service mongo-express-service
```

If the Service is internal, use port forwarding:

```bash
kubectl port-forward deployment/mongo-express 8081:8081
```

Then open [http://localhost:8081](http://localhost:8081).

## Remove the Resources

Delete all resources defined by the manifests:

```bash
kubectl delete -f .
```

## Learning Objectives

This demo covers:

- Creating Kubernetes Deployments
- Separating configuration from application definitions
- Supplying credentials with Kubernetes Secrets
- Connecting applications through Kubernetes Services
- Inspecting and troubleshooting workloads with `kubectl`
