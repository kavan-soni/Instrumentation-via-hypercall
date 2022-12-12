# CMPE 283 - Assignment 3 - Instrumentation-via-hypercall
 
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

1. Setup gcp instance with following configuration:
    Ubuntu 22.04 LTS (x86/64, amd64) with 200 GB SSD persistent disk and nested virtualisation enabled. Enable display device.
    ```
    gcloud compute instances create instance-1 --project=cmpe283-370901 --zone=us-west1-a --machine-type=n2-standard-8 --network-interface=network-tier=PREMIUM,subnet=default --maintenance-policy=MIGRATE --provisioning-model=STANDARD --service-account=57150999445-compute@developer.gserviceaccount.com --scopes=https://www.googleapis.com/auth/devstorage.read_only,https://www.googleapis.com/auth/logging.write,https://www.googleapis.com/auth/monitoring.write,https://www.googleapis.com/auth/servicecontrol,https://www.googleapis.com/auth/service.management.readonly,https://www.googleapis.com/auth/trace.append --enable-display-device --tags=http-server,https-server --create-disk=auto-delete=yes,boot=yes,device-name=instance-1,image=projects/ubuntu-os-cloud/global/images/ubuntu-1804-bionic-v20221201,mode=rw,size=200,type=projects/cmpe283-370901/zones/us-west1-a/diskTypes/pd-ssd --no-shielded-secure-boot --shielded-vtpm --shielded-integrity-monitoring --reservation-affinity=any --enable-nested-virtualization --min-cpu-platform="Intel Cascade Lake
    ```
2. Login to the instance created
    ```
    gcloud compute ssh --project=cmpe283-370901 --zone=us-west1-a instance-1
    ```

3. Fork and then clone Linus Torvalds Linux repo - git clone https://github.com/shreynadiadwala/linux

4. Install all dependencies
    ```
    sudo apt-get update
    sudo apt-get install make
    sudo apt-get install flex
    sudo apt-get install bison
    sudo apt-get install gcc
    sudo apt-get install libssl-dev
    sudo apt-get install libelf-dev
    sudo apt-get install build-essential
    sudo apt-get install kernel-package
    sudo apt-get install ccache 
    ```
5. Use ```cat /proc/cpuinfo``` and ```grep -cw vmx /proc/cpuinfo``` to verify nested virtualisation.

6. Use ```cp /boot/$(uname -a) ./.config``` to copy the contents of specific kernel version to config file inside linux directory.

7. Inside the linux directory, open config file with ```vi .config``` and comment ```CONFIG_SYSTEM_TRUSTED_KEYS```, ```CONFIG_SYSTEM_TRUSTED_KEYRING```. Update ```CONFIG_SYSTEM_REVOCATION_KEYS=""```. Save and exit vim editor.

8. cd in Linux directory and run ```make oldconfig``` to build the kernel.

9. Execute ```make prepare```.  

10. Execute ```sudo make -j 8 modules && make -j 8 && make modules_install && make install reboot```.

11. Reboot the VM with ```sudo reboot```. This finishes installation of L1 VM. Check kernel version using ```uname -a```.

12. Implemented assignment functionlity by updating ```linux/arch/x86/kvm/cpuid.c``` and ```linux/arch/x86/kvm/vmx/vmx.c``` files.

13. We have made changes in kernel source, so we have to build kernel again.
    ```
    make -j 8 modules
    sudo make -j 8 INSTALL_MOD_STRIP=1 modules_install && make install
    sudo lsmod | grep kvm
    sudo rmmod kvm_intel
    sudo rmmod kvm
    sudo modprobe kvm
    sudo modprobe kvm_intel
    sudo lsmod | grep kvm
    sudo kvm-ok
    sudo reboot
    ```

14. We have to create a new user that can create and run L2 VM.
    ```
    uname -a
    sudo adduser kavan libvirt
    sudo adduser kavan kvm
    sudo virsh list --all
    sudo systemctl status libvirtd       
    ```

15. We tried to create L2 VM directly on terminal but we ran into a lot of issues. After doing a lot of research, we found we can install GUI for L1 VM. Reference: https://ubuntu.com/blog/launch-ubuntu-desktop-on-google-cloud

16. With GUI enabled, we can install virtual manager in L1 VM and create L2 VM on it.
    ```
    sudo apt install virt-manager
    wget https://releases.ubuntu.com/jammy/ubuntu-22.04.1-desktop-amd64.iso
    sudo mv ~/ubuntu-22.04.1-desktop-amd64.iso /var/lib/libvirt/images/
    sudo virt-manager
    ```

17. Follow GUI prompts to create a new VM using the iso image we downloaded in previous step. Verify L2 VM by running ```uname -a``` in terminal.

18. Run test files in L2 VM terminal.

## Assignment 3 Q/A

1. Comment on the frequency of exits – does the number of exits increase at a stable rate? Or are there 
more exits performed during certain VM operations? Approximately how many exits does a full VM 
boot entail? 

Ans - a) Yes, for exits such as HLT(10) and CPUID(12) it increases at a stable rate. Increase rate varied from exit to exit. Yes, more exits are performed during certain VM operations. A full VM boot entails approximately 931512 exits.
     
   
2. Of the exit types defined in the SDM, which are the most frequent? Least?

Ans - Most frequent exits are MSR Access (32) and EPT Violation (48), while least frequent are MOV DR (29) and WBINVD (54)
