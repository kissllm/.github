
# KISS Linux based on LLVM and Musl

A line of code is worth a thousand words. Below is some pseudocode and comments guide.

## How to use:

### 1. Prepare a number of partitions, mount it to a mount point.

For instance,

    ```
    /         (mount /dev/nvme1n1p3 /mnt/kiss)
    /boot     (mount /dev/nvme1n1p2 /mnt/kiss/boot)
    /boot/efi (mount /dev/nvme1n1p1 /mnt/kiss/boot/efi)
    ```
### 2. Dump KISS image to it.

    Currently, you might want to download it from [here](https://github.com/glasnostlinux/glasnost/releases) 

### 3. "chroot" to it.

### 4. Install a kernel

Manual kernel configuration might be needed.

    ```
    cd /var/db/kiss
    git clone --recursive https://github.com/kissllm/glasnost.git
    cd glasnost
    git submodule update --remote --init --recursive
    kISS_PATH=/var/db/kiss/glasnost/system kiss b linux
    ```

### 5. Generate boot loaders

Dependents on your partition arguments,

    ```
    efibootmgr -c -d /dev/nvme1n1 -L "KISS Linux (EFI Stub)" -l "\EFI\efistub\vmlinuz.efi" --unicode 'root=/dev/nvme1n1p3 ro resume=/dev/nvme1n1p2 tsc=unstable'
    grub-install --target=x86_64-efi --efi-directory=/boot/efi --boot-directory=/boot --bootloader-id=kiss /dev/nvme1n1
    
    ```
    
### 6. Generate /etc/fstab, time zone, etc.

### 7. Boot into it.

### 8. Further discussion

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
