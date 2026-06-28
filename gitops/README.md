# GitOps

Argo CD should watch the Kubernetes manifests in `k8s/` and keep the acer-aio kubeadm cluster in sync with Git.

Apply this once from a machine that can reach the cluster and has Argo CD installed:

```bash
kubectl apply -f gitops/argocd/web-service-application.yaml
```

Normal delivery flow:

1. Push application code to `main`.
2. GitHub Actions builds and pushes `ghcr.io/ktcloud4-acer/web-service-api:<commit-sha>`.
3. The workflow updates `k8s/kustomization.yaml` with that image tag and pushes the manifest change.
4. Argo CD sees the Git change and syncs the `web-service` namespace.

The service is exposed through Nginx NodePort `30080`, which matches the acer-aio OpenStack security group NodePort range.

If CI/CD is unavailable, use `scripts/web-service-emergency.rc` from a shell with Docker, registry access, and kubectl access to `/home/ubuntu/.kube/acer-kubeadm.yaml`.
