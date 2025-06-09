# Linux User Space Accelerator Framework

Linux [UACCE (Unified/User-space-access-intended Accelerator
Framework)](https://docs.kernel.org/misc-devices/uacce.html) is a kernel module
in Linux that provides a standardized way for user-space applications to
interact with hardware accelerators. Its primary goal is to enable Shared
Virtual Addressing (SVA) between accelerators and processes. This allows
accelerators to directly access any data structure within the main CPU's memory,
using the same virtual addresses that the CPU and user application see. This
unified address space simplifies programming and reduces overhead compared to
traditional data sharing methods.

## How it works

* **Kernel Module:** `uacce` operates as a kernel module, managing the IOMMU
  (Input/Output Memory Management Unit) and address sharing.
* **Character Device:** For each accelerator registered with `uacce`, a
  character device (`chrdev`) is created.
* **Queues:** When a user application opens this `chrdev`, a "queue" is
  allocated. This queue acts as a FIFO-like interface for communication between
  the user application and the hardware.
* **`mmap` and IOMMU:** `uacce` leverages `mmap` (memory mapping) and the IOMMU.
  It maps regions of the queue's file descriptor space into the user
  application's virtual memory. This allows the user process to directly put
  requests onto the hardware without needing system calls for every data
  transfer.
* **Shared Virtual Addressing (SVA):** The core benefit is SVA. The accelerator,
  using the IOMMU, shares the CPU's page tables. This means the accelerator can
  translate virtual addresses to physical addresses in the same way the CPU
  does, enabling it to access user-space memory directly using virtual
  addresses.

## Key Features

* **Unified Address Space:** Simplifies data sharing by allowing both CPU and
  accelerator to use the same virtual addresses.
* **Direct Access:** Enables accelerators to directly access user-space memory
  without costly data copies.
* **IOMMU Integration:** Relies on IOMMU capabilities to provide SVA and memory
  protection.
* **User-space Driven:** Designed for user-space drivers (often referred to as
  "WarpDrive" in the context of `uacce`'s development) to interact with
  accelerators efficiently.

## Benefits of UACCE

* **Reduced Overhead:** By allowing direct access to shared memory via `mmap()`,
  UACCE eliminates costly data copies between CPU and accelerator, and reduces
  the number of kernel calls.
* **Improved Performance:** This direct access and reduced overhead lead to
  significantly higher performance for accelerator-intensive workloads.
* **Unified Programming Interface:** UACCE provides a consistent framework for
  different hardware accelerators that support SVA, making it easier for
  developers to integrate and use various accelerators.
* **CPU Offloading:** By efficiently offloading tasks to hardware accelerators,
  UACCE frees up CPU cycles for other computations.

## Typical Usage and Ecosystem

UACCE is a fundamental kernel module that enables frameworks like **UADK (User
Space Accelerator Development Kit)**. UADK builds on top of UACCE, providing
higher-level libraries and interfaces for common accelerator tasks like
cryptography and compression.

Hardware manufacturers (like HiSilicon with their Kunpeng accelerators) develop
kernel-mode drivers that register their accelerators with UACCE, making them
available to user-space applications.

## Security

Accessing a hardware device that is capable of performing DMA operations, from
user space, requires careful attention to security to guard against cross-DMA
attacks between user space processes and to guard against DMA attacks into the
Linux kernel.

### IOMMU as the Core Security Mechanism

The IOMMU is a hardware component (or a set of components) that sits between the
CPU's memory controller and I/O devices (like accelerators). Its primary
function is to translate DMA (Direct Memory Access) requests from devices from a
"bus address" (what the device sees) to a "physical address" (what the main
memory sees). Crucially, the IOMMU also provides memory protection for I/O
devices, similar to how the CPU's MMU (Memory Management Unit) protects memory
for user-space processes. Each user-space process using a uacce device is given
its own IOMMU context. This context defines the memory regions that the
associated device is allowed to access.

### Process Isolation through IOMMU

When a user-space process opens a uacce character device and allocates a
"queue," uacce (in conjunction with the IOMMU driver) sets up the IOMMU to allow
that specific accelerator to only access memory pages belonging to that
user-space process. This isolation is enforced at the hardware level by the
IOMMU. If an accelerator tries to perform a DMA access to an address that is not
mapped within its assigned IOMMU context (i.e., memory belonging to another
process or the kernel), the IOMMU will generate an I/O page fault, preventing
the access.

### Preventing Cross-DMA Attacks

Cross-DMA attacks occur when a malicious or compromised device (or a legitimate
device that has been confused by an attacker) attempts to read from or write to
memory that it should not have access to. This could include sensitive kernel
memory, memory belonging to other user-space processes, or even memory used by
the operating system itself. The IOMMU is the primary defense against such
attacks. By rigidly enforcing the memory access permissions for each device, it
ensures that:

* An accelerator belonging to Process A cannot perform DMA into the memory space
  of Process B.
* An accelerator cannot read or modify sensitive kernel memory.
* DMA operations are confined to the memory regions explicitly allocated and
mapped for that specific device and process. User-Space Driven Control with
Kernel Mediation:

While uacce allows user-space applications to directly interact with
accelerators (e.g., placing requests on a queue via mmap), the critical memory
management and IOMMU programming are still handled by the kernel. User-space
cannot arbitrarily tell the IOMMU to map any memory address. It requests memory
mappings from the kernel via uacce's API (mmap calls), and the kernel validates
these requests against security policies and available resources before
programming the IOMMU. This kernel mediation is vital for security.

### IOMMU SVA API and Security

uacce leverages the IOMMU SVA (Shared Virtual Addressing) API within the Linux
kernel. This API is designed to expose IOMMU capabilities for sharing CPU page
tables securely. The kernel ensures that the SVA contexts are properly isolated
and that an accelerator, even with SVA, cannot bypass the IOMMU's security
checks to access unauthorized memory.
