# airflow-gke

## Preparation

- Create the GKE cluster
```sh
gcloud container clusters create airflow-pool
```

- Download the GKE credentials:
```sh
gcloud container clusters get-credentials <cluster_name>
```

- Apply GKE credentials as `kubectl` [context](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/#context):
  - List available contexts:
  ```sh
  kubectl config get-contexts
  ```
  - Set context:
  ```sh
  kubectl config use-context <context>
  ```

- Reserve a GCP [static ip](https://cloud.google.com/compute/docs/ip-addresses/reserve-static-external-ip-address):
```sh
gcloud compute addresses create airflow-ip --global
```

- Deploy MySQL to the cluster
```sh
kubectl create \
    -f mysql-pv.yaml \
    -f mysql-deployment.yaml \
    -f mysql-service.yaml
```

- Deploy airflow to the cluster
```sh
kubectl create \
    -f airflow-deployment.yaml \
    -f airflow-service.yaml \
    -f ingress.yaml
```

- [Install Helm](https://github.com/ahmetb/gke-letsencrypt/blob/master/10-install-helm.md)

- [Install cert-manager](https://github.com/ahmetb/gke-letsencrypt/blob/master/20-install-cert-manager.md)

```
helm install --no-crd-hook --name cert-manager --version v0.4.1 \
    --namespace kube-system stable/cert-manager
```

_The latest version is v 0.6.2, but it needs to be v 0.4.1_

- [Set up Let‘s Encrypt](https://github.com/ahmetb/gke-letsencrypt/blob/master/30-setup-letsencrypt.md)

- Get a [certificate](https://github.com/ahmetb/gke-letsencrypt/blob/master/50-get-a-certificate.md)
```
kubectl create -f certificate.yaml
```

- Apply TLS to Ingress
```
kubectl replace --force -f ingress-tls.yaml
```
