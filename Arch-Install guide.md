Use Arch-linux.iso file as storage
Verify-Internet Connetion:
	ping -c 3 google.com

Let's Partition the Disk
	
	fdisk -l
		As you can see there are no partition are made there is only an 30gb of free space
	
	Now we have to make 3 partitions 

		/dev/sda1: EFI System Partition to install boot loader
		/dev/sda2: SWAP partition, It depends on your ram
		/dev/sda3: Linux partition, This is where we install the arch linux.

	To create a partition
		cfdisk /dev/sda
			gpt
			Free Space and select New
			Partition size: 512M ENTER
			/dev/sda1: Type EFI System
			Free Space and select New
			Parrtition size: 4GB ENTER
			/dev/sda2: Type Linux swap
			Free Space and select New
			Partition size: Left over space
			/dev/sda3: Type Linux Filesystem ENTER
			choose /dev/sda3 and write and quite

	Let's check if the  partitions are made, to check
			fdisk -l
		as you can see you have made 3 file partitions.

	Now let's trun EFI partition type, create a FAT32 file system
		mkfs.fat -F32 /dev/sda1

	Prepare the swap partiton
		mkswap /dev/sda2
		swapon /dev/sda2

	Root partition, create an ext4 file system
		mkfs.ext4 /dev/sda3

Install Arch Linux
	pacman -Syy

	mount /dev/sda3 /mnt

	pacstrap /mnt base linux linux-firmware sudo nano

Configure the insatlled Arch system
	mounting all generated partitions using fstab
		genfstab -U /mnt >> /mnt/etc/fstab
	Now open Arch linux
		arch-chroot /mnt


Install GRUB bootloader

	Install GRUB, EFIBOOTMANGER, os-prober, mtools
		pacman -S grub efibootmgr os-prober mtools

	Create Mount point for /dev/sda1 and mount it
		mkdir /boot/efi
		mount /dev/sda1 /boot/efi

	Insatll boot loader
		grub-insatll --target=x86_64-efi --bootloader-id=grub_uefi

	Finally, generate the grub.cfg file
		grub-mkconfig -o /boot/grub/grub.cfg

Setup Network Manger and wpa_supplicant
	pacman -S networkmanager wpa_supplicant


Finishing touches 
	exit
	umount -R /mnt
	reboot


To change Screen Resolution(CDL)
	nano /etc/default/grub
	LOOK FOR ' GRUB_GFXMODE=auto' and change auto to your screen size. Example 'GRUB_GFXMODE=1920x1200x32' that 32 is the pixel size.
	Then, grub-mkconfig -o /boot/grub/grub.cfg
	reboot


After reboot
	systemctl enable NetworkManager.service
	systemctl enable wpa_supplicant
	systemctl start NetworkManager.service
