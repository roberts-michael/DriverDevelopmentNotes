# Driver Development Notes

A collection of notes on Windows driver development

## Overview

**Paging**

* System memory is divided into equal-sized chunks, known as a page
* One page is the smallest unit of memory management
* Operating system memory manager responsible for swapping pages into and
* out of physical memory
* Default page size for the Windows operating system is 4KB
* The Windows operating system supports page sizes:
    * Default: 4KB
    * Large: 2MB
    * Huge: 1GB (Windows 10+)
* Each page may be:
    * Free: Unallocated, any attempt to access raises an exception
    * Committed: Allocated and available for use
    * Reserved: Same as free, except avoided for allocation unless
    explicitly requested

**Process**

* A container
* Has a private virtual address space
* Has a primary access token
    * Each thread may override this through impersonation
* Has a private handle table
* Has one or more threads of execution

**Process Memory Layout**

* The Windows operating system resides inside the address space of every process
    * Achieved via Virtual Memory
    * There is only one operating system, mapped virtually into each process
    * Drivers, as an extension of the operating system, also reside within
    each process
* Windows is an upper-half operating system
* The upper-half of process memory is where the operating system resides
and the lower-half is user-space, per-process memory
* Memory split by architecture/Windows version (user vs. kernel):
    * x86: 2GB/2GB
    * x86 (LAA): 3GB/1GB
    * x64: 8TB/8TB
    * x64 (Windows 8.1+): 128TB/128TB

**Virtual Memory**

* Every process has its own virtual, private, linear address space
* Other processes cannot access this memory
* The first 64KB is reserved
* Otherwise, address space runs from 64KB to:
    * x86: 2GB
    * x86 (LAA): 3GB
    * x86 on x64 (LAA): 4GB
    * x64: 8TB or 128TB (Windows 8.1+)

The contents of virtual memory may or may not exist in physical memory, as the
operating system may page out memory as needed. When a thread attempts to
access virtual memory which is not currently in physical memory, a page fault
exception is thrown. The operating system memory manager must then load
physical memory with the requested data and clear the page fault exception
