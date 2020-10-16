---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 9e7386e21442b5a76aae656a36e2858b52ecef65
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87102917"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

### <a name="supported-regions"></a>Regiões suportadas
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

## <a name="restrictions"></a>Restrições

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="powershell"></a>PowerShell

Pode utilizar o Azure PowerShell para criar uma imagem incremental. Necessitará da versão mais recente do Azure PowerShell, o seguinte comando irá instalá-lo ou atualizar a sua instalação existente para o mais tardar:

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

Uma vez instalado, inicie sessão no PowerShell com `Connect-AzAccount` .

Para criar um instantâneo incremental com Azure PowerShell, desafie a configuração com [New-AzSnapShotConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshotconfig?view=azps-2.7.0) com o `-Incremental` parâmetro e, em seguida, passe-a como uma variável para [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot?view=azps-2.7.0) através do `-Snapshot` parâmetro.

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

## <a name="resource-manager-template"></a>Modelo do Resource Manager

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

## <a name="next-steps"></a>Passos seguintes

Se quiser ver o código de amostra que demonstre a capacidade diferencial de instantâneos incrementais, utilizando .NET, consulte [cópias de segurança dos Discos Geridos copy Azure para outra região com capacidade diferencial de instantâneos incrementais](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots).
