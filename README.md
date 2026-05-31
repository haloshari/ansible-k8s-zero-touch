# Ansible Kubernetes Zero-Touch

[![Ansible](https://img.shields.io/badge/ansible-%23EE0000.svg?style=flat&logo=ansible&logoColor=white)](https://www.ansible.com/)
[![Kubernetes](https://img.shields.io/badge/kubernetes-%23326ce5.svg?style=flat&logo=kubernetes&logoColor=white)](https://kubernetes.io/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](./LICENSE)
[![GitHub Release](https://img.shields.io/github/v/release/haloshari/ansible-k8s-zero-touch?style=flat)](https://github.com/haloshari/ansible-k8s-zero-touch/releases)

A production-ready, modular Ansible automation framework for deploying highly available Kubernetes clusters using `kubeadm`. This project eliminates manual configuration through variable-driven Ansible roles, enabling a truly "Zero-Touch" deployment experience.

## 🎯 Features

- ✅ **Fully Automated** - Minimal manual intervention required
- ✅ **Idempotent** - Safe to run multiple times
- ✅ **Modular Design** - Easy to customize and extend
- ✅ **Production Ready** - Best practices for security and reliability
- ✅ **Multi-Node Support** - Scale from 1 control plane to multiple workers
- ✅ **Container Runtime** - Built-in Containerd configuration
- ✅ **Network Plugin Ready** - Compatible with CNI plugins (Flannel, Calico, Cilium, etc.)

## 📋 Prerequisites

### System Requirements

- **Control Node:** Linux machine with Ansible 2.9+ installed
- **Target Nodes:** Ubuntu 20.04 LTS or later (or compatible Linux distributions)
- **Network:** SSH key-based access to all target nodes with sudo privileges
- **Specifications:** Minimum 2 CPU cores, 2GB RAM per node (recommended 4GB)

### Software Dependencies

```bash
# On control node
ansible >= 2.9
python >= 3.6
```

### Network Requirements

- All nodes must be on the same network or have routing configured
- SSH port (22) accessible between control node and target nodes
- Pod network range configurable (default: 10.244.0.0/16 for Flannel)

## 🚀 Quick Start

### 1. Clone the Repository

```bash
git clone https://github.com/haloshari/ansible-k8s-zero-touch.git
cd ansible-k8s-zero-touch
```

### 2. Configure Your Cluster

Copy sample files and customize for your environment:

```bash
cp inventory.sample.yml inventory.yml
cp config.sample.yml config.yml
```

Edit `inventory.yml` with your node hostnames/IPs:

```yaml
all:
  children:
    master:
      hosts:
        k8s-master:
          ansible_host: 192.168.1.10
    workers:
      hosts:
        k8s-worker-1:
          ansible_host: 192.168.1.11
        k8s-worker-2:
          ansible_host: 192.168.1.12
```

Edit `config.yml` with your cluster settings:

```yaml
---
kubernetes:
  version: "1.28.0"
  cluster_name: "my-cluster"
  pod_network_cidr: "10.244.0.0/16"
  
container_runtime:
  type: "containerd"
  version: "1.7.0"
```

### 3. Deploy the Cluster

```bash
ansible-playbook -i inventory.yml main.yml
```

Monitor the deployment progress. Successful execution will show the control plane and worker nodes joining the cluster.

### 4. Verify Installation

```bash
# On the master node
kubectl get nodes
kubectl get pods -A
```

## 🏗️ Project Structure

```
ansible-k8s-zero-touch/
├── roles/                          # Ansible role directory
│   ├── prereq/                     # OS prerequisites (swap, kernel modules, etc.)
│   ├── container_runtime/          # Containerd installation and configuration
│   ├── kubernetes/                 # Kubernetes components installation
│   └── network/                    # CNI plugin configuration
├── inventory.sample.yml            # Inventory template
├── config.sample.yml               # Configuration template
├── main.yml                        # Main playbook entry point
├── group_vars/                     # Group-level variables
├── host_vars/                      # Host-level variables
├── CONTRIBUTING.md                 # Contribution guidelines
├── LICENSE                         # MIT License
└── README.md                       # This file
```

## 📖 How It Works

### Deployment Flow

```
1. Preparation Phase (All Nodes)
   ├── Disable swap
   ├── Load kernel modules (overlay, br_netfilter)
   ├── Configure sysctl settings
   ├── Install container runtime (Containerd)
   └── Install Kubernetes tools (kubeadm, kubelet, kubectl)

2. Control Plane Phase (Master Node)
   ├── Initialize cluster with kubeadm
   ├── Generate certificates
   ├── Deploy API server, controller manager, scheduler
   ├── Setup kubeconfig for kubectl
   └── Extract join token for worker nodes

3. Worker Join Phase (Worker Nodes)
   ├── Retrieve join token from master
   ├── Execute kubeadm join command
   ├── Kubelet starts and registers with control plane
   └── Node becomes ready for workload scheduling
```

### Key Design Principles

- **Idempotent:** Safe to run multiple times without side effects
- **Declarative:** Configuration-driven approach
- **Modular:** Each role handles a specific aspect of setup
- **Secure:** Best practices for RBAC and network policies

## 🔧 Configuration

### Main Configuration (config.yml)

| Variable | Purpose | Default |
|----------|---------|---------|
| `kubernetes.version` | Kubernetes version to deploy | `1.28.0` |
| `kubernetes.cluster_name` | Cluster identifier | `k8s-cluster` |
| `kubernetes.pod_network_cidr` | Pod network CIDR range | `10.244.0.0/16` |
| `container_runtime.type` | Container runtime | `containerd` |
| `container_runtime.version` | Runtime version | `1.7.0` |

### Inventory Variables

```yaml
master:
  vars:
    ansible_user: ubuntu
    ansible_become: yes
    ansible_become_method: sudo

workers:
  vars:
    ansible_user: ubuntu
    ansible_become: yes
```

## 🐛 Troubleshooting

### Issue: Nodes stuck in NotReady state

**Solution:** Check kubelet logs and ensure network plugin is installed:

```bash
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```

### Issue: SSH connection timeouts

**Solution:** Verify SSH access and key configuration:

```bash
ssh -i your-key.pem ubuntu@node-ip "sudo whoami"
```

### Issue: Swap not disabled

**Solution:** Manually disable swap on nodes:

```bash
sudo swapoff -a
sudo sed -i '/ swap / s/^/#/' /etc/fstab
```

## 🗺️ Roadmap

- [ ] High Availability (HA) control plane support
- [ ] Automated backup and recovery
- [ ] Ceph storage cluster integration
- [ ] Post-deployment health checks
- [ ] Prometheus monitoring stack
- [ ] Istio service mesh integration
- [ ] GitOps workflow support (FluxCD)

## 🤝 Contributing

Contributions are welcome! Please read [CONTRIBUTING.md](./CONTRIBUTING.md) for details on our code of conduct and the process for submitting pull requests.

### Quick Contributing Guide

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Make your changes
4. Test with your Kubernetes cluster
5. Commit your changes (`git commit -m 'Add amazing feature'`)
6. Push to the branch (`git push origin feature/amazing-feature`)
7. Open a Pull Request

## 📝 License

This project is licensed under the MIT License - see the [LICENSE](./LICENSE) file for details.

## 👤 Author

**Hassan Aloshari**

- GitHub: [@haloshari](https://github.com/haloshari)

## 🙏 Acknowledgments

- [Kubernetes Official Documentation](https://kubernetes.io/docs/)
- [Ansible Documentation](https://docs.ansible.com/)
- [kubeadm Documentation](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/)

## 📞 Support

For issues, questions, or suggestions:

1. Check the [Troubleshooting](#-troubleshooting) section
2. Search [existing GitHub issues](https://github.com/haloshari/ansible-k8s-zero-touch/issues)
3. Create a [new issue](https://github.com/haloshari/ansible-k8s-zero-touch/issues/new) with detailed information

---

**Last Updated:** May 31, 2026  
**Maintained By:** Hassan Aloshari
