# wordpress-stack-with-proxy

This in-class activity will assess your understanding on containerized application deployed into a Kubernetes Cluster. You will deploy a Wordpress stack along with a Proxy sever in a Kubernetes cluster. So, there will be 3 components you will deploy as part of this stack:

1) An nginx reverse proxy
2) A Wordpress application
3) A mariaDB database

For each component, you will create required Kubernetes resources to work them together. The flow is: **Internet -> Nginx-proxy -> Wordpress app -> Database.** You may use a local Kubernetes cluster or a cluster in any Cloud provider.

Follow the instruction to complete this activity:

- Each component must be deployed into it's own namespace
- You will expose Nginx proxy to external only. Use any port of your choice. Rest of them (Wordpress and Database) should NOT be exposed to public
- Create Dockerfile for a component, if you need to create an image
- Create required K8s manifests for each component and use them to deploy into your K8s cluster
- Organize the manifests, Dockerfiles, etc. into 3 different folders by their component name
- Create a GitHub repository and upload all the files into the repository
- As part of the submission, just upload a doc file having the following:
- snapshots to proof your work
- commands you used
- Github repository URL


GCP implementation for this activity as follows:

For the first time you should run	
```commandline
gcloud init
gcloud services enable container.googleapis.com
```

Set Up Google Cloud CLI
```commandline
gcloud auth login
gcloud config set project containerization-429715
```

Create a GKE Cluster
```commandline
gcloud container clusters create my-cluster --zone us-central1-a --num-nodes=1
```

Get Authentication Credentials for the Cluster
```commandline
gcloud container clusters get-credentials my-cluster --zone us-central1-a
```

Docker Authentication
```commandline
gcloud auth configure-docker
```

Tag and Push Docker Image to GCR
```commandline
docker build -t gcr.io/containerization-429715/mariadb:latest -f db/Dockerfile ./db
docker push gcr.io/containerization-429715/mariadb:latest
	
docker build -t gcr.io/containerization-429715/wordpress:latest -f wp/Dockerfile ./wp
docker push gcr.io/containerization-429715/wordpress:latest
	
docker build -t gcr.io/containerization-429715/nginx:latest -f nginx/Dockerfile ./nginx
docker push gcr.io/containerization-429715/nginx:latest
```

Namespace creation
```commandline
kubectl apply -f namespaces.yaml
```

Deploy ConfigMaps and Secrets
```commandline
kubectl apply -f db/mariadb-configmap.yaml
kubectl get configmap -n database
kubectl apply -f db/mariadb-secret.yaml
kubectl get secret -n database

kubectl apply -f wp/wordpress-configmap.yaml
kubectl get configmap -n wordpress
kubectl apply -f wp/wordpress-secret.yaml
kubectl get secret -n wordpress
```

Deploy MariaDB
```commandline
kubectl apply -f db/mariadb-deployment.yaml
kubectl get deployment -n database
kubectl get pods -n database

kubectl apply -f db/mariadb-service.yaml
kubectl get service -n database
```

Deploy WordPress
```commandline
kubectl apply -f wp/wordpress-deployment.yaml
kubectl get deployment -n wordpress
kubectl get pods -n wordpress

kubectl apply -f wp/wordpress-service.yaml
kubectl get service -n wordpress
```

Deploy Nginx
```commandline
kubectl apply -f nginx/nginx-deployment.yaml
kubectl get deployment -n nginx
kubectl get pods -n nginx
	
kubectl apply -f nginx/nginx-service.yaml
kubectl get service -n nginx
```
	
Delete all resource
```commandline
kubectl delete all --all -n database
kubectl delete all --all -n wordpress
kubectl delete all --all -n nginx
kubectl delete -f namespaces.yaml
```
