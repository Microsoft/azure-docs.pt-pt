---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/23/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a7e9e36f75d0b0638fadbf92e713a924e816807d
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012379"
---
Instantâneos incrementais (visualização) são backups pontuais para discos gerenciados que, quando tirados, consistem apenas em todas as alterações desde o último instantâneo. Quando você tenta baixar ou, de outra forma, usa um instantâneo incremental, o VHD completo é usado. Essa nova funcionalidade para instantâneos de disco gerenciado pode potencialmente permitir que eles sejam mais econômicos, já que você não precisa mais armazenar todo o disco com cada instantâneo individual, a menos que você escolha. Assim como instantâneos regulares, instantâneos incrementais podem ser usados para criar um disco gerenciado completo ou para criar um instantâneo normal.

Há algumas diferenças entre um instantâneo incremental e um instantâneo normal. Os instantâneos incrementais sempre usarão o armazenamento de HDDs padrão, independentemente do tipo de armazenamento do disco, enquanto os instantâneos regulares podem usar o SSDs Premium. Se você estiver usando instantâneos regulares no armazenamento Premium para escalar verticalmente as implantações de VM, recomendamos o uso de imagens personalizadas no armazenamento Standard na [Galeria de imagens compartilhadas](../articles/virtual-machines/linux/shared-image-galleries.md). Ele o ajudará a alcançar uma escala mais maciça com menor custo. Além disso, os instantâneos incrementais podem oferecer melhor confiabilidade com ZRS ( [armazenamento com redundância de zona](../articles/storage/common/storage-redundancy-zrs.md) ). Se ZRS estiver disponível na região selecionada, um instantâneo incremental usará o ZRS automaticamente. Se ZRS não estiver disponível na região, o instantâneo usará como padrão o [armazenamento com redundância local](../articles/storage/common/storage-redundancy-lrs.md) (LRS). Você pode substituir esse comportamento e selecionar um manualmente, mas não é recomendável.

Os instantâneos incrementais também oferecem um recurso diferencial, que está disponível exclusivamente para discos gerenciados. Eles permitem que você obtenha as alterações entre dois instantâneos incrementais dos mesmos discos gerenciados, até o nível de bloco. Você pode usar essa capacidade para reduzir o volume de dados ao copiar instantâneos entre regiões.

Se você ainda não se inscreveu para a versão prévia e gostaria de começar a usar instantâneos incrementais, envie-nos um email para AzureDisks@microsoft.com para obter acesso à visualização pública.

## <a name="restrictions"></a>Restrições

- Os instantâneos incrementais atualmente só estão disponíveis no EUA Central ocidental e Europa Setentrional.
- Não é possível criar instantâneos incrementais no momento depois de alterar o tamanho de um disco.
- Os instantâneos incrementais atualmente não podem ser movidos entre assinaturas.
- No momento, você pode gerar apenas URIs SAS de até cinco instantâneos de uma família de instantâneos específica em um determinado momento.
- Você não pode criar um instantâneo incremental para um disco específico fora da assinatura desse disco.
- Até sete instantâneos incrementais por disco podem ser criados a cada cinco minutos.
- Um total de 200 instantâneos incrementais podem ser criados para um único disco.

## <a name="powershell"></a>PowerShell

Você pode usar Azure PowerShell para criar um instantâneo incremental. Você precisará da versão mais recente do Azure PowerShell, o seguinte comando o instalará ou atualizará a instalação existente para o mais recente:

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

Depois de instalado, faça logon na sua sessão do PowerShell com `az login`.

Para criar um instantâneo incremental com Azure PowerShell, defina a configuração com [New-AzSnapShotConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshotconfig?view=azps-2.7.0) com o parâmetro `-Incremental` e, em seguida, passe-o como uma variável para [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot?view=azps-2.7.0) por meio do parâmetro `-Snapshot`.

Substitua `<yourDiskNameHere>`, `<yourResourceGroupNameHere>`e `<yourDesiredSnapShotNameHere>` pelos valores e, em seguida, você pode usar o script a seguir para criar um instantâneo incremental:

```PowerShell
# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName <yourDiskNameHere> -ResourceGroupName <yourResourceGroupNameHere>

# Create an incremental snapshot by setting the SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere> -SnapshotName <yourDesiredSnapshotNameHere> -Snapshot $snapshotConfig 
```

Você pode identificar instantâneos incrementais do mesmo disco com o `SourceResourceId` e as propriedades de `SourceUniqueId` de instantâneos. `SourceResourceId` é a ID de recurso Azure Resource Manager do disco pai. `SourceUniqueId` é o valor herdado da propriedade `UniqueId` do disco. Se você for excluir um disco e, em seguida, criar um novo disco com o mesmo nome, o valor da propriedade `UniqueId` será alterado.

Você pode usar `SourceResourceId` e `SourceUniqueId` para criar uma lista de todos os instantâneos associados a um disco específico. Substitua `<yourResourceGroupNameHere>` pelo valor e, em seguida, você pode usar o exemplo a seguir para listar seus instantâneos incrementais existentes:

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

## <a name="cli"></a>CLI

Você pode criar um instantâneo incremental com o CLI do Azure, será necessário ter a versão mais recente do CLI do Azure. O comando a seguir instalará ou atualizará sua instalação existente para a versão mais recente:

```PowerShell
Invoke-WebRequest -Uri https://aka.ms/installazurecliwindows -OutFile .\AzureCLI.msi; Start-Process msiexec.exe -Wait -ArgumentList '/I AzureCLI.msi /quiet'
```

Para criar um instantâneo incremental, use [AZ snapshot Create](https://docs.microsoft.com/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-create) com o parâmetro `--incremental`.

O exemplo a seguir cria um instantâneo incremental, substitui `<yourDesiredSnapShotNameHere>`, `<yourResourceGroupNameHere>`,`<exampleDiskName>`e `<exampleLocation>` com seus próprios valores e, em seguida, executa o exemplo:

```bash
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot create -g <yourResourceGroupNameHere> \
-n <yourDesiredSnapShotNameHere> \
-l <exampleLocation> \
--source "$sourceResourceId" \
--incremental
```

Você pode identificar instantâneos incrementais do mesmo disco com o `SourceResourceId` e as propriedades de `SourceUniqueId` de instantâneos. `SourceResourceId` é a ID de recurso Azure Resource Manager do disco pai. `SourceUniqueId` é o valor herdado da propriedade `UniqueId` do disco. Se você for excluir um disco e, em seguida, criar um novo disco com o mesmo nome, o valor da propriedade `UniqueId` será alterado.

Você pode usar `SourceResourceId` e `SourceUniqueId` para criar uma lista de todos os instantâneos associados a um disco específico. O exemplo a seguir listará todos os instantâneos incrementais associados a um disco específico, mas exigirá alguma configuração.

Este exemplo usa JQ para consultar os dados. Para executar o exemplo, você deve [instalar o JQ](https://stedolan.github.io/jq/download/).

Substitua `<yourResourceGroupNameHere>` e `<exampleDiskName>` com seus valores. em seguida, você pode usar o exemplo a seguir para listar seus instantâneos incrementais existentes, desde que você também tenha instalado o JQ:

```bash
sourceUniqueId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[uniqueId]' -o tsv)

 
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot list -g <yourResourceGroupNameHere> -o json \
| jq -cr --arg SUID "$sourceUniqueId" --arg SRID "$sourceResourceId" '.[] | select(.incremental==true and .creationData.sourceUniqueId==$SUID and .creationData.sourceResourceId==$SRID)'
```

## <a name="resource-manager-template"></a>Modelo do Resource Manager

Você também pode usar modelos de Azure Resource Manager para criar um instantâneo incremental. Você precisará certificar-se de que apiVersion está definido como **2019-03-01** e que a propriedade incremental também é definida como true. O trecho a seguir é um exemplo de como criar um instantâneo incremental com modelos do Resource Manager:

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

Se você ainda não se inscreveu para a versão prévia e gostaria de começar a usar instantâneos incrementais, envie-nos um email para AzureDisks@microsoft.com para obter acesso à visualização pública.
