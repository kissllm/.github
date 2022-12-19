
# KISS Linux based on LLVM and Musl

A line of code is worth a thousand words. Below is some pseudocode and comments guide.

## How to use:

### 1. Prepare a number of partitions, mount it to a mount point.

For instance,

    /         (mount /dev/nvme1n1p3 /mnt/kiss)          [ext4]
    /boot     (mount /dev/nvme1n1p2 /mnt/kiss/boot)     [ext4]
    /boot/efi (mount /dev/nvme1n1p1 /mnt/kiss/boot/efi) [fat32]

### 2. Dump KISS image to it.
    
Currently, you might want to download it from [here](https://github.com/glasnostlinux/glasnost/releases). Please notice that the "core" versions are not enough for bootstrapping. 

    curl -fLO https://github.com/glasnostlinux/glasnost/releases/download/2021.10-01/glasnost-chroot-x86_64-2021.10-01.tar.xz
    tar --numeric-owner --overwrite -xJf ./glasnost-chroot-x86_64-2021.10-01.tar.xz --directory /mnt/kiss --strip-components=1 2>&1

### 3. "chroot" to it.

An example partition mount "[load-kiss](https://github.com/kissllm/dotconfig/blob/master/load-kiss)" and a chroot script "[root](https://github.com/kissllm/dotconfig/blob/master/local/bin/root)"

    doas load-kiss
    doas root /mnt/kiss /bin/ash

### 4. Install a kernel

Manual kernel configuration might be needed.

    cd /var/db/kiss
    git clone --recursive https://github.com/kissllm/glasnost.git
    cd glasnost
    git submodule update --remote --init --recursive
    kISS_PATH=/var/db/kiss/glasnost/system kiss b linux

### 5. Generate boot loaders

Substitude partition parametes to actual values. One of the following methods[[1]](https://github.com/kissllm/dotconfig/blob/master/b) might be more convenient for you:

    doas efibootmgr -c -d /dev/nvme1n1 -L "KISS Linux (EFI Stub)" -l "\EFI\efistub\vmlinuz.efi" 'root=/dev/nvme1n1p3 ro resume=/dev/nvme1n1p2 tsc=unstable'
    doas grub-install --target=x86_64-efi --efi-directory=/boot/efi --boot-directory=/boot --bootloader-id=kiss /dev/nvme1n1
    
### 6. Generate /etc/fstab, time zone, etc.


    doas /mnt/local/bin/fstab -U -P / >> /etc/fstab

"[fstab](https://github.com/kissllm/dotconfig/blob/master/local/bin/fstab)" is an ash script, you can run it outside chroot

    doas /mnt/local/bin/fstab -U -P /mnt/kiss >> /mnt/kiss/etc/fstab

Manually comment out some optional items.
### 7. Boot into it.

Quit chroot

    sync && exit
    doas reboot

### 8. Further discussion / development

[llvm-musl on Telegram][llvm-musl]

<!-- p><a href="https://matrix.org/#/%23llvm-musl%3Amatrix.org"> llv-mmusl on matrix </a></p -->

[llvm-musl on Matrix][matrix]

[KISS Linux on Telegram][kiss-linux]

[KISS Linux on Reddit][reddit]


[llvm-musl]: https://t.me/llvm_musl
[matrix]: https://matrix.org/#/%23llvm-musl%3Amatrix.org
[kiss-linux]: https://t.me/Kiss_Linux
[reddit]: https://www.reddit.com/r/kisslinux


