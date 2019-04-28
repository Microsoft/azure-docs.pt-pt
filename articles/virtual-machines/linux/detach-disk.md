---
title: Desanexar um disco de dados de uma VM do Linux - Azure | Documentos da Microsoft
description: Saiba como desanexar um disco de dados de uma máquina virtual no Azure com a CLI do Azure ou o portal do Azure.
services: virtual-machines-linux
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.topic: article
ms.date: 07/18/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 02cb970b5c70064abbbc71e585fe3dd1540fda90
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766330"
---
# <a name="how-to-detach-a-data-disk-from-a-linux-virtual-machine"></a>Como desanexar um disco de dados de uma máquina virtual do Linux

Quando já não precisar de um disco de dados que esteja ligado a uma máquina virtual, pode desligá-lo facilmente. Isso remove o disco da máquina virtual, mas não o remove do armazenamento. Neste artigo, estamos a trabalhar com uma distribuição de Ubuntu LTS 16.04. Se estiver a utilizar uma distribuição diferente, as instruções para desmontar o disco podem ser diferentes.

> [!WARNING]
> Se desligar um disco que não é eliminado automaticamente. Se tiver inscrito para o armazenamento Premium, continuará a incorrer em custos de armazenamento para o disco. Para obter mais informações, consulte [preços e faturação quando utiliza o armazenamento Premium](https://azure.microsoft.com/pricing/details/storage/page-blobs/).

Se pretender voltar a utilizar os dados existentes no disco, pode voltar a ligá-lo à mesma máquina virtual ou a outra.  


## <a name="connect-to-the-vm-to-unmount-the-disk"></a>Ligar à VM desmontar o disco

Para poder desligar o disco com a CLI ou o portal, é necessário desmontar o disco e removido referências a se do seu ficheiro fstab.

Ligue à VM. Neste exemplo, é o endereço IP público da VM *10.0.1.4* com o nome de utilizador *azureuser*: 

```bash
ssh azureuser@10.0.1.4
```

Em primeiro lugar, localize o disco de dados que pretende desligar. O exemplo seguinte utiliza dmesg para filtrar os discos SCSI:

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

Aqui, *sdc* é o disco que queremos para anular a exposição. Também deve obter o UUID do disco.

```bash
sudo -i blkid
```

O resultado será semelhante ao seguinte exemplo:

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```


Editar a *etc/fstab* ficheiro para remover as referências para o disco. 

> [!NOTE]
> Editar incorretamente o **etc/fstab** ficheiros poderiam resultar num sistema não inicializável. Se não souber, consulte a documentação da distribuição para obter informações sobre como editar corretamente esse arquivo. Também é recomendável que uma cópia de segurança do ficheiro /etc/fstab. é criada antes de editar.

Abra o *etc/fstab* ficheiro num editor de texto da seguinte forma:

```bash
sudo vi /etc/fstab
```

Neste exemplo, a seguinte linha tem de ser eliminado dos *etc/fstab* ficheiro:

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

Utilize `umount` desmontar o disco. O exemplo seguinte desmonta o */desenvolvimento/sdc1* partição da */datadrive* ponto de montagem:

```bash
sudo umount /dev/sdc1 /datadrive
```


## <a name="detach-a-data-disk-using-azure-cli"></a>Desanexar um disco de dados com a CLI do Azure 

Neste exemplo desliga o *myDataDisk* disco da VM com o nome *myVM* na *myResourceGroup*.

```azurecli
az vm disk detach \
    -g myResourceGroup \
    --vm-name myVm \
    -n myDataDisk
```

O disco permanece no armazenamento, mas já não está ligado a uma máquina virtual.


## <a name="detach-a-data-disk-using-the-portal"></a>Desanexar um disco de dados com o portal

1. No menu da esquerda, selecione **máquinas virtuais**.
2. Selecione a máquina virtual que tem o disco de dados que pretende desanexar e clique em **parar** ao desalocar a VM.
3. No painel da máquina virtual, selecione **discos**.
4. Na parte superior a **discos** painel, selecione **editar**.
5. Na **discos** painel, na extrema direita do disco de dados que pretende desanexar, clique nas ![imagem do botão de anulação de exposições](./media/detach-disk/detach.png) desanexar o botão.
5. Depois do disco tiver sido removido, clique em Guardar na parte superior do painel.
6. No painel da máquina virtual, clique em **descrição geral** e, em seguida, clique nas **iniciar** botão na parte superior do painel para reiniciar a VM.

O disco permanece no armazenamento, mas já não está ligado a uma máquina virtual.



## <a name="next-steps"></a>Passos Seguintes
Se quiser reutilizar o disco de dados, simplesmente [anexá-lo a outra VM](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

