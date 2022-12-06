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

Steps:

1. As done in assignment 1, setup gcp instance, clone Linux repo, build kernel, and reboot.
2. Update linux/arch/x86/kvm/cpuid.c and linux/arch/x86/kvm/vmx/vmx.c
3. compile the kernel code changes by executing sudo make -j && make modules && make install && make modules_install
4. 



