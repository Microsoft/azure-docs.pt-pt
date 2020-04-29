---
title: Utilize a sequenciação de extensão com conjuntos de escala de máquinas virtuais Azure
description: Aprenda a sequenciar o fornecimento de extensões ao implementar várias extensões em conjuntos de escala de máquinas virtuais.
author: mimckitt
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: mimckitt
ms.openlocfilehash: 737040699dd62d722b9a9ad4d8915ccb270c2d06
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81273754"
---
# <a name="sequence-extension-provisioning-in-virtual-machine-scale-sets"></a>Fornecimento de extensão de sequência em conjuntos de escala de máquina virtual
As extensões de máquinas virtuais Azure fornecem capacidades como configuração e gestão pós-implantação, monitorização, segurança e muito mais. As implementações de produção normalmente usam uma combinação de múltiplas extensões configuradas para os casos vm para obter resultados desejados.

Ao utilizar várias extensões numa máquina virtual, é importante garantir que as extensões que requerem os mesmos recursos de Os não estão a tentar adquirir esses recursos ao mesmo tempo. Algumas extensões também dependem de outras extensões para fornecer configurações necessárias, tais como configurações de ambiente e segredos. Sem a ordenação e a sequenciação corretas, as extensões dependentes podem falhar.

Este artigo detalha como pode sequenciar extensões a configurar para as instâncias VM em conjuntos de escala de máquinavirtual.

## <a name="prerequisites"></a>Pré-requisitos
Este artigo assume que está familiarizado com:
-   [Extensões](../virtual-machines/extensions/overview.md) de máquinas virtuais Azure
-   [Modificação de](virtual-machine-scale-sets-upgrade-scale-set.md) conjuntos de escala de máquinavirtual

## <a name="when-to-use-extension-sequencing"></a>Quando utilizar a sequência de extensão
As extensões de sequenciação não obrigatórias para conjuntos de escala, e a menos que especificadas, as extensões podem ser previstas numa instância definida por escala, em qualquer ordem.

Por exemplo, se o seu modelo de conjunto de escala tiver duas extensões – Extensão A e ExtensãoB – especificadas no modelo, então qualquer uma das seguintes sequências de provisionamento pode ocorrer:
-   Extensão -extensão de extensão de >
-   Extensão -extensão >A

Se o seu pedido exigir que a extensão A seja sempre disponibilizada antes da extensão B, então deve utilizar a sequência de extensão, tal como descrito neste artigo. Com a sequência de extensão, apenas uma sequência ocorrerá agora:
-   ExtensionA - extensão >

Quaisquer extensões não especificadas numa sequência de provisionamento definida podem ser aprovisionadas a qualquer momento, incluindo antes, depois ou durante uma sequência definida. A sequenciação da extensão apenas especifica que uma extensão específica será prevista após outra extensão específica. Não afeta o fornecimento de qualquer outra extensão definida no modelo.

Por exemplo, se o seu modelo de conjunto de escala tiver três extensões – Extensão A, Extensão B e Extensão C – especificadas no modelo, e a extensão C for definida para ser aprovisionada após a extensão A, então qualquer uma das seguintes sequências de provisionamento pode ocorrer:
-   Extensão -> extensão de extensão -extensão de extensão de extensão ->
-   Extensão -> Extensão A -extensão >
-   Extensão -> extensão -extensão >

Se precisar de garantir que nenhuma outra extensão é prevista enquanto a sequência de extensão definida estiver a ser executada, recomendamos a sequenciação de todas as extensões do seu modelo de conjunto de escala. No exemplo acima, a extensão B pode ser definida após a extensão C de modo a que apenas uma sequência possa ocorrer:
-   Extensão -> extensão de extensão -extensão de extensão de extensão ->


## <a name="how-to-use-extension-sequencing"></a>Como utilizar a sequenciação de extensões
Para sequenciar o fornecimento de extensão, deve atualizar a definição de extensão no modelo de conjunto de escala para incluir a propriedade "provisionAfterExtensions", que aceita um conjunto de nomes de extensão. As extensões mencionadas no valor da matriz de propriedade devem ser totalmente definidas no modelo de conjunto de escala.

### <a name="template-deployment"></a>Implantação do modelo
O exemplo seguinte define um modelo em que o conjunto de escala tem três extensões – Extensão, Extensão B e ExtensãoC – de tal forma que as extensões são aprovisionadas na ordem:
-   Extensão -> extensão -extensão >

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

Uma vez que a propriedade "provisionAfterExtensions" aceita um conjunto de nomes de extensão, o exemplo acima pode ser modificado de modo a que o ExtensionC seja provisionado após extensão A e ExtensionB, mas não é necessária qualquer encomenda entre extensionA e ExtensionB. O seguinte modelo pode ser usado para alcançar este cenário:

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
O exemplo seguinte adiciona uma nova extensão chamada ExtensionC a um modelo de conjunto de escala. A Extensão C tem dependências de Extensão A e ExtensionB, que já foram definidas no modelo de conjunto de escala.

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

Se o ExtensionC foi definido mais cedo no modelo de conjunto de escala `PATCH` e agora quer adicionar as suas dependências, pode executar um para editar as propriedades da extensão já implantada.

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
Alterações às instâncias definidas de escala existentes são aplicadas na próxima [atualização](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model).

### <a name="azure-powershell"></a>Azure PowerShell
Utilize o cmdlet [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) para adicionar a extensão de saúde da aplicação à definição do modelo de conjunto de escala. A sequenciação da extensão requer a utilização de Az PowerShell 1.2.0 ou superior.

O exemplo seguinte adiciona a `extensionProfile` extensão de Saúde da [Aplicação](virtual-machine-scale-sets-health-extension.md) ao modelo de conjunto de escala de um conjunto de escala baseado no Windows. A extensão de saúde de aplicação será aprovisionada após o fornecimento da [extensão do script personalizado,](../virtual-machines/extensions/custom-script-windows.md)já definida no conjunto de escala.

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
Utilize o [conjunto de extensão az vmss](/cli/azure/vmss/extension#az-vmss-extension-set) para adicionar a extensão de saúde de aplicação à definição do modelo de conjunto de escala. A sequenciação da extensão requer a utilização do Azure CLI 2.0.55 ou superior.

O exemplo seguinte adiciona a extensão de Saúde de [Aplicação](virtual-machine-scale-sets-health-extension.md) ao modelo de conjunto de escala de um conjunto de escala baseado no Windows. A extensão de saúde de aplicação será aprovisionada após o fornecimento da [extensão do script personalizado,](../virtual-machines/extensions/custom-script-windows.md)já definida no conjunto de escala.

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

### <a name="not-able-to-add-extension-with-dependencies"></a>Não é capaz de adicionar extensão com dependências?
1. Certifique-se de que as extensões especificadas nas disposições AfterExtensions são definidas no modelo de conjunto de escala.
2. Certifique-se de que não estão a ser introduzidas dependências circulares. Por exemplo, não é permitida a seguinte sequência: Extensão -> extensão -extensão extensão -> Extens >ão A
3. Certifique-se de que quaisquer extensões em que assuma dependências, tenha uma propriedade de "configurações" em extensão "propriedades". Por exemplo, se a ExtentionB tiver de ser aprovisionada após a extensão A, então a Extensão A deve ter o campo de "definições" em extensão "propriedades". Pode especificar uma propriedade "configurações" vazia se a extensão não ordenar quaisquer definições necessárias.

### <a name="not-able-to-remove-extensions"></a>Não consegue remover extensões?
Certifique-se de que as extensões que estão a ser removidas não estão listadas ao abrigo das disposições AfterExtensions para quaisquer outras extensões.

## <a name="next-steps"></a>Passos seguintes
Aprenda a implementar a [sua aplicação](virtual-machine-scale-sets-deploy-app.md) em conjuntos de escala de máquinas virtuais.
