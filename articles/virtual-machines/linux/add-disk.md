---
title: Adicionar um disco de dados à VM do Linux com a CLI do Azure | Documentos da Microsoft
description: Saiba como adicionar um disco de dados persistente a sua VM do Linux com a CLI do Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 06/13/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.subservice: disks
ms.openlocfilehash: 81805188c72bce6a7ea89496c8036743b29e9075
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57452764"
---
# <a name="add-a-disk-to-a-linux-vm"></a>Adicionar um disco a uma VM com Linux
Este artigo mostra-lhe como anexar um disco persistente a sua VM, para que pode preservar seus dados - mesmo que a VM é reaprovisionada devido a manutenção ou redimensionar.


## <a name="attach-a-new-disk-to-a-vm"></a>Anexar um disco novo a uma VM

Se pretender adicionar um disco de dados novo e vazio na sua VM, utilize o [anexar o disco da vm az](/cli/azure/vm/disk?view=azure-cli-latest) comando com o `--new` parâmetro. Se a VM numa zona de disponibilidade, o disco é automaticamente criado na mesma zona da VM. Para obter mais informações, consulte [descrição geral das zonas de disponibilidade](../../availability-zones/az-overview.md). O exemplo seguinte cria um disco chamado *myDataDisk* ou seja, 50 Gb de tamanho:

```azurecli
az vm disk attach \
   -g myResourceGroup \
   --vm-name myVM \
   --disk myDataDisk \
   --new \
   --size-gb 50
```

## <a name="attach-an-existing-disk"></a>Anexar um disco existente

Para anexar um disco existente, localizar o ID do disco e passar a ID para o [anexar o disco da vm az](/cli/azure/vm/disk?view=azure-cli-latest) comando. As seguintes consultas de exemplo para um disco chamado *myDataDisk* na *myResourceGroup*, em seguida, liga-a VM com o nome *myVM*:

```azurecli
diskId=$(az disk show -g myResourceGroup -n myDataDisk --query 'id' -o tsv)

az vm disk attach -g myResourceGroup --vm-name myVM --disk $diskId
```

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

Aqui, *sdc* é o disco que Desejamos. Particionar o disco com `parted`, se o tamanho do disco é 2 tebibytes (TiB) ou superior, em seguida, tem de utilizar GPT criação de partições, se está sob 2TiB, em seguida, pode usar o particionamento de MBR ou GPT. Se estiver a utilizar o MBR criação de partições, pode utilizar `fdisk`. Torná-lo um disco primário na partição 1 e aceite as outras predefinições. O exemplo seguinte inicia o `fdisk` processos no */desenvolvimento/sdc*:

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
Utilize o comando para atualizar o kernel abaixo:
```
partprobe 
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

Agora, crie um diretório para montar o sistema de ficheiros com `mkdir`. O exemplo seguinte cria um diretório em */datadrive*:

```bash
sudo mkdir /datadrive
```

Utilize `mount` , em seguida, montar o sistema de ficheiros. Monta o exemplo a seguir a */desenvolvimento/sdc1* de partição para o */datadrive* ponto de montagem:

```bash
sudo mount /dev/sdc1 /datadrive
```

Para garantir que a unidade é a remontadas automaticamente após um reinício, tem de ser adicionado para o *etc/fstab* ficheiro. Também recomendamos que o UUID (Identificador exclusivo universalmente) é utilizado numa *etc/fstab* referir-se para a unidade em vez de apenas o nome do dispositivo (como, por exemplo */desenvolvimento/sdc1*). Se o sistema operacional detetar um erro de disco durante o arranque, utilizar o UUID evita o que está a ser montado para uma determinada localização de disco incorreta. Restantes discos de dados poderiam então ser atribuído esses mesmos IDs de dispositivo. Para localizar o UUID da unidade de novo, utilize o `blkid` utilitário:

```bash
sudo blkid
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
>
> Consola de série de VM do Azure pode ser utilizada para acesso à consola para a VM, se modificar fstab resultou numa falha de inicialização. Estão disponíveis em mais detalhes a [documentação da consola de série](https://docs.microsoft.com/en-us/azure/virtual-machines/troubleshooting/serial-console-linux).

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

## <a name="troubleshooting"></a>Resolução de problemas

[!INCLUDE [virtual-machines-linux-lunzero](../../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>Passos Seguintes

* Para garantir a sua VM do Linux está configurada corretamente, reveja os [otimizar o desempenho da máquina Linux](optimization.md) recomendações.
* Expandir a sua capacidade de armazenamento através da adição de discos adicionais e [configurar o RAID](configure-raid.md) para desempenho adicionais.
