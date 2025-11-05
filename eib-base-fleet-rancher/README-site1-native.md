# Site1 - Native EIB Configuration

This directory contains the native Edge Image Builder (EIB) configuration for the `site1` node, following the official SUSE Edge 3.4 documentation.

## Configuration Overview

### Image Definition
- **File**: `iso-definition-site1-native.yaml`
- **Base Image**: SL-Micro.x86_64-6.1-Base-SelfInstall-GM.install.iso
- **Output**: site1-native.iso
- **Architecture**: x86_64

### Users Configuration
- **root**: Password `linux`, SSH key configured
- **tofix**: Password `linux`, SSH key configured, sudo access

### SSH Configuration
- **PermitRootLogin**: yes
- **PasswordAuthentication**: yes
- **PubkeyAuthentication**: yes
- **SSH Keys**: Configured for both root and tofix users

### System Configuration
- **Hostname**: site1.cledorze.lan
- **Timezone**: Europe/Paris (configured via osFiles)
- **Network**: Static IP configuration (100.100.100.248/24)

### Kubernetes Stack
- **Version**: v1.33.3+rke2r1
- **CNI**: Cilium (default)
- **Helm Charts**:
  - KubeVirt 304.0.1+up0.6.0 (VM management)
  - CDI 304.0.1+up0.6.0 (Container Disk Images)

## Directory Structure

```
eib-base-fleet-rancher/
├── iso-definition-site1-native.yaml  # Main EIB definition
├── osFiles/                                 # System configuration files
│   ├── etc/
│   │   ├── hostname                        # Hostname configuration
│   │   ├── hosts                           # Hosts file
│   │   └── sudoers.d/wheel                 # Sudo configuration
│   ├── root/.ssh/authorized_keys           # Root SSH key
│   ├── home/tofix/.ssh/authorized_keys     # Tofix SSH key
│   └── sshd_config.d/permitroot.conf       # SSH configuration
└── base-images/
    └── SL-Micro.x86_64-6.1-Base-SelfInstall-GM.install.iso
```

## Building the Image

```bash
export CONFIG_DIR=/home/tofix/LAB/EIB-demo/eib-base-fleet-rancher
podman run --rm -it --privileged \
  -v $CONFIG_DIR:/eib \
  registry.suse.com/edge/3.4/edge-image-builder:1.3.0 \
  build --definition-file iso-definition-site1-native.yaml
```

## Key Features

### Native EIB Configuration
- Uses official EIB sections: `operatingSystem.users`, `kubernetes`
- Leverages `osFiles/` directory for system configuration
- Follows SUSE Edge 3.4 documentation patterns

### System Integration
- SSH keys automatically deployed to correct user directories
- Hostname and network configuration via system files
- Sudo access configured for wheel group

### Kubernetes Ready
- RKE2 cluster with Cilium CNI
- KubeVirt for VM management capabilities
- CDI for container disk image support

## Network Configuration Note

The network configuration (static IP, gateway, DNS) is currently handled via the `osFiles/` approach rather than the NMC (Network Management Configuration) section due to EIB 1.3.0 limitations. The network configuration files are prepared but not automatically applied during the build process.

For complete network configuration, additional post-deployment steps may be required to apply the static IP settings.

## Validation

The configuration has been validated using:
```bash
podman run --rm -it --privileged \
  -v $CONFIG_DIR:/eib \
  registry.suse.com/edge/3.4/edge-image-builder:1.3.0 \
  validate --definition-file iso-definition-site1-native.yaml
```

## Deployment

The generated `site1-native.iso` can be deployed to bare metal or virtual machines. After deployment, the system will have:

- Pre-configured users with SSH access
- Kubernetes cluster ready for VM management
- Proper hostname and system configuration
- SSH access enabled for both password and key authentication
