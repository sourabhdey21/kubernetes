# Kubernetes Ansible Playbook 🚀

Automate your Kubernetes cluster setup with this Ansible playbook!

## 🛠 Prerequisites

Make sure you have the following installed:

- Ansible 
- Ubuntu 22.04 Nodes (Master and Worker Nodes)

## 🚀 Quick Start

1. Clone the repository:
   ```
   git clone https://github.com/sourabhdey21/kubernetes.git
   cd kubernetes/Play/Playbook
   ansible-playbook -i hosts.ini dependencies.yaml
   ansible-playbook -i hosts.ini control-plane.yaml
   ansible-playbook -i hosts.ini worker.yaml
   ```

 2. Access the Cluster:

   ![linkedin-k8s](https://github.com/sourabhdey21/kubernetes/assets/98477908/3d7651cc-f8b7-439b-95f0-750d0cf23962)
   
