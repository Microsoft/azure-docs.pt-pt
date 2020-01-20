---
title: Usar o sequenciamento de extensão com conjuntos de dimensionamento de máquinas virtuais do Azure
description: Saiba como sequenciar o provisionamento de extensão ao implantar várias extensões em conjuntos de dimensionamento de máquinas virtuais.
author: mayanknayar
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: manayar
ms.openlocfilehash: cde3fb8b56d8509a45bde00dde55e3c69d015b8e
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278057"
---
# <a name="sequence-extension-provisioning-in-virtual-machine-scale-sets"></a>Provisionamento de extensão de sequência em conjuntos de dimensionamento de máquinas virtuais
As extensões de máquina virtual do Azure fornecem recursos como configuração e gerenciamento de pós-implantação, monitoramento, segurança e muito mais. As implantações de produção normalmente usam uma combinação de várias extensões configuradas para as instâncias de VM para obter os resultados desejados.

Ao usar várias extensões em uma máquina virtual, é importante garantir que as extensões que exigem os mesmos recursos do sistema operacional não estejam tentando adquirir esses recursos ao mesmo tempo. Algumas extensões também dependem de outras extensões para fornecer as configurações necessárias, como configurações de ambiente e segredos. Sem a ordenação correta e o sequenciamento em vigor, as implantações de extensão dependentes podem falhar.

Este artigo fornece detalhes sobre como você pode sequenciar extensões a serem configuradas para as instâncias de VM em conjuntos de dimensionamento de máquinas virtuais.

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que você esteja familiarizado com:
-   [Extensões](../virtual-machines/extensions/overview.md) de máquina virtual do Azure
-   [Modificando](virtual-machine-scale-sets-upgrade-scale-set.md) conjuntos de dimensionamento de máquinas virtuais

## <a name="when-to-use-extension-sequencing"></a>Quando usar o sequenciamento de extensão
Extensões de sequenciamento não são obrigatórias para conjuntos de dimensionamento e, a menos que especificado, as extensões podem ser provisionadas em uma instância de conjunto de dimensionamento em qualquer ordem.

Por exemplo, se o modelo do conjunto de dimensionamento tiver duas extensões – Extensiona e ExtensionB – especificado no modelo, qualquer uma das seguintes sequências de provisionamento poderá ocorrer:
-   Extensãoa-> ExtensionB
-   Extensão ExtensionB->a

Se seu aplicativo exigir que a extensão a seja sempre provisionada antes da extensão B, você deverá usar o sequenciamento de extensão, conforme descrito neste artigo. Com o sequenciamento de extensão, apenas uma sequência ocorrerá agora:
-   Extensãoa-> ExtensionB

Todas as extensões não especificadas em uma sequência de provisionamento definida podem ser provisionadas a qualquer momento, incluindo antes, depois ou durante uma sequência definida. O sequenciamento de extensão especifica apenas que uma extensão específica será provisionada após outra extensão específica. Ele não afeta o provisionamento de qualquer outra extensão definida no modelo.

Por exemplo, se o modelo do conjunto de dimensionamento tiver três extensões – extensão A, extensão B e extensão C – especificado no modelo e a extensão C for definida para ser provisionada após A extensão A, uma das seguintes sequências de provisionamento poderá ocorrer:
-   Extensãoa-> ExtensionC-> ExtensionB
-   Extensão ExtensionB-> > ExtensionC
-   Extensãoa-> ExtensionB-> ExtensionC

Se você precisar garantir que nenhuma outra extensão seja provisionada enquanto a sequência de extensão definida estiver em execução, é recomendável sequenciar todas as extensões em seu modelo de conjunto de dimensionamento. No exemplo acima, a extensão B pode ser configurada para ser provisionada após a extensão C, de modo que apenas uma sequência possa ocorrer:
-   Extensãoa-> ExtensionC-> ExtensionB


## <a name="how-to-use-extension-sequencing"></a>Como usar o sequenciamento de extensão
Para o provisionamento de extensão de sequência, você deve atualizar a definição de extensão no modelo de conjunto de dimensionamento para incluir a propriedade "provisionAfterExtensions", que aceita uma matriz de nomes de extensão. As extensões mencionadas no valor da matriz de propriedades devem ser totalmente definidas no modelo do conjunto de dimensionamento.

### <a name="template-deployment"></a>Implantação de modelo
O exemplo a seguir define um modelo em que o conjunto de dimensionamento tem três extensões – Extensiona, ExtensionB e ExtensionC – de forma que as extensões sejam provisionadas na ordem:
-   Extensãoa-> ExtensionB-> ExtensionC

```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "name": "ExtensionA",
        "properties": {
          "publisher": "ExtensionA.Publisher",
          "settings": {},
          "typeHandlerVersion": "1.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionA"
        }
      },
      {
        "name": "ExtensionB",
        "properties": {
          "provisionAfterExtensions": [
            "ExtensionA"
          ],
          "publisher": "ExtensionB.Publisher",
          "settings": {},
          "typeHandlerVersion": "2.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionB"
        }
      }, 
      {
        "name": "ExtensionC",
        "properties": {
          "provisionAfterExtensions": [
            "ExtensionB"
          ],
          "publisher": "ExtensionC.Publisher",
          "settings": {},
          "typeHandlerVersion": "3.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionC"                   
        }
      }
    ]
  }
}
```

Como a propriedade "provisionAfterExtensions" aceita uma matriz de nomes de extensão, o exemplo acima pode ser modificado de modo que ExtensionC seja provisionado após a extensão e ExtensionB, mas nenhuma ordem é necessária entre a extensão e a ExtensionB. O modelo a seguir pode ser usado para obter esse cenário:

```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "name": "ExtensionA",
        "properties": {
          "publisher": "ExtensionA.Publisher",
          "settings": {},
          "typeHandlerVersion": "1.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionA"
        }
      },
      {
        "name": "ExtensionB",
        "properties": {
          "publisher": "ExtensionB.Publisher",
          "settings": {},
          "typeHandlerVersion": "2.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionB"
        }
      }, 
      {
        "name": "ExtensionC",
        "properties": {
          "provisionAfterExtensions": [
            "ExtensionA","ExtensionB"
          ],
          "publisher": "ExtensionC.Publisher",
          "settings": {},
          "typeHandlerVersion": "3.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionC"                   
        }
      }
    ]
  }
}
```

### <a name="rest-api"></a>API REST
O exemplo a seguir adiciona uma nova extensão chamada ExtensionC a um modelo de conjunto de dimensionamento. ExtensionC tem dependências na Extensãoa e ExtensionB, que já foram definidas no modelo do conjunto de dimensionamento.

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/extensions/ExtensionC?api-version=2018-10-01`
```

```json
{ 
  "name": "ExtensionC",
  "properties": {
    "provisionAfterExtensions": [
      "ExtensionA","ExtensionB"
    ],
    "publisher": "ExtensionC.Publisher",
    "settings": {},
    "typeHandlerVersion": "3.0",
    "autoUpgradeMinorVersion": true,
    "type": "ExtensionC" 
  }                  
}
```

Se ExtensionC tiver sido definido anteriormente no modelo do conjunto de dimensionamento e agora você quiser adicionar suas dependências, você poderá executar um `PATCH` para editar as propriedades da extensão já implantada.

```
PATCH on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/extensions/ExtensionC?api-version=2018-10-01`
```

```json
{ 
  "properties": {
    "provisionAfterExtensions": [
      "ExtensionA","ExtensionB"
    ]
  }                  
}
```
As alterações nas instâncias existentes do conjunto de dimensionamento são aplicadas na próxima [atualização](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model).

### <a name="azure-powershell"></a>Azure PowerShell
Use o cmdlet [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) para adicionar a extensão de integridade do aplicativo à definição do modelo do conjunto de dimensionamento. O sequenciamento de extensão requer o uso de AZ PowerShell 1.2.0 ou superior.

O exemplo a seguir adiciona a [extensão de integridade do aplicativo](virtual-machine-scale-sets-health-extension.md) ao `extensionProfile` em um modelo de conjunto de dimensionamento de um conjunto de dimensionamento baseado no Windows. A extensão de integridade do aplicativo será provisionada após o provisionamento da [extensão de script personalizado](../virtual-machines/extensions/custom-script-windows.md), já definida no conjunto de dimensionamento.

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
  -ProvisionAfterExtension "CustomScriptExtension" `
  -AutoUpgradeMinorVersion $True

# Update the scale set
Update-AzVmss -ResourceGroupName $vmScaleSetResourceGroup `
  -Name $vmScaleSetName `
  -VirtualMachineScaleSet $vmScaleSet
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Use [AZ vmss Extension Set](/cli/azure/vmss/extension#az-vmss-extension-set) para adicionar a extensão de integridade do aplicativo à definição do modelo do conjunto de dimensionamento. O sequenciamento de extensão requer o uso de CLI do Azure 2.0.55 ou superior.

O exemplo a seguir adiciona a [extensão de integridade do aplicativo](virtual-machine-scale-sets-health-extension.md) ao modelo do conjunto de dimensionamento de um conjunto de dimensionamento baseado no Windows. A extensão de integridade do aplicativo será provisionada após o provisionamento da [extensão de script personalizado](../virtual-machines/extensions/custom-script-windows.md), já definida no conjunto de dimensionamento.

```azurecli-interactive
az vmss extension set \
  --name ApplicationHealthWindows \
  --publisher Microsoft.ManagedServices \
  --version 1.0 \
  --resource-group <myVMScaleSetResourceGroup> \
  --vmss-name <myVMScaleSet> \
  --provision-after-extensions CustomScriptExtension \
  --settings ./extension.json
```


## <a name="troubleshoot"></a>Resolução de problemas

### <a name="not-able-to-add-extension-with-dependencies"></a>Não é possível adicionar extensão com dependências?
1. Verifique se as extensões especificadas em provisionAfterExtensions estão definidas no modelo do conjunto de dimensionamento.
2. Certifique-se de que não há nenhuma dependência circular sendo introduzida. Por exemplo, a sequência a seguir não é permitida: Extensiona-> ExtensionB-> ExtensionC-> Extensãoa
3. Verifique se as extensões nas quais você assume dependências têm uma propriedade "Settings" sob a extensão "Properties". Por exemplo, se ExtentionB precisa ser provisionado após a Extensãoa, a Extensãoa deve ter o campo "configurações" sob a extensão "Propriedades". Você pode especificar uma propriedade "Settings" vazia se a extensão não exigir nenhuma configuração necessária.

### <a name="not-able-to-remove-extensions"></a>Não é possível remover extensões?
Verifique se as extensões que estão sendo removidas não estão listadas em provisionAfterExtensions para quaisquer outras extensões.

## <a name="next-steps"></a>Passos seguintes
Saiba como [implantar seu aplicativo](virtual-machine-scale-sets-deploy-app.md) em conjuntos de dimensionamento de máquinas virtuais.
