---
title: Recuperar Os VMs do Linux utilizando cromo onde o LVM (Gestor de Volume Lógico) é usado - Azure VMs
description: Recuperação de LMs Linux com LVMs.
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
ms.openlocfilehash: 98514bad6a04e0c3058faf3133fc44333039ce53
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91361471"
---
# <a name="troubleshooting-a-linux-vm-when-there-is-no-access-to-the-azure-serial-console-and-the-disk-layout-is-using-lvm-logical-volume-manager"></a>Resolução de problemas de um Linux VM quando não há acesso à consola em série Azure e o layout do disco está a utilizar o LVM (Gestor de Volume Lógico)

Este guia de resolução de problemas é benéfico para cenários em que um Linux VM não está a arrancar, o ssh não é possível e o layout do sistema de ficheiros subjacente está configurado com LVM (Logical Volume Manager).

## <a name="take-snapshot-of-the-failing-vm"></a>Tire uma foto do VM falhado

Tira uma foto do VM afetado. 

O instantâneo será então anexado a um VM **de resgate.** Siga as instruções [aqui](../linux/snapshot-copy-managed-disk.md#use-azure-portal) sobre como tirar uma **foto.**

## <a name="create-a-rescue-vm"></a>Criar um VM de resgate
Normalmente é recomendado um VM de resgate da mesma versão do sistema operativo ou similar. Utilize a mesma **região** e grupo de **recursos** do VM afetado

## <a name="connect-to-the-rescue-vm"></a>Ligue-se ao VM de resgate
Ligue-se usando ssh no **VM de resgate.** Elevar os privilégios e tornar-se super utilizador usando

`sudo su -`

## <a name="attach-the-disk"></a>Prenda o disco
Fixe um disco ao **VM de salvamento** feito a partir da foto tirada anteriormente.

Portal Azure -> selecione os discos VM -> **de** **resgate** 

![Criar disco](./media/chroot-logical-volume-manager/create-disk-from-snap.png)

Povoar os campos. Atribua um nome ao seu novo disco, selecione o mesmo Grupo de Recursos que o instantâneo, vM afetado e Rescue VM.

O **tipo de Fonte** é **Snapshot** .
O **instantâneo Source** é o nome do **instantâneo** anteriormente criado.

![criar disco 2](./media/chroot-logical-volume-manager/create-disk-from-snap-2.png)

Crie um ponto de montagem para o disco anexo.

`mkdir /rescue`

Executar o comando **fdisk -l** para verificar se o disco instantâneo foi anexado e listar todos os dispositivos e divisórias disponíveis

`fdisk -l`

Na maioria dos cenários, o disco instantâneo anexo será visto como **/dev/sdc** exibindo duas divisórias **/dev/sdc1** e **/dev/sdc2**

![Fdisk](./media/chroot-logical-volume-manager/fdisk-output-sdc.png)

Indica **\*** uma partição de arranque, ambas as divisórias devem ser montadas.

Executar o comando **lsblk** para ver os LVMs dos VM afetados

`lsblk`

![Executar lsblk](./media/chroot-logical-volume-manager/lsblk-output-mounted.png)


Verifique se são apresentados LVMs dos VM afetados.
Caso contrário, utilize os comandos abaixo para os ativar e **repercutir o Isblk**.
Certifique-se de que os LVMs do disco anexo são visíveis antes de prosseguir.

```
vgscan --mknodes
vgchange -ay
lvscan
mount –a
lsblk
```

Localize o caminho para montar o Volume Lógico que contém a partição /(raiz). Tem os ficheiros de configuração tais como /etc/default/grub

Neste exemplo, tirar a saída do anterior bloco de comando **de Lsblk**  **rootvg-rootlv** é o LV **raiz** correto para montar e pode ser usado no comando seguinte.

A saída do próximo comando mostrará o caminho a seguir para o LV **raiz**

`pvdisplay -m | grep -i rootlv`

![Rootlv](./media/chroot-logical-volume-manager/locate-rootlv.png)

Proceda à montagem deste dispositivo no diretório /salvamento

`mount /dev/rootvg/rootlv /rescue`

Monte a partição que tem a **bandeira da Bota** definida em /salvamento/bota

`
mount /dev/sdc1 /rescue/boot
`

Verifique se os sistemas de ficheiros do disco anexo estão agora corretamente montados utilizando o comando **Lsblk**

![Executar lsblk](./media/chroot-logical-volume-manager/lsblk-output-1.png)

ou o comando **df -Th**

![Df](./media/chroot-logical-volume-manager/df-output.png)

## <a name="gaining-chroot-access"></a>Obtenção de acesso a cromos

Obtenha acesso **à cromo,** o que lhe permitirá executar várias correções, existem ligeiras variações para cada distribuição linux.

```
 cd /rescue
 mount -t proc proc proc
 mount -t sysfs sys sys/
 mount -o bind /dev dev/
 mount -o bind /dev/pts dev/pts/
 chroot /rescue
```

Se for cometido um erro, como:

**crove: não executou o comando '/bin/bash': Nenhum ficheiro ou diretório**

tentativa de montar o **usr** Volume Lógico

`
mount  /dev/mapper/rootvg-usrlv /rescue/usr
`

> [!TIP]
> Ao executar comandos num ambiente **de cromos,** note que são executados contra o disco oss anexado e não contra o **VM de resgate** local. 

Os comandos podem ser utilizados para instalar, remover e atualizar software. VMs de resolução de problemas para corrigir erros.


Execute o comando lsblk e o /rescue is now / and /rescue/boot is /boot ![ Screenshot shows a console window with the l blk command and his output tree.](./media/chroot-logical-volume-manager/chrooted.png)

## <a name="perform-fixes"></a>Executar Correções

### <a name="example-1---configure-the-vm-to-boot-from-a-different-kernel"></a>Exemplo 1 - configurar o VM para arrancar a partir de um núcleo diferente

Um cenário comum é forçar um VM a arrancar a partir de um núcleo anterior, uma vez que o núcleo instalado a corrente pode ter-se tornado corrupto ou uma atualização não foi concluída corretamente.


```
cd /boot/grub2

grep -i linux grub.cfg

grub2-editenv list

grub2-set-default "CentOS Linux (3.10.0-1062.1.1.el7.x86_64) 7 (Core)"

grub2-editenv list

grub2-mkconfig -o /boot/grub2/grub.cfg
```

*walkthrough*

O comando **grep** lista os núcleos que **a comida.cfg** está ciente.
![A screenshot mostra uma janela da consola que mostra o resultado de uma busca de grãos.](./media/chroot-logical-volume-manager/kernels.png)

**lista grub2-editenv** mostra quais o núcleo será carregado na próxima bota ![ Kernel padrão](./media/chroot-logical-volume-manager/kernel-default.png)

**grub2-set-default** é usado para mudar para outro ![ conjunto de kernel Grub2](./media/chroot-logical-volume-manager/grub2-set-default.png)

**grub2-editenv** lista mostra quais o núcleo será carregado na próxima bota ![ Novo kernel](./media/chroot-logical-volume-manager/kernel-new.png)

**grub2-mkconfig** reconstrói grub.cfg usando as versões necessárias ![ Grub2 mkconfig](./media/chroot-logical-volume-manager/grub2-mkconfig.png)



### <a name="example-2---upgrade-packages"></a>Exemplo 2 - pacotes de upgrade

Uma atualização falhada do núcleo pode tornar o VM insutilizável.
Monte todos os Volumes Lógicos para permitir que as embalagens sejam removidas ou reinstaladas

Executar o comando **lvs** para verificar quais **os LVs** disponíveis para montagem, cada VM, que foi migrado ou vem de outro Cloud Provider variará na configuração.

Saia do ambiente **cromoso** monte o **LV** necessário

![A screenshot mostra uma janela da consola com um comando l vs e, em seguida, montando um L V.](./media/chroot-logical-volume-manager/advanced.png)

Agora aceda ao ambiente **cromos novamente** correndo

`chroot /rescue`

Todos os LVs devem ser visíveis como divisórias montadas

![Avançado](./media/chroot-logical-volume-manager/chroot-all-mounts.png)

Consulta do **núcleo** instalado

![Avançado](./media/chroot-logical-volume-manager/rpm-kernel.png)

Se necessário remover ou atualizar o **núcleo** 
 ![ Avançado](./media/chroot-logical-volume-manager/rpm-remove-kernel.png)


### <a name="example-3---enable-serial-console"></a>Exemplo 3 - ativar a Consola em Série
Se o acesso não tiver sido possível para a consola em série Azure, verifique os parâmetros de configuração GRUB para o seu Linux VM e corrija-os. Informações detalhadas podem ser [encontradas neste doc](./serial-console-grub-proactive-configuration.md)

### <a name="example-4---kernel-loading-with-problematic-lvm-swap-volume"></a>Exemplo 4 - carregamento de núcleo com volume de swap LVM problemático

Um VM pode não conseguir arrancar completamente e cair no **pedido de dracut.**
Mais detalhes sobre a falha podem ser localizados a partir da consola em série Azure ou navegar para o portal Azure -> diagnósticos de arranque -> registo em série


Um erro semelhante a este pode estar presente:

```
[  188.000765] dracut-initqueue[324]: Warning: /dev/VG/SwapVol does not exist
         Starting Dracut Emergency Shell...
Warning: /dev/VG/SwapVol does not exist
```

A grub.cfg está configurada neste exemplo para carregar um LV com o nome de **rd.lvm.lv=VG/SwapVol** e o VM não consegue localizá-lo. Esta linha mostra como o núcleo está a ser carregado referindo-se ao LV SwapVol

```
[    0.000000] Command line: BOOT_IMAGE=/vmlinuz-3.10.0-1062.4.1.el7.x86_64 root=/dev/mapper/VG-OSVol ro console=tty0 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0 biosdevname=0 crashkernel=256M rd.lvm.lv=VG/OSVol rd.lvm.lv=VG/SwapVol nodmraid rhgb quiet
[    0.000000] e820: BIOS-provided physical RAM map:
```

 Retire o LV ofensivo da configuração /etc/default/grub e reconstrua grub2.cfg


## <a name="exit-chroot-and-swap-the-os-disk"></a>Saia da croba e troque o disco DE

Depois de reparar o problema, proceda a desmontar e desprender o disco do VM de salvamento, permitindo a sua troca com o disco VM OS afetado.

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

Retire o disco do VM de resgate e execute uma troca de discos.

Selecione o VM a partir dos **Discos do** portal e selecione **desprender** 
 ![ o disco de desprendimento](./media/chroot-logical-volume-manager/detach-disk.png) 

Guardar as alterações ![ Salvar desprendimento](./media/chroot-logical-volume-manager/save-detach.png) 

O disco ficará agora disponível permitindo a sua troca com o disco de oss original do VM afetado.

Navegue no portal Azure para o VM em falha e selecione **Discos**  ->  **Swap OS Disk**Swap 
 ![ Disk](./media/chroot-logical-volume-manager/swap-disk.png) 

Complete os campos que o **disco Choose** é o disco instantâneo que acaba de ser destacado no passo anterior. O nome VM do VM afetado também é necessário e, em seguida, selecione **OK**

![Novo disco de oss](./media/chroot-logical-volume-manager/new-osdisk.png) 

Se o VM estiver a executar o Disk Swap irá desligá-lo, reinicie o VM uma vez concluída a operação de troca de discos.


## <a name="next-steps"></a>Passos seguintes
Mais informações sobre

 [Consola em série Azure]( ./serial-console-linux.md)

[Modo de utilizador único](./serial-console-grub-single-user-mode.md)