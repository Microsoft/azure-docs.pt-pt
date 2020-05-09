---
title: Aplicação Aplicação Extensão de saúde com conjuntos de escala de máquina seleção azul
description: Saiba como utilizar a extensão de Saúde de Aplicação para monitorizar a saúde das suas aplicações implementadas em conjuntos de escala de máquinas virtuais.
author: mimckitt
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: mimckitt
ms.openlocfilehash: 30f68d22a228e6de596e6999490ea7789ab21547
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864373"
---
# <a name="using-application-health-extension-with-virtual-machine-scale-sets"></a>Utilizar a extensão Estado da Aplicação com conjuntos de dimensionamento de máquinas virtuais
Monitorizar a saúde da sua aplicação é um sinal importante para gerir e melhorar a sua implementação. Os conjuntos de escala de máquinas virtuais Azure fornecem suporte para [atualizações de rolos,](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) incluindo [atualizações automáticas de imagem de OS,](virtual-machine-scale-sets-automatic-upgrade.md)que dependem da monitorização da saúde das instâncias individuais para atualizar a sua implementação. Também pode utilizar uma extensão de saúde para monitorizar a saúde da aplicação de cada instância no seu conjunto de escala e efetuar reparações por exemplo utilizando [reparações automáticas](virtual-machine-scale-sets-automatic-instance-repairs.md)de instâncias .

Este artigo descreve como pode utilizar a extensão de Saúde de Aplicação para monitorizar a saúde das suas aplicações implementadas em conjuntos de escala de máquinas virtuais.

## <a name="prerequisites"></a>Pré-requisitos
Este artigo assume que está familiarizado com:
-   [Extensões](../virtual-machines/extensions/overview.md) de máquinas virtuais Azure
-   [Modificação de](virtual-machine-scale-sets-upgrade-scale-set.md) conjuntos de escala de máquinavirtual

## <a name="when-to-use-the-application-health-extension"></a>Quando utilizar a extensão de saúde de aplicação
A extensão de saúde de aplicação é implantada dentro de uma instância de conjunto de escala de máquina virtual e relatórios sobre a saúde vm de dentro da instância definida de escala. Pode configurar a extensão para sondar um ponto final da aplicação e atualizar o estado da aplicação nessa instância. Este estado de exemplo é verificado pelo Azure para determinar se uma instância é elegível para operações de atualização.

Como a extensão reporta a saúde de dentro de um VM, a extensão pode ser usada em situações em que sondas externas como sondas de saúde de aplicação (que utilizam [sondas personalizadas](../load-balancer/load-balancer-custom-probe-overview.md)do Equilíbrio de Carga Azure) não podem ser usadas.

## <a name="extension-schema"></a>Esquema de extensão

O seguinte JSON mostra o esquema para a extensão da Saúde de Aplicação. A extensão requer, no mínimo, um pedido "tcp", "http" ou "https" com uma via portuária ou de pedido associada, respectivamente.

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

### <a name="property-values"></a>Valores patrimoniais

| Name | Valor / Exemplo | Tipo de Dados
| ---- | ---- | ---- 
| apiVersion | `2018-10-01` | data |
| publicador | `Microsoft.ManagedServices` | string |
| tipo | `ApplicationHealthLinux`(Linux), `ApplicationHealthWindows` (Janelas) | string |
| typeHandlerVersion | `1.0` | int |

### <a name="settings"></a>Definições

| Name | Valor / Exemplo | Tipo de Dados
| ---- | ---- | ----
| protocolo | `http`ou `https` ou ou`tcp` | string |
| porta | Opcional quando `http` o `https`protocolo é ou, obrigatório quando o protocolo é`tcp` | int |
| requestPath | Obrigatório quando o `http` `https`protocolo é ou , não permitido quando o protocolo é`tcp` | string |

## <a name="deploy-the-application-health-extension"></a>Implementar a extensão da Saúde de Aplicação
Existem várias formas de implementar a extensão de Saúde de Aplicação aos seus conjuntos de escala, conforme detalhado nos exemplos abaixo.

### <a name="rest-api"></a>API REST

O exemplo seguinte adiciona a extensão de saúde da aplicação (com nome myHealthExtension) à extensãoPerfil no modelo de conjunto de escala de um conjunto de escala baseado no Windows.

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
Utilize `PATCH` para editar uma extensão já implantada.

### <a name="azure-powershell"></a>Azure PowerShell

Utilize o cmdlet [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) para adicionar a extensão de saúde da aplicação à definição do modelo de conjunto de escala.

O exemplo seguinte adiciona a `extensionProfile` extensão de Saúde da Aplicação ao modelo de conjunto de escala de um conjunto de escala baseado no Windows. O exemplo utiliza o novo módulo Az PowerShell.

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

Utilize o [conjunto de extensão az vmss](/cli/azure/vmss/extension#az-vmss-extension-set) para adicionar a extensão de saúde de aplicação à definição do modelo de conjunto de escala.

O exemplo seguinte adiciona a extensão da Saúde da Aplicação ao modelo de conjunto de escala de um conjunto de escala baseado em Linux.

```azurecli-interactive
az vmss extension set \
  --name ApplicationHealthLinux \
  --publisher Microsoft.ManagedServices \
  --version 1.0 \
  --resource-group <myVMScaleSetResourceGroup> \
  --vmss-name <myVMScaleSet> \
  --settings ./extension.json
```
O conteúdo do ficheiro extension.json.

```json
{
  "protocol": "<protocol>",
  "port": "<port>",
  "requestPath": "</requestPath>"
}
```


## <a name="troubleshoot"></a>Resolução de problemas
A saída de execução de extensão é registada em ficheiros encontrados nos seguintes diretórios:

```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.ManagedServices.ApplicationHealthWindows\<version>\
```

```Linux
/var/lib/waagent/apphealth
```

Os registos também captam periodicamente o estado de saúde da aplicação.

## <a name="next-steps"></a>Passos seguintes
Aprenda a implementar a [sua aplicação](virtual-machine-scale-sets-deploy-app.md) em conjuntos de escala de máquinas virtuais.
