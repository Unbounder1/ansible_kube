# Ansible Kubernetes Deployment Playbooks

This repository contains Ansible playbooks for automating the setup and management of a Kubernetes cluster. These playbooks streamline cluster configuration, node setup, and other Kubernetes-related tasks.

## Key Features
- Automates Kubernetes node configuration
- Includes role-based playbooks for easy customization
- Simplifies deployment of cluster components
- Currently only supports Debian & Red-Hat distros
- Jenkinsfile CI/CD
- No SELINUX control (requires subscription management)
## Usage

1. Clone the repository:
   ```bash
   git clone https://github.com/Unbounder1/ansible_kube.git
   cd ansible_kube
2.	Update ansible.cfg and inventory for your environment.
3.	Run the playbook:
    ```bash
    ansible-playbook apply.yml
    ```

## Structure

•	roles/kubernetes_node: Configuration for setting up nodes.
•	apply.yml: Main playbook for applying configurations.