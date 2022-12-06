# CMPE 283 - Assignment 2 - Instrumentation-via-hypercall
 
Team Members:

Kavan Vipinbhai Soni (016085966)
- Set up the environment and created VM in GCP.
- Built the kernel for the first time.
- Rewatched class lectures for reference.
- Studied atomic variables working.
- Worked on cpuid.c file to insert functionality of reading atomic variables and print in kernel logs the required parameters (number of exits and time taken). Extracted lower 32 and higher 32 bits of required cycles. 
- Wrote the test file and compiled.
- Created documentation.

Shrey Miteshbhai Nadiadwala (015331134)
- Set up the environment to rebuild the kernel after code changes.
- Rebuilt the kernel with updated cpuid.c and vmx.c
- Worked on vmx.c to measure time taken in exits and monitor total number of exits. Utilized the vmx_handle_exit() function in vmx.c. Exported the total time and number of exits variable for fetching at cpuid.c
- Researched the assignment problem, scope and alternative methods to create nested VM such as VNC client.
- Created nested VM L2 in GCP by running Ubuntu OS in Google Remote Desktop where UI is enabled. Used virt-manager and qemu to create nested VM to execute test file. 
- Created documentation

Steps to reproduce:

1. Fork and then clone Linus Torvalds Linux repo - ***git clone https://github.com/shreynadiadwala/linux***
2. Install necessary dependencies- 
   ***sudo bash***
   ***apt-get install build-essential kernel-package fakeroot libncurses5-dev libssl-dev ccache bison flex libelf-dev***
3. Check the current kernel version - ***uname -a***
4. Build linux kernel for the first time - 
   ***cd linux***
   
   ***cp /boot/$(uname -a) ./.config***
   
   ***make oldconfig***  (keep pressing enter to use default setting for everything)
   
   ***make -j 8 modules && make -j 8 && make modules_install && make install reboot***
   
   ***uname -a***   Again check kernel version, it should be updated

5. Implemented assignment functionlity by updating cpuid.c and vmx.c files.
6. Setting up KVM to build nested L2 VM. 
   ***sudo apt-get install qemu-kvm libvirt-bin bridge-utils virt-manager***
7. Launch Ubuntu in GCP using this guide - https://ubuntu.com/blog/launch-ubuntu-desktop-on-google-cloud
8. Check kernel version using ***uname -a*** and start nested VM using ***sudo virt-manager***
9. In the new window under QEMU/KVM open the inner virtual machine. 
10. Create test.c in this VM and execute it. 
11. Check the kernel logs in outer VM using ***tail -n20 /var/log/kern.log***



