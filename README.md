# CS-notes

### Virtual memory

Virtual memory is a layer of abstraction provided to each process. The computer has, say, 2GB of physical RAM, addressed from 0 to 2G. A process might see an address space of 4GB, which it has entirely to itself. The mapping from virtual addresses to physical addresses is handled by a memory management unit, which is managed by the operating system. Typically this is done in 4KB "pages".

This gives several features:

A process can not see memory in other processes (unless the OS wants it to!)
Memory at a given virtual address may not be located at the same physical address
Memory at a virtual address can be "paged out" to disk, and then "paged in" when it is accessed again.
Your textbook defines virtual memory (incorrectly) as just #3.

Even without any swapping, you particularly need to be aware of virtual memory if you write a device driver for a device which does DMA (direct memory access). Your driver code runs on the CPU, which means its memory accesses are through the MMU (virtual). The device probably does not go through the MMU, so it sees raw physical addresses. So as a driver writer you need to ensure:

Any raw memory addresses you pass to the hardware are physical, not virtual.
Any large (multi page) blocks of memory you send are physically contiguous. An 8K array might be virtually contiguous (through the MMU) but two physically separate pages. If you tell the device to write 8K of data to the physical address corresponding to the start of that array, it will write the first 4K where you expect, but the second 4K will corrupt some memory somewhere. 
