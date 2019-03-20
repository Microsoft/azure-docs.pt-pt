---
title: Utilizar a extensão de estado de funcionamento do aplicativo com conjuntos de dimensionamento de máquina virtual do Azure | Documentos da Microsoft
description: Saiba como utilizar a extensão de estado de funcionamento do aplicativo para monitorizar o estado de funcionamento das suas aplicações implementadas em conjuntos de dimensionamento de máquina virtual.
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
ms.openlocfilehash: d1cff1011e190e5fbb2874657cbdfbdc68bde0c0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58084400"
---
# <a name="using-application-health-extension-with-virtual-machine-scale-sets"></a>Conjuntos de extensão com o dimensionamento de máquinas virtuais com o estado de funcionamento da aplicação
Monitorizar o estado de funcionamento do aplicativo é um sinal importante para gerenciar e atualizar a sua implementação. Conjuntos de dimensionamento de máquina virtual do Azure fornecem suporte para [atualizações sem interrupção](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) incluindo [atualizações automáticas da imagem do SO](virtual-machine-scale-sets-automatic-upgrade.md), que dependem de monitorização de estado de funcionamento das instâncias individuais para atualizar a sua implementação .

Este artigo descreve como pode utilizar a extensão de estado de funcionamento do aplicativo para monitorizar o estado de funcionamento das suas aplicações implementadas em conjuntos de dimensionamento de máquina virtual.

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que está familiarizado com:
-   Máquina virtual do Azure [extensões](../virtual-machines/extensions/overview.md)
-   [Modificar](virtual-machine-scale-sets-upgrade-scale-set.md) conjuntos de dimensionamento de máquina virtual

## <a name="when-to-use-the-application-health-extension"></a>Quando utilizar a extensão de estado de funcionamento da aplicação
A extensão de estado de funcionamento da aplicação é implementada no interior de uma instância de conjunto de dimensionamento de máquina virtual e relatórios sobre o estado de funcionamento VM de dentro da instância de conjunto de dimensionamento. Pode configurar a extensão para pesquisa num ponto de extremidade do aplicativo e atualizar o estado do aplicativo nessa instância. Este estado de instância é verificado pelo Azure para determinar se uma instância é elegível para operações de atualização.

Como o extensão relatórios Estado de funcionamento de dentro de uma VM, a extensão pode ser utilizada em situações onde externo as sondas como sondas de estado de funcionamento do aplicativo (que utilizam o Balanceador de carga do Azure personalizadas [sondas](../load-balancer/load-balancer-custom-probe-overview.md)) não pode ser utilizado.

## <a name="extension-schema"></a>Esquema de extensão

O JSON seguinte mostra o esquema para a extensão de estado de funcionamento do aplicativo. A extensão requer pelo menos um "tcp" ou "http" pedido com uma porta associados ou caminho do pedido, respetivamente.

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
| publicador | `Microsoft.ManagedServices` | string |
| tipo | `ApplicationHealthLinux` (Linux), `ApplicationHealthWindows` (Windows) | string |
| typeHandlerVersion | `1.0` | int |

### <a name="settings"></a>Definições

| Name | Valor / exemplo | Tipo de Dados
| ---- | ---- | ----
| protocolo | `http` ou `tcp` | string |
| porta | Opcional quando o protocolo é `http`obrigatório quando o protocolo é `tcp` | int |
| requestPath | Obrigatório quando o protocolo é `http`, não permitido quando o protocolo é `tcp` | string |

## <a name="deploy-the-application-health-extension"></a>Implementar a extensão de estado de funcionamento da aplicação
Existem várias formas de implementar o estado de funcionamento do aplicativo extensão para seu dimensionamento define conforme explicado nos exemplos abaixo.

### <a name="rest-api"></a>API REST

O exemplo seguinte adiciona a extensão de estado de funcionamento da aplicação (com nome myHealthExtension) para o extensionProfile no modelo de conjunto de dimensionamento de um conjunto de dimensionamento baseados no Windows.

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
Utilize `PATCH` para editar uma extensão já implementada.

### <a name="azure-powershell"></a>Azure PowerShell

Utilize o [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) definição de modelo do conjunto de cmdlet para adicionar a extensão de estado de funcionamento da aplicação à escala.

O exemplo seguinte adiciona a extensão do Estado de funcionamento do aplicativo para o `extensionProfile` na escala de definir o modelo de um conjunto de dimensionamento baseados no Windows. O exemplo utiliza o novo módulo do PowerShell de Az.

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

Uso [conjunto de extensão az vmss](/cli/azure/vmss/extension#az-vmss-extension-set) para adicionar a extensão de estado de funcionamento do aplicativo para o conjunto de dimensionamento da definição do modelo.

O exemplo seguinte adiciona a extensão de estado de funcionamento do aplicativo para o modelo de conjunto de dimensionamento de um conjunto de dimensionamento baseados no Windows.

```azurecli-interactive
az vmss extension set \
  --name ApplicationHealthWindows \
  --publisher Microsoft.ManagedServices \
  --version 1.0 \
  --resource-group <myVMScaleSetResourceGroup> \
  --vmss-name <myVMScaleSet> \
  --settings ./extension.json
```


## <a name="troubleshoot"></a>Resolução de problemas
Resultado da execução de extensão é registado para arquivos encontrados nos seguintes diretórios:

```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.ManagedServices.ApplicationHealthWindows\<version>\
```

```Linux
/var/lib/waagent/apphealth
```

Os registos também periodicamente capturam o estado de funcionamento da aplicação.

## <a name="next-steps"></a>Passos Seguintes
Saiba como [implementar a sua aplicação](virtual-machine-scale-sets-deploy-app.md) conjuntos de dimensionamento de máquina virtual.
