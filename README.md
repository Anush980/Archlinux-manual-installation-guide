

# Arch Linux Installation Manual Guide

This guide walks you through the manual installation of Arch Linux step by step. Each command is explained to ensure clarity.
```
https://archlinux.org/download/
```

# Things required:

Bootable Pendrive of Arch<br>
Laptop (CPU Optional)<br>
Know how to type<br>
Patience and Time<br>

---
If you don't know how to make bootable pendrive then switch back to window,You are Incapable for using linux.
---

## Step 1: Connect to Wi-Fi Using `iwctl`

1. Start `iwctl`:
   ```bash
   iwctl
   device list
   ```
   Launches the interactive wireless configuration tool.<br>
   Device list will your wireless device name

3. Scan for available networks:
   ```bash
   station <your_interface> scan
   ```
   Scans for Wi-Fi networks using the `your interface` interface.

4. List available networks:
   ```bash
   station <your_interface> get-networks
   ```
   Displays the Wi-Fi networks detected.

5. Connect to a Wi-Fi network:
   ```bash
   station <your_interface> connect <wifi_name>
   ```
   Replace `<wifi_name>` with your Wi-Fi network's name. Enter the passphrase when prompted.

6. Exit `iwctl`:
   ```bash
   exit
   ```

7. Verify the connection:
   ```bash
   ping google.com
   ```
   Use `Ctrl+C` to stop the ping activity.

---

## Step 2: Partition the Disk

1. List available disks:
   ```bash
   fdisk -l
   ```
   Displays information about your disks and partitions.

2. Create partitions:
   ```bash
   gdisk /dev/<disk>
   ```
   Replace `<disk>` with your disk (e.g., `sda` or `nvme`).

   - Press `n` and hit Enter to create a new partition.
     - Set the **last sector** to `+1G`.
     - Enter the hex code: `ef00` (EFI System Partition).
   - Press `n` again for the next partition.
     - Set the **last sector** to `+8G` (or adjust based on swap size).
     - Enter the hex code: `8200` (Linux Swap).
   - Press `n` again for the final partition.
     - Leave all values as default for the root partition.
   - Press `w` and `y` to write the changes.

---

## Step 3: Format and Mount Partitions

1. Format the partitions:
   ```bash
   lsblk
   mkfs.fat -F32 /dev/<efi_partition|sda1>
   mkswap /dev/<swap_partition|sda2>
   mkfs.ext4 /dev/<root_partition|sda3>
   ```
   Replace `<efi_partition>`, `<swap_partition>`, and `<root_partition>` with their respective device names.

2. Mount the partitions:
   ```bash
   mount /dev/<root_partition|sda3> /mnt
   mkdir /mnt/boot
   mount /dev/<efi_partition|sda1> /mnt/boot
   swapon /dev/<swap_partition|sda2>
   ```

---

## Step 4: Install the Base System

1. Install base packages:
   ```bash
   pacstrap -K /mnt base linux-zen linux-firmware linux-zen-headers
   ```

2. Generate `fstab`:
   ```bash
   genfstab -U /mnt >> /mnt/etc/fstab
   ```

3. Change root into the new system:
   ```bash
   arch-chroot /mnt
   ```

---

## Step 5: Configure the System

1. Install necessary tools:
   ```bash
   pacman -S sudo vim nano
   ```

2. Set locale:
   ```bash
   nano /etc/locale.gen
   ```
   Uncomment `en_US.UTF-8 UTF-8`. Save and exit.
   ```bash
   nano /etc/locale.conf
   ```
   Add: `LANG=en_US.UTF-8`. Save and exit.
   ```bash
   locale-gen
   ```

3. Set hostname:
   ```bash
   nano /etc/hostname
   ```
   Enter your desired hostname. Save and exit.

4. Generate initial RAM disk:
   ```bash
   mkinitcpio -P
   ```

5. Set root password:
   ```bash
   passwd
   ```

6. Add a user:
   ```bash
   useradd -m -G wheel <username>
   passwd <username>
   ```

7. Enable sudo for the user:
   ```bash
   visudo
   ```
   Uncomment `%wheel ALL=(ALL:ALL) ALL`. Save and exit.
> To save and exit press escape and enter :wq or :x and enter.

---

## Step 6: Install Bootloader

1. Install GRUB:
   ```bash
   pacman -S grub efibootmgr
   ```

2. Install GRUB to the EFI directory:
   ```bash
   grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB
   grub-mkconfig -o /boot/grub/grub.cfg
   ```

---

## Optional: Install Display Manager and Desktop Environment

1. Install packages:
   ```bash
   sudo pacman -S sddm networkmanager bluez bluez-utils iwd kitty
   ```

2. Enable services:
   ```bash
   systemctl enable sddm
   systemctl enable NetworkManager
   systemctl enable bluetooth
   ```
Now, Time to setup Desktop environment.<br>
---
For Gnome Desktop environemt:
 ```bash
   pacman -S gnome gnome-extra
   reboot
   ```
For Hyprland Desktop environemt:
 ```bash
   pacman -S hyprland
   reboot
   ```
For Hyprland hyprdot configuration, please refer to the [Hyprland Repository](https://github.com/Anush980/hyprdot-config).

---

This completes the manual Arch Linux installation process. Happy exploring!

---
 ### Additional things i love to do:
```bash
 sudo nano /etc/default/grub
```
set timeout to 0 and change the code:<br>
```bash
GRUB_CMDLINE_LINUX_DEFAULT = "loglevel=3 splash"
```
Save and exit from nano.
```bash
sudo grub-mkconfig -o /boot/grub/grub.cfg
```
