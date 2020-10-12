# Instalação minimalista da distribuição Void Linux
## 11/10/2020

Este guia é um passo a passo de como instalar a distribuição Void Linux de maneira manual e bastante enxuta. É um meio alternativo de se instalar e selecionar a dedo cada componente que fará parte do seu sistema.

Quando me aventurei no Linux pela primeira vez, com o Mandrake, sempre tive a sensação de que o sistema era  um pouco cheio de pacotes e softwares redundantes. Isso vem da época que as distribuições vinham em vários CDs. Até que um dia me deparei com Slackware e comecei a entender como tudo funcionava por baixo dos panos.

Anos se passaram e com toda a experiência adquirida, finalmente achei meu lar na distribuição Gentoo. Como vocês devem saber, o Gentoo da um bocado de trabalho pra instalar e manter, mas depois que se pega o jeito você acaba até adquirindo um certo apego, pois conhece tudo no seu sistema e sabe exatamente como e porque as coisas funcionam.
Um certo dia acabei formatando sem querer a partição onde o Gentoo estava instalado e não havia nenhum backup. A idéia de começar tudo do zero, configurar e recompilar o kernel, me deixou extremamente desanimado.

Foi aí que me deparei com o Void Linux.

Simples, minimalista, rápido e sem necessidade de configurar o próprio kernel. Não senti a mínima vontade de voltar para o Gentoo, apesar de ainda me passar pela cabeça uma ou duas vezes no ano.

Eu pensei até que estaria aos poucos abandonando essa idéia de ter um sistema minimalista e simplificado. O que me fez algumas vezes migrar para distribuições mais automatizadas como Manjaro ou Fedora, mas após utilizar por duas ou três semanas acabo voltando para o Void.

Eu sempre fico com aquela sensação de falta de controle das coisas. Onde tudo acontece nos bastidores, um monte de pacotes e softwares desnecessários são instalados, serviços demais rodando, pra fazer exatamente a mesma coisa que eu fazia no Void (ou Gentoo) de maneira mais rápida e utilizando menos recursos.

Já ficou claro que esse é um texto/guia bem particular. Não significa que essa seja a maneira certa de se manter um sistema operacional e que utilizar Fedora ou Ubuntu signifique que você não tenha controle do seu sistema. Pelo contrário. Acredito que a maioria dos administradores de sistemas Linux utilizam esse tipo de distribuição.
Essa é só a maneira como cresci e me envolvi com Linux e como prefiro lidar com o meu computador.

Caso você prefira instalar rapidamente e já ter tudo pronto para utilizar, esse guia não é para você. Esse texto assume que você tem alguns conhecimentos básicos de Linux.

## Formatando e particionando

Eu prefiro utilizar outra distribuição para instalar o Void Linux, pois apesar das imagens do Void serem suficiente, gosto de ter um sistema mais completo e automatizado para fazer outras coisas enquanto instalo e também para não correr riscos de faltar algum software especĩfico. Pode ser Fedora, Ubuntu, Manjaro ou mesmo o Void Linux, fica a seu critério.

Assim que baixar a distribuição de sua escolha, crie o seu pendrive USB bootável.
No meu caso, escolhi o Manjaro XFCE.

``` {.bash}
dd if=manjaro-xfce.iso of=/dev/sdc # /dev/sdc é o meu pendrive
```

Agora é só reiniciar o computador e bootar no pendrive.

Feito isso, é hora de particionar. No Manjaro eu utilizo o GParted para facilitar o processo. Irei utilizar o sistema de arquivos btrfs para a partição raiz. Tudo ficará dessa maneira:

/dev/sda1: fat32, 600MB (partição EFI onde ficará o kernel)

/dev/sda2: ext4, 1 GB (partição /boot)

/dev/sda3: btrfs (restante do espaço onde ficará a partição raiz e diretório home)

Montando a partição raiz e criando subvolumes no sistema de arquivos btrfs:

``` {.bash}
mount -o rw,noatime,ssd,compress=zstd,space_cache,commit=120 /dev/sda3 /mnt
btrfs subvolume create /mnt/@
btrfs subvolume create /mnt/@home
btrfs subvolume create /mnt/@snapshots
```

Agora desmonte a partição para remontarmos novamente utilizando os subvolumes:

``` {.bash}
umount /mnt
```

## Montando todas as partições e instalando Void Linux

Montando a partição raiz e subvolumes:

``` {.bash}
mount -o rw,noatime,ssd,compress=zstd,space_cache,commit=120,subvol=@ /dev/sda3 /mnt
mkdir /mnt/{home,snapshots}
mount -o rw,noatime,ssd,compress=zstd,space_cache,commit=120,subvol=@home /dev/sda3 /mnt/home
mount -o rw,noatime,ssd,compress=zstd,space_cache,commit=120,subvol=@snapshots /dev/sda3 /mnt/snapshots
```

Montando partição boot e efi:

``` {.bash}
mkdir /mnt/boot
mount -o rw,noatime /dev/sda2 /mnt/boot
mkdir /mnt/boot/efi
mount -o rw,noatime /dev/sda1 /mnt/boot/efi
```

Hora de instalar o sistema base. Você precisará baixar o arquivo rootfs do Void Linux no [site](https://alpha.de.repo.voidlinux.org/live/current/).
O arquivo, no meu caso, é o void-x86_64-ROOTFS-20191109.tar.xz.

Assim que finalizar o download, iremos descompactar no diretório raiz do sistema que acabamos de montar:

``` {.bash}
tar xvf void-x86_64-ROOTFS-20191109.tar.xz.tar.xz -C /mnt
```

Agora iremos entrar no novo sistema:

``` {.bash}
mount -t proc proc /mnt/proc
mount -t sysfs sys /mnt/sys
mount -o bind /dev /mnt/dev
mount -t devpts pts /mnt/dev/pts
cp -L /etc/resolv.conf /mnt/etc/
cd /mnt
chroot /mnt /bin/bash
```

Pronto! Estamos no Void Linux. O próximo passo é criar a senha para o root e ajustar as permissões:

``` {.bash}
passwd root
chown root:root / && chmod 755 /
```

Antes de seguir com a instalação, irei bloquear alguns pacotes de serem instalados.

No meu caso, não quero que sejam instalados os pacotes linux-firmware-amd, wpa_supplicant e dhcpcd.

O primeiro porque não tenho nada da AMD no meu laptop, o segundo e terceiro porque utilizarei o iwd e openresolv pra conectar o wifi. Como o iwd também tem um cliente dhcp interno, é desnecessário utilizar o dhcpcd. 

``` {.bash}
echo "ignorepkg=linux-firmware-amd" > /etc/xbps.d/10-ignore.conf
echo "ignorepkg=wpa_supplicant" >> /etc/xbps.d/10-ignore.conf
echo "ignorepkg=dhcpcd" >> /etc/xbps.d/10-ignore.conf
```

Atualizando e instalando o sistema:

``` {.bash}
xbps-install -Su xbps
xbps-install -u
xbps-install base-system
xbps-remove base-voidstrap
xbps-remove -o
```

Instalação básica completa! Agora iremos configurar.

## Configurando o sistema

Especifique o hostname em /etc/hostname e edite /etc/rc.conf com suas preferências.

Escolha a(s) localização(ões) correta(s) editando o arquivo /etc/default/libc-locales removendo o # do início da linha desejada e digite o comando a seguir:

``` {.bash}
xbps-reconfigure -f glibc-locales
```

Hora de editar o /etc/fstab.

Primeiro copie o arquivo de referência:

``` {.bash}
cp /proc/mounts /etc/fstab
```

Remova as linhas que começam com proc, sys, devtmpfs and pts.

Troque o último 0 por 1 da linha referente à partição raiz e das outras linhas troque por 2.

Agora a parte chata. Você deve substituir todos os /dev/sda*/ por seus respectivos UUIDS.

Para descobrir cada UUID, digite:

``` {.bash}
blkid -s UUID
```

Por fim, ajuste as linhas referentes ao /boot e /boot/efi para defaults,noatime e adicione a linha seguinte para montar /tmp na memória RAM.

Meu /etc/fstab ficou assim:

``` {.bash}
UUID=7c29705b-086c-49b6-a1ae-3b3dbf594dde / btrfs rw,noatime,compress=zstd:3,ssd,space_cache,commit=120,subvolid=256,subvol=/@ 0 1
UUID=7c29705b-086c-49b6-a1ae-3b3dbf594dde /home btrfs rw,noatime,compress=zstd:3,ssd,space_cache,commit=120,subvolid=257,subvol=/@home 0 2
UUID=7c29705b-086c-49b6-a1ae-3b3dbf594dde /snapshots btrfs rw,noatime,compress=zstd:3,ssd,space_cache,commit=120,subvolid=258,subvol=/@snaps
UUID=e2f9d238-fc81-44ea-b2c4-11d40ee1e49c /boot ext4 defaults,noatime 0 2
UUID=3D6A-2654 /boot/efi vfat defaults,noatime 0 2
tmpfs /tmp tmpfs defaults,nosuid,nodev 0 0
```

## Instalando e configurando o Grub e outros pacotes

Adicionaremos o módulo btrfs no dracut e também uma linha de referência ao diretório temporário:

``` {.bash}
echo 'add_dracutmodules+="btrfs"' >> /etc/dracut.conf
echo 'tmpdir=/tmp' >> /etc/dracut.conf
```

Instale o Grub:

``` {.bash}
xbps-install grub-x86_64-efi
```

Como tenho placa Nvidia e Intel no laptop, adicionei nvidia-drm.modeset=1 e i915.modeset=1 na configuração do Grub em /etc/default/grub. Pode pular esta etapa caso não tenha.

``` {.bash}
GRUB_CMDLINE_LINUX_DEFAULT="loglevel=4 slub_debug=P page_poison=1 nvidia-drm.modeset=1 i915.modeset=1"
```

Reconfigurando dracut:

``` {.bash}
dracut --force --hostonly --kver 5.8.14_1 #coloque a versão do seu kernel
```

Instalando o bootloader:

``` {.bash}
grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=void_grub --boot-directory=/boot --recheck --debug
```

Caso apareça o erro "EFI variables are not supported on this system", abra um outro terminal (que estará fora do Void Linux) e digite:

``` {.bash}
mount -B /sys/firmware/efi/efivars /mnt/sys/firmware/efi/efivars
```

Rode novamente o comando para instalar o Grub.

Vamos nos certificar de que todos os pacotes estão devidamente configurados:

``` {.bash}
xbps-reconfigure -fa
```

Instalo agora os drivers de vídeo, Xorg e também o iwd e openresolv para conectar a internet:

``` {.bash}
xbps-install void-repo-nonfree
xbps-install nvidia xf86-video-intel xf86-video-fbdev intel-video-accel mesa-vulkan-intel
xbps-install xorg-minimal xf86-input-evdev
xbps-install iwd openresolv
xbps-install NetworkManager # caso queira utilizar o NetworkManager para conectar a internet
```

O seu sistema está pronto para bootar. Pode instalar tudo o que quiser agora antes de reiniciar. Fica a seu critério.

Saindo do chroot e reiniciando:

``` {.bash}
exit
umount -R /mnt
shutdown -r now
```

## Configurando a internet e finalizando

Remova o pendrive, ligue o computador e logue como root.

Para conectar o wifi com o iwd, é necessário adicionar os serviços ao sistema.

``` {.bash}
ln -s /etc/sv/dbus /var/service/
ln -s /etc/sv/iwd /var/service/
```

Para utilizar o iwd você encontra vários tutoriais na internet, ele é muito mais estável e simples do que o wpa_supplicant. Aqui só irei ensinar como configurá-lo para utilizar o cliente dhcp interno e o openresolv.

Edite ou crie o arquivo /etc/iwd/main.conf e coloque:

``` {.bash}
[General]
EnableNetworkConfiguration=true

[Network]
NameResolvingService=resolvconf
```

Se for utilizar o NetworkManager para conectar a internet, ele também pode ser configurado para utilizar o iwd e openresolv.

Crie um arquivo em /etc/NetworkManager/conf.d/ com o nome de sua preferência, como rc-manager.conf por exemplo, e coloque:

``` {.bash}
[main]
rc-manager=resolvconf
```

Para utilizar o iwd crie outro arquivo em /etc/NetworkManager/conf.d/, como wifi.conf, com o seguinte conteúdo:

``` {.bash}
[device]
wifi.backend=iwd
```

Coloque o serviço para iniciar:

``` {.bash}
ln -s /etc/sv/NetworkManager /var/service/
```

Feito. Utilize o NetworkManager normalmente como utilizaria em qualquer outro sistema.

Agora é só prosseguir normalmente, criando seu usuário (coloque ele no grupo network para poder conectar a internet e utilizar o NetworkManager). Logado no seu usuário é só se conectar à internet, instalar os programas que quiser e por fim começar a utilizar uma das distribuições Linux mais enxutas e rápidas atualmente!
