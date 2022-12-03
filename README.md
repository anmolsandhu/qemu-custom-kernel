# qemu-custom-kernel
commands to build custom kernel


1 - Packages to install

##########
sudo apt install git
sudo apt install build-essential kernel-package fakeroot libncurses5-dev libssl-dev ccache flex bison libelf-dev
sudo apt install qemu qemu-system
sudo apt install gdb

2 - getting the kernel, depth is 1 to get repo without git log (version history), otherwise it takes longer and the repo size is quite large

############
git init
git remote add origin https://github.com/torvalds/linux.git
git pull --depth=1 origin master

3 - Configure the Linux Kernel

Configure the Linux kernel to build kernel image that is able to run on qemu(x86_64 architecture). You can find config file what is used as base for x86_64 architecture in arch/x86/configs/x86_64_defconfig. Let’s find out how to .config file as default configuration file for x86_64 architecture.

########################
make ARCH=x86_64 x86_64_defconfig   

4 (CUSTOMIZE KERNEL CONFIG, OPTIONAL) below command will open TUI(Text-based UI) to help you configure the linux kernel easly and also load current .config file automatically.

#################
make ARCH=x86_64 menuconfig

5 - (build the kernel)

##########
make -j8

#############
will have output as shown below
"arch/x86/boot/bzImage is ready"

bzImage is the kernel build file

At this point we have the kernel image ready -  what we need is a file system to run a minimilast shell

Options for building a file system
1) creating a ramdisk is the most straightforward way
###############
mkinitramfs -o ramdisk.img 

running the qemu with minimilast filesystem
qemu-system-x86_64 -kernel arch/x86_64/boot/bzImage -nographic -append "console=ttyS0" -initrd ramdisk.img -m 512

