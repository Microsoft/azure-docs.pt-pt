---
title: Localizar e eliminar discos não ligados do Azure geridos e não geridos
description: Como encontrar e eliminar discos não ligados do Azure geridos e não geridos (VHDs/page blobs) utilizando o Azure CLI.
author: roygara
ms.service: virtual-machines
ms.topic: article
ms.date: 03/30/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 6cf48c53e7b5c1cc8537abeda164460de66abddb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945150"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks-using-the-azure-cli"></a>Localizar e eliminar discos azure não ligados e não geridos utilizando o Azure CLI
Quando elimina uma máquina virtual (VM) em Azure, por padrão, os discos que estejam ligados ao VM não são eliminados. Esta funcionalidade ajuda a prevenir a perda de dados devido à eliminação não intencional de VMs. Depois de um VM ser eliminado, continuará a pagar por discos não ligados. Este artigo mostra-lhe como encontrar e apagar quaisquer discos não ligados e reduzir custos desnecessários. 


## <a name="managed-disks-find-and-delete-unattached-disks"></a>Discos geridos: Encontrar e eliminar discos não ligados 

O seguinte script procura [discos geridos](managed-disks-overview.md) não ligados examinando o valor da propriedade **ManagedBy.** Quando um disco gerido é ligado a um VM, a propriedade **ManagedBy** contém o ID de recurso do VM. Quando um disco gerido é desligado, a propriedade **ManagedBy** é nula. O script examina todos os discos geridos numa subscrição azure. Quando o script localiza um disco gerido com a propriedade **ManagedBy** definida como nula, o script determina que o disco está desconectado.

>[!IMPORTANT]
>Em primeiro lugar, executar o script definindo a variável **deleteUnattachedDisks** para 0. Esta ação permite-lhe encontrar e ver todos os discos geridos desapegados.
>
>Depois de rever todos os discos não ligados, volte a executar o script e desloque a variável **deleteUnattachedDisks** para 1. Esta ação permite-lhe eliminar todos os discos geridos desapegados.
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

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Discos não geridos: Encontrar e eliminar discos não ligados 

Os discos não geridos são ficheiros VHD que são [armazenados](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) como bolhas de página nas contas de [armazenamento do Azure](../../storage/common/storage-create-storage-account.md). O seguinte script procura discos não geridos não ligados (bolhas de página) examinando o valor da propriedade **LeaseStatus.** Quando um disco não gerido é ligado a um VM, a propriedade **LeaseStatus** é definida para **Locked**. Quando um disco não gerido é desligado, a propriedade **LeaseStatus** está definida para **Desbloquear**. O script examina todos os discos não geridos em todas as contas de armazenamento azure numa subscrição Azure. Quando o script localiza um disco não gerido com uma propriedade **LeaseStatus** definida para **Desbloquear,** o script determina que o disco está desconectado.

>[!IMPORTANT]
>Em primeiro lugar, executar o script definindo a variável **deleteUnattachedVHDs** para 0. Esta ação permite-lhe encontrar e ver todos os VHDs não ligados.
>
>Depois de rever todos os discos não ligados, volte a executar o script e defina a variável **deleteUnattachedVHDs** para 1. Esta ação permite-lhe eliminar todos os VHDs não ligados não geridos.
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

[Eliminar conta de armazenamento](../../storage/common/storage-create-storage-account.md)



