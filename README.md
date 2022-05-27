# AFD
CVE-2022-24494

The vulnerability is an arbitrary memory read. The root cause is a lack of validation in a memory address suplied from user-land that is used in a memcpy operation in order to copy data from this memory address into the pool memory.
The lack of check is in the addres Afd!AfdTliIoControl+0x40B

![Image](/images/root_cause.jpg)

Later this address is used as the second argument of a memcpy operation. Here is where the the crash happend:

![Image](/images/memcpy.jpg)

The PoC bellow shows the kernel address provided from user-land (0xffffffdeadbeef01)

![Image](/images/PoC.jpg)

The driver try to read from this invalid memory address leading in a BSOD:

![Image](/images/crash.jpg)

