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
ms.openlocfilehash: e39f294f7902eabef401d4c8145f4f19a07f267f
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71224573"
---
# <a name="creating-an-incremental-snapshot-preview-for-managed-disks"></a>Criando um instantâneo incremental (versão prévia) para discos gerenciados

Instantâneos incrementais (visualização) são backups pontuais para discos gerenciados que, quando tirados, consistem apenas em todas as alterações desde o último instantâneo. Quando você tenta baixar ou, de outra forma, usa um instantâneo incremental, o VHD completo é usado. Essa nova funcionalidade para instantâneos de disco gerenciado pode potencialmente permitir que eles sejam mais econômicos, já que você não precisa mais armazenar todo o disco com cada instantâneo individual, a menos que você escolha. Assim como instantâneos regulares, instantâneos incrementais podem ser usados para criar um disco gerenciado completo ou para criar um instantâneo normal.

Há algumas diferenças entre um instantâneo incremental e um instantâneo normal. Os instantâneos incrementais sempre usarão o armazenamento de HDDs padrão, independentemente do tipo de armazenamento do disco, enquanto os instantâneos regulares podem usar o SSDs Premium. Se você estiver usando instantâneos regulares no armazenamento Premium para escalar verticalmente as implantações de VM, recomendamos que você use imagens personalizadas no armazenamento Standard na [Galeria de imagens compartilhadas](../articles/virtual-machines/linux/shared-image-galleries.md). Ele o ajudará a alcançar uma escala mais maciça com menor custo. Além disso, os instantâneos incrementais podem oferecer melhor confiabilidade com ZRS ( [armazenamento com redundância de zona](../articles/storage/common/storage-redundancy-zrs.md) ). Se ZRS estiver disponível na região selecionada, um instantâneo incremental usará o ZRS automaticamente. Se ZRS não estiver disponível na região, o instantâneo usará como padrão o [armazenamento com redundância local](../articles/storage/common/storage-redundancy-lrs.md) (LRS). Você pode substituir esse comportamento e selecionar um manualmente, mas não é recomendável.

Os instantâneos incrementais também oferecem um recurso diferencial, que está disponível exclusivamente para discos gerenciados. Eles permitem que você obtenha as alterações entre dois instantâneos incrementais dos mesmos discos gerenciados, até o nível de bloco. Você pode usar essa capacidade para reduzir o volume de dados ao copiar instantâneos entre regiões.

Se você ainda não se inscreveu para a versão prévia e gostaria de começar a usar instantâneos incrementais, envie um AzureDisks@microsoft.com email para para obter acesso à visualização pública.

## <a name="restrictions"></a>Restrições

- Não é possível criar instantâneos incrementais no momento depois de alterar o tamanho de um disco.
- Os instantâneos incrementais atualmente não podem ser movidos entre assinaturas.
- No momento, você pode gerar apenas URIs SAS de até cinco instantâneos de uma família de instantâneos específica em um determinado momento.
- Você não pode criar um instantâneo incremental para um disco específico fora da assinatura desse disco.
- Até sete instantâneos incrementais por disco podem ser criados a cada cinco minutos.
- Um total de 200 instantâneos incrementais podem ser criados para um único disco.

## <a name="powershell"></a>PowerShell

Você pode usar Azure PowerShell para criar um instantâneo incremental. Você pode instalar a versão mais recente do PowerShell localmente. Você precisará da versão mais recente do Azure PowerShell, o seguinte comando o instalará ou atualizará a instalação existente para o mais recente:

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

Depois de instalado, faça logon na sua sessão do PowerShell `az login`com.

Substitua `<yourDiskNameHere>`, `<yourResourceGroupNameHere>` e`<yourDesiredSnapShotNameHere>` por seus valores, você pode usar o script a seguir para criar um instantâneo incremental:

```PowerShell
# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName <yourDiskNameHere> -ResourceGroupName <yourResourceGroupNameHere>

# Create an incremental snapshot by setting:
# 1. Incremental property
# 2. SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere> -SnapshotName <yourDesiredSnapshotNameHere> -Snapshot $snapshotConfig 

# You can identify incremental snapshots of the same disk by using the SourceResourceId and SourceUniqueId properties of snapshots. 
# SourceResourceId is the Azure Resource Manager resource ID of the parent disk. 
# SourceUniqueId is the value inherited from the UniqueId property of the disk. If you delete a disk and then create a disk with the same name, the value of the UniqueId property will change. 
# Following script shows how to get all the incremental snapshots in a resource group of same disk
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

## <a name="cli"></a>CLI

Você pode criar um instantâneo incremental com o CLI do Azure usando [AZ snapshot Create](https://docs.microsoft.com/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-create). Um comando de exemplo seria semelhante ao seguinte:

```bash
az snapshot create -g <exampleResourceGroup> \
-n <exampleSnapshotName> \
-l <exampleLocation> \
--source <exampleVMId> \
--incremental
```

Você também pode identificar quais instantâneos são instantâneos incrementais na CLI com usando o `--query` parâmetro em [AZ snapshot show](https://docs.microsoft.com/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-show). Você pode usar esse parâmetro para consultar diretamente as propriedades **SourceResourceId** e **SourceUniqueId** de instantâneos. SourceResourceId é a ID de recurso Azure Resource Manager do disco pai. **SourceUniqueId** é o valor herdado da propriedade **UniqueId** do disco. Se você excluir um disco e, em seguida, criar um disco com o mesmo nome, o valor da propriedade **UniqueId** será alterado.

Exemplos de consultas seriam semelhantes ao seguinte:

```bash
az snapshot show -g <exampleResourceGroup> \
-n <yourSnapShotName> \
--query [creationData.sourceResourceId] -o tsv

az snapshot show -g <exampleResourceGroup> \
-n <yourSnapShotName> \
--query [creationData.sourceUniqueId] -o tsv
```

## <a name="next-steps"></a>Passos seguintes

Se você ainda não se inscreveu para a versão prévia e gostaria de começar a usar instantâneos incrementais, envie um AzureDisks@microsoft.com email para para obter acesso à visualização pública.
