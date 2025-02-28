# Kubernetes High Availability Cluster Ansible Role

[![Ansible Galaxy](https://img.shields.io/badge/galaxy-salwan--mohamed.k8s__ha__cluster-blue.svg)](https://galaxy.ansible.com/salwan-mohamed/k8s_ha_cluster)
[![GitHub Actions](https://github.com/salwan-mohamed/ansible-role-k8s-ha-cluster/workflows/Ansible%20Lint/badge.svg)](https://github.com/salwan-mohamed/ansible-role-k8s-ha-cluster/actions)
[![License](https://img.shields.io/badge/license-MIT-brightgreen.svg)](LICENSE)

This Ansible role automates the deployment of a production-ready, high-availability Kubernetes cluster using kubeadm with the following components:

- 3 Control Plane Nodes (master01-03)
- 3 External etcd Nodes (etcd01-03)
- 2 HAProxy Load Balancers with Keepalived (haproxy01-02)
- 3 Worker Nodes (worker01-03)

## Architecture
![topology](https://github.com/user-attachments/assets/26bd95b0-cb91-4c69-a9c8-214e7add9192)

The architecture follows Kubernetes best practices for a production-grade deployment:
                       

Benefits of this architecture:

- **High availability**: No single point of failure in the control plane
- **External etcd**: Database separated from control plane for better stability
- **Load balancers**: HAProxy with Keepalived ensures continuous API access
- **Scalable**: Easily add more worker nodes as needed

## Requirements

- Ubuntu 20.04 (Focal) or 22.04 (Jammy) servers
- Ansible 2.9 or higher
- SSH access to all nodes with sudo privileges
- All servers must have static IP addresses
- Firewall rules allowing required ports (see [Networking Requirements](#networking-requirements))
- Minimum system requirements per node:
  - Control Plane/etcd: 2 CPUs, 4GB RAM, 50GB disk
  - Workers: 2 CPUs, 8GB RAM, 100GB disk
  - HAProxy: 1 CPU, 2GB RAM, 20GB disk

### Networking Requirements

The following ports must be open:

**HAProxy Nodes**
- TCP 6443 (Kubernetes API)
- VRRP (for Keepalived)

**Control Plane Nodes**
- TCP 6443 (Kubernetes API)
- TCP 2379-2380 (etcd)
- TCP 10250 (Kubelet API)
- TCP 10251 (kube-scheduler)
- TCP 10252 (kube-controller-manager)

**etcd Nodes**
- TCP 2379-2380 (etcd client & peer)

**Worker Nodes**
- TCP 10250 (Kubelet API)
- TCP 30000-32767 (NodePort Services)

## Role Variables

All variables are defined in `defaults/main.yml`. Here are the key variables you may want to customize:

| Variable | Description | Default |
|----------|-------------|---------|
| `kubernetes_version` | Kubernetes version to install | "1.30" |
| `kubernetes_cni_plugin_url` | CNI plugin installation URL | Calico URL |
| `pod_network_cidr` | CIDR for pod network | "192.168.0.0/16" |
| `service_network_cidr` | CIDR for service network | "10.96.0.0/12" |
| `virtual_ip` | Virtual IP for Kubernetes API | "10.1.5.10" |
| `virtual_ip_interface` | Network interface for virtual IP | "ens3" |
| `virtual_router_id` | VRRP router ID | 51 |
| `keepalived_auth_pass` | Keepalived authentication password | "K8SHA_KP" |
| `etcd_version` | Version of etcd to install | "v3.5.9" |
| `etcd_cluster_token` | etcd cluster token | "etcd-cluster-1" |
| `install_metrics_server` | Whether to install metrics-server | true |
| `create_admin_account` | Create admin service account | true |
| `install_helm` | Whether to install Helm | true |

See `defaults/main.yml` for a complete list of variables.

## Example Playbook

```yaml
---
- name: Deploy High-Availability Kubernetes Cluster
  hosts: all
  become: true
  gather_facts: true
  
  roles:
    - role: salwan-mohamed.k8s_ha_cluster
With custom variables:
---
- name: Deploy High-Availability Kubernetes Cluster
  hosts: all
  become: true
  gather_facts: true
  
  roles:
    - role: salwan-mohamed.k8s_ha_cluster
      vars:
        kubernetes_version: "1.29"
        virtual_ip: "192.168.1.100"
        virtual_ip_interface: "eth0"
        install_metrics_server: true
        install_helm: true
Inventory Example
Create an inventory file like this:
all:
  children:
    control_plane:
      hosts:
        master01:
          ansible_host: 10.1.5.2
        master02:
          ansible_host: 10.1.5.3
        master03:
          ansible_host: 10.1.5.4
    etcd:
      hosts:
        etcd01:
          ansible_host: 10.1.5.5
        etcd02:
          ansible_host: 10.1.5.6
        etcd03:
          ansible_host: 10.1.5.7
    haproxy:
      hosts:
        haproxy01:
          ansible_host: 10.1.5.8
        haproxy02:
          ansible_host: 10.1.5.9
    workers:ansible-galaxy install -r requirements.yml
      hosts:
        worker01:
          ansible_host: 10.1.5.11
        worker02:
          ansible_host: 10.1.5.12
        worker03:
          ansible_host: 10.1.5.13
  vars:
    ansible_user: ubuntu  # Change to your SSH user
    ansible_ssh_private_key_file: ~/.ssh/id_rsa  # Change to your key path
    ansible_become: true
Installation
Install from Ansible Galaxy
ansible-galaxy install salwan-mohamed.k8s_ha_cluster
Or add to your requirements.yml:
---
roles:
  - name: salwan-mohamed.k8s_ha_cluster
    version: v1.0.0

Then install:
ansible-galaxy install -r requirements.yml
Deploy the Cluster

Create your inventory file (see example above)
Create a playbook (see example above)
Run the playbook:

ansible-playbook -i inventory.yml playbook.yml

Verification
After deployment completes successfully:

SSH to one of the master nodes
Run kubectl get nodes to verify all nodes have joined and are Ready
Run kubectl get pods -A to verify system pods are Running

Contributing
Contributions are welcome! Please feel free to submit a Pull Request.
License
This project is licensed under the MIT License - see the LICENSE file for details.
Author Information
Salwan Mohamed - salwan.m.saeid@gmail.com
