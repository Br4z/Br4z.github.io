---
layout: single
title:  Arch - Guía de instalación (UEFI)
excerpt: "Guía básica para instalar Arch."
date: 2023-02-21
classes: wide
header:
    teaser: /assets/images/post thumbnails/3_Instalacion-Arch.png
    teaser_home_page: true
    icon: /assets/images/icons/Arch.png
categories:
    - Linux
tags:
    - guía
---

Si se te ha pasado por la cabeza instalar el "temido" Arch Linux, pero has desistido por su aparente dificultad, este post es para ti. De una forma sencilla te guiará a través del proceso de instalación de este sistema operativo para finalizar con un sistema funcional, listo para usarse.

> Te recomiendo leer toda la guía antes de proceder con los pasos.

> ADVERTENCIA: trata de verificar la información que puedas de la guía, porque puede que algunos procesos cambien en el futuro, aunque trataré de mantener la guía actualizada.

## Pasos previos

- Evitar conflicto de la hora entre Windows y Linux.

	> Esto se debe a que Linux guarda la hora en formato **UTC** mientras que Windows lo hace en **Local**, esta información es guardada en la bios, por lo que al cambiar entre sistemas, el respectivo sistema (el que se está iniciado) leerá la hora en un formato que no le corresponde, lo que ocasiona los desfases en la hora.

	A continuación mostraré dos métodos:

	> Eligen **uno y solo un método**.

	- Windows.

		1. Abrimos regedit (`WIN + r` y escribimos "regedit").

		2. Nos vamos a `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\TimeZoneInformation`.

		3. Creamos un registro `DWORD (32 bits)` con el nombre `RealTimeIsUniversal`.

		4. Asignamos el valor "1" (en Hexadecimal) el registro que acabamos de crear.

		5. Abrimos un CMD con privilegios administrativos y escribimos el siguiente comando `sc config w32time start=disabled`.

	- Linux.

		> NOTA: asegúrense de tener su sistema actualizado.

		Es tan simple como escribir el siguiente comando `sudo timedatectl set-local-rtc 1`.

> El paso anterior no es necesario si no van a tener dualboot.

- Recomiendo cambiar la fuente de la terminal para tener una mejor vista (`setfont ter-132n`).

	> Si tu pantalla no es 1920x1080, busca un tamaño de fuente inferior.

- `ls /sys/firmware/efi/efivars/` verificar si el sistema ha iniciado en modo UEFI (deberíamos ver varias líneas como resultado).

- Cargar la distribución de tu teclado.

	- Ver las distribuciones disponibles: `localectl list-keymaps`.

		> El comando `grep` te servirá para encontrar tu distribución (`localectl list-keymaps | greq <pattern>`). La distribución latinoamericana se encuentra como "la-latin1" y la estadounidense como "us".

	- Cargar la distribución: `loadkeys <keyboard distribution>`.

## Internet

- `ip addr show` nos mostrará nuestras ip's (asociadas a las diferentes interfaces de red).

Lo más fácil es hacer la instalación vía Ethernet, para no tener que configurar el Wifi.

En caso de que estés haciendo la instalación con Wifi, sigue los siguientes pasos:

1. `iwctl` inicia el prompt de la herramienta "iwd".

2. `device list` lista todos los dispositivos inalámbricos disponibles en el sistema.

	> En caso de que no aparezca su adaptador y está seguro de que tiene uno, deberá buscar cuál es la fuente del problema antes de seguir con la instalación, la recomendación es buscar en la [documentación de la herramienta](https://wiki.archlinux.org/title/iwd).

3. `station <wireless device name> scan` Escanear las redes cercanas.

4. `station <wireless device name> get-networks` Mostrar las redes escaneadas.

5. `station <wireless device name> connect "<network name>"` Para conectarse a la red (te dejará el prompt para introducir la contraseña).

6. Espera algunos segundos (3 - 4 deberían ser suficientes) y procede a salirte de la herramienta (`exit` o `CTRL + d`).

> Si no hubo algún error, al mostrar las ip's (`ip addr show` o `ip a`) su interfaz inalámbrica debería tener asociada una ip.

Para probar nuestra conexión a internet podemos escribir `ping -c 5 8.8.8.8` para enviar peticiones a las DNS de Google, y si tenemos internet recibiremos respuestas.

## Disco

Vamos a usar `fdisk` para manejar el tema de las particiones.

1. `fdisk -l` listar los discos (también podemos usar `lsbkl`).

2. `fdisk /dev/<device>` empezar el proceso en el disco seleccionado.

3. `p` listar las particiones que hay en el disco.

4. `g` crear la tabla de particiones (para el caso de UEFI es GPT).

5. Creación de la partición `EFI`

	1. `n` crear una partición.

	2. `2x Enter` dejar por defecto algunos valores de la partición.

	3. `+300M` tamaño que tendrá nuestra partición EFI.

	4. `t` cambiar el tipo de partición.

	5. `1`.

6. Creación de la partición `LVM`

	1. `n` crear una partición.

	2. `3x Enter` dejar por defecto algunos valores de la partición.

	3. `t` cambiar el tipo de partición.

	4. `Enter` seleccionar la partición.

	5. `43`.

		> Este número puede cambiar, por lo que sugiero revisar los tipos con `l` y buscar la que diga `Linux LVM`.

7. `w` para escribir las particiones.

	> Esto nos sacará de la herramienta.

8. `mkfs.fat -F32 /dev/<efi partition>`. (en mi caso fue `sda1`) para formatear la partición `EFI`.

	> Puedes ver el nombre de las particiones con `fdisk -l`.

9. Configurar `LVM`.

	1. `pvcreate --dataalignment 1m /dev/<LVM partition>` (en mi caso fue `sda2`).

	2. `vgcreate volgroup0 /dev/<LVM partition>`.

	3. `lvcreate -L 40GB volgroup0 -n lv_root`.

	4. `lvcreate -l 100%FREE volgroup0 -n lv_home` (con el `100%FREE` nos referimos al espacio restante).

		> Obviamente, los tamaños de las particiones los puedes cambiar.

		> Aunque la distribución "home" no es necesaria hacerla, es recomendable puesto que tenemos la posibilidad de rescatar nuestra información si la partición "root" (que sería donde se guardaría nuestra información como usuario si no creamos el "home") se corrompe.

	5. `modprobe dm_mod`.

	6. `vgscan`.

	7. `vgchange -ay`.

10. Formateo y montaje de particiones.

	1. `mkfs.ext4 /dev/volgroup0/lv_root`.

	2. `mount /dev/volgroup0/lv_root /mnt`.

	3. `mkfs.ext4 /dev/volgroup0/lv_home`.

	4. `mkdir /mnt/home`.

	5. `mount /dev/volgroup0/lv_home /mnt/home`.

## Estableciendo las mirrors más rápidas

1. `cp /etc/pacman.d/mirrorlist /etc/pacman.d/mirrorlist.bak`.

2. `reflector --verbose --latest 10 --protocol https --sort rate --save /etc/pacman.d/mirrorlist`.

## Instalación

1. `pacstrap -i /mnt base base-devel linux linux-headers linux-firmware lvm2 git`.

	> Podemos instalar varios kernels.

	> Si vas a usar wifi tendrás que instalar [paquetes adicionales](https://wiki.archlinux.org/title/Network_configuration/Wireless#Utilities) además de activar sus respectivos servicios.

2. `arch-chroot /mnt` entrar en nuestra instalación (refiriéndome al prompt).

3. `pacman -S neovim` para instalar un editor de texto.

4. Editamos `/etc/mkinitcpio.conf`.

	1. `nvim /etc/mkinitcpio.conf`.

	2. En `MODULES` agregamos la marca de la tarjeta gráfica que estemos usando, en mi caso agrego `nvidia`.

	3. En `HOOKS` entre `block` y `filesystems` agregamos `lvm2`.

5. `mkinitcpio -p linux`.

6. `nvim /etc/locale.gen` descomentamos `en_US.UTF-8` y `es_ES.UTF-8`.

7. `locale-gen`.

8. `echo LANG=<lang> >> /etc/locale.conf`.

	> En mi caso quiero el idioma inglés estadounidense, por lo que pongo `en_US.UTF-8` en vez de `<lang>`.

9. `echo KEYMAP=<keyboard layout> >> /etc/vconsole.conf`.

	> En mi caso tengo la distribución estadounidense, por lo que pongo `us` en vez de `<keyboard layout>`.

10. `passwd` establecer la contraseña del root.

11. `useradd -m -g users -G wheel <username>` crear tu usuario.

	> También puedes crear el usuario sin tener que agregarlo a un grupo (`useradd -m <username>`), pero en el `sudoers.d` tendrás que hacer algo diferente.

12. `passwd <username>` establecer la contraseña de tu usuario.

13. `EDITOR=nvim visudo` y descomentamos `%wheel ALL=(ALL) ALL` para que el grupo `wheel` pueda usar permisos administrativos.

	> Si escogiste solo crear el usuario, tendrás que asignarle los permisos a ese usuario, agregando `<username> ALL=(ALL) ALL` al `sudoers.d`.

14. `mkdir /mnt/etc` y `genfstab -U /mnt >> /mnt/etc/fstab`.

	> Si hicimos el paso anterior bien deberíamos ver nuestras particiones en el archivo `fstab` (para hacerlo usamos `cat /mnt/etc/fstab`).

### Bonus (opcional)

Si tienes un disco donde almacenas información general, puedes agregarlo:

1. `mkdir /mnt/files`.

2. `pacman -S ntfs-3g`.

3. `mount /dev/<disk partition> /mnt/files`.

> Recuerda regenerar el `fstab`.

La configuración de mi disco duro en mi fstab es:

```
UUID=64A6257CA625503A /home/braz/Files ntfs-3g auto,exec,users,uid=1000,gid=1000,noatime 0 2
```

## GRUB

1. `pacman -S grub efibootmgr`.

	> Si vas a hacer una instalación con en conjunto con Windows, incluye `os-prober`.

2. `mkdir /boot/EFI`.

3. `mount /dev/<efi partition> /boot/EFI` (en mi caso sda1).

4. `nvim /etc/default/grub` y descomentamos la línea `GRUB_DISABLE_OS_PROBER`.

	> Si no estás haciendo una instalación en conjunto con Windows, puedes saltarte este paso.

5. `grub-install --target=x86_64-efi --bootloader-id=grub_uefi --recheck`.

6. `mkdir /mnt/windows` y `mount /dev/<windows efi partition> /mnt/windows`.

	> Si no estás haciendo la instalación con Windows, omite este paso.

7. `grub-mkconfig -o /boot/grub/grub.cfg`.

Por último salimos (`exit`), desmontamos todo `umount -a` o `umount -lR /mnt` y reiniciamos (`reboot`).

> Recuerda quitar el medio de instalación.

## Post-Instalación

1. Creación de `swapfile`.

	> Si tiene un computador moderno (computadores que, generalmente, tienen más de 8 GB de RAM), la memoria swap no es muy necesaria.

	1. `dd if=/dev/zero of=/swapfile bs=1M count=<size in mb> status=progress`.

	2. `chmod 600 /swapfile`.

	3. `mkswap /swapfile`.

	4. `echo '/swapfile none swap sw 0 0' | tee -a /etc/fstab`.

2. Establecer zona horaria.

	1. `timedatectl list-timezones` ver las zonas horarias.

	2. `timedatectl set-timezone <time zone>` (en mi caso `America/Bogota`).

		> El comando `grep` te servirá para encontrar tu zona horaria (`timedatectl list-timezones | greq <pattern>`).

	3. `systemctl enable systemd-timesyncd`.

		> Podemos versificar si todo ha salido correctamente con `timedatectl status`.

	4. `ln -sf /usr/share/zoneinfo/<location> /etc/localtime` (en mi caso fue `America/Bogota`).

	5. `hwclock --systohc`: actualizar la hora del sistema con la del computador (a nivel de hardware).

3. Establecer HOSTNAME.

	1. `hostnamectl set-hostname <hostname>`.
	2. `nvim /etc/hosts` y agregamos:

		```bash
		127.0.0.1 localhost
		::1       localhost
		127.0.1.1 <hostname>.localdomain    <hostname>
		```

4. `pacman -S amd-ucode` instalar microdes para el procesador (si tienes un procesador Intel es `intel-ucode`).

5. `pacman -S nvidia nvidia-utils nvidia-settings` instalar drivers para tarjetas Nvidia (para AMD es `mesa` y para instalar nvidia en el kernel LTS es `nvidia-lts`).

	> `pacman -S virtualbox-guest-utils` si estás usando VirtualBox.

6. `pacman -S pipewire pipewire-pulse pipewire-alsa pipewire-jack` instalación de drivers de audio.

7. Instalación de yay.

	1. `git clone https://aur.archlinux.org/yay-bin.git`.

	2. `cd yay-bin`.

	3. `makepkg -si`.

### Display manager

> Entorno o interfaz en el que nos logueamos.

1. `pacman -S ly`.

2. `systemctl enable ly.service`.

> El archivo de configuración está en `/etc/ly`.

Felicidades hasta este punto ya tiene una instalación de Arch "mínima", lo siguiente es escoger si quieres usar un **desktop environmnet** o un **window manager**. No deberías tener problemas instalando cualquier desktop environmnet, en sus paquetes ya tienen todo lo necesario para una experiencia de usuario "completa". Estaré subiendo post sobre un set-up de [**Bspwm**](https://wiki.archlinux.org/title/Bspwm) por si quieres usar este window manager.
