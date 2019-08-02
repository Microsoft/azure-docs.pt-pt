---
title: Desanexar um disco de dados de uma VM do Linux-Azure | Microsoft Docs
description: Saiba como desanexar um disco de dados de uma máquina virtual no Azure usando CLI do Azure ou o portal do Azure.
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: e026617db4da58c12a454000f6d97f8b6843e95d
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68695872"
---
# <a name="how-to-detach-a-data-disk-from-a-linux-virtual-machine"></a>Como desanexar um disco de dados de uma máquina virtual Linux

Quando já não precisar de um disco de dados que esteja ligado a uma máquina virtual, pode desligá-lo facilmente. Isso remove o disco da máquina virtual, mas não o Remove do armazenamento. Neste artigo, estamos trabalhando com uma distribuição Ubuntu LTS 16, 4. Se você estiver usando uma distribuição diferente, as instruções para desmontar o disco podem ser diferentes.

> [!WARNING]
> Se você desanexar um disco, ele não será excluído automaticamente. Se você assinou o armazenamento Premium, continuará a incorrer em encargos de armazenamento para o disco. Para obter mais informações, consulte [preços e cobrança ao usar o armazenamento Premium](https://azure.microsoft.com/pricing/details/storage/page-blobs/).

Se pretender voltar a utilizar os dados existentes no disco, pode voltar a ligá-lo à mesma máquina virtual ou a outra.  


## <a name="connect-to-the-vm-to-unmount-the-disk"></a>Conectar-se à VM para desmontar o disco

Antes de poder desanexar o disco usando a CLI ou o portal, você precisa desmontar o disco e remover as referências a If do arquivo fstab.

Ligue à VM. Neste exemplo, o endereço IP público da VM é *10.0.1.4* com o nome de usuário *azureuser*: 

```bash
ssh azureuser@10.0.1.4
```

Primeiro, localize o disco de dados que você deseja desanexar. O exemplo a seguir usa dmesg para filtrar em discos SCSI:

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

Aqui, *SDC* é o disco que desejamos desanexar. Você também deve obter o UUID do disco.

```bash
sudo -i blkid
```

A saída é semelhante ao exemplo a seguir:

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```


Edite o arquivo */etc/fstab* para remover as referências ao disco. 

> [!NOTE]
> A edição inadequada do arquivo **/etc/fstab** pode resultar em um sistema não inicializável. Se não tiver certeza, consulte a documentação da distribuição para obter informações sobre como editar corretamente esse arquivo. Também é recomendável que um backup do arquivo/etc/fstab seja criado antes da edição.

Abra o arquivo */etc/fstab* em um editor de texto da seguinte maneira:

```bash
sudo vi /etc/fstab
```

Neste exemplo, a linha a seguir precisa ser excluída do arquivo */etc/fstab* :

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

Use `umount` para desmontar o disco. O exemplo a seguir desmonta a partição */dev/sdc1* do ponto de montagem */DataDrive* :

```bash
sudo umount /dev/sdc1 /datadrive
```


## <a name="detach-a-data-disk-using-azure-cli"></a>Desanexar um disco de dados usando CLI do Azure 

Este exemplo desanexa o disco *myDataDisk* da VM denominada *MyVM* nomyresourceproperties.

```azurecli
az vm disk detach \
    -g myResourceGroup \
    --vm-name myVm \
    -n myDataDisk
```

O disco permanece no armazenamento, mas não está mais anexado a uma máquina virtual.


## <a name="detach-a-data-disk-using-the-portal"></a>Desanexar um disco de dados com o portal

1. No menu à esquerda, selecione **máquinas virtuais**.
2. Selecione a máquina virtual que tem o disco de dados que você deseja desanexar e clique em **parar** para desalocar a VM.
3. No painel máquina virtual, selecione **discos**.
4. Na parte superior do painel **discos** , selecione **Editar**.
5. No painel **discos** , na extrema direita do disco de dados que você deseja desanexar, clique no botão desanexar ![imagem](./media/detach-disk/detach.png) do botão desanexar.
5. Depois que o disco for removido, clique em salvar na parte superior do painel.
6. No painel máquina virtual, clique em **visão geral** e, em seguida, clique no botão **Iniciar** na parte superior do painel para reiniciar a VM.

O disco permanece no armazenamento, mas não está mais anexado a uma máquina virtual.



## <a name="next-steps"></a>Passos seguintes
Se você quiser reutilizar o disco de dados, você pode apenas [anexá-lo a outra VM](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

