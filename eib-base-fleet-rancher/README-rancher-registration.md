# Rancher Registration - EIB Configuration

This directory contains the Edge Image Builder (EIB) configuration for automatically registering nodes with Rancher at `rancher.cledorze.lan` using Elemental.

## Configuration Overview

### Image Definition
- **File**: `iso-definition-rancher-registration.yaml`
- **Base Image**: SL-Micro.x86_64-6.1-Base-SelfInstall-GM.install.iso
- **Output**: rancher-registration.iso
- **Architecture**: x86_64

### Elemental Registration
The image will automatically register with Rancher on first boot using the Elemental configuration file.

## Prerequisites

1. **Rancher Server**: Must be accessible at `rancher.cledorze.lan`
2. **Elemental Extension**: Must be installed in Rancher
3. **Machine Registration**: Must be created in Rancher UI

## Setup Instructions

### Step 1: Create Machine Registration in Rancher

1. Log in to Rancher UI at `https://rancher.cledorze.lan`
2. Navigate to **Elemental** extension
3. Create a new **Machine Registration**:
   - Configure the registration settings
   - Set the hostname pattern (if needed)
   - Configure labels and annotations
   - Download the `elemental_config.yaml` file

### Step 2: Place Configuration File

1. Copy the downloaded `elemental_config.yaml` from Rancher
2. Replace the file in `eib-base-fleet-rancher/elemental/elemental_config.yaml`
   - The current file is a placeholder template
   - Replace it with the actual configuration from Rancher

### Step 3: Build the Image

```bash
export CONFIG_DIR=/home/tofix/LAB/EIB-demo/eib-base-fleet-rancher
podman run --rm -it --privileged \
  -v $CONFIG_DIR:/eib \
  registry.suse.com/edge/3.4/edge-image-builder:1.3.0 \
  build --definition-file iso-definition-rancher-registration.yaml
```

> **NOTE**: The `--privileged` flag is required for Elemental builds as they use EIB's package resolution process to download necessary RPM packages.

### Step 4: Deploy the Image

1. Burn the generated `rancher-registration.iso` to a USB drive or use it in a VM
2. Boot the target machine from the ISO
3. The installation will be fully unattended (installDevice: /dev/sda)
4. On first boot, the node will automatically register with Rancher

## Directory Structure

```
eib-base-fleet-rancher/
├── iso-definition-rancher-registration.yaml  # Main EIB definition
├── elemental/
│   └── elemental_config.yaml                 # Elemental registration config (from Rancher)
├── os-files/                                 # System configuration files
│   ├── etc/
│   │   ├── hostname                          # Hostname configuration
│   │   ├── hosts                             # Hosts file
│   │   └── ssh/sshd_config.d/                # SSH configuration
│   ├── root/.ssh/authorized_keys             # Root SSH key
│   └── home/tofix/.ssh/authorized_keys       # Tofix SSH key
└── base-images/
    └── SL-Micro.x86_64-6.1-Base-SelfInstall-GM.install.iso
```

## Configuration Details

### Operating System
- **Timezone**: Europe/Paris
- **NTP Servers**: French NTP pools
- **Keymap**: French
- **Install Device**: /dev/sda (automatic, unattended)
- **Users**: root, tofix, cledorze (with SSH keys)

### Elemental Registration
- **Rancher URL**: rancher.cledorze.lan
- **Registration**: Automatic on first boot
- **Configuration**: From `elemental/elemental_config.yaml`

## Key Features

### Automatic Registration
- Nodes automatically register with Rancher on first boot
- No manual intervention required
- Configuration managed through Rancher UI

### Unattended Installation
- Fully automated installation process
- No user interaction required during installation
- Automatic disk partitioning and OS installation

### System Integration
- Pre-configured users with SSH access
- Timezone and NTP configured
- SSH keys automatically deployed

## Validation

Validate the configuration before building:

```bash
export CONFIG_DIR=/home/tofix/LAB/EIB-demo/eib-base-fleet-rancher
podman run --rm -it --privileged \
  -v $CONFIG_DIR:/eib \
  registry.suse.com/edge/3.4/edge-image-builder:1.3.0 \
  validate --definition-file iso-definition-rancher-registration.yaml
```

## Troubleshooting

### Registration Fails
- Verify `rancher.cledorze.lan` is accessible from the target machine
- Check that the `elemental_config.yaml` file is correct and from the latest Rancher registration
- Ensure the Elemental extension is properly installed in Rancher

### Build Requires Privileged Mode
- Elemental builds require `--privileged` flag for RPM resolution
- This is required by EIB's package resolution process
- See [EIB documentation](https://github.com/suse-edge/edge-image-builder/blob/main/docs/building-images.md) for details

### SCC Registration Code
- The `sccRegistrationCode` is required for SL Micro 6.0 when using Elemental
- This allows downloading necessary Elemental RPM packages
- Update the code if needed for your environment

## References

- [EIB Building Images Documentation](https://github.com/suse-edge/edge-image-builder/blob/main/docs/building-images.md)
- [Elemental Documentation](https://elemental.docs.rancher.com/)
- [Rancher Documentation](https://rancher.com/docs/)

