# Accelerator Device Access from Linux User Space using VFIO

## VFIO (Virtual Function I/O)

VFIO is a Linux kernel framework that provides a secure, IOMMU-protected
environment for exposing direct device access to user-space. It's often used for
device pass-through in virtualization (e.g., giving a VM direct access to a GPU
or network card) but can also be used by user-space drivers for bare-metal
access.

## How it works (relevant to DMA)

* **IOMMU Protection:** VFIO heavily relies on the IOMMU to provide memory
  isolation. Each device is associated with an IOMMU group, and the IOMMU
  ensures that the device can only access the memory regions explicitly mapped
  for it.
* **Userspace Driver:** VFIO allows non-privileged user-space applications to
  act as device drivers. They communicate with the device through `ioctl` calls
  and by memory-mapping the device's I/O regions.
* **DMA with VFIO:** When a user-space application wants to perform DMA with a
  VFIO-assigned device, it maps regions of its virtual memory to the device's
  IOMMU page tables. The IOMMU then translates the device's DMA addresses to the
  corresponding physical memory addresses. This effectively "pins" the memory in
  the sense that the IOMMU ensures those physical pages are available for the
  device's DMA.
