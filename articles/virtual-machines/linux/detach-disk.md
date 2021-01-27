---
title: Retire um disco de dados de um Linux VM - Azure
description: Aprenda a separar um disco de dados de uma máquina virtual em Azure utilizando o Azure CLI ou o portal Azure.
author: roygara
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 07/18/2018
ms.author: rogarana
ms.subservice: disks
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7e618475d7d0160bce5aee39c267bb191a3a9f0a
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897144"
---
# <a name="how-to-detach-a-data-disk-from-a-linux-virtual-machine"></a>Como desanexar um disco de dados de uma máquina virtual do Linux

Quando já não precisar de um disco de dados que esteja ligado a uma máquina virtual, pode desligá-lo facilmente. Isto remove o disco da máquina virtual, mas não o remove do armazenamento. Neste artigo, estamos a trabalhar com uma distribuição Ubuntu LTS 16.04. Se estiver a utilizar uma distribuição diferente, as instruções para desmontar o disco podem ser diferentes.

> [!WARNING]
> Se separar um disco, não é apagado automaticamente. Se subscrever o armazenamento Premium, continuará a incorrer em custos de armazenamento para o disco. Para obter mais informações, consulte [preços e faturação ao utilizar o Premium Storage](https://azure.microsoft.com/pricing/details/storage/page-blobs/).

Se pretender voltar a utilizar os dados existentes no disco, pode voltar a ligá-lo à mesma máquina virtual ou a outra.  


## <a name="connect-to-the-vm-to-unmount-the-disk"></a>Ligue-se ao VM para desmontar o disco

Antes de conseguir separar o disco utilizando o CLI ou o portal, tem de desmontar o disco e remover referências para se for do seu ficheiro fstab.

Ligue à VM. Neste exemplo, o endereço IP público do VM é *10.0.1.4* com o nome de utilizador *azureuser:* 

```bash
ssh azureuser@10.0.1.4
```

Primeiro, encontre o disco de dados que pretende desmontar. O exemplo a seguir utiliza o dmesg para filtrar em discos SCSI:

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

Aqui, *o SDC* é o disco que queremos desmontar. Também deve pegar o UUID do disco.

```bash
sudo -i blkid
```

A saída é semelhante ao seguinte exemplo:

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```


Edite o ficheiro */etc/fstab* para remover referências ao disco. 

> [!NOTE]
> A edição indevida do ficheiro **/etc/fstab** pode resultar num sistema inabitável. Se não tiver a certeza, consulte a documentação de distribuição para obter mais informações sobre como editar corretamente este ficheiro. Recomenda-se também que seja criada uma cópia de segurança do ficheiro /etc/fstab antes da edição.

Abra o ficheiro */etc/fstab* num editor de texto da seguinte forma:

```bash
sudo vi /etc/fstab
```

Neste exemplo, a seguinte linha deve ser eliminada do ficheiro */etc/fstab:*

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

Use `umount` para desmontar o disco. O exemplo a seguir desmonta a partição */dev/sdc1* do ponto de montagem */datadrive:*

```bash
sudo umount /dev/sdc1 /datadrive
```


## <a name="detach-a-data-disk-using-azure-cli"></a>Descodifice um disco de dados usando O Azure CLI 

Este exemplo destaca o *disco myDataDisk* da VM nomeado *myVM* no *myResourceGroup*.

```azurecli
az vm disk detach \
    -g myResourceGroup \
    --vm-name myVm \
    -n myDataDisk
```

O disco permanece no armazenamento mas já não está ligado a uma máquina virtual.


## <a name="detach-a-data-disk-using-the-portal"></a>Desanexar um disco de dados com o portal

1. No menu esquerdo, selecione **Máquinas Virtuais.**
1. Na lâmina da máquina virtual, selecione **Discos**.
1. Na parte superior da lâmina **discos,** **selecione Editar**.
1. Na lâmina **Discos,** à extrema direita do disco de dados que gostaria de desmontar, clique no ![ ](./media/detach-disk/detach.png) botão de desconexão da imagem do botão Desprender.
1. Depois de removido o disco, clique em **Guardar** na parte superior da lâmina.

O disco permanece no armazenamento mas já não está ligado a uma máquina virtual.



## <a name="next-steps"></a>Próximos passos
Se pretender reutilizar o disco de dados, pode [fixá-lo a outro VM](add-disk.md).

Se pretender eliminar o disco, para que não incorram mais custos de armazenamento, consulte [Localizar e eliminar discos geridos e não geridos do Azure não geridos e não geridos - portal Azure](../disks-find-unattached-portal.md).