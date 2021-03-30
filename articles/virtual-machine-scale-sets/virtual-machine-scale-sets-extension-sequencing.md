---
title: Utilize sequenciação de extensão com conjuntos de escala de máquina virtual Azure
description: Aprenda a sequenciar o fornecimento de extensões ao implementar várias extensões em conjuntos de escala de máquina virtual.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: extensions
ms.date: 01/30/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 3271041b9f4db100cd05588129c7d714d4478f10
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "83121036"
---
# <a name="sequence-extension-provisioning-in-virtual-machine-scale-sets"></a>Provisão de extensão de sequência em conjuntos de escala de máquina virtual
As extensões de máquinas virtuais Azure fornecem capacidades como configuração e gestão pós-implantação, monitorização, segurança e muito mais. As implementações de produção normalmente utilizam uma combinação de extensões múltiplas configuradas para as instâncias VM para obter os resultados desejados.

Ao utilizar várias extensões numa máquina virtual, é importante garantir que as extensões que requerem os mesmos recursos de SO não estão a tentar adquirir esses recursos ao mesmo tempo. Algumas extensões também dependem de outras extensões para fornecer configurações necessárias, tais como configurações de ambiente e segredos. Sem a correta encomenda e sequenciação no lugar, as implementações de extensão dependente podem falhar.

Este artigo detalha como pode sequenciar extensões a configurar para as instâncias VM em conjuntos de escala de máquina virtual.

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que está familiarizado com:
-   [Extensões](../virtual-machines/extensions/overview.md) de máquina virtual Azure
-   [Modificando conjuntos de](virtual-machine-scale-sets-upgrade-scale-set.md) escala de máquina virtual

## <a name="when-to-use-extension-sequencing"></a>Quando utilizar a sequência de extensão
Sequenciação de extensões não obrigatórias para conjuntos de escala, e a menos que especificadas, as extensões podem ser previstas em qualquer ordem.

Por exemplo, se o seu modelo de conjunto de escalas tiver duas extensões – Extensão E ExtensãoB – especificadas no modelo, então qualquer uma das seguintes sequências de provisionamento poderá ocorrer:
-   Extensão extensão -> extensãoB
-   Extensão -> Extensão de Extensão

Se a sua aplicação exigir que a extensão A seja sempre prevista antes da extensão B, então deve utilizar a sequência de extensão, conforme descrito neste artigo. Com a sequência de extensão, apenas uma sequência ocorrerá agora:
-   Extensão - extensão > B

Quaisquer extensões não especificadas numa sequência de provisionamento definida podem ser previstas a qualquer momento, incluindo antes, depois ou durante uma sequência definida. A sequência de extensão apenas especifica que uma extensão específica será prevista após outra extensão específica. Não afeta o provisionamento de qualquer outra extensão definida no modelo.

Por exemplo, se o seu modelo de conjunto de escalas tiver três extensões – extensão A, extensão B e extensão C – especificadas no modelo, e a extensão C estiver definida para ser prevista após a extensão A, então uma das seguintes sequências de provisionamento pode ocorrer:
-   Extensão -> extensão -> extensãoB
-   Extensão -> Extensão -> Extensão
-   Extensão -> extensão -> extensão

Se precisar de garantir que não é prevista qualquer outra extensão enquanto a sequência de extensão definida estiver a ser executada, recomendamos a sequência de todas as extensões no seu modelo de conjunto de escala. No exemplo acima, a extensão B pode ser definida após a extensão C de modo a que apenas uma sequência possa ocorrer:
-   Extensão -> extensão -> extensãoB


## <a name="how-to-use-extension-sequencing"></a>Como usar a sequência de extensão
Para sequenciar o provisionamento de extensão, deve atualizar a definição de extensão no modelo definido de escala para incluir a propriedade "provisionAfterExtensions", que aceita uma série de nomes de extensão. As extensões mencionadas no valor da matriz da propriedade devem ser totalmente definidas no modelo definido em escala.

### <a name="template-deployment"></a>Implementação do modelo
O exemplo a seguir define um modelo em que o conjunto de escalas tem três extensões – Extensão, ExtensãoB e ExtensãoC – de modo a que as extensões sejam previstas na ordem:
-   Extensão -> extensão -> extensão

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

Uma vez que a propriedade "provisionAfterExtensions" aceita uma série de nomes de extensão, o exemplo acima pode ser modificado de modo a que a ExtensãoC seja prevista após extensão E ExtensãoB, mas não é necessária nenhuma encomenda entre a Extensão E a ExtensãoB. O modelo a seguir pode ser usado para alcançar este cenário:

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
O exemplo a seguir adiciona uma nova extensão chamada ExtensionC a um modelo de conjunto de escala. A ExtensionC tem dependências de ExtensionA e ExtensionB, que já foram definidas no modelo de escala definida.

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

Se o ExtensionC foi definido anteriormente no modelo de conjunto de escala e agora pretende adicionar as suas dependências, pode executar um `PATCH` para editar as propriedades da extensão já implantada.

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
As alterações às instâncias definidas em escala existentes são aplicadas na próxima [atualização](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model).

### <a name="azure-powershell"></a>Azure PowerShell
Utilize o [cmdlet Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) para adicionar a extensão de Saúde da Aplicação à definição do modelo definido em escala. A sequenciação de extensão requer a utilização do Az PowerShell 1.2.0 ou superior.

O exemplo a seguir adiciona a [extensão de Saúde da Aplicação](virtual-machine-scale-sets-health-extension.md) ao `extensionProfile` modelo de escala de um conjunto de escala baseado no Windows. A extensão de Saúde da Aplicação será disponibilizada após a disponibilização da [Extensão de Script Personalizado,](../virtual-machines/extensions/custom-script-windows.md)já definida no conjunto de escala.

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
Utilize [o conjunto de extensão az vmss](/cli/azure/vmss/extension#az-vmss-extension-set) para adicionar a extensão de Saúde da Aplicação à definição do modelo definido em escala. A sequência de extensão requer a utilização do Azure CLI 2.0.55 ou superior.

O exemplo a seguir adiciona a [extensão de Saúde da Aplicação](virtual-machine-scale-sets-health-extension.md) ao modelo de escala definida de um conjunto de escala baseado no Windows. A extensão de Saúde da Aplicação será disponibilizada após a disponibilização da [Extensão de Script Personalizado,](../virtual-machines/extensions/custom-script-windows.md)já definida no conjunto de escala.

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
1. Certifique-se de que as extensões especificadas nas disposiçõesAfterExtensions são definidas no modelo de conjunto de escala.
2. Certifique-se de que não são introduzidas dependências circulares. Por exemplo, não é permitida a seguinte sequência: Extensão -> extensão -> extensão -> Extensão
3. Certifique-se de que quaisquer extensões em que assuma dependências, tenham uma propriedade de "configurações" sob extensão de "propriedades". Por exemplo, se a ExtensãoB precisar de ser prevista após a Extensão, então a ExtensionA deve ter o campo de "definições" sob "propriedades" da ExtensionA. Pode especificar uma propriedade vazia de "definições" se a extensão não ordenar as definições necessárias.

### <a name="not-able-to-remove-extensions"></a>Não consegue remover extensões?
Certifique-se de que as extensões que estão a ser removidas não estão listadas ao abrigo de extensões posteriores a quaisquer outras extensões.

## <a name="next-steps"></a>Passos seguintes
Saiba como implementar a [sua aplicação](virtual-machine-scale-sets-deploy-app.md) em conjuntos de escala de máquina virtual.
