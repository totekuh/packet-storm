## Resources
- https://eclipsys.ca/how-to-enable-nested-virtualization-in-virtualbox-new-feature/
- https://vitux.com/how-to-install-kvm-and-virtmanager-on-kali-linux/
- https://www.exoscale.com/syslog/install-kvm-on-a-virtual-machine-to-get-nested-instances/
- https://www.techotopia.com/index.php/Installing_a_KVM_Guest_OS_from_the_Command-line_(virt-install)
- https://blog.agchapman.com/using-qemu-to-emulate-a-raspberry-pi/

## Device Emulation with KVM

Prepare the KVM emulation environment:

```bash
mkdir -p /var/kvm/images
apt install libguestfs-dev virt-manager ovmf qemu-kvm libvirt-glib-1.0-0 libvirt-dbus
apt install libvirt
```

Convert to qcow2:

```bash
qemu-img convert -f raw -O qcow2 image.img image.qcow2
```

Convert to vmdk:
```bash
qemu-img convert -f raw -O vmdk system.img system.vmdk
```

Install an Ubuntu VM:

```bash
sudo virt-install --name ubuntu-guest --os-variant ubuntu20.04 --vcpus 2 --ram 2048 --location http://ftp.ubuntu.com/ubuntu/dists/focal/main/installer-amd64/ --network none --graphics none --extra-args='console=ttyS0,115200n8 serial'
```

Destroy a VM:

```bash
sudo virsh
virst # shutdown ubuntu-guest
virsh # destroy ubuntu-guest
virsh # undefine ubuntu-vm
```


Download an Android ISO:
- https://www.android-x86.org/

Create a filesystem for the device:

```bash
qemu-img create -f qcow2 hcp5-hda.img 5G
```

Run the emulator and install the Android OS:

```bash
qemu-system-x86_64 -cdrom android-x86_64-9.0-r2.iso -m 2048M -hda hcp5-hda.img
```

Once installed, you can start the virtual machine without specifying the -cdrom argument:
```bash
qemu-system-x86_64 -m 2048M -hda hcp5-hda.img
```

*Hint: try using debugmode whenever you're always seeing a black screen while booting*

Device Emulation with Vagrant:
```bash
apt install vagrant virtualbox-qt
vagrant init ramdsc/aarch64-development --box-version 1.0.0
vagrant up
```

Running the emulator with QEMU:

```bash
#!/bin/bash

# qemu-img create -f qcow2 hcp5.img 10G

qemu-system-x86_64 \
-enable-kvm \
-m 2048 \
-smp 2 \
-cpu host \
-soundhw es1370 \
-device virtio-mouse-pci -device virtio-keyboard-pci \
-serial mon:stdio \
-boot menu=on \
-net nic \
-net user,hostfwd=tcp::5555-:22 \
-device virtio-vga,virgl=on \
-display gtk,gl=on \
-hda hcp5.img \
-cdrom system.img
```