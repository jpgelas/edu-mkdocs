
# Introduction au systèmes embarqués

> Responsable : Jean-Patrick Gelas < jean-patrick.gelas@univ-lyon1.fr >

- - - 

## TP1 - Outil de diagnostic mémoire embarqué sur clé USB

**Travail à rendre :** Un script Shell à déposer sur *Tomuss*.

Ce premier exercice propose de construire un mini système logiciel destiné à
réaliser une tâche unique : diagnostiquer l'état de la mémoire de n'importe
quelle machine équipée d'un processeur de la famille x86.

Nous nous baserons principalement sur les outils suivants :

  - memtest86+ [http://www.memtest.org](http://www.memtest.org)
  - syslinux (`apt update && apt install syslinux`)

ainsi que les outils `dd`, `mkfs.msdos`, `mount` et `umount`.

Les premiers tests pourront être réalisés sur *QEMU* puis sur une clé USB.

Étapes à suivre :

  - Créez un fichier (rempli de zéros) de 512 kB (`dd`). Nommez le `boot.img`
  - Formatez le au format *ms-dos* (`mkfs.msdos`)
  - Montez ce système de fichiers encore vide via un loop device (`mount -o loop`)
  - Copiez-y à la racine le binaire `memtest86+-x.xx.bin` (pensez à le renommer).
  - Créez-y le fichier de configuration `syslinux.cfg` et le fichier d'accueil
    `display.txt` qui contiendra au minimum le texte *Press enter* par exemple.

Pour vous aider voici un exemple de `syslinux.cfg`.

```
    display display.txt
    default memtest
    label memtest
      kernel memtest
    implicit 0
    prompt 1
    timeout 80
```

Rendez `boot.img` bootable (syslinux)

Testez avec QEMU. Pensez à démonter l'image avant.

Rédigez un script shell qui automatise les étapes à suivre pour la création de
`boot.img` ou de la clé USB.

- - - 

## TP2 - Elagage (initrd)

**Objectif :** Be the smallest! :-)

Faire une démo à votre enseignant qui relèvera la taille de votre image *initrd*.

L'objectif de ce TP est de réduire au minimum l'empreinte mémoire d'un système
de fichiers stocké dans un initrd. Dans un premier temps à coup de `rm` vous ne
garderez que le strict minimum. Puis proposez des solutions pour aller au delà
du résultat obtenu précédemment.

L'étudiant (ou le binôme) qui obtiendra le plus petit *initrd* gagne !

**Ingrédients**

Récupérez ce noyau Linux relativement minimaliste compilé avec le support
de l'initrd

`wget http://perso.univ-lyon1.fr/jean-patrick.gelas/doc/sle/bzImage-3.16-jpgelas`

Récuperez le fichier initrd initial (issu d'une distribution Debian et modifié par mes soins)

`wget http://perso.univ-lyon1.fr/jean-patrick.gelas/doc/sle/BIG-initrd.img-3.16.0-4-686-pae` 

**Premier test**

Sans avoir encore modifié l'initrd testez (avec Qemu) le noyau et l'initrd
fraichement récuperés.  

```
qemu-system-x86_64 -kernel ./bzImage-3.16-jpgelas \
           -initrd ./BIG-initrd.img-3.16.0-4-686-pae \
           -append "quiet loglevel=3"
```

Vous devriez obtenir une machine (virtuelle) qui boote sur un noyau Linux, puis
pose une question en boucle.

**Comment modifier le contenu du fichier initrd ?**

```
  mkdir initrd
  cd initrd
  gzip -dc ../initrd-big.img | cpio -id
```
**Comment reconstruire le fichier initrd ?**

```
  cd initrd
  find ./ | cpio -H newc -o > ../new-initrd.cpio
  cd ..
  gzip -9 new-initrd.cpio
  mv new-initrd.cpio.gz new-initrd.img
```

**Comment tester l'initrd modifié ?**

```
qemu-system-x86_64  -kernel ./bzImage-3.16-jpgelas \                   
            -initrd ./new-initrd.img \
                    -append "quiet loglevel=3"
```

- - -

 

## TP3 - Clé USB (Green PDF Viewer / Framebuffer)

**Ingrédients**

 - Une clé USB
 - Une connexion réseau et un peu de patience...

**Objectif 1**

L'objectif principal de cette séance de TP est d'installer une distribution
Debian minimaliste. Installez la sur votre clé USB en vous appuyant sur
*debootstrap* (utilisez une version stable (jessie ?)). Faites alors en sorte
que votre clé devienne bootable (utilisez GRUB).  

**Busybox** (optionnel)

Ensuite, téléchargez [Busybox](http://www.busybox.net). Configurez le pour
obtenir toutes les commandes et outils dont vous souhaitez disposer. Enfin,
compilez et installez le sur votre clé USB.  

**Réseau** (optionnel)

Puis, activez (ou configurez) les fonctions d'accés au réseau (IP) afin que de
votre systeme embarqué sur la clé USB vous puissiez accédez au réseau.
Installez y ensuite *dropbear* (client/serveur ssh minimaliste) pour que vous
puissiez vous connectez à votre système via *ssh*. Remarquez qu'avec *Qemu* il
est possible de connectez la machine virtuelle au réseau physique via un switch
virtuel.

**Un peu d'aide...**

Voici un mini tutoriel des étapes à suivre.

  -  passez root : `sudo -i` ou `su -`
  -  créez ou assurez vous d'avoir une partition primaire : `fdisk /dev/sdb`
  -  formatez cette partition : `mkfs.ext4 /dev/sdb1`
  -  creez un point de montage : `mkdir fs`
  -  montez la partition : `mount /dev/sdb1 fs`
  -  installez debootstrap : `apt install debootstrap`
  -  Lancez debootstrap : `debootstrap --arch amd64 jessie fs http://ftp.fr.debian.org/debian`
   
  -  Liez le `/proc` et le `/dev` de votre poste à ceux de la clé usb :

```sh
mount -t proc none fs/proc
mount -o bind /dev fs/dev
```

  - chrootez : `chroot fs`
  - Initialisez un mot de passe au compte root : `passwd`
  - Installez un noyau : `apt-get update && apt-get install linux-image-amd64`
  - Recuperez l'UUID de la clé USB : `blkid`
  - Editez /etc/fstab : `vim.tiny /etc/fstab`

```
proc /proc proc defaults
UUID=xxxxxxxxxxx    /    ext4 errors=remount-ro 0 1
```

  - Initialisez le hostname : `echo "MyLittleKey" > /etc/hostname`
  - Editez le fichier `/etc/network/interfaces`

```
auto lo
iface lo inet loopback

allow-hotplug eth0
auto eth0
iface eth0 inet dhcp
```

   - Supportez un clavier *azerty* : `apt install console-data`
   - (toujours chroote) installez grub2 : `apt install grub2` (sur /dev/sdb)
   - quittez le chroot : `exit`
   - démontez les trois montage : `umount fs/{dev,proc} ; umount fs`
   - testez : `qemu-system-x86_64 /dev/sdb`
   - Connectez vous en tant que root
   - Stoppez proprement avec : `shutdown -h now`

Dans Qemu pour installer de nouveaux packages :

   - Positionnez la var d'env. : `export http_proxy="http://10.......:3128"`
   - Testez : `apt-get update`
   - Nettoyez les archives en cache : `apt-get clean` 

Lien utile

[http://debian-facile.org/doc:install:debootstrap-chroot](http://debian-facile.org/doc:install:debootstrap-chroot)


**Objectif 2**

  - Activez le mode *framebuffer* cela évitera d'avoir à installer un serveur X trop lourd pour ce type de projet.
  - Téléchargez, compilez et installer le binaire *green* sur votre clé [Green PDF viewer](https://github.com/schandinat/green)
  - Faites en sorte que lorsque la machine boot sur votre clé USB, un document
    PDF de votre choix s'affiche à l'écran (sans avoir à toucher au clavier !)
  - Faites une démo à votre enseignant.

**Un peu d'aide**

[Comment installer le Green PDF viewer](https://blog.madadipouya.com/2018/01/29/how-to-install-green-pdf-viewer-on-ubuntu-16-04/)


