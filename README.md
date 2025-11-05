# OpenSUSE Tumbleweed VM Configuration

## Description
Configuration for the OpenSUSE Tumbleweed virtual machine deployed on the RKE2 Kubernetes cluster.

## Specifications
- **Name**: opensuse-tumbleweed-vm
- **Image**: quay.io/containerdisks/opensuse-tumbleweed:1.0.0
- **CPU**: 2 cores
- **RAM**: 16 GB
- **OS**: OpenSUSE Tumbleweed

## Usage
To redeploy this VM:
```bash
kubectl apply -f kubevirt/opensuse-tumbleweed-vm.yaml
```

To delete this VM:
```bash
kubectl delete -f kubevirt/opensuse-tumbleweed-vm.yaml
```

## Repository Structure
This repository contains the following directories:
- `eib/`: Contains the base image configuration and ISO definition for building custom images
- `kubevirt/`: Contains the VM configuration files for KubeVirt deployment

## Status
- VM is running
- Ready for use
- Live migration enabled

## Creation Date
$(date)