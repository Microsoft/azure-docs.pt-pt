---
title: Recuperar VMs do Linux usando chroot em que o LVM (Gerenciador de volume lógico) é usado-VMs do Azure
description: Recuperação de VMs do Linux com LVMs.
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
ms.openlocfilehash: 9c3f054a1bae745e4ee7ce9e3bddca3c9bf31083
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74536006"
---
# <a name="troubleshooting-a-linux-vm-when-there-is-no-access-to-the-azure-serial-console-and-the-disk-layout-is-using-lvm-logical-volume-manager"></a>Solução de problemas de uma VM do Linux quando não há acesso ao console serial do Azure e o layout do disco está usando o LVM (Gerenciador de volume lógico)

Este guia de solução de problemas é de benefício para cenários em que uma VM Linux não está inicializando, o SSH não é possível e o layout do sistema de arquivos subjacente está configurado com o LVM (Logical Volume Manager).

## <a name="take-snapshot-of-the-failing-vm"></a>Tirar instantâneo da VM com falha

Tire um instantâneo da VM afetada. 

O instantâneo será então anexado a uma VM de **resgate** . Siga as instruções [aqui](https://docs.microsoft.com/azure/virtual-machines/linux/snapshot-copy-managed-disk#use-azure-portal) sobre como tirar um **instantâneo**.

## <a name="create-a-rescue-vm"></a>Criar uma VM de resgate
Normalmente, é recomendável uma VM de resgate igual ou versão de sistema operacional semelhante. Usar a mesma **região** e **grupo de recursos** da VM afetada

## <a name="connect-to-the-rescue-vm"></a>Conectar-se à VM de resgate
Conecte-se usando SSH na VM de **resgate** . Eleve privilégios e torne-se superusuário usando

`sudo su -`

## <a name="attach-the-disk"></a>Anexar o disco
Anexe um disco à VM de **resgate** feita a partir do instantâneo tirado anteriormente.

Portal do Azure-> selecionar a VM de **resgate** - **discos** de > 

![Criar disco](./media/chroot-logical-volume-manager/create-disk-from-snap.png)

Preencha os campos. Atribua um nome ao novo disco, selecione o mesmo grupo de recursos que o instantâneo, a VM afetada e a VM de resgate.

O **tipo de origem** é **instantâneo** .
O **instantâneo de origem** é o nome do **instantâneo** criado anteriormente.

![criar disco 2](./media/chroot-logical-volume-manager/create-disk-from-snap-2.png)

Crie um ponto de montagem para o disco anexado.

`mkdir /rescue`

Execute o comando **fdisk-l** para verificar se o disco de instantâneo foi anexado e listar todos os dispositivos e partições disponíveis

`fdisk -l`

A maioria dos cenários, o disco de instantâneo anexado será visto como **/dev/sdc** exibindo duas partições **/dev/sdc1** e **/dev/SDC2**

![Fdisk](./media/chroot-logical-volume-manager/fdisk-output-sdc.png)

O **\*** indica uma partição de inicialização, ambas as partições devem ser montadas.

Execute o comando **lsblk** para ver o LVMS da VM afetada

`lsblk`

![Executar lsblk](./media/chroot-logical-volume-manager/lsblk-output-mounted.png)


Verifique se LVMs da VM afetada são exibidos.
Caso contrário, use os comandos abaixo para habilitá-los e executar novamente **lsblk**.
Certifique-se de que o LVMs do disco anexado esteja visível antes de continuar.

```
vgscan --mknodes
vgchange -ay
lvscan
mount –a
lsblk
```

Localize o caminho para montar o volume lógico que contém a partição/(raiz). Ele tem os arquivos de configuração, como/etc/default/grub

Neste exemplo, pegar a saída do comando **lsblk** anterior **rootvg-rootlv** é a **raiz** correta de LV para montar e pode ser usada no próximo comando.

A saída do próximo comando mostrará o caminho para montar para o LV **raiz**

`pvdisplay -m | grep -i rootlv`

![Rootlv](./media/chroot-logical-volume-manager/locate-rootlv.png)

Vá para montar este dispositivo no diretório/Rescue

`mount /dev/rootvg/rootlv /rescue`

Montar a partição que tem o **sinalizador de inicialização** definido em/Rescue/boot

`
mount /dev/sdc1 /rescue/boot
`

Verifique se os sistemas de arquivos do disco anexado agora estão montados corretamente usando o comando **lsblk**

![Executar lsblk](./media/chroot-logical-volume-manager/lsblk-output-1.png)

ou o comando **DF-ésimo**

![Ativo](./media/chroot-logical-volume-manager/df-output.png)

## <a name="gaining-chroot-access"></a>Obtendo acesso chroot

Obter acesso **chroot** , que permitirá que você execute várias correções, pequenas variações existem para cada distribuição do Linux.

```
 cd /rescue
 mount -t proc proc proc
 mount -t sysfs sys sys/
 mount -o bind /dev dev/
 mount -o bind /dev/pts dev/pts/
 chroot /rescue
```

Se ocorrer um erro, como:

**chroot: falha ao executar o comando '/bin/bash ': esse arquivo ou diretório não existe**

tentativa de montar o volume lógico **usr**

`
mount  /dev/mapper/rootvg-usrlv /rescue/usr
`

> [!TIP]
> Ao executar comandos em um ambiente **chroot** , observe que eles são executados no disco do sistema operacional anexado e não na VM de **resgate** local. 

Os comandos podem ser usados para instalar, remover e atualizar o software. Solucionar problemas de VMs a fim de corrigir erros.


Execute o comando lsblk e o/Rescue agora é/e/Rescue/boot é/boot ![chrooted](./media/chroot-logical-volume-manager/chrooted.png)

## <a name="perform-fixes"></a>Executar correções

### <a name="example-1---configure-the-vm-to-boot-from-a-different-kernel"></a>Exemplo 1: configurar a VM para inicializar a partir de um kernel diferente

Um cenário comum é forçar a inicialização de uma VM a partir de um kernel anterior, pois o kernel instalado atual pode ter sido corrompido ou uma atualização não foi concluída corretamente.


```
cd /boot/grub2

grep -i linux grub.cfg

grub2-editenv list

grub2-set-default "CentOS Linux (3.10.0-1062.1.1.el7.x86_64) 7 (Core)"

grub2-editenv list

grub2-mkconfig -o /boot/grub2/grub.cfg
```

*instruções*

O comando **grep** lista os kernels dos quais o **grub. cfg** está ciente.
![kernels](./media/chroot-logical-volume-manager/kernels.png)

**Grub2-editenv lista** exibe qual kernel será carregado na próxima inicialização ![padrão do kernel](./media/chroot-logical-volume-manager/kernel-default.png)

**Grub2-Set-Default** é usado para alterar para outro kernel ![grub2 definido](./media/chroot-logical-volume-manager/grub2-set-default.png)

**Grub2-editenv** lista exibe qual kernel será carregado na próxima inicialização ![novo kernel](./media/chroot-logical-volume-manager/kernel-new.png)

**Grub2-mkconfig** recompila o grub. cfg usando as versões necessárias ![grub2 mkconfig](./media/chroot-logical-volume-manager/grub2-mkconfig.png)



### <a name="example-2---upgrade-packages"></a>Exemplo 2-atualizar pacotes

Uma atualização do kernel com falha pode renderizar a VM não inicializável.
Montar todos os volumes lógicos para permitir que os pacotes sejam removidos ou reinstalados

Execute o comando **LVS** para verificar quais **LVS** estão disponíveis para montagem, todas as VMs, que foram migradas ou vêm de outro provedor de nuvem variam na configuração.

Saia do ambiente **chroot** montar o **LV** necessário

![Segurança](./media/chroot-logical-volume-manager/advanced.png)

Agora acesse o ambiente **chroot** novamente executando

`chroot /rescue`

Todos os LVs devem ser visíveis como partições montadas

![Segurança](./media/chroot-logical-volume-manager/chroot-all-mounts.png)

Consultar o **kernel** instalado

![Segurança](./media/chroot-logical-volume-manager/rpm-kernel.png)

Se necessário, atualize o **kernel**
![avançado](./media/chroot-logical-volume-manager/rpm-remove-kernel.png)


### <a name="example-3---enable-serial-console"></a>Exemplo 3 – habilitar o console serial
Se o acesso não foi possível para o console serial do Azure, verifique os parâmetros de configuração do GRUB para sua VM Linux e corrija-os. Informações detalhadas podem ser encontradas [neste documento](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-proactive-configuration)


## <a name="exit-chroot-and-swap-the-os-disk"></a>Sair do chroot e trocar o disco do sistema operacional

Depois de reparar o problema, vá para desmontar e desanexe o disco da VM de resgate, permitindo que ele seja trocado pelo disco do sistema operacional da VM afetado.

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

Desanexe o disco da VM de resgate e execute uma troca de disco.

Selecione a VM nos **discos** do portal e selecione **desanexar**
![desanexar disco](./media/chroot-logical-volume-manager/detach-disk.png) 

Salve as alterações ![salvar desanexar](./media/chroot-logical-volume-manager/save-detach.png) 

O disco agora ficará disponível, permitindo que ele seja trocado pelo disco do sistema operacional original da VM afetada.

Navegue na portal do Azure para a VM com falha e selecione **discos** -> **disco do so de permuta**
![disco de permuta](./media/chroot-logical-volume-manager/swap-disk.png) 

Preencha os campos a **escolha disco** é o disco de instantâneo que acabou de ser desanexado na etapa anterior. O nome da VM da VM afetada também é necessário e, em seguida, selecione **OK**

![Novo disco do sistema operacional](./media/chroot-logical-volume-manager/new-osdisk.png) 

Se a VM estiver em execução, a permuta de disco será desligada, reinicializará a VM depois que a operação de permuta de disco for concluída.


## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre o [console serial do Azure]( https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux)
