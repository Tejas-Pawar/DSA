


## Questions:

1. Where does the kernel scheduler run ? 
2. Since kernel process scheduling is done as a part of timer interrupt, do interrupt handlers have there own thread and stack ?
3. signaction handlers set from user space application are run in userspace or kernel space
    This is nothing but a signal handling. signal can be sent from user space to kernel, kernel to user space, user space to userspace
    https://embetronicx.com/tutorials/linux/device-drivers/sending-signal-from-linux-device-driver-to-user-space/
4. How does device write at particular address ? How is that address decided ?
5. How is address of isr decided for different peripheral devices ?
6. Where is DMA controller ? inside each io device or common for bus ?
7. How is bus shared between dma and process ?
8. How is ram with less than 1GB mapped with linux kernel ?
    Top 1GB is mapped for kernel in virtual space. So whenever processor asks for kernel memory, MMU will translate and do paging if required.
https://stackoverflow.com/a/4607342

x86 CISC
Arm RISC