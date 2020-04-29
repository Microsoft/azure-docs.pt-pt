---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 3eec6583ebdff35d7e40d2eec305a947de0cb87c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79299465"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

### <a name="supported-regions"></a>Regiões suportadas
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

## <a name="restrictions"></a>Restrições

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="powershell"></a>PowerShell

Pode utilizar o Azure PowerShell para criar um instantâneo incremental. Necessitará da versão mais recente do Azure PowerShell, o seguinte comando irá instalá-lo ou atualizar a sua instalação existente para mais recente:

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

Uma vez instalado, inicie o login `az login`na sua sessão PowerShell com .

Para criar um instantâneo incremental com o Azure PowerShell, defina a `-Incremental` configuração com [o New-AzSnapShotConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshotconfig?view=azps-2.7.0) com o parâmetro e, em seguida, passe-a como variável para [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot?view=azps-2.7.0) através do `-Snapshot` parâmetro.

Substitua, `<yourDiskNameHere>` `<yourResourceGroupNameHere>` `<yourDesiredSnapShotNameHere>` e com os seus valores, depois pode utilizar o seguinte script para criar um instantâneo incremental:

```PowerShell
# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName <yourDiskNameHere> -ResourceGroupName <yourResourceGroupNameHere>

# Create an incremental snapshot by setting the SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere> -SnapshotName <yourDesiredSnapshotNameHere> -Snapshot $snapshotConfig 
```

Pode identificar instantâneos incrementais do `SourceResourceId` mesmo `SourceUniqueId` disco com as propriedades e as propriedades dos instantâneos. `SourceResourceId`é o Id de recurso do gestor de recursos Azure do disco-mãe. `SourceUniqueId`é o valor herdado da `UniqueId` propriedade do disco. Se apagar um disco e, em seguida, criar um novo `UniqueId` disco com o mesmo nome, o valor da propriedade muda.

Pode utilizar `SourceResourceId` `SourceUniqueId` e criar uma lista de todos os instantâneos associados a um determinado disco. Substitua `<yourResourceGroupNameHere>` pelo seu valor e depois pode usar o seguinte exemplo para listar as suas imagens incrementais existentes:

```PowerShell
$snapshots = Get-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere>

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

Também pode usar modelos do Gestor de Recursos Azure para criar um instantâneo incremental. Terá de se certificar de que a apiVersion está definida para **2019-03-01** e que a propriedade incremental também está definida como verdadeira. O seguinte corte é um exemplo de como criar um instantâneo incremental com modelos de Gestor de Recursos:

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

Se quiser ver o código de amostra que demonstra a capacidade diferencial de instantâneos incrementais, utilizando .NET, consulte cópia [seleções de discos geridos por Copy Azure para outra região com capacidade diferencial de instantâneos incrementais](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots).
