---
title: Anexar um disco de dados a uma VM do Linux | Documentos da Microsoft
description: Utilize o portal para anexar o disco de dados de novo ou existente para uma VM do Linux.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 5e1c6212-976c-4962-a297-177942f90907
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: d5dd916f7e4434640db6dae6f8c5a73d1ff2d3e0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60187999"
---
# <a name="use-the-portal-to-attach-a-data-disk-to-a-linux-vm"></a>Utilizar o portal para anexar um disco de dados a uma VM do Linux 
Este artigo mostra-lhe como anexar discos de novos e existentes para máquinas virtuais do Linux através do portal do Azure. Também pode [anexar um disco de dados a uma VM do Windows no portal do Azure](../windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Antes de anexar discos à sua VM, reveja estas dicas:

* O tamanho da máquina virtual controla quantos discos de dados, pode anexar. Para obter detalhes, consulte [tamanhos de máquinas virtuais](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Os discos anexados a máquinas virtuais são, na verdade, os ficheiros. vhd armazenados no Azure. Para obter detalhes, consulte nosso [introdução aos discos geridos](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Depois de anexar o disco, precisa [ligar à VM do Linux para montar o disco novo](#connect-to-the-linux-vm-to-mount-the-new-disk).


## <a name="find-the-virtual-machine"></a>Localizar a máquina virtual
1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. No menu da esquerda, clique em **máquinas virtuais**.
3. Selecione a máquina virtual na lista.
4. Para o Virtual máquinas página, em **Essentials**, clique em **discos**.
   
    ![Abra as definições do disco](./media/attach-disk-portal/find-disk-settings.png)


## <a name="attach-a-new-disk"></a>Anexar um disco novo

1. Sobre o **discos** painel, clique em **+ adicionar disco de dados**.
2. Clique no menu pendente para **Name** e selecione **criar disco**:

    ![Criar Azure disco gerido](./media/attach-disk-portal/create-new-md.png)

3. Introduza um nome para o disco gerido. Reveja as definições predefinidas, atualizar conforme necessário e, em seguida, clique em **criar**.
   
   ![Reveja as definições do disco](./media/attach-disk-portal/create-new-md-settings.png)

4. Clique em **guardar** para criar o disco gerido e atualizar a configuração da VM:

   ![Guardar o novo disco gerido do Azure](./media/attach-disk-portal/confirm-create-new-md.png)

5. Depois do Azure cria o disco e anexa-o para a máquina virtual, o novo disco está listado nas definições de disco da máquina virtual em **discos de dados**. Como os discos geridos são um recurso de nível superior, o disco aparece na raiz do grupo de recursos:

   ![Disco gerido do Azure no grupo de recursos](./media/attach-disk-portal/view-md-resource-group.png)

## <a name="attach-an-existing-disk"></a>Anexar um disco existente
1. Sobre o **discos** painel, clique em **+ adicionar disco de dados**.
2. Clique no menu pendente para **nome** para ver uma lista de discos geridos existentes acessíveis à sua subscrição do Azure. Selecione o disco gerido para anexar:

   ![Anexar o disco gerido do Azure](./media/attach-disk-portal/select-existing-md.png)

3. Clique em **guardar** para anexar o disco gerido existente e atualizar a configuração de VM:
   
   ![Guardar atualizações do disco gerido do Azure](./media/attach-disk-portal/confirm-attach-existing-md.png)

4. Depois de Azure anexa o disco à máquina virtual, está listado nas definições de disco da máquina virtual em **discos de dados**.

## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Ligar à VM do Linux para montar o disco novo
Para particionar e formatar para montar o disco novo, para que a sua VM do Linux pode utilizá-lo, SSH à VM. Para obter mais informações, veja [como utilizar SSH com Linux no Azure](mac-create-ssh-keys.md). O exemplo seguinte liga a uma VM com a entrada DNS pública dos *mypublicdns.westus.cloudapp.azure.com* com o nome de utilizador *azureuser*: 

```bash
ssh azureuser@mypublicdns.westus.cloudapp.azure.com
```

Assim que estiver ligado à sua VM, está pronto para anexar um disco. Em primeiro lugar, localizar o disco com `dmesg` (o método utilizado para detetar o disco novo pode variar). O exemplo seguinte utiliza dmesg para filtrar os *SCSI* discos:

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

Aqui, *sdc* é o disco que Desejamos. 

### <a name="partition-a-new-disk"></a>Um novo disco de partição
Se estiver a utilizar um disco existente que contém dados, avance para a montagem do disco. Se associar um novo disco, precisa particionar o disco.

Particione o disco com `fdisk`. Se o tamanho do disco é 2 tebibytes (TiB) ou superior, em seguida, tem de utilizar GPT particionamento, pode usar `parted` para executar a criação de partições de GPT. Se o tamanho do disco está sob 2TiB, em seguida, pode utilizar MBR ou GPT criação de partições. Torná-lo um disco primário na partição 1 e aceite as outras predefinições. O exemplo seguinte inicia o `fdisk` processos no */desenvolvimento/sdc*:

```bash
sudo fdisk /dev/sdc
```

Utilize o `n` comando para adicionar uma nova partição. Neste exemplo, escolhemos também `p` para um site primário de partição e aceitar o restante dos valores predefinidos. O resultado será semelhante ao seguinte exemplo:

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

A tabela de partições de impressão, escrevendo `p` e, em seguida, utilizar `w` escrever a tabela para o disco e de saída. O resultado deverá ter um aspeto semelhante ao seguinte exemplo:

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

Agora, escrever um sistema de ficheiros para a partição com o `mkfs` comando. Especifique o tipo de sistema de ficheiros e o nome do dispositivo. O exemplo seguinte cria um *ext4* no sistema de ficheiros a */desenvolvimento/sdc1* partição que foi criada nos passos anteriores:

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
### <a name="mount-the-disk"></a>Monte o disco
Crie um diretório para montar o sistema de ficheiros com `mkdir`. O exemplo seguinte cria um diretório em */datadrive*:

```bash
sudo mkdir /datadrive
```

Utilize `mount` , em seguida, montar o sistema de ficheiros. Monta o exemplo a seguir a */desenvolvimento/sdc1* de partição para o */datadrive* ponto de montagem:

```bash
sudo mount /dev/sdc1 /datadrive
```

Para garantir que a unidade é a remontadas automaticamente após um reinício, tem de ser adicionado para o *etc/fstab* ficheiro. Também recomendamos que o UUID (Identificador exclusivo universalmente) é utilizado numa *etc/fstab* referir-se para a unidade em vez de apenas o nome do dispositivo (como, por exemplo */desenvolvimento/sdc1*). Se o sistema operacional detetar um erro de disco durante o arranque, utilizar o UUID evita o que está a ser montado para uma determinada localização de disco incorreta. Restantes discos de dados poderiam então ser atribuído esses mesmos IDs de dispositivo. Para localizar o UUID da unidade de novo, utilize o `blkid` utilitário:

```bash
sudo -i blkid
```

O resultado será semelhante ao seguinte exemplo:

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

> [!NOTE]
> Editar incorretamente o **etc/fstab** ficheiros poderiam resultar num sistema não inicializável. Se não souber, consulte a documentação da distribuição para obter informações sobre como editar corretamente esse arquivo. Também é recomendável que uma cópia de segurança do ficheiro /etc/fstab. é criada antes de editar.

Em seguida, abra a *etc/fstab* ficheiro num editor de texto da seguinte forma:

```bash
sudo vi /etc/fstab
```

Neste exemplo, utilize o valor UUID para o */desenvolvimento/sdc1* dispositivo que foi criado nos passos anteriores e o ponto de montagem de */datadrive*. Adicione a seguinte linha ao final dos *etc/fstab* ficheiro:

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

> [!NOTE]
> A remover um disco de dados mais tarde sem editar fstab pode fazer com que a VM falhe efetuar o arranque. A maioria das distribuições fornecem nenhuma a *nofail* e/ou *nobootwait* fstab opções. Estas opções que o sistema de arranque, mesmo se o disco não consegue montar no momento da inicialização. Para obter mais informações sobre estes parâmetros, consulte a documentação de sua distribuição.
> 
> O *nofail* opção garante que a VM é iniciado, mesmo que o sistema de ficheiros está danificado ou o disco não existe no momento da inicialização. Sem esta opção, pode encontrar o comportamento, conforme descrito em [não é possível SSH à VM do Linux devido a erros FSTAB](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/)

### <a name="trimunmap-support-for-linux-in-azure"></a>Suporte de cortar/UNMAP para Linux no Azure
Alguns kernels de Linux suportam operações de cortar/UNMAP descartar blocos não utilizados no disco. Esta funcionalidade é útil principalmente no armazenamento standard para informar o Azure eliminado páginas já não são válido e podem ser descartados e pode economizar dinheiro, se cria arquivos grandes e, em seguida, eliminá-los.

Existem duas formas de ativar a limitação de suporte na sua VM do Linux. Como sempre, consulte sua distribuição para a abordagem recomendada:

* Utilize o `discard` montar opção na *etc/fstab*, por exemplo:

    ```bash
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2
    ```
* Em alguns casos, o `discard` opção pode ter implicações de desempenho. Em alternativa, pode executar o `fstrim` comando manualmente a partir da linha de comandos, ou adicione-o para seu crontab para executar regularmente:
  
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

## <a name="next-steps"></a>Passos Seguintes
Também pode [anexar um disco de dados](add-disk.md) com a CLI do Azure.
