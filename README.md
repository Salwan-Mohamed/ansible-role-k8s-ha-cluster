# Kubernetes High Availability Cluster Ansible Role

[![Ansible Galaxy](https://img.shields.io/badge/galaxysalwan-mohamed.k8s_ha_cluster-blue.svg)](https://galaxy.ansible.com/salwan-mohamed/k8s_ha_cluster)
[![GitHub Actions](https://github.com/salwan-mohamed/ansible-role-k8s-ha-cluster/workflows/Ansible%20Lint/badge.svg)](https://github.com/salwan-mohamed/ansible-role-k8s-ha-cluster/actions)
[![License](https://img.shields.io/badge/license-MIT-brightgreen.svg)](LICENSE)

This Ansible role automates the deployment of a production-ready, high-availability Kubernetes cluster using kubeadm with the following components:

- 3 Control Plane Nodes (master01-03)
- 3 External etcd Nodes (etcd01-03)
- 2 HAProxy Load Balancers with Keepalived (haproxy01-02)
- 3 Worker Nodes (worker01-03)

## Architecture

The architecture follows Kubernetes best practices for a production-grade deployment:

                           ┌────────────┐
                           │   Client   │
                           └─────┬──────┘
                                 │
                                 ▼
                            (10.1.5.10)
                          Virtual IP (VIP)
                                 │
                     ┌───────────┴──────────┐
                     │                      │
               ┌─────▼─────┐          ┌─────▼─────┐
               │  HAProxy1 │          │  HAProxy2 │
               └─────┬─────┘          └─────┬─────┘
                     │                      │
  ┌──────────────────┼──────────────────────┼──────────────────────┐
  │                  │                      │                      │
 ┌─▼───────────┐ ┌────▼────────┐ ┌───────────▼──┐                  │
 │  Master01   │ │   Master02  │ │  Master03   │                  │
 └─┬───────────┘ └─┬───────────┘ └─┬────────────┘                  │
   │               │               │                               │
   │  Control Plane Nodes          │                               │
   └───────────────┼───────────────┘                               │
                   │                                               │
      ┌────────────┼──────────────┬──────────────────┐             │
      │            │              │                  │             │
 ┌────▼─────┐ ┌────▼─────┐ ┌──────▼─────┐       ┌────▼─────┐ ┌─────▼────┐ ┌─────▼────┐
 │  etcd01  │ │  etcd02  │ │   etcd03   │       │ Worker01 │ │ Worker02 │ │ Worker03 │
 └──────────┘ └──────────┘ └────────────┘       └──────────┘ └──────────┘ └──────────┘

    External etcd Cluster                          Worker Nodes
Benefits of this architecture:

High availability: No single point of failure in the control plane
External etcd: Database separated from control plane for better stability
Load balancers: HAProxy with Keepalived ensures continuous API access
Scalable: Easily add more worker nodes as needed
Requirements

Ubuntu 20.04 (Focal) or 22.04 (Jammy) servers
Ansible 2.9 or higher
SSH access to all nodes with sudo privileges
All servers must have static IP addresses
Firewall rules allowing required ports (see Networking Requirements)
Minimum system requirements per node:

Control Plane/etcd: 2 CPUs, 4GB RAM, 50GB disk
Workers: 2 CPUs, 8GB RAM, 100GB disk
HAProxy: 1 CPU, 2GB RAM, 20GB disk



Networking Requirements
The following ports must be open:
HAProxy Nodes

TCP 6443 (Kubernetes API)
VRRP (for Keepalived)

Control Plane Nodes

TCP 6443 (Kubernetes API)
TCP 2379-2380 (etcd)
TCP 10250 (Kubelet API)
TCP 10251 (kube-scheduler)
TCP 10252 (kube-controller-manager)

etcd Nodes

TCP 2379-2380 (etcd client & peer)

Worker Nodes

TCP 10250 (Kubelet API)
TCP 30000-32767 (NodePort Services)

Installation
Install from Ansible Galaxy
bashCopyansible-galaxy install yourusername.k8s_ha_cluster
Or add to your requirements.yml:
yamlCopy---
roles:
  - name: yourusername.k8s_ha_cluster
    version: v1.0.0
Then install:
bashCopyansible-galaxy install -r requirements.yml
Deploy the Cluster

Create your inventory file (see example in examples/inventory.yml)
Create a playbook (see example in examples/playbook.yml)
Run the playbook:

bashCopyansible-playbook -i inventory.yml playbook.yml
Verification
After deployment completes successfully:

SSH to one of the master nodes
Run kubectl get nodes to verify all nodes have joined and are Ready
Run kubectl get pods -A to verify system pods are Running

Role Variables
All variables are defined in defaults/main.yml. Here are the key variables you may want to customize:
VariableDescriptionDefaultkubernetes_versionKubernetes version to install"1.30"kubernetes_cni_plugin_urlCNI plugin installation URLCalico URLpod_network_cidrCIDR for pod network"192.168.0.0/16"service_network_cidrCIDR for service network"10.96.0.0/12"virtual_ipVirtual IP for Kubernetes API"10.1.5.10"virtual_ip_interfaceNetwork interface for virtual IP"ens3"virtual_router_idVRRP router ID51keepalived_auth_passKeepalived authentication password"K8SHA_KP"etcd_versionVersion of etcd to install"v3.5.9"etcd_cluster_tokenetcd cluster token"etcd-cluster-1"install_metrics_serverWhether to install metrics-servertruecreate_admin_accountCreate admin service accounttrueinstall_helmWhether to install Helmtrue
Contributing
Contributions are welcome! Please feel free to submit a Pull Request.
License
This project is licensed under the MIT License - see the LICENSE file for details.
Author Information
Salwan.m.saeid@gmail.com
