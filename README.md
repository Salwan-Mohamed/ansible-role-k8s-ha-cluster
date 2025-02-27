# Kubernetes High Availability Cluster Ansible Role

[![Ansible Galaxy](https://img.shields.io/badge/galaxy-yourusername.k8s_ha_cluster-blue.svg)](https://galaxy.ansible.com/yourusername/k8s_ha_cluster)
[![GitHub Actions](https://github.com/yourusername/ansible-role-k8s-ha-cluster/workflows/Ansible%20Lint/badge.svg)](https://github.com/yourusername/ansible-role-k8s-ha-cluster/actions)
[![License](https://img.shields.io/badge/license-MIT-brightgreen.svg)](LICENSE)

This Ansible role automates the deployment of a production-ready, high-availability Kubernetes cluster using kubeadm with the following components:

- 3 Control Plane Nodes (master01-03)
- 3 External etcd Nodes (etcd01-03)
- 2 HAProxy Load Balancers with Keepalived (haproxy01-02)
- 3 Worker Nodes (worker01-03)

## Architecture

The architecture follows
