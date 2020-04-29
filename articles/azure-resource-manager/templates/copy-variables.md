---
title: Definir múltiplos casos de uma variável
description: Utilize a operação de cópia num modelo de Gestor de Recursos Azure para iterar várias vezes ao criar uma variável.
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: ed0c2d87c48a18b0a065f6c76e1e69142a9df048
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80153306"
---
# <a name="variable-iteration-in-arm-templates"></a>Iteração variável em modelos ARM

Este artigo mostra-lhe como criar mais do que um valor para uma variável no seu modelo de Gestor de Recursos Azure (ARM). Ao adicionar o elemento **de cópia** à secção de variáveis do seu modelo, pode configurar dinamicamente o número de itens para uma variável durante a implantação. Também evita ter que repetir a sintaxe do modelo.

Também pode utilizar cópias com [recursos,](copy-resources.md) [propriedades num recurso,](copy-properties.md)e [saídas.](copy-outputs.md)

## <a name="variable-iteration"></a>Iteração variável

O elemento de cópia tem o seguinte formato geral:

```json
"copy": [
  {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "input": <values-for-the-variable>
  }
]
```

A propriedade **do nome** é qualquer valor que identifique o loop. A propriedade **de contagem** especifica o número de iterações que você quer para a variável.

A propriedade **de entrada** especifica as propriedades que pretende repetir. Você cria uma variedade de elementos construídos a partir do valor na propriedade de **entrada.** Pode ser uma única propriedade (como uma corda), ou um objeto com várias propriedades.

O exemplo que se segue mostra como criar uma série de valores de cordas:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "itemCount": {
            "type": "int",
            "defaultValue": 5
        }
     },
    "variables": {
        "copy": [
            {
                "name": "stringArray",
                "count": "[parameters('itemCount')]",
                "input": "[concat('item', copyIndex('stringArray', 1))]"
            }
        ]
    },
    "resources": [],
    "outputs": {
        "arrayResult": {
            "type": "array",
            "value": "[variables('stringArray')]"
        }
    }
}
```

O modelo anterior devolve uma matriz com os seguintes valores:

```json
[
    "item1",
    "item2",
    "item3",
    "item4",
    "item5"
]
```

O exemplo seguinte mostra como criar uma série de objetos com três propriedades - nome, diskSizeGB e diskIndex.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "itemCount": {
            "type": "int",
            "defaultValue": 5
        }
    },
    "variables": {
        "copy": [
            {
                "name": "objectArray",
                "count": "[parameters('itemCount')]",
                "input": {
                    "name": "[concat('myDataDisk', copyIndex('objectArray', 1))]",
                    "diskSizeGB": "1",
                    "diskIndex": "[copyIndex('objectArray')]"
                }
            }
        ]
    },
    "resources": [],
    "outputs": {
        "arrayResult": {
            "type": "array",
            "value": "[variables('objectArray')]"
        }
    }
}
```

O exemplo anterior devolve uma matriz com os seguintes valores:

```json
[
    {
        "name": "myDataDisk1",
        "diskSizeGB": "1",
        "diskIndex": 0
    },
    {
        "name": "myDataDisk2",
        "diskSizeGB": "1",
        "diskIndex": 1
    },
    {
        "name": "myDataDisk3",
        "diskSizeGB": "1",
        "diskIndex": 2
    },
    {
        "name": "myDataDisk4",
        "diskSizeGB": "1",
        "diskIndex": 3
    },
    {
        "name": "myDataDisk5",
        "diskSizeGB": "1",
        "diskIndex": 4
    }
]
```

> [!NOTE]
> A iteração variável suporta um argumento compensado. A contrapartida deve ser seguida do nome da iteração, como o copyIndex ('diskNames', 1). Se não fornecer um valor de compensação, não se importa a 0 para a primeira instância.
>

Também pode utilizar o elemento de cópia dentro de uma variável. O exemplo seguinte cria um objeto que tem uma matriz como um dos seus valores.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "itemCount": {
            "type": "int",
            "defaultValue": 5
        }
    },
    "variables": {
        "topLevelObject": {
            "sampleProperty": "sampleValue",
            "copy": [
                {
                    "name": "disks",
                    "count": "[parameters('itemCount')]",
                    "input": {
                        "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
                        "diskSizeGB": "1",
                        "diskIndex": "[copyIndex('disks')]"
                    }
                }
            ]
        }
    },
    "resources": [],
    "outputs": {
        "objectResult": {
            "type": "object",
            "value": "[variables('topLevelObject')]"
        }
    }
}
```

O exemplo anterior devolve um objeto com os seguintes valores:

```json
{
    "sampleProperty": "sampleValue",
    "disks": [
        {
            "name": "myDataDisk1",
            "diskSizeGB": "1",
            "diskIndex": 0
        },
        {
            "name": "myDataDisk2",
            "diskSizeGB": "1",
            "diskIndex": 1
        },
        {
            "name": "myDataDisk3",
            "diskSizeGB": "1",
            "diskIndex": 2
        },
        {
            "name": "myDataDisk4",
            "diskSizeGB": "1",
            "diskIndex": 3
        },
        {
            "name": "myDataDisk5",
            "diskSizeGB": "1",
            "diskIndex": 4
        }
    ]
}
```

O próximo exemplo mostra as diferentes formas de usar a cópia com variáveis.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "disk-array-on-object": {
      "copy": [
        {
          "name": "disks",
          "count": 5,
          "input": {
            "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
            "diskSizeGB": "1",
            "diskIndex": "[copyIndex('disks')]"
          }
        },
        {
          "name": "diskNames",
          "count": 5,
          "input": "[concat('myDataDisk', copyIndex('diskNames', 1))]"
        }
      ]
    },
    "copy": [
      {
        "name": "top-level-object-array",
        "count": 5,
        "input": {
          "name": "[concat('myDataDisk', copyIndex('top-level-object-array', 1))]",
          "diskSizeGB": "1",
          "diskIndex": "[copyIndex('top-level-object-array')]"
        }
      },
      {
        "name": "top-level-string-array",
        "count": 5,
        "input": "[concat('myDataDisk', copyIndex('top-level-string-array', 1))]"
      },
      {
        "name": "top-level-integer-array",
        "count": 5,
        "input": "[copyIndex('top-level-integer-array')]"
      }
    ]
  },
  "resources": [],
  "outputs": {
    "exampleObject": {
      "value": "[variables('disk-array-on-object')]",
      "type": "object"
    },
    "exampleArrayOnObject": {
      "value": "[variables('disk-array-on-object').disks]",
      "type" : "array"
    },
    "exampleObjectArray": {
      "value": "[variables('top-level-object-array')]",
      "type" : "array"
    },
    "exampleStringArray": {
      "value": "[variables('top-level-string-array')]",
      "type" : "array"
    },
    "exampleIntegerArray": {
      "value": "[variables('top-level-integer-array')]",
      "type" : "array"
    }
  }
}
```

## <a name="copy-limits"></a>Limites de cópia

A contagem não pode exceder 800.

A contagem não pode ser um número negativo. Se implementar um modelo com O Azure PowerShell 2.6 ou mais tarde, Azure CLI 2.0.74 ou mais tarde, ou rest API versão **2019-05-10** ou mais tarde, pode definir a contagem para zero. Versões anteriores de PowerShell, CLI e rest API não suportam zero para contagem.

## <a name="example-templates"></a>Modelos de exemplo

Os exemplos seguintes mostram cenários comuns para criar mais do que um valor para uma variável.

|Modelo  |Descrição  |
|---------|---------|
|[Copiar variáveis](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) |Demonstra as diferentes formas de iterar sobre variáveis. |
|[Múltiplas regras de segurança](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |Implementa várias regras de segurança para um grupo de segurança de rede. Constrói as regras de segurança a partir de um parâmetro. Para o parâmetro, consulte [vários ficheiros de parâmetros NSG](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json). |

## <a name="next-steps"></a>Passos seguintes

* Para passar por um tutorial, consulte [Tutorial: crie múltiplas instâncias](template-tutorial-create-multiple-instances.md)de recursos utilizando modelos ARM .
* Para outras utilizações do elemento cópia, consulte:
  * [Iteração de recursos em modelos ARM](copy-resources.md)
  * [Iteração de propriedade em modelos ARM](copy-properties.md)
  * [Iteração de saída em modelos ARM](copy-outputs.md)
* Se quiser saber sobre as secções de um modelo, consulte [os modelos ARM autores](template-syntax.md).
* Para aprender a implementar o seu modelo, consulte [implementar uma aplicação com modelo ARM](deploy-powershell.md).

