---
title: Anexar um disco de dados a uma VM do Linux
description: Use o portal para anexar um disco de dados novo ou existente a uma VM do Linux.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 5e1c6212-976c-4962-a297-177942f90907
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 07/12/2018
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 78604a4f6fd5a6bcd21d0adc80c1c60278068836
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037056"
---
# <a name="use-the-portal-to-attach-a-data-disk-to-a-linux-vm"></a>Usar o portal para anexar um disco de dados a uma VM do Linux 
Este artigo mostra como anexar discos novos e existentes a uma máquina virtual Linux por meio do portal do Azure. Você também pode [anexar um disco de dados a uma VM do Windows no portal do Azure](../windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Antes de anexar discos à sua VM, examine estas dicas:

* O tamanho da máquina virtual controla quantos discos de dados você pode anexar. Para obter detalhes, consulte [tamanhos das máquinas virtuais](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Os discos anexados às máquinas virtuais são, na verdade, arquivos. vhd armazenados no Azure. Para obter detalhes, consulte nossa [introdução aos Managed disks](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Depois de anexar o disco, você precisa [se conectar à VM do Linux para montar o novo disco](#connect-to-the-linux-vm-to-mount-the-new-disk).


## <a name="find-the-virtual-machine"></a>Localizar a máquina virtual
1. Iniciar sessão no [portal do Azure](https://portal.azure.com/).
2. No menu à esquerda, clique em **máquinas virtuais**.
3. Selecione a máquina virtual na lista.
4. Para a página máquinas virtuais, em **Essentials**, clique em **discos**.
   
    ![Abrir configurações de disco](./media/attach-disk-portal/find-disk-settings.png)


## <a name="attach-a-new-disk"></a>Anexar um novo disco

1. No painel **discos** , clique em **+ adicionar disco de dados**.
2. Clique no menu suspenso para **nome** e selecione **criar disco**:

    ![Criar um disco gerenciado do Azure](./media/attach-disk-portal/create-new-md.png)

3. Insira um nome para o disco gerenciado. Examine as configurações padrão, atualize conforme necessário e, em seguida, clique em **criar**.
   
   ![Examinar as configurações de disco](./media/attach-disk-portal/create-new-md-settings.png)

4. Clique em **salvar** para criar o disco gerenciado e atualizar a configuração da VM:

   ![Salvar novo disco gerenciado do Azure](./media/attach-disk-portal/confirm-create-new-md.png)

5. Depois que o Azure cria o disco e o anexa à máquina virtual, o novo disco é listado nas configurações de disco da máquina virtual em **discos de dados**. Como os discos gerenciados são um recurso de nível superior, o disco aparece na raiz do grupo de recursos:

   ![Disco gerenciado do Azure no grupo de recursos](./media/attach-disk-portal/view-md-resource-group.png)

## <a name="attach-an-existing-disk"></a>Anexar um disco existente
1. No painel **discos** , clique em **+ adicionar disco de dados**.
2. Clique no menu suspenso para **nome** para exibir uma lista de discos gerenciados existentes acessíveis para sua assinatura do Azure. Selecione o disco gerenciado a ser anexado:

   ![Anexar um disco gerenciado do Azure existente](./media/attach-disk-portal/select-existing-md.png)

3. Clique em **salvar** para anexar o disco gerenciado existente e atualizar a configuração da VM:
   
   ![Salvar atualizações de disco gerenciado do Azure](./media/attach-disk-portal/confirm-attach-existing-md.png)

4. Depois que o Azure anexa o disco à máquina virtual, ele é listado nas configurações de disco da máquina virtual em **discos de dados**.

## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Conectar-se à VM do Linux para montar o novo disco
Para particionar, Formatar e montar seu novo disco para que sua VM do Linux possa usá-lo, use SSH em sua VM. Para obter mais informações, veja [como utilizar SSH com Linux no Azure](mac-create-ssh-keys.md). O exemplo a seguir se conecta a uma VM com a entrada DNS pública de *mypublicdns.westus.cloudapp.Azure.com* com o nome de usuário *azureuser*: 

```bash
ssh azureuser@mypublicdns.westus.cloudapp.azure.com
```

Uma vez conectado à sua VM, você estará pronto para anexar um disco. Primeiro, localize o disco usando `dmesg` (o método usado para descobrir o novo disco pode variar). O exemplo a seguir usa dmesg para filtrar em discos *SCSI* :

```bash
dmesg | grep SCSI
```

O resultado é semelhante ao seguinte exemplo:

```bash
[    0.294784] SCSI subsystem initialized
[    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
[    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
[    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
[ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
```

Aqui, *SDC* é o disco que queremos. 

### <a name="partition-a-new-disk"></a>Particionar um novo disco
Se você estiver usando um disco existente que contém dados, pule para montar o disco. Se estiver anexando um novo disco, você precisará particionar o disco.

> [!NOTE]
> É recomendável que você use as versões mais recentes do fdisk ou parcialmente disponíveis para seu distribuição.

Particione o disco com `fdisk`. Se o tamanho do disco for 2 tebibytes (TiB) ou maior, você deverá usar o particionamento GPT, poderá usar `parted` para executar o particionamento GPT. Se o tamanho do disco estiver em 2TiB, você poderá usar o particionamento MBR ou GPT. Transforme-o em um disco primário na partição 1 e aceite os outros padrões. O exemplo a seguir inicia o processo de `fdisk` em */dev/sdc*:

```bash
sudo fdisk /dev/sdc
```

Use o comando `n` para adicionar uma nova partição. Neste exemplo, também escolhemos `p` para uma partição primária e aceitamos o restante dos valores padrão. A saída será semelhante ao exemplo a seguir:

```bash
Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
Building a new DOS disklabel with disk identifier 0x2a59b123.
Changes will remain in memory only, until you decide to write them.
After that, of course, the previous content won't be recoverable.

Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-10485759, default 2048):
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-10485759, default 10485759):
Using default value 10485759
```

Imprima a tabela de partição digitando `p` e, em seguida, use `w` para gravar a tabela em disco e sair. A saída deve ser semelhante ao exemplo a seguir:

```bash
Command (m for help): p

Disk /dev/sdc: 5368 MB, 5368709120 bytes
255 heads, 63 sectors/track, 652 cylinders, total 10485760 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x2a59b123

   Device Boot      Start         End      Blocks   Id  System
/dev/sdc1            2048    10485759     5241856   83  Linux

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
```

Agora, grave um sistema de arquivos na partição com o comando `mkfs`. Especifique o tipo de sistema de arquivos e o nome do dispositivo. O exemplo a seguir cria um sistema de arquivos *ext4* na partição */dev/sdc1* que foi criada nas etapas anteriores:

```bash
sudo mkfs -t ext4 /dev/sdc1
```

O resultado é semelhante ao seguinte exemplo:

```bash
mke2fs 1.42.9 (4-Feb-2014)
Discarding device blocks: done
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
327680 inodes, 1310464 blocks
65523 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=1342177280
40 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks:
    32768, 98304, 163840, 229376, 294912, 819200, 884736
Allocating group tables: done
Writing inode tables: done
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done
```
### <a name="mount-the-disk"></a>Montar o disco
Crie um diretório para montar o sistema de arquivos usando `mkdir`. O exemplo a seguir cria um diretório em */DataDrive*:

```bash
sudo mkdir /datadrive
```

Use `mount` para montar o sistema de arquivos. O exemplo a seguir monta a partição */dev/sdc1* para o ponto de montagem */DataDrive* :

```bash
sudo mount /dev/sdc1 /datadrive
```

Para garantir que a unidade seja remontada automaticamente após uma reinicialização, ela deve ser adicionada ao arquivo */etc/fstab* . Também é altamente recomendável que o UUID (identificador universal exclusivo) seja usado em */etc/fstab* para se referir à unidade em vez de apenas ao nome do dispositivo (como, */dev/sdc1*). Se o sistema operacional detectar um erro de disco durante a inicialização, usar o UUID evita que o disco incorreto seja montado em um determinado local. Em seguida, os discos de dados restantes seriam atribuídos a essas mesmas IDs de dispositivo. Para localizar o UUID da nova unidade, use o utilitário `blkid`:

```bash
sudo -i blkid
```

A saída é semelhante ao exemplo a seguir:

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

> [!NOTE]
> A edição inadequada do arquivo **/etc/fstab** pode resultar em um sistema não inicializável. Se não tiver certeza, consulte a documentação da distribuição para obter informações sobre como editar corretamente esse arquivo. Também é recomendável que um backup do arquivo/etc/fstab seja criado antes da edição.

Em seguida, abra o arquivo */etc/fstab* em um editor de texto da seguinte maneira:

```bash
sudo vi /etc/fstab
```

Neste exemplo, use o valor UUID para o dispositivo */dev/sdc1* que foi criado nas etapas anteriores e o mountpoint de */DataDrive*. Adicione a seguinte linha ao final do arquivo */etc/fstab* :

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```
Quando terminar, salve o arquivo */etc/fstab* e reinicialize o sistema.
> [!NOTE]
> Posteriormente, remover um disco de dados sem editar fstab pode fazer com que a VM falhe na inicialização. A maioria das distribuições fornece as opções fstab *nofail* e/ou *nobootwait* . Essas opções permitem que um sistema inicialize mesmo que o disco não seja montado no momento da inicialização. Consulte a documentação da sua distribuição para obter mais informações sobre esses parâmetros.
> 
> A opção *nofail* garante que a VM seja iniciada mesmo que o sistema de arquivos esteja corrompido ou o disco não exista no momento da inicialização. Sem essa opção, você pode encontrar um comportamento conforme descrito em [não pode SSH para VM Linux devido a erros de fstab](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/)

### <a name="trimunmap-support-for-linux-in-azure"></a>Suporte para APARAr/cancelar mapeamento para Linux no Azure
Alguns kernels do Linux dão suporte a operações de corte/desmapeamento para descartar blocos não utilizados no disco. Esse recurso é útil principalmente no armazenamento Standard para informar ao Azure que as páginas excluídas não são mais válidas e podem ser descartadas e podem economizar dinheiro se você criar arquivos grandes e, em seguida, excluí-los.

Há duas maneiras de habilitar o suporte a corte em sua VM Linux. Como de costume, consulte sua distribuição para obter a abordagem recomendada:

* Use a opção de montagem `discard` em */etc/fstab*, por exemplo:

    ```bash
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2
    ```
* Em alguns casos, a opção `discard` pode ter implicações de desempenho. Como alternativa, você pode executar o comando `fstrim` manualmente na linha de comando ou adicioná-lo ao crontab para ser executado regularmente:
  
    **Ubuntu**
  
    ```bash
    sudo apt-get install util-linux
    sudo fstrim /datadrive
    ```
  
    **RHEL/CentOS**

    ```bash
    sudo yum install util-linux
    sudo fstrim /datadrive
    ```

## <a name="next-steps"></a>Passos seguintes
Você também pode [anexar um disco de dados](add-disk.md) usando o CLI do Azure.
