---
title: Definir múltiplas instâncias de um valor de saída
description: Utilize a operação de cópia num modelo de Gestor de Recursos Azure para iterar várias vezes ao devolver um valor a partir de uma implementação.
ms.topic: conceptual
ms.date: 04/17/2020
ms.openlocfilehash: 50c4b4b8f301ad88d3dfde98ace1aed4431693db
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82583422"
---
# <a name="output-iteration-in-arm-templates"></a>Iteração de saída em modelos ARM

Este artigo mostra-lhe como criar mais do que um valor para uma saída no seu modelo Degestor de Recursos Azure (ARM). Ao adicionar o elemento **de cópia** à secção de saídas do seu modelo, pode dinamicamente devolver uma série de itens durante a implementação.

Também pode utilizar cópiacom [recursos,](copy-resources.md) [propriedades num recurso,](copy-properties.md)e [variáveis.](copy-variables.md)

## <a name="syntax"></a>Sintaxe

O elemento de cópia tem o seguinte formato geral:

```json
"copy": {
  "count": <number-of-iterations>,
  "input": <values-for-the-output>
}
```

A propriedade **de contagem** especifica o número de iterações que deseja para o valor de saída.

A propriedade **de entrada** especifica as propriedades que pretende repetir. Você cria uma variedade de elementos construídos a partir do valor na propriedade de **entrada.** Pode ser uma única propriedade (como uma corda), ou um objeto com várias propriedades.

## <a name="copy-limits"></a>Limites de cópia

A contagem não pode exceder 800.

A contagem não pode ser um número negativo. Pode ser zero se implementar o modelo com uma versão recente do Azure CLI, PowerShell ou REST API. Especificamente, deve usar:

* Azure PowerShell **2.6** ou mais tarde
* Azure CLI **2.0.74** ou mais tarde
* VERSÃO REST API **2019-05-10** ou mais tarde
* [As implementações ligadas](linked-templates.md) devem utilizar a versão API **2019-05-10** ou posteriormente para o tipo de recurso de implantação

Versões anteriores de PowerShell, CLI e rest API não suportam zero para contagem.

## <a name="outputs-iteration"></a>Iteração de saídas

O exemplo seguinte cria um número variável de contas de armazenamento e devolve um ponto final para cada conta de armazenamento:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageCount": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "variables": {
        "baseName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(copyIndex(), variables('baseName'))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": "[parameters('storageCount')]"
            }
        }
    ],
    "outputs": {
        "storageEndpoints": {
            "type": "array",
            "copy": {
                "count": "[parameters('storageCount')]",
                "input": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]"
            }
        }
    }
}
```

O modelo anterior devolve uma matriz com os seguintes valores:

```json
[
    "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
    "https://1storagecfrbqnnmpeudi.blob.core.windows.net/"
]
```

O próximo exemplo devolve três propriedades das novas contas de armazenamento.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageCount": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "variables": {
        "baseName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(copyIndex(), variables('baseName'))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": "[parameters('storageCount')]"
            }
        }
    ],
    "outputs": {
        "storageInfo": {
            "type": "array",
            "copy": {
                "count": "[parameters('storageCount')]",
                "input": {
                    "id": "[reference(concat(copyIndex(), variables('baseName')), '2019-04-01', 'Full').resourceId]",
                    "blobEndpoint": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]",
                    "status": "[reference(concat(copyIndex(), variables('baseName'))).statusOfPrimary]"
                }
            }
        }
    }
}
```

O exemplo anterior devolve uma matriz com os seguintes valores:

```json
[
    {
        "id": "Microsoft.Storage/storageAccounts/0storagecfrbqnnmpeudi",
        "blobEndpoint": "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
        "status": "available"
    },
    {
        "id": "Microsoft.Storage/storageAccounts/1storagecfrbqnnmpeudi",
        "blobEndpoint": "https://1storagecfrbqnnmpeudi.blob.core.windows.net/",
        "status": "available"
    }
]
```

## <a name="next-steps"></a>Passos seguintes

* Para passar por um tutorial, consulte [Tutorial: crie múltiplas instâncias](template-tutorial-create-multiple-instances.md)de recursos utilizando modelos ARM .
* Para outras utilizações do elemento cópia, consulte:
  * [Iteração de recursos em modelos ARM](copy-resources.md)
  * [Iteração de propriedade em modelos ARM](copy-properties.md)
  * [Iteração variável em modelos ARM](copy-variables.md)
* Se quiser saber sobre as secções de um modelo, consulte [os modelos ARM autores](template-syntax.md).
* Para aprender a implementar o seu modelo, consulte [implementar uma aplicação com modelo ARM](deploy-powershell.md).

