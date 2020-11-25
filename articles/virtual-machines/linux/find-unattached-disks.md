---
title: Azure CLI - Encontre e elimine discos geridos e não geridos não geridos
description: Como encontrar e eliminar discos Azure não ligados geridos e não geridos (VHDs/blobs de página) utilizando O Azure CLI.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/30/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 0c3e8bb2ff6f3313e851a4253a95a5ad923a8f70
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016221"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks-using-the-azure-cli"></a>Localizar e apagar discos geridos e não geridos pelo Azure utilizando o Azure CLI
Quando elimina uma máquina virtual (VM) em Azure, por padrão, quaisquer discos que estejam ligados ao VM não são eliminados. Esta funcionalidade ajuda a prevenir a perda de dados devido à eliminação não intencional de VMs. Depois de eliminado um VM, continuará a pagar por discos não ligados. Este artigo mostra-lhe como encontrar e eliminar quaisquer discos não ligados e reduzir custos desnecessários. 


## <a name="managed-disks-find-and-delete-unattached-disks"></a>Discos geridos: Localizar e apagar discos não ligados 

O seguinte script procura [discos geridos](../managed-disks-overview.md) não ligados examinando o valor da propriedade **ManagedBy.** Quando um disco gerido é ligado a um VM, a propriedade **ManagedBy** contém o ID de recurso do VM. Quando um disco gerido não está ligado, a propriedade **ManagedBy** é nula. O script examina todos os discos geridos numa subscrição do Azure. Quando o script localiza um disco gerido com a propriedade **ManagedBy** definida para nula, o script determina que o disco está desapegado.

>[!IMPORTANT]
>Em primeiro lugar, executar o script definindo a variável **deleteUnattachedDisks** para 0. Esta ação permite-lhe encontrar e ver todos os discos geridos desvinculados.
>
>Depois de rever todos os discos não ligados, volte a executar o script e desave a variável **deleteUnattachedDisks** para 1. Esta ação permite eliminar todos os discos geridos não ligados.
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

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Discos não geridos: Encontrar e apagar discos não ligados 

Os discos não geridos são ficheiros VHD que são armazenados como [bolhas de página](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) nas contas de armazenamento [Azure](../../storage/common/storage-account-overview.md). O seguinte script procura discos não geridos (bolhas de página) examinando o valor da propriedade **LeaseStatus.** Quando um disco não gerido é ligado a um VM, a propriedade **LeaseStatus** está definida para **Bloqueado**. Quando um disco não gerido não é ligado, a propriedade **LeaseStatus** está definida para **Desbloqueado**. O script examina todos os discos não geridos em todas as contas de armazenamento Azure numa subscrição do Azure. Quando o script localiza um disco não gerido com uma propriedade **LeaseStatus** definida para **Desbloqueado,** o script determina que o disco está desapegado.

>[!IMPORTANT]
>Em primeiro lugar, executar o script definindo a variável **deleteUnattachedVHDs** para 0. Esta ação permite-lhe encontrar e ver todos os VHDs não geridos desapegados.
>
>Depois de rever todos os discos não ligados, volte a executar o script e desloque a variável **deleteUnattachedVHDs** para 1. Esta ação permite eliminar todos os VHDs não geridos desapegados.
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

Para obter mais informações, consulte [Eliminar uma conta de armazenamento.](../../storage/common/storage-account-create.md#delete-a-storage-account)