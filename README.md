# AFD
CVE-2022-24494

The vulnerability is an arbitrary memory read. The root cause is a lack of validation in a memory address suplied from user-land that is used in a memcpy operation in order to copy data from this memory address into the pool memory.
The lack of check is in the addres Afd!AfdTliIoControl+0x40B

![Image](/images/root_cause.png)

Later this address is used as the second argument of a memcpy operation. Here is where the the crash happend:

![Image](/images/memcpy.png)

The PoC bellow shows the kernel address provided from user-land (0xffffffdeadbeef01)

![Image](/images/PoC.png)

The driver try to read from this invalid memory address leading in a BSOD:

![Image](/images/crash.png)

The value copied to the dst pointer in the memcpy operation is later passed to the tcpip.sys driver. I have not dig into what internal objects are affected and if it's possible to leak memory from userland calling APIs which could get data from this potential internal objects.

Microsoft released some months later a research that talk about potential EoP impact of this kind of vulnerabilities (arbitrary memory read):

https://msrc-blog.microsoft.com/2022/03/22/exploring-a-new-class-of-kernel-exploit-primitive/

