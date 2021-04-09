---
title: Criar um instantâneo incremental
description: Saiba mais sobre instantâneos incrementais para discos geridos, incluindo como criá-los usando o portal Azure, módulo Azure PowerShell e Azure Resource Manager.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 01/15/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 52e491c88d3483f21aa74f1a9f176246033bee3c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735797"
---
# <a name="create-an-incremental-snapshot-for-managed-disks"></a>Crie um instantâneo incremental para discos geridos

[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](../../includes/virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="restrictions"></a>Restrições

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](../../includes/virtual-machines-disks-incremental-snapshots-restrictions.md)]


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pode utilizar o Azure PowerShell para criar uma imagem incremental. Necessitará da versão mais recente do Azure PowerShell, o seguinte comando irá instalá-lo ou atualizar a sua instalação existente para o mais tardar:

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

Uma vez instalado, inicie sessão no PowerShell com `Connect-AzAccount` .

Para criar um instantâneo incremental com Azure PowerShell, desafie a configuração com [New-AzSnapShotConfig](/powershell/module/az.compute/new-azsnapshotconfig) com o `-Incremental` parâmetro e, em seguida, passe-a como uma variável para [New-AzSnapshot](/powershell/module/az.compute/new-azsnapshot) através do `-Snapshot` parâmetro.

```PowerShell
$diskName = "yourDiskNameHere>"
$resourceGroupName = "yourResourceGroupNameHere"
$snapshotName = "yourDesiredSnapshotNameHere"

# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName $diskName -ResourceGroupName $resourceGroupName

# Create an incremental snapshot by setting the SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName -Snapshot $snapshotConfig 
```

Pode identificar instantâneos incrementais do mesmo disco com `SourceResourceId` as propriedades e propriedades dos `SourceUniqueId` instantâneos. `SourceResourceId` é o ID de recursos Azure Resource Manager do disco-mãe. `SourceUniqueId` é o valor herdado da `UniqueId` propriedade do disco. Se eliminar um disco e criar um novo disco com o mesmo nome, o valor da `UniqueId` propriedade muda.

Pode utilizar `SourceResourceId` e criar uma lista de todos os `SourceUniqueId` instantâneos associados a um disco em particular. `<yourResourceGroupNameHere>`Substitua-o pelo seu valor e poderá utilizar o seguinte exemplo para listar as suas imagens incrementais existentes:

```PowerShell
$snapshots = Get-AzSnapshot -ResourceGroupName $resourceGroupName

$incrementalSnapshots = New-Object System.Collections.ArrayList
foreach ($snapshot in $snapshots)
{
    
    if($snapshot.Incremental -and $snapshot.CreationData.SourceResourceId -eq $yourDisk.Id -and $snapshot.CreationData.SourceUniqueId -eq $yourDisk.UniqueId){

        $incrementalSnapshots.Add($snapshot)
    }
}

$incrementalSnapshots
```

# <a name="portal"></a>[Portal](#tab/azure-portal)
[!INCLUDE [virtual-machines-disks-incremental-snapshots-portal](../../includes/virtual-machines-disks-incremental-snapshots-portal.md)]

# <a name="resource-manager-template"></a>[Modelo de gestor de recursos](#tab/azure-resource-manager)

Também pode usar modelos do Gestor de Recursos Azure para criar uma imagem incremental. Você precisará ter certeza de que a apiversão está definida para **2019-03-01** e que o imóvel incremental também está definido para verdade. O seguinte corte é um exemplo de como criar um instantâneo incremental com modelos de Gestor de Recursos:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskName": {
      "type": "string",
      "defaultValue": "contosodisk1"
    },
  "diskResourceId": {
    "defaultValue": "<your_managed_disk_resource_ID>",
    "type": "String"
  }
  }, 
  "resources": [
  {
    "type": "Microsoft.Compute/snapshots",
    "name": "[concat( parameters('diskName'),'_snapshot1')]",
    "location": "[resourceGroup().location]",
    "apiVersion": "2019-03-01",
    "properties": {
      "creationData": {
        "createOption": "Copy",
        "sourceResourceId": "[parameters('diskResourceId')]"
      },
      "incremental": true
    }
  }
  ]
}
```
---

## <a name="next-steps"></a>Passos seguintes

Se quiser ver o código de amostra que demonstre a capacidade diferencial de instantâneos incrementais, utilizando .NET, consulte [cópias de segurança dos Discos Geridos copy Azure para outra região com capacidade diferencial de instantâneos incrementais](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots).
