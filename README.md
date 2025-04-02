# libvirt-hooks

Custom libvirt hook scripts for specific system management \
libvirt hooks overview\
https://libvirt.org/hooks.html

## QEMU hook script

Executed when a QEMU guest is started, stopped, or migrated.

### Custom system management supported for QEMU guest

#### Shared NVSwitch GPU Partition Automatic Configuration

A custom libvirt hook script for automatic configuration of NVIDIA Fabric Manager Shared NVSwitch [GPU Partitions](https://docs.nvidia.com/datacenter/tesla/fabric-manager-user-guide/index.html#gpu-partitions) during startup and shutdown of a QEMU guest virtual machine.

##### Limitation

The QEMU libvirt hook script for the automatic configuration of NVIDIA Fabric Manager Shared NVSwitch [GPU Partitions](https://docs.nvidia.com/datacenter/tesla/fabric-manager-user-guide/index.html#gpu-partitions) is only supported on NVIDIA HGX H100 and later systems with Autonomous Link Initialization (ALI) hardware feature.  For NVIDIA HGX-2 and NVIDIA HGX A100 systems without ALI hardware feature, once the Shared NVSwitch GPU partition is activated, GPU reset should be skipped during guest VM start.  If the GPUs get a PCIe reset as part of guest VM launch, the GPU NVLinks will be in an InActive state on the guest VM.  Starting the guest VM without a GPU reset might require a modification in the hypervosr VM launch sequence.  For details please refer to the [NVIDIA Fabric Manager User Guide](https://docs.nvidia.com/datacenter/tesla/fabric-manager-user-guide/index.html#starting-a-guest-virtual-machine)

##### Installation Prerequisite

1. The custom libvirt hook script is a Python script. \
Install Python on the system.

2. The custom libvirt hook script depends on [Fabric Manager Partition Manager](https://github.com/NVIDIA/Fabric-Manager-Client).  \
Install its dependencies.
    1. Install the NVIDIA Fabric Manager Development package. \
       On Ubuntu, this package is named "nvidia-fabricmanager-dev-\<version\>" \
       On RHEL, this package is named "nvidia-fabricmanager-devel-\<version\>"

    2. Install the JSON CPP development package. \
       On Ubuntu, this package is named "libjsoncpp-dev" \
       On RHEL, this package is named "jsoncpp-devel \
       The [EPEL repository](https://www.redhat.com/en/blog/install-epel-linux) must be set up on your system to access this package.
       
    3. Obtain [Fabric Manager Partition Manager](https://github.com/NVIDIA/Fabric-Manager-Client) source and build it. \
       Deploy the binary fmpm in /usr/bin/

3. Install the NVIDIA Fabric Manager. \
The package is named "nvidia-fabricmanager-\<version\>" \
The version shall match the version of NVIDIA GPU driver installed on the system.

4. Configure Fabric Manager to Shared NVSwitch Mode. \
Set FABRIC_MODE=1 in /usr/share/nvidia/nvswitch/fabricmanager.cfg \
Restart Fabric Manager service.

```
sed -i 's/FABRIC_MODE=./FABRIC_MODE=1/g' /usr/share/nvidia/nvswitch/fabricmanager.cfg
sudo systemctl restart nvidia-fabricmanager.service
```

5. Install the NVIDIA Fabric Manager Development package for the Fabric Manager SDK. \
On RHEL, this package is named "nvidia-fabricmanager-devel-\<version\>" \
On Ubuntu, this package is named "nvidia-fabricmanager-dev-\<version\>" 

## Deploy the QEMU libvirt hook script
Deploy the libvirt hook script for QEMU at /etc/libvirt/hooks/qemu
```
sudo wget 'https://raw.githubusercontent.com/NVIDIA/libvirt-hooks/refs/heads/main/qemu' -O /etc/libvirt/hooks/qemu
sudo chmod +x /etc/libvirt/hooks/qemu
sudo systemctl restart libvirtd
```
# License

By downloading or using this software, I agree to the terms of the [LICENSE](LICENSE)
