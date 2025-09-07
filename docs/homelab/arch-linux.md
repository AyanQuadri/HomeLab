# Arch Linux VM Setup

## Initial Installation

### Start Arch Installation

**Start Arch Installation:**

```bash
pacman -Sy archinstall
```

**What this does:** Updates package database and installs the guided Arch installer  
**Why this matters:** Provides a user-friendly installation method

```bash
archinstall
```

**What this does:** Launches the guided Arch Linux installation wizard  
**Why this matters:** Simplifies the traditionally complex Arch installation process

## Manual Partition Setup (Alternative)

**Check Available Disks:**

```bash
lsblk
```

**What this does:** Lists all block devices and their partitions  
**Why this matters:** Helps identify the target disk for installation

```bash
ip addr
```

**What this does:** Displays network interface configuration  
**Why this matters:** Confirms network connectivity for package downloads

**Partition the Disk:**

```bash
cfdisk /dev/sda
```

**What this does:** Opens a text-based partition editor  
**Why this matters:** Allows manual control over partition layout  
**Gotcha:** Replace `/dev/sda` with your actual target device

**Format Partitions:**

```bash
mkfs.fat -F32 /dev/sda1
```

**What this does:** Creates a FAT32 filesystem on the EFI partition  
**Why this matters:** UEFI systems require FAT32 for the boot partition

```bash
mkfs.ext4 /dev/sda2
```

**What this does:** Creates an ext4 filesystem on the root partition  
**Why this matters:** ext4 is the standard Linux filesystem with good performance

## Mount and Install Base System

```bash
mount /dev/sda2 /mnt
mkdir -p /mnt/boot/efi
mount /dev/sda1 /mnt/boot/efi
```

**What this does:** Mounts the root and EFI partitions  
**Why this matters:** Prepares the target filesystem for installation

```bash
reflector --country India --latest 5 --sort rate --save /etc/pacman.d/mirrorlist
```

**What this does:** Generates an optimized mirror list for faster downloads  
**Why this matters:** Improves package installation speed  
**Gotcha:** Replace "India" with your country for better speeds

```bash
pacstrap -K /mnt base linux linux-firmware
```

**What this does:** Installs the base Arch Linux system  
**Why this matters:** Provides the minimal system needed to boot

```bash
genfstab -U /mnt >> /mnt/etc/fstab
```

**What this does:** Generates filesystem table with UUIDs  
**Why this matters:** Ensures proper mounting after boot

## System Configuration

```bash
arch-chroot /mnt
```

**What this does:** Changes root into the newly installed system  
**Why this matters:** Allows configuration of the installed system

```bash
ln -sf /usr/share/zoneinfo/Asia/Kolkata /etc/localtime
hwclock --systohc
```

**What this does:** Sets the system timezone and syncs hardware clock  
**Why this matters:** Ensures correct time configuration

```bash
pacman -S nano vim
```

**What this does:** Installs text editors  
**Why this matters:** Needed for editing configuration files

**Locale Configuration:**

```bash
nano /etc/locale.gen
```

Uncomment: `en_US.UTF-8 UTF-8`

```bash
locale-gen
echo "LANG=en_US.UTF-8" > /etc/locale.conf
```

**What this does:** Configures system language settings  
**Why this matters:** Sets the default system language

**Network Configuration:**

```bash
echo "your-hostname" > /etc/hostname
```

**What this does:** Sets the system hostname  
**Why this matters:** Identifies the system on the network

Edit `/etc/hosts`:

```bash
nano /etc/hosts
```

Add:

```
127.0.0.1   localhost
::1         localhost
127.0.1.1   your-hostname.localdomain your-hostname
```

## User and Security Setup

```bash
passwd
```

**What this does:** Sets the root password  
**Why this matters:** Secures the system administrator account

```bash
pacman -S openssh networkmanager sudo grub efibootmgr base-devel git curl qemu-guest-agent
```

**What this does:** Installs essential system packages  
**Why this matters:** Provides networking, SSH, bootloader, and virtualization support

```bash
systemctl enable NetworkManager
systemctl enable sshd
systemctl enable qemu-guest-agent
```

**What this does:** Enables essential services to start at boot  
**Why this matters:** Ensures network and SSH access after reboot

## Bootloader Installation

```bash
grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=GRUB
grub-mkconfig -o /boot/grub/grub.cfg
```

**What this does:** Installs and configures the GRUB bootloader  
**Why this matters:** Makes the system bootable

```bash
exit
umount -R /mnt
reboot
```

## System Enhancements

### Swap File Configuration

```bash
sudo fallocate -l 4G /swapfile
```

**What this does:** Creates a 4GB swap file  
**Why this matters:** Provides virtual memory for better system performance

```bash
sudo chmod 600 /swapfile
```

**What this does:** Sets proper permissions on the swap file  
**Why this matters:** Security best practice for swap files

```bash
sudo mkswap /swapfile
sudo swapon /swapfile
```

**What this does:** Initializes and activates the swap file  
**Why this matters:** Makes the swap space immediately available

```bash
echo '/swapfile none swap defaults 0 0' | sudo tee -a /etc/fstab
```

**What this does:** Makes swap permanent across reboots  
**Why this matters:** Ensures swap is available after system restart

**Verification:**

```bash
free -h
swapon --show
```

### Development Environment Setup

**Install Neovim with NvChad:**

```bash
sudo pacman -S nvim
```

**What this does:** Installs Neovim text editor  
**Why this matters:** Provides a powerful, modern text editor

```bash
git clone https://github.com/NvChad/starter ~/.config/nvim
```

**What this does:** Installs NvChad configuration for Neovim  
**Why this matters:** Provides a pre-configured IDE-like experience

### Shell Environment Enhancement

**Install and Configure Zsh:**

```bash
sudo pacman -S zsh
chsh -s /bin/zsh
```

**What this does:** Installs Zsh and sets it as the default shell  
**Why this matters:** Zsh offers better features than bash

```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

**What this does:** Installs Oh My Zsh framework  
**Why this matters:** Provides themes and plugins for enhanced productivity

**Install Useful Plugins:**

```bash
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```

```bash
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

**What these do:** Add autosuggestions and syntax highlighting to Zsh  
**Why this matters:** Improves command-line productivity and reduces errors

Edit `~/.zshrc` to enable plugins:

```bash
plugins=(git zsh-autosuggestions zsh-syntax-highlighting)
```

**Install Powerlevel10k Theme:**

```bash
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/themes/powerlevel10k
```

```bash
sudo pacman -S ttf-hack-nerd
```

**What this does:** Installs a powerline-compatible font  
**Why this matters:** Required for proper theme display

Set theme in `~/.zshrc`:

```bash
ZSH_THEME="powerlevel10k/powerlevel10k"
```

```bash
exec zsh
p10k configure
```

**Additional Tools:**

```bash
sudo pacman -S fzf nnn neovim htop wget tmux fastfetch elinks yt-dlp mpv
```

**What this does:** Installs various productivity and system tools  
**Why this matters:** Provides a complete development environment
