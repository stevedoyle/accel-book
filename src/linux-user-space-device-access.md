# Accelerator Device Access from Linux User Space

There are two primary methods for accessing an accelerator device from a Linux
user space process.

1. [Secure device access using VFIO](./vfio.md)
2. [Linux User Space Accelerator Access Framework (UACCE)](./uacce.md)

## Key Differences from between VFIO and `uacce`

* **Granularity of Control:** VFIO provides more granular, low-level control
  over the device and its IOMMU mappings. It's about direct device assignment
  and allowing a user-space entity to entirely "own" and manage a device,
  including its DMA.
* **Primary Use Case:** While both enable user-space device interaction, VFIO's
  primary motivation is often secure device pass-through for virtualization, or
  building full-fledged user-space device drivers. `uacce` is more specifically
  focused on providing a unified virtual address space for *accelerators* to
  access CPU memory directly.
* **Shared Virtual Addressing (SVA):** `uacce` explicitly aims for SVA, meaning
  the accelerator *sees* the same virtual addresses as the CPU process. VFIO,
  while using the IOMMU for address translation, doesn't inherently guarantee
  that the device will operate on the *same virtual addresses* as the host
  process; rather, it sets up mappings so the device's DMA addresses translate
  to the correct physical pages. `uacce` leverages the IOMMU SVA API to achieve
  this deeper integration.
* **Framework vs. Abstraction:** `uacce` is a framework designed to standardize
  accelerator interaction by providing a shared queue and SVA. VFIO is a more
  general framework for secure direct device access.
