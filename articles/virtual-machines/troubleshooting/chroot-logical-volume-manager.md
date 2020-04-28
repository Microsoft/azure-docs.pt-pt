---
title: Recupere VMs Linux usando chroot onde LVM (Gestor de Volume Lógico) é usado - VMs Azure
description: Recuperação de VMs Linux com LVMs.
services: virtual-machines-linux
documentationcenter: ''
author: vilibert
manager: spogge
editor: ''
tags: Linux chroot LVM
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/24/2019
ms.author: vilibert
ms.openlocfilehash: 20d710f717a9dff26f46ac7a201a9b694f3fbe84
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "74684139"
---
# <a name="troubleshooting-a-linux-vm-when-there-is-no-access-to-the-azure-serial-console-and-the-disk-layout-is-using-lvm-logical-volume-manager"></a>Resolução de problemas de um VM Linux quando não há acesso à consola de série Azure e o layout do disco está a usar LVM (Gestor de Volume Lógico)

Este guia de resolução de problemas é benéfico para cenários em que um VM Linux não está a arrancar, o ssh não é possível e o layout do sistema de ficheiros subjacente é configurado com LVM (Gestor de Volume Lógico).

## <a name="take-snapshot-of-the-failing-vm"></a>Tire uma foto do VM falhado

Tire uma foto do VM afetado. 

O instantâneo será então anexado a um VM **de resgate.** Siga as instruções [aqui](https://docs.microsoft.com/azure/virtual-machines/linux/snapshot-copy-managed-disk#use-azure-portal) sobre como tirar uma **foto**.

## <a name="create-a-rescue-vm"></a>Criar um VM de resgate
Normalmente recomenda-se um VM de resgate da mesma versão do sistema operativo ou similar. Utilize a mesma **região** e grupo de **recursos** do VM afetado

## <a name="connect-to-the-rescue-vm"></a>Ligue-se ao VM de resgate
Ligue-se usando ssh no VM **de resgate.** Elevar privilégios e tornar-se super utilizador usando

`sudo su -`

## <a name="attach-the-disk"></a>Anexar o disco
Prenda um disco ao **VM** de resgate feito a partir da foto tirada anteriormente.

Portal Azure -> selecione o **VM** de resgate -> **Discos** 

![Criar disco](./media/chroot-logical-volume-manager/create-disk-from-snap.png)

Povoe os campos. Atribua um nome ao seu novo disco, selecione o mesmo Grupo de Recursos que o instantâneo, a vm afetado e o VM de resgate.

O **tipo Fonte** é **Snapshot** .
O **snapshot Source** é o nome do **instantâneo** anteriormente criado.

![criar disco 2](./media/chroot-logical-volume-manager/create-disk-from-snap-2.png)

Crie um ponto de montagem para o disco anexado.

`mkdir /rescue`

Executar o comando **fdisk -l** para verificar se o disco instantâneo foi ligado e listar todos os dispositivos e divisórias disponíveis

`fdisk -l`

Na maioria dos cenários, o disco instantâneo anexado será visto como **/dev/sdc** exibindo duas divisórias **/dev/sdc1** e **/dev/sdc2**

![Disco](./media/chroot-logical-volume-manager/fdisk-output-sdc.png)

Indica **\*** uma partição de botas, ambas as divisórias devem ser montadas.

Executar o **comando lsblk** para ver os LVMs do VM afetado

`lsblk`

![Correr lsblk](./media/chroot-logical-volume-manager/lsblk-output-mounted.png)


Verifique se são apresentados LVMs do VM afetado.
Caso contrário, utilize os comandos abaixo para os ativar e reexecutar **o lsblk**.
Certifique-se de que os LVMs do disco em anexo são visíveis antes de prosseguir.

```
vgscan --mknodes
vgchange -ay
lvscan
mount –a
lsblk
```

Localize o caminho para montar o Volume Lógico que contém a partição /(raiz). Tem os ficheiros de configuração tais como /etc/padrão/grub

Neste exemplo, tirar a saída do comando **de lsblk** **rootvg-rootlv** é a **raiz** correta para montar e pode ser usada no comando seguinte.

A saída do próximo comando mostrará o caminho a montar para a **raiz** LV

`pvdisplay -m | grep -i rootlv`

![Porta-enxertos](./media/chroot-logical-volume-manager/locate-rootlv.png)

Proceda ao montar este dispositivo no diretório/salvamento

`mount /dev/rootvg/rootlv /rescue`

Monte a divisória que tem a bandeira da **bota** colocada em /rescue/boot

`
mount /dev/sdc1 /rescue/boot
`

Verifique se os sistemas de ficheiros do disco em anexo estão agora corretamente montados utilizando o comando **lsblk**

![Correr lsblk](./media/chroot-logical-volume-manager/lsblk-output-1.png)

ou o comando **df -Th**

![Df](./media/chroot-logical-volume-manager/df-output.png)

## <a name="gaining-chroot-access"></a>Obtendo acesso chroot

Obtenha acesso **chroot,** o que lhe permitirá executar várias correções, existem ligeiras variações para cada distribuição linux.

```
 cd /rescue
 mount -t proc proc proc
 mount -t sysfs sys sys/
 mount -o bind /dev dev/
 mount -o bind /dev/pts dev/pts/
 chroot /rescue
```

Se for experimentado um erro como:

**chroot: não executou o comando '/bin/bash': Não existe tal ficheiro ou diretório**

tentar montar o volume lógico **usr**

`
mount  /dev/mapper/rootvg-usrlv /rescue/usr
`

> [!TIP]
> Ao executar comandos em ambiente **de chroot,** note que são executados contra o Disco osso anexado e não o VM de **resgate** local. 

Os comandos podem ser utilizados para instalar, remover e atualizar o software. VMs de resolução de problemas para corrigir erros.


Execute o comando de Lsblk e o /rescue é ![agora / e /rescue/boot is /boot Chrooted](./media/chroot-logical-volume-manager/chrooted.png)

## <a name="perform-fixes"></a>Executar Correções

### <a name="example-1---configure-the-vm-to-boot-from-a-different-kernel"></a>Exemplo 1 - configure o VM para arrancar de um núcleo diferente

Um cenário comum é forçar um VM a arrancar de um núcleo anterior, uma vez que o núcleo instalado atual pode ter-se tornado corrupto ou uma atualização não foi concluída corretamente.


```
cd /boot/grub2

grep -i linux grub.cfg

grub2-editenv list

grub2-set-default "CentOS Linux (3.10.0-1062.1.1.el7.x86_64) 7 (Core)"

grub2-editenv list

grub2-mkconfig -o /boot/grub2/grub.cfg
```

*walkthrough*

O comando **grep** lista os núcleos que **grub.cfg** está ciente.
![Kernels](./media/chroot-logical-volume-manager/kernels.png)

mostra a **lista grub2-editenv** que o ![kernel será carregado na próxima bota Kernel padrão](./media/chroot-logical-volume-manager/kernel-default.png)

**grub2-set-default** é usado para mudar ![para outro conjunto grub2 kernel](./media/chroot-logical-volume-manager/grub2-set-default.png)

mostra a lista **grub2-editenv** que o ![kernel será carregado na próxima bota Novo kernel](./media/chroot-logical-volume-manager/kernel-new.png)

**grub2-mkconfig** reconstrói grub.cfg usando as ![versões necessárias Grub2 mkconfig](./media/chroot-logical-volume-manager/grub2-mkconfig.png)



### <a name="example-2---upgrade-packages"></a>Exemplo 2 - pacotes de upgrade

Uma atualização de kernel falhada pode tornar o VM insaciável.
Monte todos os Volumes Lógicos para permitir que as embalagens sejam removidas ou reinstaladas

Executar o comando **lvs** para verificar quais **os LVs** disponíveis para montagem, cada VM, que tenha sido migrado ou venha de outro Fornecedor de Nuvem variará na configuração.

Saia do ambiente **chroot** monte o **LV** necessário

![Avançado](./media/chroot-logical-volume-manager/advanced.png)

Agora aceda ao ambiente **chroot** novamente, correndo

`chroot /rescue`

Todos os LVs devem ser visíveis como divisórias montadas

![Avançado](./media/chroot-logical-volume-manager/chroot-all-mounts.png)

Consulta do **núcleo** instalado

![Avançado](./media/chroot-logical-volume-manager/rpm-kernel.png)

Se necessário, remova ou atualize o **kernel**
![Advanced](./media/chroot-logical-volume-manager/rpm-remove-kernel.png)


### <a name="example-3---enable-serial-console"></a>Exemplo 3 - ativar consola em série
Se o acesso não tiver sido possível à consola em série Azure, verifique os parâmetros de configuração GRUB para o seu VM Linux e corrija-os. Informações detalhadas podem ser encontradas [neste doc](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-proactive-configuration)

### <a name="example-4---kernel-loading-with-problematic-lvm-swap-volume"></a>Exemplo 4 - carregamento de kernel com volume de swap lvm problemático

Um VM pode falhar no arranque e cair na solicitação de **dacut.**
Mais detalhes sobre a falha podem ser localizados a partir de consola em série Azure ou navegar para o portal Azure - > diagnósticos de arranque - > registo em série


Um erro semelhante a este pode estar presente:

```
[  188.000765] dracut-initqueue[324]: Warning: /dev/VG/SwapVol does not exist
         Starting Dracut Emergency Shell...
Warning: /dev/VG/SwapVol does not exist
```

A grub.cfg está configurada neste exemplo para carregar um LV com o nome de **rd.lvm.lv=VG/SwapVol** e o VM não consegue localizá-lo. Esta linha mostra como o núcleo está sendo carregado fazendo referência ao LV SwapVol

```
[    0.000000] Command line: BOOT_IMAGE=/vmlinuz-3.10.0-1062.4.1.el7.x86_64 root=/dev/mapper/VG-OSVol ro console=tty0 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0 biosdevname=0 crashkernel=256M rd.lvm.lv=VG/OSVol rd.lvm.lv=VG/SwapVol nodmraid rhgb quiet
[    0.000000] e820: BIOS-provided physical RAM map:
```

 Retire o LV ofensivo da configuração /etc/predefinido/grub e reconstrua grub2.cfg


## <a name="exit-chroot-and-swap-the-os-disk"></a>Sair chroot e trocar o disco OS

Depois de reparar o problema, proceda à desmontagem e retire o disco do VM de resgate, permitindo que seja trocado com o disco VM OS afetado.

```
exit
cd /
umount /rescue/proc/
umount /rescue/sys/
umount /rescue/dev/pts
umount /rescue/dev/
umount /rescue/boot
umount /rescue
```

Retire o disco do VM de resgate e realize uma troca de discos.

Selecione o VM a partir do portal **Disks** e selecione disprendimento de **descolação**
![](./media/chroot-logical-volume-manager/detach-disk.png) 

Guardar as ![alterações Salvar o desapego](./media/chroot-logical-volume-manager/save-detach.png) 

O disco ficará agora disponível permitindo que seja trocado com o disco osso original do VM afetado.

Navegue no portal Azure até ao VM em falha e selecione **disks** -> **Swap OS Disk**
![Swap Disk Swap disk](./media/chroot-logical-volume-manager/swap-disk.png) 

Complete os campos o **disco Escolha** é o disco instantâneo apenas desconectado no passo anterior. O nome VM do VM afetado também é necessário e, em seguida, selecione **OK**

![Novo disco os](./media/chroot-logical-volume-manager/new-osdisk.png) 

Se o VM estiver a executar a troca de discos, irá desligá-lo, reiniciar o VM assim que a operação de troca de discos estiver concluída.


## <a name="next-steps"></a>Passos seguintes
Mais informações sobre

 [Consola em série Azure]( https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux)

[Modo de utilizador único](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-single-user-mode)
