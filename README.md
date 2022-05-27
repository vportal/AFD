# AFD
CVE-2022-24494

The vulnerability is an arbitrary memory read. The root cause is a lack of validation in a memory address suplied from user-land that is used in a memcpy operation in order to copy data from this memory address into the pool memory.
The lack of check is in the addres Afd!AfdTliIoControl+0x40B

![Image](/images/img1.png)

As you can see in the screenshot above, the user-land memory address is copied from InputBuffer+0x20 a first time properly checking with MmUserProbeAddress if the memory address is inside the user-land memory address space. However, a second time this same memory address is copied from InputBuffer+0x20 to RAX register but this time without properly check the memory address using MmUSerProbeAddress. Due to this lack of validation, it is possible to provide an arbirtray kernel address.
This address is later copied to a pool memory allocation (tag: AfdL) using a memcpy operation as you can see below in AfdTliIoControl+0x5AB:

![Image](/images/img2.png)

The PoC bellow shows the kernel address provided from user-land (0xffffffdeadbeef01)

![Image](/images/img3.png)

The driver try to read from this invalid memory address leading in a BSOD:

![Image](/images/img4.png)

The value copied to the dst pointer in the memcpy operation is later passed to the tcpip.sys driver. I have not dig into what internal objects are affected and if it's possible to leak memory from userland calling APIs which could get data from this potential internal objects.

Microsoft released some months later a research that talk about potential EoP impact of this kind of vulnerabilities (arbitrary memory read):

https://msrc-blog.microsoft.com/2022/03/22/exploring-a-new-class-of-kernel-exploit-primitive/

