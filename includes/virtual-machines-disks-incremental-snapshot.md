---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 12/06/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 3361f4723c5a9776cb156417e57d609175d11621
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77446203"
---
Os instantâneos incrementais (pré-visualização) são cópias de segurança pontual para discos geridos que, quando retirados, consistem apenas em todas as alterações desde o último instantâneo. Quando tenta descarregar ou utilizar um instantâneo incremental, o VHD completo é utilizado. Esta nova capacidade para instantâneos de disco geridos pode potencialmente permitir que sejam mais rentáveis, uma vez que já não é obrigado a armazenar todo o disco com cada instantâneo individual, a menos que escolha. Tal como as imagens regulares, as imagens incrementais podem ser usadas para criar um disco gerido completo ou, para fazer uma imagem regular.

Existem algumas diferenças entre um instantâneo incremental e um instantâneo regular. Os instantâneos incrementais utilizarão sempre o armazenamento padrão dos HDDs, independentemente do tipo de armazenamento do disco, enquanto os instantâneos regulares podem utilizar SSDs premium. Se estiver a utilizar instantâneos regulares no Armazenamento Premium para aumentar as implementações vm, recomendamos que utilize imagens personalizadas no armazenamento padrão na Galeria de [Imagem Partilhada.](../articles/virtual-machines/linux/shared-image-galleries.md) Irá ajudá-lo a alcançar uma escala mais massiva com um custo mais baixo. Além disso, instantâneos incrementais potencialmente oferecem uma melhor fiabilidade com [armazenamento redundante de zona](../articles/storage/common/storage-redundancy-zrs.md) (ZRS). Se o ZRS estiver disponível na região selecionada, um instantâneo incremental utilizará o ZRS automaticamente. Se o ZRS não estiver disponível na região, então o instantâneo será indefinido para [armazenamento localmente redundante](../articles/storage/common/storage-redundancy-lrs.md) (LRS). Pode anular este comportamento e selecionar um manualmente, mas não o recomendamos.

Os instantâneos incrementais também oferecem uma capacidade diferencial, que está exclusivamente disponível para discos geridos. Permitem-lhe obter as alterações entre duas imagens incrementais dos mesmos discos geridos, até ao nível do bloco. Pode utilizar esta capacidade para reduzir a sua pegada de dados ao copiar instantâneos em todas as regiões.

## <a name="restrictions"></a>Restrições

- Atualmente, os instantâneos incrementais só estão disponíveis nos EUA Orientais, Leste dos EUA 2, Nos EUA Centrais, Oeste dos EUA, Canadá Leste, Canadá Central, Norte da Europa, Sudeste Asiático.
- Imagens incrementais atualmente não podem ser criadas depois de ter mudado o tamanho de um disco.
- Atualmente, os instantâneos incrementais não podem ser movidos entre subscrições.
- Atualmente, só pode gerar URIs SAS de até cinco instantâneos de uma determinada família instantânea a qualquer momento.
- Não é possível criar um instantâneo incremental para um disco específico fora da subscrição do disco.
- Até sete instantâneos incrementais por disco podem ser criados a cada cinco minutos.
- Um total de 200 instantâneos incrementais podem ser criados para um único disco.

## <a name="powershell"></a>PowerShell

Pode utilizar o Azure PowerShell para criar um instantâneo incremental. Necessitará da versão mais recente do Azure PowerShell, o seguinte comando irá instalá-lo ou atualizar a sua instalação existente para mais recente:

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

Uma vez instalado, inicie o login na sua sessão PowerShell com `az login`.

Para criar um instantâneo incremental com o Azure PowerShell, defina a configuração com [o Novo AzSnapShotConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshotconfig?view=azps-2.7.0) com o parâmetro `-Incremental` e, em seguida, passe-a como variável para [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot?view=azps-2.7.0) através do parâmetro `-Snapshot`.

Substitua `<yourDiskNameHere>`, `<yourResourceGroupNameHere>`e `<yourDesiredSnapShotNameHere>` com os seus valores, depois pode utilizar o seguinte script para criar um instantâneo incremental:

```PowerShell
# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName <yourDiskNameHere> -ResourceGroupName <yourResourceGroupNameHere>

# Create an incremental snapshot by setting the SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere> -SnapshotName <yourDesiredSnapshotNameHere> -Snapshot $snapshotConfig 
```

Pode identificar instantâneos incrementais do mesmo disco com o `SourceResourceId` e as propriedades `SourceUniqueId` de instantâneos. `SourceResourceId` é a identificação de recursos do Gestor de Recursos Azure do disco-mãe. `SourceUniqueId` é o valor herdado da propriedade `UniqueId` do disco. Se apagar um disco e, em seguida, criar um novo disco com o mesmo nome, o valor da propriedade `UniqueId` muda.

Pode utilizar `SourceResourceId` e `SourceUniqueId` para criar uma lista de todos os instantâneos associados a um determinado disco. Substitua `<yourResourceGroupNameHere>` pelo seu valor e depois pode usar o seguinte exemplo para listar as suas imagens incrementais existentes:

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

Pode criar um instantâneo incremental com o Azure CLI, vai precisar da versão mais recente do Azure CLI. 

No Windows, o seguinte comando irá instalar ou atualizar a sua instalação existente para a versão mais recente:
```PowerShell
Invoke-WebRequest -Uri https://aka.ms/installazurecliwindows -OutFile .\AzureCLI.msi; Start-Process msiexec.exe -Wait -ArgumentList '/I AzureCLI.msi /quiet'
```
No Linux, a instalação CLI variará consoante a versão do sistema operativo.  Consulte [a instalação do Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) para a sua versão Linux em particular.

Para criar um instantâneo incremental, utilize [az snapshot criar](https://docs.microsoft.com/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-create) com o parâmetro `--incremental`.

O exemplo seguinte cria um instantâneo incremental, substitua `<yourDesiredSnapShotNameHere>`, `<yourResourceGroupNameHere>`,`<exampleDiskName>`e `<exampleLocation>` com os seus próprios valores, e, em seguida, execute o exemplo:

```bash
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot create -g <yourResourceGroupNameHere> \
-n <yourDesiredSnapShotNameHere> \
-l <exampleLocation> \
--source "$sourceResourceId" \
--incremental
```

Pode identificar instantâneos incrementais do mesmo disco com o `SourceResourceId` e as propriedades `SourceUniqueId` de instantâneos. `SourceResourceId` é a identificação de recursos do Gestor de Recursos Azure do disco-mãe. `SourceUniqueId` é o valor herdado da propriedade `UniqueId` do disco. Se apagar um disco e, em seguida, criar um novo disco com o mesmo nome, o valor da propriedade `UniqueId` muda.

Pode utilizar `SourceResourceId` e `SourceUniqueId` para criar uma lista de todos os instantâneos associados a um determinado disco. O exemplo seguinte listará todos os instantâneos incrementais associados a um determinado disco, mas requer alguma configuração.

Este exemplo utiliza jq para consulta dos dados. Para executar o exemplo, tem de [instalar jq](https://stedolan.github.io/jq/download/).

Substitua `<yourResourceGroupNameHere>` e `<exampleDiskName>` com os seus valores, então pode usar o seguinte exemplo para listar os seus instantâneos incrementais existentes, desde que também tenha instalado jq:

```bash
sourceUniqueId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[uniqueId]' -o tsv)

 
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot list -g <yourResourceGroupNameHere> -o json \
| jq -cr --arg SUID "$sourceUniqueId" --arg SRID "$sourceResourceId" '.[] | select(.incremental==true and .creationData.sourceUniqueId==$SUID and .creationData.sourceResourceId==$SRID)'
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
