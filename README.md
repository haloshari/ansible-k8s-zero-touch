# Install Kubernetes with Zero-Touch

A modular, automated, and "Zero-Touch" deployment framework for building a Kubernetes cluster using `kubeadm`. This project eliminates manual configuration by using variable-driven Ansible roles, allowing for scalable and repeatable cluster deployments.

## 🚀 Overview
This project uses an **imperative-to-declarative flow** to automate Kubernetes deployment:
1. **Preparation Phase:** Configures OS requirements (Swap, Kernel modules, Containerd runtime).
2. **Control Plane Phase:** Initializes the Master node using `kubeadm init`.
3. **Worker Join Phase:** Automatically retrieves join tokens and registers Worker nodes to the cluster.

## 🏗️ Project Structure
```text
.
├── roles/               # Functional Ansible roles (Prereqs, K8s, Network)
├── inventory.sample.yml # Inventory template for cluster nodes
├── config.sample.yml    # Configuration template for global settings
├── main.yml             # Main playbook entry point
└── README.md            # Documentation
```
🛠️ Quick Start
1. Prerequisites
Control node with Ansible installed.

SSH key-based access is configured for all target nodes.

2. Configuration
Copy the sample files to create your active configuration:
```
cp inventory.sample.yml inventory.ini
cp config.sample.yml config.yml
```
3. Execution
Run the playbook to deploy your cluster:
```
ansible-playbook -i inventory.yml main.yml
```
💡 How it works (kubeadm process)
Idempotent Preparation: Ensures OS-level dependencies (containerd, sysctl settings) are consistent across all nodes.

Control Plane Bootstrap: The Master node initializes the API server, controller manager, and scheduler, generating secure certificates.

Dynamic Join: The playbook automatically captures the join token from the master node and injects it into the worker nodes for a seamless "Zero-Touch" experience.

🔮 Future Roadmap
[ ] Integrate Ceph storage cluster for persistent storage.

[ ] Add automated health checks post-deployment.

[ ] Implement High Availability (HA) master support.

🛡️ License
This project is licensed under the MIT License. See the LICENSE file for details.

© 2026 [Hassan Aloshari]
