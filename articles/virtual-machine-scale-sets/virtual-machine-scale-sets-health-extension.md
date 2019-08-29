---
title: Usar extensão de integridade do aplicativo com conjuntos de dimensionamento de máquinas virtuais do Azure | Microsoft Docs
description: Saiba como usar a extensão de integridade do aplicativo para monitorar a integridade de seus aplicativos implantados em conjuntos de dimensionamento de máquinas virtuais.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: drewm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2019
ms.author: manayar
ms.openlocfilehash: e074d76f9ed095725d99bddc9eb21925f4b3697c
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114472"
---
# <a name="using-application-health-extension-with-virtual-machine-scale-sets"></a>Usando a extensão de integridade do aplicativo com conjuntos de dimensionamento de máquinas virtuais
Monitorar a integridade do aplicativo é um sinal importante para gerenciar e atualizar sua implantação. Os conjuntos de dimensionamento de máquinas virtuais do Azure oferecem suporte para [atualizações sem interrupção](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) , incluindo [atualizações automáticas de imagem do sistema operacional](virtual-machine-scale-sets-automatic-upgrade.md), que dependem do monitoramento de integridade das instâncias individuais para atualizar sua implantação.

Este artigo descreve como você pode usar a extensão de integridade do aplicativo para monitorar a integridade de seus aplicativos implantados em conjuntos de dimensionamento de máquinas virtuais.

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que você esteja familiarizado com:
-   [Extensões](../virtual-machines/extensions/overview.md) de máquina virtual do Azure
-   [Modificando](virtual-machine-scale-sets-upgrade-scale-set.md) conjuntos de dimensionamento de máquinas virtuais

## <a name="when-to-use-the-application-health-extension"></a>Quando usar a extensão de integridade do aplicativo
A extensão de integridade do aplicativo é implantada em uma instância do conjunto de dimensionamento de máquinas virtuais e relata a integridade da VM de dentro da instância do conjunto de dimensionamento. Você pode configurar a extensão para investigação em um ponto de extremidade do aplicativo e atualizar o status do aplicativo nessa instância. Esse status de instância é verificado pelo Azure para determinar se uma instância está qualificada para operações de atualização.

Como a extensão relata a integridade de dentro de uma VM, a extensão pode ser usada em situações em que investigações externas, como investigações de integridade do aplicativo (que utilizam [investigações](../load-balancer/load-balancer-custom-probe-overview.md)de Azure Load Balancer personalizadas), não podem ser usadas.

## <a name="extension-schema"></a>Esquema de extensão

O JSON a seguir mostra o esquema para a extensão de integridade do aplicativo. A extensão requer, no mínimo, uma solicitação "TCP" ou "http" com uma porta ou um caminho de solicitação associado, respectivamente.

```json
{
  "type": "extensions",
  "name": "HealthExtension",
  "apiVersion": "2018-10-01",
  "location": "<location>",  
  "properties": {
    "publisher": "Microsoft.ManagedServices",
    "type": "< ApplicationHealthLinux or ApplicationHealthWindows>",
    "autoUpgradeMinorVersion": true,
    "typeHandlerVersion": "1.0",
    "settings": {
      "protocol": "<protocol>",
      "port": "<port>",
      "requestPath": "</requestPath>"
    }
  }
}  
```

### <a name="property-values"></a>Valores de propriedade

| Nome | Valor / exemplo | Tipo de Dados
| ---- | ---- | ---- 
| apiVersion | `2018-10-01` | date |
| publisher | `Microsoft.ManagedServices` | Cadeia de caracteres |
| type | `ApplicationHealthLinux`(Linux), `ApplicationHealthWindows` (Windows) | Cadeia de caracteres |
| typeHandlerVersion | `1.0` | int |

### <a name="settings"></a>Definições

| Name | Valor / exemplo | Tipo de Dados
| ---- | ---- | ----
| protocol | `http` ou `tcp` | Cadeia de caracteres |
| port | Opcional quando o protocolo `http`é, obrigatório quando o protocolo é`tcp` | int |
| requestPath | Obrigatório quando o protocolo `http`é, não é permitido quando o protocolo é`tcp` | Cadeia de caracteres |

## <a name="deploy-the-application-health-extension"></a>Implantar a extensão de integridade do aplicativo
Há várias maneiras de implantar a extensão de integridade do aplicativo em seus conjuntos de dimensionamento, conforme detalhado nos exemplos abaixo.

### <a name="rest-api"></a>API REST

O exemplo a seguir adiciona a extensão de integridade do aplicativo (com o nome myHealthExtension) ao extensionProfile no modelo do conjunto de dimensionamento de um conjunto de dimensionamento baseado no Windows.

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/extensions/myHealthExtension?api-version=2018-10-01`
```

```json
{
  "name": "myHealthExtension",
  "properties": {
    "publisher": " Microsoft.ManagedServices",
    "type": "< ApplicationHealthWindows>",
    "autoUpgradeMinorVersion": true,
    "typeHandlerVersion": "1.0",
    "settings": {
      "protocol": "<protocol>",
      "port": "<port>",
      "requestPath": "</requestPath>"
    }
  }
}
```
Use `PATCH` para editar uma extensão já implantada.

### <a name="azure-powershell"></a>Azure PowerShell

Use o cmdlet [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) para adicionar a extensão de integridade do aplicativo à definição do modelo do conjunto de dimensionamento.

O exemplo a seguir adiciona a extensão de integridade do `extensionProfile` aplicativo ao no modelo do conjunto de dimensionamento de um conjunto de dimensionamento baseado no Windows. O exemplo usa o novo módulo AZ PowerShell.

```azurepowershell-interactive
# Define the scale set variables
$vmScaleSetName = "myVMScaleSet"
$vmScaleSetResourceGroup = "myVMScaleSetResourceGroup"

# Define the Application Health extension properties
$publicConfig = @{"protocol" = "http"; "port" = 80; "requestPath" = "/healthEndpoint"};
$extensionName = "myHealthExtension"
$extensionType = "ApplicationHealthWindows"
$publisher = "Microsoft.ManagedServices"

# Get the scale set object
$vmScaleSet = Get-AzVmss `
  -ResourceGroupName $vmScaleSetResourceGroup `
  -VMScaleSetName $vmScaleSetName

# Add the Application Health extension to the scale set model
Add-AzVmssExtension -VirtualMachineScaleSet $vmScaleSet `
  -Name $extensionName `
  -Publisher $publisher `
  -Setting $publicConfig `
  -Type $extensionType `
  -TypeHandlerVersion "1.0" `
  -AutoUpgradeMinorVersion $True

# Update the scale set
Update-AzVmss -ResourceGroupName $vmScaleSetResourceGroup `
  -Name $vmScaleSetName `
  -VirtualMachineScaleSet $vmScaleSet
```


### <a name="azure-cli-20"></a>CLI 2.0 do Azure

Use [AZ vmss Extension Set](/cli/azure/vmss/extension#az-vmss-extension-set) para adicionar a extensão de integridade do aplicativo à definição do modelo do conjunto de dimensionamento.

O exemplo a seguir adiciona a extensão de integridade do aplicativo ao modelo do conjunto de dimensionamento de um conjunto de dimensionamento baseado em Linux.

```azurecli-interactive
az vmss extension set \
  --name ApplicationHealthLinux \
  --publisher Microsoft.ManagedServices \
  --version 1.0 \
  --resource-group <myVMScaleSetResourceGroup> \
  --vmss-name <myVMScaleSet> \
  --settings ./extension.json
```
O conteúdo do arquivo extension. JSON.

```json
{
  "protocol": "<protocol>",
  "port": "<port>",
  "requestPath": "</requestPath>"
}
```


## <a name="troubleshoot"></a>Resolução de problemas
A saída de execução de extensão é registrada nos arquivos encontrados nos seguintes diretórios:

```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.ManagedServices.ApplicationHealthWindows\<version>\
```

```Linux
/var/lib/waagent/apphealth
```

Os logs também capturam periodicamente o status de integridade do aplicativo.

## <a name="next-steps"></a>Passos Seguintes
Saiba como [implantar seu aplicativo](virtual-machine-scale-sets-deploy-app.md) em conjuntos de dimensionamento de máquinas virtuais.
