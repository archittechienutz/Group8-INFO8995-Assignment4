## Jenkins CI/CD on Kubernetes with GitHub, Gitea, and ngrok
## Overview
## This project demonstrates how to install Jenkins on a Kubernetes cluster using Ansible and Helm, configure Jenkins pipelines integrated with GitHub and Gitea repositories, and expose Jenkins UI using ngrok or Traefik with Cloudflare ingress.
## Prerequisites
- Kubernetes cluster up and running (Minikube, k3s, or vSphere VMs)
- `kubectl` configured and connected to your cluster
- `helm` installed and configured
- `ansible` installed with `kubernetes.core` collection
- Access to GitHub and Gitea accounts
- ngrok account (for exposing Jenkins UI)
- Azure CLI and Azure Function Core Tools (if deploying Azure Functions)
Setup Steps
##1. Kubernetes cluster verification
Check Kubernetes is accessible:

```bash
kubectl get nodes
```

If you see connection errors, ensure your cluster is running and kubeconfig is properly set.
## 2. Install Jenkins on Kubernetes
Edit `jenkins-values.yaml` to configure Jenkins admin credentials:

```yaml
controller:
  admin:
    username: admin
    password: admin123
```

Run Ansible playbook to install Jenkins and Traefik (optional):

```bash
ansible-playbook up.yml
```

To uninstall Jenkins:

```bash
ansible-playbook down.yml
```
## 3. Expose Jenkins
Using ngrok
- Install ngrok:

```bash
npm install -g ngrok
```

- Authenticate with your ngrok authtoken:

```bash
ngrok config add-authtoken YOUR_NGROK_AUTH_TOKEN
```

- Start tunnel to Jenkins service port (e.g., 8080):

```bash
kubectl port-forward svc/jenkins 8080:8080
ngrok http 8080
```

Copy the forwarding URL from ngrok to access Jenkins UI externally.
Using Traefik and Cloudflare Ingress
- Configure Traefik ingress controller in your cluster.
- Create an Ingress resource for Jenkins.
- Set Cloudflare DNS to point to your Traefik ingress IP.
## 4. Jenkins Pipeline Setup with GitHub
- Fork the GitHub repo containing the Jenkinsfile.
- In Jenkins UI, create a new Pipeline project.
- Set SCM to Git and provide your repo URL.
- Configure credentials if needed.
- Enable webhook in GitHub to trigger builds on push.
## 5. Jenkins Pipeline Setup with Gitea
- Fork or create a repo in your Gitea server with the Jenkinsfile.
- Create a Jenkins Pipeline project pointing to the Gitea repo.
- Configure Jenkins Gitea plugin for webhooks.
- Use your cluster IP or exposed Jenkins URL to receive webhook events.
##6. Deploying Azure Functions (Optional)
- Edit Jenkinsfile to include Azure CLI commands for deployment.
- Ensure Azure Service Principal credentials are stored in Jenkins.
- The Jenkins pipeline stages typically include:

  - Build: `npm install` and `func start`
  - Test: run tests if any
  - Deploy: `func azure functionapp publish <app-name> --force`
Troubleshooting
- `kubectl` connection refused → Check your kubeconfig and cluster status.
- Helm install errors about `controller.adminUser` → Update `jenkins-values.yaml` keys to `controller.admin.username` and `controller.admin.password`.
- Helm secret errors → Ensure `existingSecret` is removed or properly created.
- ngrok `command not found` → Install ngrok and authenticate.
- ngrok auth token errors → Use the correct token from your ngrok dashboard (not `cr_` prefixed API tokens).
## References
- Jenkins Helm Chart: https://charts.jenkins.io/
- Ansible Kubernetes Collection: https://docs.ansible.com/ansible/latest/collections/kubernetes/core/
- ngrok Documentation: https://ngrok.com/docs
- Kubernetes Documentation: https://kubernetes.io/docs/
- Azure Functions Core Tools: https://learn.microsoft.com/en-us/azure/azure-functions/functions-core-tools
Submission
Please submit links to:

1. This repository containing `up.yml` and `down.yml`
2. Your GitHub repository with Jenkinsfile and pipeline setup
3. Your Gitea repository with Jenkinsfile and pipeline setup