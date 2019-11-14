---
title: Adicionar um disco de dados à VM do Linux usando o CLI do Azure
description: Saiba como adicionar um disco de dados persistente à sua VM Linux com o CLI do Azure
services: virtual-machines-linux
documentationcenter: ''
author: roygara
manager: twooley
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 06/13/2018
ms.author: rogarana
ms.custom: H1Hack27Feb2017
ms.subservice: disks
ms.openlocfilehash: 5d7ec2cbbc5cc1bf8bdc87d7f82a965b3bc8c267
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037111"
---
# <a name="add-a-disk-to-a-linux-vm"></a>Adicionar um disco a uma VM com Linux
Este artigo mostra como anexar um disco persistente à sua VM para que você possa preservar seus dados, mesmo que sua VM seja reprovisionada devido à manutenção ou ao redimensionamento.


## <a name="attach-a-new-disk-to-a-vm"></a>Anexar um novo disco a uma VM

Se você quiser adicionar um novo disco de dados vazio em sua VM, use o comando [AZ VM Disk Attach](/cli/azure/vm/disk?view=azure-cli-latest) com o parâmetro `--new`. Se sua VM estiver em uma zona de disponibilidade, o disco será criado automaticamente na mesma zona que a VM. Para obter mais informações, consulte [visão geral do zonas de disponibilidade](../../availability-zones/az-overview.md). O exemplo a seguir cria um disco chamado *myDataDisk* que tem 50 GB de tamanho:

```azurecli
az vm disk attach \
   -g myResourceGroup \
   --vm-name myVM \
   --name myDataDisk \
   --new \
   --size-gb 50
```

## <a name="attach-an-existing-disk"></a>Anexar um disco existente

Para anexar um disco existente, localize a ID do disco e passe a ID para o comando [AZ VM Disk Attach](/cli/azure/vm/disk?view=azure-cli-latest) . O exemplo a seguir consulta um disco chamado *myDataDisk* no *MyResource*e, em seguida, anexa-o à VM chamada *myVM*:

```azurecli
diskId=$(az disk show -g myResourceGroup -n myDataDisk --query 'id' -o tsv)

az vm disk attach -g myResourceGroup --vm-name myVM --name $diskId
```

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

> [!NOTE]
> É recomendável que você use as versões mais recentes do fdisk ou parcialmente disponíveis para seu distribuição.

Aqui, *SDC* é o disco que queremos. Particione o disco com `parted`, se o tamanho do disco for 2 tebibytes (TiB) ou maior, você deverá usar o particionamento GPT, se ele estiver em 2TiB, então você poderá usar o particionamento MBR ou GPT. Se você estiver usando o particionamento MBR, poderá usar `fdisk`. Transforme-o em um disco primário na partição 1 e aceite os outros padrões. O exemplo a seguir inicia o processo de `fdisk` em */dev/sdc*:

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
Use o comando abaixo para atualizar o kernel:
```
partprobe 
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

Agora, crie um diretório para montar o sistema de arquivos usando `mkdir`. O exemplo a seguir cria um diretório em */DataDrive*:

```bash
sudo mkdir /datadrive
```

Use `mount` para montar o sistema de arquivos. O exemplo a seguir monta a partição */dev/sdc1* para o ponto de montagem */DataDrive* :

```bash
sudo mount /dev/sdc1 /datadrive
```

Para garantir que a unidade seja remontada automaticamente após uma reinicialização, ela deve ser adicionada ao arquivo */etc/fstab* . Também é altamente recomendável que o UUID (identificador universal exclusivo) seja usado em */etc/fstab* para se referir à unidade em vez de apenas ao nome do dispositivo (como, */dev/sdc1*). Se o sistema operacional detectar um erro de disco durante a inicialização, usar o UUID evita que o disco incorreto seja montado em um determinado local. Em seguida, os discos de dados restantes seriam atribuídos a essas mesmas IDs de dispositivo. Para localizar o UUID da nova unidade, use o utilitário `blkid`:

```bash
sudo blkid
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

> [!NOTE]
> Posteriormente, remover um disco de dados sem editar fstab pode fazer com que a VM falhe na inicialização. A maioria das distribuições fornece as opções fstab *nofail* e/ou *nobootwait* . Essas opções permitem que um sistema inicialize mesmo que o disco não seja montado no momento da inicialização. Consulte a documentação da sua distribuição para obter mais informações sobre esses parâmetros.
>
> A opção *nofail* garante que a VM seja iniciada mesmo que o sistema de arquivos esteja corrompido ou o disco não exista no momento da inicialização. Sem essa opção, você pode encontrar um comportamento conforme descrito em [não pode SSH para VM Linux devido a erros de fstab](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/)
>
> O console serial da VM do Azure pode ser usado para acesso ao console para sua VM se a modificação de fstab resultar em uma falha de inicialização. Mais detalhes estão disponíveis na [documentação do console serial](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux).

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

## <a name="troubleshooting"></a>Resolução de problemas

[!INCLUDE [virtual-machines-linux-lunzero](../../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>Passos seguintes

* Para garantir que sua VM Linux esteja configurada corretamente, revise as recomendações de [desempenho de computador otimizado para Linux](optimization.md) .
* Expanda a capacidade de armazenamento adicionando mais discos e [Configure o RAID](configure-raid.md) para obter um desempenho adicional.
