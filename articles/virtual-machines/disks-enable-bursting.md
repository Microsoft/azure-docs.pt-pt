---
title: Ativar a explosão do disco a pedido
description: Ativar o rebentamento do disco a pedido no seu disco gerido.
author: albecker1
ms.author: albecker
ms.date: 03/02/2021
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: f5865646200a783e7139bb5e22576ea404f58203
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102216654"
---
# <a name="enable-on-demand-bursting"></a>Permitir a explosão a pedido

As unidades premium de estado sólido (SSD) têm dois modelos de rebentamento disponíveis; rebentamento baseado em crédito e explosão a pedido. Este artigo abrange como mudar para o rebentamento a pedido. Os discos que utilizam o modelo a pedido podem irromper para além dos seus alvos originais. A explosão a pedido ocorre sempre que necessário pela carga de trabalho, até ao alvo máximo de explosão. Rebentamento a pedido incorre em taxas adicionais.

Para obter detalhes sobre a explosão do disco, consulte [o disco gerido a rebentar](disk-bursting.md).

> [!IMPORTANT]
> Não é preciso seguir os passos deste artigo para usar a explosão baseada no crédito. Por defeito, a explosão baseada no crédito está ativada em todos os discos elegíveis.

Antes de permitir o rebentamento a pedido, compreenda o seguinte:

[!INCLUDE [managed-disk-bursting-regions-limitations](../../includes/managed-disk-bursting-regions-limitations.md)]

### <a name="regional-availability"></a>Disponibilidade regional

[!INCLUDE [managed-disk-bursting-availability](../../includes/managed-disk-bursting-availability.md)]

## <a name="get-started"></a>Introdução

A explosão a pedido pode ser ativada com o módulo Azure PowerShell, o Azure CLI ou o Azure Resource Manager. Os exemplos que se seguem cobrem como criar um novo disco com rebentamento a pedido ativado e permitindo a explosão a pedido nos discos existentes.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Os cmdlets de rebentamento a pedido estão disponíveis na versão 5.5.0 e mais recente do módulo Az. Em alternativa, pode utilizar a [Concha da Nuvem Azure.](https://shell.azure.com/)
### <a name="create-an-empty-data-disk-with-on-demand-bursting"></a>Criar um disco de dados vazio com rebentamento a pedido

Um disco gerido deve ser maior do que 512 GiB para permitir a explosão a pedido. Substitua os `<myResourceGroupDisk>` `<myDataDisk>` parâmetros e, em seguida, executar o seguinte script para criar um SSD premium com rebentamento a pedido:

```azurepowershell
Set-AzContext -SubscriptionName <yourSubscriptionName>

$diskConfig = New-AzDiskConfig -Location 'WestCentralUS' -CreateOption Empty -DiskSizeGB 1024 -SkuName Premium_LRS -BurstingEnabled $true

$dataDisk = New-AzDisk -ResourceGroupName <myResourceGroupDisk> -DiskName <myDataDisk> -Disk $diskConfig
```

### <a name="enable-on-demand-bursting-on-an-existing-disk"></a>Permitir a explosão a pedido num disco existente

Um disco gerido deve ser maior do que 512 GiB para permitir a explosão a pedido. Substitua os `<myResourceGroupDisk>` `<myDataDisk>` parâmetros e execute este comando para permitir a explosão a pedido num disco existente:

```azurepowershell
New-AzDiskUpdateConfig -BurstingEnabled $true | Update-AzDisk -ResourceGroupName <myResourceGroupDisk> -DiskName <myDataDisk> //Set the flag to $false to disable on-demand bursting
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Os cmdlets de rebentamento a pedido estão disponíveis na versão 2.19.0 e mais recentes do [módulo Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). Em alternativa, pode utilizar a [Concha da Nuvem Azure.](https://shell.azure.com/)

### <a name="create-and-attach-a-on-demand-bursting-data-disk"></a>Criar e anexar um disco de dados de rebentamento a pedido

Um disco gerido deve ser maior do que 512 GiB para permitir a explosão a pedido. Substitua os `<yourDiskName>` `<yourResourceGroup>` , e `<yourVMName>` parâmetros, em seguida, executar os seguintes comandos para criar um SSD premium com rebentamento a pedido:

```azurecli
az disk create -g <yourResourceGroup> -n <yourDiskName> --size-gb 1024 --sku Premium_LRS -l westcentralus --enable-bursting true

az vm disk attach --vm-name <yourVMName> --name <yourDiskName> --resource-group <yourResourceGroup>
```

### <a name="enable-on-demand-bursting-on-an-existing-disk---cli"></a>Ativar a explosão a pedido num disco existente - CLI

Um disco gerido deve ser maior do que 512 GiB para permitir a explosão a pedido. Substitua os `<myResourceGroupDisk>` `<yourDiskName>` parâmetros e execute este comando para permitir a explosão a pedido num disco existente:

```azurecli
az disk update --name <yourDiskName> --resource-group <yourResourceGroup> --enable-bursting true //Set the flag to false to disable on-demand bursting
```

# <a name="azure-resource-manager"></a>[Azure Resource Manager](#tab/azure-resource-manager)

Com a API do `2020-09-30` disco, pode ativar a explosão a pedido em SSDs recém-criados ou já existentes superiores a 512 GiB. A `2020-09-30` API introduziu um novo `burstingEnabled` imóvel, . Por padrão, esta propriedade está definida como falsa. O modelo de amostra a seguir cria um SSD premium 1TiB no Centro-Oeste dos EUA, com a rutura do disco ativada:

```
{
  "$schema": "http://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskSkuName": {
        "type": "string",
        "defaultValue": "Premium_LRS" //Supported on premium SSDs only
},
    "dataDiskSizeInGb": {
      "type": "string",
      "defaultValue": "1024" //Supported on disk size > 512 GiB
},
    "location": {
      "type": "string",
      "defaultValue": "westcentralus" //Preview regions: West Central US
},
"diskApiVersion": {
      "type": "string",
      "defaultValue": "2020-09-30" //Preview supported version: 2020-09-30 or above
    }
  },
  "resources": [
    {
      "apiVersion": "[parameters('diskApiVersion')]",
      "type": "Microsoft.Compute/disks",
      "name": "[parameters('diskName')]",
      "location": "[parameters(location)]",
      "properties": {
        "creationData": {
          "createOption": "Empty"
        },
        "diskSizeGB": "[parameters('dataDiskSizeInGb')]",
        "burstingEnabled": "true" //Feature flag to enable disk bursting on disks > 512 GiB
      },
      "sku": {
        "name": "[parameters('diskSkuName')]"
      }
    ]
}
```
---
 
## <a name="next-steps"></a>Passos seguintes

Para aprender a obter informações sobre os seus recursos de explosão, consulte [as métricas de explosão do disco.](disks-metrics.md)