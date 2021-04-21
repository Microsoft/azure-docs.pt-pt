---
title: Use extensão de saúde de aplicação com conjuntos de escala de máquina virtual Azure
description: Saiba como utilizar a extensão De Saúde de Aplicação para monitorizar a saúde das suas aplicações implantadas em conjuntos de escala de máquina virtual.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: extensions
ms.date: 05/06/2020
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 381573ae40f6c31a1c7dbf18bc60be5944fff39e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762898"
---
# <a name="using-application-health-extension-with-virtual-machine-scale-sets"></a>Utilizar a extensão Estado da Aplicação com conjuntos de dimensionamento de máquinas virtuais
Monitorizar a saúde da sua aplicação é um sinal importante para gerir e melhorar a sua implementação. Os conjuntos de escala de máquina virtual Azure fornecem suporte para [upgrades rolantes,](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) incluindo [atualizações automáticas de imagem OS,](virtual-machine-scale-sets-automatic-upgrade.md)que dependem da monitorização de saúde de cada instância para atualizar a sua implementação. Também pode utilizar a extensão de saúde para monitorizar a saúde da aplicação de cada instância no seu conjunto de escalas e realizar reparações de instâncias com [reparações automáticas](virtual-machine-scale-sets-automatic-instance-repairs.md)de instâncias .

Este artigo descreve como pode utilizar a extensão De Saúde da Aplicação para monitorizar a saúde das suas aplicações implantadas em conjuntos de escala de máquina virtual.

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que está familiarizado com:
-   [Extensões](../virtual-machines/extensions/overview.md) de máquina virtual Azure
-   [Modificando conjuntos de](virtual-machine-scale-sets-upgrade-scale-set.md) escala de máquina virtual

## <a name="when-to-use-the-application-health-extension"></a>Quando utilizar a extensão de Saúde de Aplicação
A extensão de Saúde da Aplicação é implantada dentro de uma instância de conjunto de escala de máquina virtual e relatórios sobre a saúde VM de dentro da instância definida em escala. Pode configurar a extensão para sondar um ponto final da aplicação e atualizar o estado da aplicação nesse caso. Este estado de instância é verificado pela Azure para determinar se um caso é elegível para operações de upgrade.

Como a extensão informa a saúde de dentro de um VM, a extensão pode ser usada em situações em que sondas externas como Sondas de Saúde de Aplicação (que utilizam sondas de balanço de carga Azure personalizadas) não podem ser [usadas.](../load-balancer/load-balancer-custom-probe-overview.md)

## <a name="extension-schema"></a>Esquema de extensão

O seguinte JSON mostra o esquema para a extensão de Saúde de Aplicação. A extensão requer, no mínimo, um pedido "tcp", "http" ou "https" com uma porta ou um caminho de pedido associado, respectivamente.

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
| tipo | `ApplicationHealthLinux` (Linux), `ApplicationHealthWindows` (Janelas) | string |
| typeHandlerVersion | `1.0` | int |

### <a name="settings"></a>Definições

| Name | Valor / Exemplo | Tipo de Dados
| ---- | ---- | ----
| protocolo | `http` ou `https` ou `tcp` | string |
| porta | Opcional quando o protocolo é `http` `https` ou, obrigatório quando o protocolo é `tcp` | int |
| requestPath | Obrigatório quando o protocolo é `http` ou , não é permitido quando o protocolo `https` é `tcp` | string |

## <a name="deploy-the-application-health-extension"></a>Implementar a extensão de Saúde de Aplicação
Existem várias formas de implementar a extensão de Saúde da Aplicação para os conjuntos de escala, conforme detalhado nos exemplos abaixo.

### <a name="rest-api"></a>API REST

O exemplo a seguir adiciona a extensão de Saúde da Aplicação (com o nome myHealthExtension) à extensãoProfile no modelo de escala de um conjunto de escala baseado no Windows.

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

Utilize o [cmdlet Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) para adicionar a extensão de Saúde da Aplicação à definição do modelo definido em escala.

O exemplo a seguir adiciona a extensão de Saúde da Aplicação ao `extensionProfile` modelo de escala definida de um conjunto de escala baseado no Windows. O exemplo utiliza o novo módulo Az PowerShell.

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

Utilize [o conjunto de extensão az vmss](/cli/azure/vmss/extension#az_vmss_extension_set) para adicionar a extensão de Saúde da Aplicação à definição do modelo definido em escala.

O exemplo a seguir adiciona a extensão de Saúde da Aplicação ao modelo de escala de um conjunto de escala à base de Linux.

```azurecli-interactive
az vmss extension set \
  --name ApplicationHealthLinux \
  --publisher Microsoft.ManagedServices \
  --version 1.0 \
  --resource-group <myVMScaleSetResourceGroup> \
  --vmss-name <myVMScaleSet> \
  --settings ./extension.json
```
O extension.jsno conteúdo do ficheiro.

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
/var/lib/waagent/Microsoft.ManagedServices.ApplicationHealthLinux-<extension_version>/status
/var/log/azure/applicationhealth-extension
```

Os registos também captam periodicamente o estado de saúde da aplicação.

## <a name="next-steps"></a>Passos seguintes
Saiba como implementar a [sua aplicação](virtual-machine-scale-sets-deploy-app.md) em conjuntos de escala de máquina virtual.
