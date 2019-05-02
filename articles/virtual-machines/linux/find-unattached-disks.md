---
title: Localize e elimine desanexados discos geridos e não geridos do Azure | Documentos da Microsoft
description: Como encontrar e eliminar do Azure geridos e não geridos (blobs de páginas/VHDs) discos desanexados com a CLI do Azure.
services: virtual-machines-linux
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 21c15a943974b80469eb9bd71cbaf11a7bc34b4a
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64721830"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>Localize e elimine desanexados discos geridos e não geridos do Azure
Quando elimina uma máquina virtual (VM) no Azure, por predefinição, não são eliminados todos os discos que estão ligados à VM. Esta funcionalidade ajuda a evitar a perda de dados devido à exclusão involuntária de VMs. Depois de eliminar uma VM, continuará a pagar pelos discos desanexados. Este artigo mostra-lhe como encontrar e eliminar quaisquer discos desanexados e reduzir custos desnecessários. 


## <a name="managed-disks-find-and-delete-unattached-disks"></a>Discos geridos: Localize e elimine discos desanexados 

O script a seguir procura desanexados [discos geridos](managed-disks-overview.md) examinando o valor da **ManagedBy** propriedade. Quando um disco gerido está ligado a uma VM, o **ManagedBy** propriedade contém o ID de recurso da VM. Quando um disco gerido é desligado, o **ManagedBy** propriedade é nula. O script examina todos os discos geridos numa subscrição do Azure. Quando o script localiza um disco gerido com o **ManagedBy** propriedade definida como null, o script determina que o disco é desligado.

>[!IMPORTANT]
>Em primeiro lugar, execute o script, definindo a **deleteUnattachedDisks** variável como 0. Esta ação permite-lhe localizar e ver todos os discos geridos ligados.
>
>Depois de analisar todos os discos desanexados, execute novamente o script e defina a **deleteUnattachedDisks** variável como 1. Esta ação permite-lhe eliminar todos os discos geridos ligados.
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

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Discos não geridos: Localize e elimine discos desanexados 

Discos não geridos são arquivos VHD que são armazenados como [blobs de páginas](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) na [contas de armazenamento do Azure](../../storage/common/storage-create-storage-account.md). O script a seguir procura desanexados discos não geridos (blobs de páginas), examinando o valor do **LeaseStatus** propriedade. Quando um disco não gerido está ligado a uma VM, o **LeaseStatus** estiver definida como **bloqueado**. Quando um disco não gerido é desligado, o **LeaseStatus** estiver definida como **Unlocked**. O script examina todos os discos não geridos em todas as contas de armazenamento do Azure numa subscrição do Azure. Quando o script localiza um disco não gerido com um **LeaseStatus** definida como **Unlocked**, o script determina que o disco é desligado.

>[!IMPORTANT]
>Em primeiro lugar, execute o script, definindo a **deleteUnattachedVHDs** variável como 0. Esta ação permite-lhe localizar e ver todos os VHDs não geridos ligados.
>
>Depois de analisar todos os discos desanexados, execute novamente o script e defina a **deleteUnattachedVHDs** variável como 1. Esta ação permite-lhe eliminar todos os VHDs não geridos ligados.
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

## <a name="next-steps"></a>Passos Seguintes

[Eliminar conta de armazenamento](../../storage/common/storage-create-storage-account.md)



