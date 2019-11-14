---
title: Localizar e excluir discos gerenciados e não gerenciados do Azure desconectados
description: Como localizar e excluir discos gerenciados e não (BLOBs/páginas) do Azure desconectados usando CLI do Azure.
author: roygara
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 03/30/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: e475fcc5812177366b8c27ae2cca6b45d6c00709
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036159"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>Localizar e excluir discos gerenciados e não gerenciados do Azure desconectados
Quando você exclui uma VM (máquina virtual) no Azure, por padrão, todos os discos anexados à VM não são excluídos. Esse recurso ajuda a evitar a perda de dados devido à exclusão não intencional de VMs. Depois que uma VM for excluída, você continuará a pagar por discos não anexados. Este artigo mostra como localizar e excluir discos desanexados e reduzir custos desnecessários. 


## <a name="managed-disks-find-and-delete-unattached-disks"></a>Managed disks: localizar e excluir discos desanexados 

O script a seguir procura [discos gerenciados](managed-disks-overview.md) desconectados examinando o valor da propriedade **ManagedBy** . Quando um disco gerenciado é anexado a uma VM, a propriedade **ManagedBy** contém a ID de recurso da VM. Quando um disco gerenciado é desanexado, a propriedade **ManagedBy** é nula. O script examina todos os discos gerenciados em uma assinatura do Azure. Quando o script localiza um disco gerenciado com a propriedade **ManagedBy** definida como NULL, o script determina que o disco está desanexado.

>[!IMPORTANT]
>Primeiro, execute o script definindo a variável **deleteUnattachedDisks** como 0. Essa ação permite localizar e exibir todos os discos gerenciados desconectados.
>
>Depois de examinar todos os discos desanexados, execute o script novamente e defina a variável **deleteUnattachedDisks** como 1. Essa ação permite que você exclua todos os discos gerenciados desconectados.
>

```azurecli

# Set deleteUnattachedDisks=1 if you want to delete unattached Managed Disks
# Set deleteUnattachedDisks=0 if you want to see the Id of the unattached Managed Disks
deleteUnattachedDisks=0

unattachedDiskIds=$(az disk list --query '[?managedBy==`null`].[id]' -o tsv)
for id in ${unattachedDiskIds[@]}
do
    if (( $deleteUnattachedDisks == 1 ))
    then

        echo "Deleting unattached Managed Disk with Id: "$id
        az disk delete --ids $id --yes
        echo "Deleted unattached Managed Disk with Id: "$id

    else
        echo $id
    fi
done
```

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Discos não gerenciados: localizar e excluir discos desanexados 

Discos não gerenciados são arquivos VHD armazenados como [blobs de páginas](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) em [contas de armazenamento do Azure](../../storage/common/storage-create-storage-account.md). O script a seguir procura discos não gerenciados desanexados (BLOBs de página) examinando o valor da propriedade **LeaseStatus** . Quando um disco não gerenciado é anexado a uma VM, a propriedade **LeaseStatus** é definida como **Locked**. Quando um disco não gerenciado é desanexado, a propriedade **LeaseStatus** é definida como **desbloqueada**. O script examina todos os discos não gerenciados em todas as contas de armazenamento do Azure em uma assinatura do Azure. Quando o script localiza um disco não gerenciado com uma propriedade **LeaseStatus** definida como **desbloqueada**, o script determina que o disco está desanexado.

>[!IMPORTANT]
>Primeiro, execute o script definindo a variável **deleteUnattachedVHDs** como 0. Essa ação permite localizar e exibir todos os VHDs não gerenciados desanexados.
>
>Depois de examinar todos os discos desanexados, execute o script novamente e defina a variável **deleteUnattachedVHDs** como 1. Essa ação permite que você exclua todos os VHDs não gerenciados desanexados.
>

```azurecli
   
# Set deleteUnattachedVHDs=1 if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=0 if you want to see the details of the unattached VHDs
deleteUnattachedVHDs=0

storageAccountIds=$(az storage account list --query [].[id] -o tsv)

for id in ${storageAccountIds[@]}
do
    connectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
    containers=$(az storage container list --connection-string $connectionString --query [].[name] -o tsv)

    for container in ${containers[@]}
    do 
        
        blobs=$(az storage blob list -c $container --connection-string $connectionString --query "[?properties.blobType=='PageBlob' && ends_with(name,'.vhd')].[name]" -o tsv)
        
        for blob in ${blobs[@]}
        do
            leaseStatus=$(az storage blob show -n $blob -c $container --connection-string $connectionString --query "properties.lease.status" -o tsv)
            
            if [ "$leaseStatus" == "unlocked" ]
            then 

                if (( $deleteUnattachedVHDs == 1 ))
                then 

                    echo "Deleting VHD: "$blob" in container: "$container" in storage account: "$id

                    az storage blob delete --delete-snapshots include  -n $blob -c $container --connection-string $connectionString

                    echo "Deleted VHD: "$blob" in container: "$container" in storage account: "$id
                else
                    echo "StorageAccountId: "$id" container: "$container" VHD: "$blob
                fi

            fi
        done
    done
done 
```

## <a name="next-steps"></a>Passos seguintes

[Excluir conta de armazenamento](../../storage/common/storage-create-storage-account.md)



