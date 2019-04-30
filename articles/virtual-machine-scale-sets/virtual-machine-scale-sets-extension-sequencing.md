---
title: Utilizar o sequenciamento de extensão com conjuntos de dimensionamento de máquina virtual do Azure | Documentos da Microsoft
description: Aprenda a sequência de aprovisionamento de extensão quando implementar várias extensões em conjuntos de dimensionamento de máquina virtual.
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
ms.openlocfilehash: 2e5dfda16c4828b3113fc50d4cffc79fe6ff19e8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60620177"
---
# <a name="sequence-extension-provisioning-in-virtual-machine-scale-sets"></a>Define o aprovisionamento de extensão de sequência no dimensionamento de máquinas virtuais
Extensões de máquina virtual do Azure fornecem capacidades, como a configuração de pós-implementação e gestão, monitorização, segurança e muito mais. Implementações de produção, normalmente, utilizem uma combinação de várias extensões configurado para as instâncias VM para alcançar resultados desejados.

Se utilizar várias extensões numa máquina virtual, é importante certificar-se de que extensões que requerem os mesmos recursos de sistema operacional não estão tentando adquirir estes recursos ao mesmo tempo. Algumas extensões também dependem de outras extensões para fornecer as configurações necessárias, tais como definições de ambiente e segredos. Sem a encomenda correto e sequenciamento in-loco, implementações de extensão dependentes podem falhar.

Este artigo detalha como é possível sequenciar extensões para ser configurado para as instâncias VM em conjuntos de dimensionamento de máquina virtual.

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que esteja familiarizado com:
-   Máquina virtual do Azure [extensões](../virtual-machines/extensions/overview.md)
-   [Modificar](virtual-machine-scale-sets-upgrade-scale-set.md) conjuntos de dimensionamento de máquina virtual

## <a name="when-to-use-extension-sequencing"></a>Quando utilizar o sequenciamento de extensão
Sequenciação de extensões em não define obrigatório para dimensionamento e, a menos que especificado, as extensões podem ser provisionadas numa instância de conjunto de dimensionamento por qualquer ordem.

Por exemplo, se seu modelo de conjunto de dimensionamento tem duas extensões – ExtensionA e ExtensionB – especificado no modelo, em seguida, qualquer uma das seguintes sequências de aprovisionamento pode ocorrer:
-   ExtensionA -> ExtensionB
-   ExtensionB -> ExtensionA

Se seu aplicativo exigir uma extensão para sempre aprovisionar antes da extensão B, em seguida, deve usar sequenciamento de extensão, conforme descrito neste artigo. Com o sequenciamento de extensão, apenas uma sequência agora irá ocorrer:
-   ExtensionA - > ExtensionB

Nenhuma extensão não especificado numa sequência definida de aprovisionamento pode ser aprovisionado em qualquer altura, incluindo antes, depois ou durante uma sequência definida. Sequenciamento de extensão especifica apenas o que será aprovisionada uma extensão específica após a outra extensão específica. Ela não afeta o aprovisionamento de qualquer outra extensão definida no modelo.

Por exemplo, se seu modelo de conjunto de dimensionamento tem três extensões – extensão, extensão B e C de extensão – especificado no modelo, e C de extensão estiver definido para ser aprovisionado após uma extensão, em seguida, qualquer uma das seguintes sequências de aprovisionamento pode ocorrer:
-   ExtensionA -> ExtensionC -> ExtensionB
-   ExtensionB -> ExtensionA -> ExtensionC
-   ExtensionA -> ExtensionB -> ExtensionC

Se precisa garantir que nenhuma outra extensão é aprovisionado, enquanto a sequência de extensão definidos está em execução, recomendamos que todas as extensões no seu modelo de conjunto de dimensionamento a sequenciar. No exemplo acima, a extensão B pode ser definida a ser aprovisionado após C de extensão, de modo a que pode ocorrer apenas uma sequência:
-   ExtensionA -> ExtensionC -> ExtensionB


## <a name="how-to-use-extension-sequencing"></a>Como utilizar o sequenciamento de extensão
Para o aprovisionamento de extensão de sequência, tem de atualizar a definição da extensão no modelo de conjunto de dimensionamento para incluir a propriedade "provisionAfterExtensions", que aceita uma matriz de nomes de extensão. As extensões mencionadas no valor de matriz de propriedade tem de ser totalmente definidas no modelo de conjunto de dimensionamento.

### <a name="template-deployment"></a>Implementação do modelo
O exemplo seguinte define um modelo em que o conjunto de dimensionamento tem três extensões – ExtensionA ExtensionB e ExtensionC –, de modo a que as extensões são aprovisionadas na ordem:
-   ExtensionA -> ExtensionB -> ExtensionC

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

Uma vez que a propriedade "provisionAfterExtensions" aceita uma matriz de nomes de extensão, o exemplo acima pode ser modificado, de modo que ExtensionC é aprovisionado após ExtensionA e ExtensionB, mas não ordenação é necessário entre ExtensionA e ExtensionB. O modelo seguinte pode ser utilizado para alcançar este cenário:

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
O exemplo seguinte adiciona uma nova extensão com o nome ExtensionC a um modelo de conjunto de dimensionamento. ExtensionC tem dependências em ExtensionA e ExtensionB, que já foram definidos no modelo de conjunto de dimensionamento.

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

Se ExtensionC estava definido anteriormente no conjunto de dimensionamento de modelo e agora pretende adicionar as respetivas dependências, pode executar um `PATCH` para editar as propriedades da extensão já implementado.

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
As alterações às instâncias do conjunto de dimensionamento existente são aplicadas na próxima [atualizar](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model).

### <a name="azure-powershell"></a>Azure PowerShell
Utilize o [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) definição de modelo do conjunto de cmdlet para adicionar a extensão de estado de funcionamento da aplicação à escala. Sequenciamento de extensão requer a utilização do PowerShell de Az 1.2.0 ou superior.

O exemplo seguinte adiciona o [extensão de estado de funcionamento do aplicativo](virtual-machine-scale-sets-health-extension.md) para o `extensionProfile` num conjunto de dimensionamento modelo de um conjunto de dimensionamento baseados no Windows. A extensão de estado de funcionamento da aplicação será aprovisionada após o aprovisionamento a [extensão de Script personalizado](../virtual-machines/extensions/custom-script-windows.md), já definido no conjunto de dimensionamento.

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

### <a name="azure-cli-20"></a>CLI 2.0 do Azure
Uso [conjunto de extensão az vmss](/cli/azure/vmss/extension#az-vmss-extension-set) para adicionar a extensão de estado de funcionamento do aplicativo para o conjunto de dimensionamento da definição do modelo. Sequenciamento de extensão requer a utilização da CLI do Azure 2.0.55 ou superior.

O exemplo seguinte adiciona o [extensão de estado de funcionamento do aplicativo](virtual-machine-scale-sets-health-extension.md) à escala definida no modelo de um conjunto de dimensionamento baseados no Windows. A extensão de estado de funcionamento da aplicação será aprovisionada após o aprovisionamento a [extensão de Script personalizado](../virtual-machines/extensions/custom-script-windows.md), já definido no conjunto de dimensionamento.

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

### <a name="not-able-to-add-extension-with-dependencies"></a>Não é possível adicionar a extensão com dependências?
1. Certifique-se de que as extensões especificadas no provisionAfterExtensions são definidas no modelo de conjunto de dimensionamento.
2. Certifique-se de que não há nenhuma dependência circular que está a ser introduzida. Por exemplo, não é permitida a seguinte sequência: ExtensionA -> ExtensionB -> ExtensionC -> ExtensionA
3. Certifique-se de que quaisquer extensões assumir dependências, ter uma propriedade de "definições" em "Propriedades" de extensão. Por exemplo, se ExtentionB tem de ser aprovisionada após ExtensionA, em seguida, ExtensionA tem de ter o campo de "definições" em ExtensionA "Propriedades". Pode especificar uma propriedade de "definições" vazia se a extensão não impõe quaisquer definições necessárias.

### <a name="not-able-to-remove-extensions"></a>Não é possível remover as extensões?
Certifique-se de que as extensões que está a ser removidas não estão listadas na provisionAfterExtensions para quaisquer outras extensões.

## <a name="next-steps"></a>Passos Seguintes
Saiba como [implementar a sua aplicação](virtual-machine-scale-sets-deploy-app.md) conjuntos de dimensionamento de máquina virtual.
