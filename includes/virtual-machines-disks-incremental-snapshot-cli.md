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
ms.openlocfilehash: 4bdeef537556db94338ed50fcfa6e9d88431f25a
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "96016267"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="restrictions"></a>Restrições

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="cli"></a>CLI

Pode criar uma imagem incremental com o CLI Azure, precisará da versão mais recente do Azure CLI. 

No Windows, o seguinte comando irá instalar ou atualizar a instalação existente para a versão mais recente:
```PowerShell
Invoke-WebRequest -Uri https://aka.ms/installazurecliwindows -OutFile .\AzureCLI.msi; Start-Process msiexec.exe -Wait -ArgumentList '/I AzureCLI.msi /quiet'
```
No Linux, a instalação CLI variará consoante a versão do sistema operativo.  Consulte [a Instalação do CLI Azure](/cli/azure/install-azure-cli) para a sua versão Linux em particular.

Para criar um instantâneo incremental, use [az snapshot criar](/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-create) com o `--incremental` parâmetro.

O exemplo a seguir cria um instantâneo incremental, `<yourDesiredSnapShotNameHere>` substitua, , , e pelos seus `<yourResourceGroupNameHere>` `<exampleDiskName>` `<exampleLocation>` próprios valores, e depois executar o exemplo:

```bash
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot create -g <yourResourceGroupNameHere> \
-n <yourDesiredSnapShotNameHere> \
-l <exampleLocation> \
--source "$sourceResourceId" \
--incremental
```

Pode identificar instantâneos incrementais do mesmo disco com `SourceResourceId` as propriedades e propriedades dos `SourceUniqueId` instantâneos. `SourceResourceId` é o ID de recursos Azure Resource Manager do disco-mãe. `SourceUniqueId` é o valor herdado da `UniqueId` propriedade do disco. Se eliminar um disco e criar um novo disco com o mesmo nome, o valor da `UniqueId` propriedade muda.

Pode utilizar `SourceResourceId` e criar uma lista de todos os `SourceUniqueId` instantâneos associados a um disco em particular. O exemplo seguinte irá listar todos os instantâneos incrementais associados a um disco específico, mas, requer alguma configuração.

Este exemplo utiliza jq para consulta dos dados. Para dar o exemplo, tem de [instalar o JQ](https://stedolan.github.io/jq/download/).

Substitua `<yourResourceGroupNameHere>` e `<exampleDiskName>` pelos seus valores, então pode usar o seguinte exemplo para listar as suas imagens incrementais existentes, desde que também tenha instalado jq:

```bash
sourceUniqueId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[uniqueId]' -o tsv)

 
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot list -g <yourResourceGroupNameHere> -o json \
| jq -cr --arg SUID "$sourceUniqueId" --arg SRID "$sourceResourceId" '.[] | select(.incremental==true and .creationData.sourceUniqueId==$SUID and .creationData.sourceResourceId==$SRID)'
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