# Ansible Kubernetes Zero-Touch

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
