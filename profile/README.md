
# KISS Linux based on LLVM and Musl

A line of code is worth a thousand words. Below is some pseudocode and comments guide.

## How to use:

### 1. Prepare a number of partitions, mount it to a mount point.

For instance,

    /         (mount /dev/nvme1n1p3 /mnt/kiss)          [ext4]
    /boot     (mount /dev/nvme1n1p2 /mnt/kiss/boot)     [ext4]
    /boot/efi (mount /dev/nvme1n1p1 /mnt/kiss/boot/efi) [fat32]

### 2. Dump KISS image to it.

    # Currently, you might want to download it from [here](https://github.com/glasnostlinux/glasnost/releases). Please notice that "core" version is not enough for bootstrap. 
    curl -fLO https://github.com/glasnostlinux/glasnost/releases/download/2021.10-01/glasnost-chroot-x86_64-2021.10-01.tar.xz
    tar --numeric-owner --overwrite -xJf ./glasnost-chroot-x86_64-2021.10-01.tar.xz --directory /mnt/kiss --strip-components=1 2>&1

### 3. "chroot" to it.

    # A chroot script named "root"
    doas root /mnt/kiss /bin/ash

### 4. Install a kernel

Manual kernel configuration might be needed.

    cd /var/db/kiss
    git clone --recursive https://github.com/kissllm/glasnost.git
    cd glasnost
    git submodule update --remote --init --recursive
    kISS_PATH=/var/db/kiss/glasnost/system kiss b linux

### 5. Generate boot loaders

Substitude partition parametes to actual values. One of the following methods might be more convenient for you:

    efibootmgr -c -d /dev/nvme1n1 -L "KISS Linux (EFI Stub)" -l "\EFI\efistub\vmlinuz.efi" --unicode 'root=/dev/nvme1n1p3 ro resume=/dev/nvme1n1p2 tsc=unstable'
    grub-install --target=x86_64-efi --efi-directory=/boot/efi --boot-directory=/boot --bootloader-id=kiss /dev/nvme1n1
    
### 6. Generate /etc/fstab, time zone, etc.

    # genfstab is a bash script, you can run it outside chroot if you do not want to install bash in kiss
    genfstab -U -p / >> /etc/fstab

### 7. Boot into it.

    # quit chroot
    sync && exit
    doas reboot

### 8. Further discussion / development

    [Telegram](https://t.me/llvm_musl)
    [Matrix](https://matrix.org/#/%23llvm-musl%3Amatrix.org)
    [Reddit](https://www.reddit.com/r/kisslinux)

<!--

**Here are some ideas to get you started:**

🙋‍♀️ A short introduction - what is your organization all about?
🌈 Contribution guidelines - how can the community get involved?
👩‍💻 Useful resources - where can the community find your docs? Is there anything else the community should know?
🍿 Fun facts - what does your team eat for breakfast?
🧙 Remember, you can do mighty things with the power of [Markdown](https://docs.github.com/github/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax)
-->
