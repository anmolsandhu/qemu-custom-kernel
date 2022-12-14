# qemu-custom-kernel
commands to build custom kernel


1 - Packages to install

sudo apt install git

sudo apt install build-essential kernel-package fakeroot libncurses5-dev libssl-dev ccache flex bison libelf-dev

sudo apt install qemu qemu-system

sudo apt install gdb

2 - getting the kernel, depth is 1 to get repo without git log (version history), otherwise it takes longer and the repo size is quite large

git init

git remote add origin https://github.com/torvalds/linux.git

git pull --depth=1 origin master

3 - Configure the Linux Kernel

Configure the Linux kernel to build kernel image that is able to run on qemu(x86_64 architecture). You can find config file what is used as base for x86_64 architecture in arch/x86/configs/x86_64_defconfig. Let’s find out how to .config file as default configuration file for x86_64 architecture.

make ARCH=x86_64 x86_64_defconfig   

4 (CUSTOMIZE KERNEL CONFIG, OPTIONAL) below command will open TUI(Text-based UI) to help you configure the linux kernel easly and also load current .config file automatically.

make ARCH=x86_64 menuconfig

5 - (build the kernel)

make -j8

will have output as shown below
"arch/x86/boot/bzImage is ready"

bzImage is the kernel build file

At this point we have the kernel image ready -  what we need is a file system to run a minimilast shell

Options for building a file system
1) creating a ramdisk is the most straightforward way

mkinitramfs -o ramdisk.img 

running the qemu with minimilast filesystem
qemu-system-x86_64 -kernel arch/x86_64/boot/bzImage -nographic -append "console=ttyS0" -initrd ramdisk.img -m 512

-------------------
good resource for kernel coding chat-guide
custom android kernel 
https://fadeevab.com/build-android-kernel-and-run-on-qemu-minimal-step-by-step/

git clone https://android.googlesource.com/kernel/goldfish kernel/goldfish

cd ./kernel/goldfish

git checkout -b android-4.4-dev origin/android-4.4

make defconfig

make -j4

wget https://storage.googleapis.com/syzkaller/wheezy.img

qemu-system-x86_64 -m 1GB -kernel arch/x86/boot/bzImage -hda wheezy.img -append "root=/dev/sda"


setting up buildroot filesystem

https://buildroot.org/downloads/manual/manual.html#requirement-mandatory

make nconfig

target -> x86

filesystem -> ext4

qemu-system-x86_64 -kernel arch/x86/boot/bzImage \
-boot c -m 512 -hda ../buildroot/output/images/rootfs.ext2 \
-append "root=/dev/sda rw console=ttyS0" \
-serial stdio -display none

