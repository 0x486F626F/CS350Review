# Virtual Memory

* one physical address space per machine, size = addressable physical memory
* one virtual address space per process
* Address Translation: virtual address -> physical address

## Memory Management Unit (MMU)

* relocation register and limit register

```
if virtual address <= limit register
	physical address = base address + virtual address
else
	exception
```

MMU always contain the base address and limit of the currently-running process (address space switch).

## Dynamic Relocation

* each virtual address space corresponds to a contiguous range of physical addresses
* OS is responsible for address translation
* OS must track which parts of physical memory are in use
* different address space may have different sizes
* potential external fragmentation

## Paging

* each virtual address space is divided into fixed-size chunks called pages
* physical address space is divided into frames.
* frame size = page size
* OS maintains a page table for each process
* potential internal fragmentation
* do not need to be physically contiguous in physical space
* MMU registers refer to the page table of the currently-running process, base address = first page table entry, length register = number of entries in the page table

```
virtual address = |page number|offset|

if page number <= page table length
	physical address = |frame number|offset|
else
	exception
```

## Roles of the Kernel and the MMU

### Kernel:

* manage MMU state on the address space switch
* create and manage page table
* allocate/deallocate physical memory
* handle MMU exceptions

### MMU(hardware):

* address translation
* raise exception

## Translation Lookaside Buffer (TLB)

* address translation may be slow, so save address translation result in the TLB
* TLB hit/TLB miss
* all entries in TLB are checked simultaneously	
* kernel must clear the TLB on each address space switch

### hardware-controlled TLB:

```
if TLB is not full
	TLB.add(page number, frame number)
else
	TLB.evict()
```

### software-controlled TLB:

```
while (TLB.lookup(page number) == miss)
	throw exception //triggers exception handler

exception handler:
	if TLB is not full
		TLB.add(page number, frame number)
	else
		TLB.evict()
```

## Executable and Linking Format (ELF)

* ELF file contains a header describing the segments and segments images
* image is an exact copy of the binary data that should be loaded into the specified portion of the virtual address space
* each ELF segments describes a contiguous region of the virtual address space
	* the virtual address of the start of the segment
	* the length of the segment
	* the location of the start of the segment image in the ELF
	* the length of the segment image in the ELF
* image may be smaller than the address space segment (zero-filled)

## Problem of Sparse Address Spaces

Assume 4k page, to map 2G virtual address space, need 2^19 page table entries, but program may only use few of them.

* If dynamic relocation, the kernel will need to map a 2G address space contiguously into physical memory
* If paging, kernel will create a page table with 2^19 PTEs, most of them are marked as invalid

### multi-level paging

* replace the single large linear page table with a hierarchy of smaller page tables
* each page table must be contiguous in physical memory (translated by MMU)

## Exploiting Secondary Storage

Goal:

* allow virtual address spaces that are larger than the physical address space
* allow greater multiprogramming levels by using less of the primary memory for each process.

Method:

* allow pages from the virtual address space to be stored in secondary storage
* move pages between secondary storage and primary memory

Paging:

* Demand paging brings pages into memory when they are used
* OS can attempt to guess which pages will be used and prefetch them

Replace:

* if no unused frames, old page must be replaced by the new page
* A replacement policy specifies how to determine which page to replace

### Page Faults

page is valid (V=1), but is not in memory (P=0)

* issue a request to the disk to copy the missing page into memory
* block the faulting process
* once the copy has completed, set P=1 in the PTE and unblock the process

MMUs with software-controlled TLBs never check page tables, if TLB miss, exception handler determine the reason:

* not in TLB?
* page fault?
* invalid page?

Translation in the TLB only if the page is valid and in memory.

Replacement Policies:

* FIFO: replace the page that has been in memory the longest
* OPT: replace the page that will not be referenced for the longest time (requires knowledge of the future)
# LRU: replace the page that has not been used for the longest time

FIFO is simple, but it does not consider:

* Frequency of Use: how often a page has been used
* Recency of Use: when was a page last used
* Cleanliness: has the page been changed while it is in memory

### Locality

Locality is a property of the page reference string.

* Temporal locality: pages that have been used recently are likely to be used again
* Spatial locality: pages close to those that have been used are likely to be used next

### The "use" bit

The use bit is set by the MMU each time the page is used.

The clock algorithm is identical to FIFO, except that a page is skipped if its use bit is set

```
while use bit of victim is set
	clear use bit of victim
	victim = (victim + 1) % num_frames
choose victim for replacement
victim = (victim + 1) % num_frames
```

### The "modified" bit

a page is modified (dirty) if it has been changed since it was loaded into memory

a modified page is more costly to replace than a clean page (need to write back).

## Thrashing and Load Control

What is a good multiprogramming level?

* if too low: resources are idle
* if too high: too few resources per process

a system that is spending too much time paging is said to be thrashing.
Thrashing occurs when there are too many processes competing for the available memory.

### Swapping Out Processes

remove all of its pages from memory and suspend the process

* low priority processes
* blocked processes
* large processes (for lost of space) or small processes (easier to reload)