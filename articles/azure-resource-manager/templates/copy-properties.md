---
title: Definir múltiplas instâncias de uma propriedade
description: Utilize a operação de cópia num modelo de Gestor de Recursos Azure para iterar várias vezes ao criar uma propriedade num recurso.
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 831ae1af202a1cdf52bdd2bdf0d9a042a97ba52f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81391343"
---
# <a name="property-iteration-in-arm-templates"></a>Iteração de propriedade em modelos ARM

Este artigo mostra-lhe como criar mais do que uma instância de uma propriedade no seu modelo Degestor de Recursos Azure (ARM). Ao adicionar o elemento **de cópia** à secção de propriedades de um recurso no seu modelo, pode configurar dinamicamente o número de itens para uma propriedade durante a implementação. Também evita ter que repetir a sintaxe do modelo.

Também pode utilizar cópias com [recursos,](copy-resources.md) [variáveis](copy-variables.md)e [saídas.](copy-outputs.md)

## <a name="property-iteration"></a>Iteração de propriedade

O elemento de cópia tem o seguinte formato geral:

```json
"copy": [
  {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "input": <values-for-the-property>
  }
]
```

Para **nome**, forneça o nome da propriedade de recursos que pretende criar.

A propriedade **de contagem** especifica o número de iterações que você quer para a propriedade.

A propriedade **de entrada** especifica as propriedades que pretende repetir. Você cria uma variedade de elementos construídos a partir do valor na propriedade de **entrada.**

O exemplo que se `copy` segue mostra como aplicar à propriedade dataDisks numa máquina virtual:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numberOfDataDisks": {
      "type": "int",
      "minValue": 0,
      "maxValue": 16,
      "defaultValue": 16,
      "metadata": {
        "description": "The number of dataDisks to create."
      }
    },
    ...
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2017-03-30",
      ...
      "properties": {
        "storageProfile": {
          ...
          "copy": [
            {
              "name": "dataDisks",
              "count": "[parameters('numberOfDataDisks')]",
              "input": {
                "diskSizeGB": 1023,
                "lun": "[copyIndex('dataDisks')]",
                "createOption": "Empty"
              }
            }
          ]
        }
      }
    }
  ]
}
```

Note que `copyIndex` ao usar dentro de uma iteração de propriedade, você deve fornecer o nome da iteração. A iteração imobiliária também suporta um argumento compensado. A contrapartida deve ser seguida do nome da iteração, como o copyIndex('dataDisks', 1).

O Gestor de `copy` Recursos expande a matriz durante a implantação. O nome da matriz torna-se o nome da propriedade. Os valores de entrada tornam-se as propriedades do objeto. O modelo implantado torna-se:

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2017-03-30",
  "properties": {
    "storageProfile": {
      "dataDisks": [
        {
          "lun": 0,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 2,
          "createOption": "Empty",
          "diskSizeGB": 1023
        }
      ],
      ...
```

A operação de cópia é útil quando se trabalha com matrizes porque pode iterar através de cada elemento da matriz. Utilize `length` a função na matriz para especificar `copyIndex` a contagem para iterações e para recuperar o índice atual na matriz.

O seguinte modelo de exemplo cria um grupo de falhas para bases de dados que são transmitidas como uma matriz.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "primaryServerName": {
            "type": "string"
        },
        "secondaryServerName": {
            "type": "string"
        },
        "databaseNames": {
            "type": "array",
            "defaultValue": [
                "mydb1",
                "mydb2",
                "mydb3"
            ]
        }
    },
    "variables": {
        "failoverName": "[concat(parameters('primaryServerName'),'/', parameters('primaryServerName'),'failovergroups')]"
    },
    "resources": [
        {
            "type": "Microsoft.Sql/servers/failoverGroups",
            "apiVersion": "2015-05-01-preview",
            "name": "[variables('failoverName')]",
            "properties": {
                "readWriteEndpoint": {
                    "failoverPolicy": "Automatic",
                    "failoverWithDataLossGracePeriodMinutes": 60
                },
                "readOnlyEndpoint": {
                    "failoverPolicy": "Disabled"
                },
                "partnerServers": [
                    {
                        "id": "[resourceId('Microsoft.Sql/servers', parameters('secondaryServerName'))]"
                    }
                ],
                "copy": [
                    {
                        "name": "databases",
                        "count": "[length(parameters('databaseNames'))]",
                        "input": "[resourceId('Microsoft.Sql/servers/databases', parameters('primaryServerName'), parameters('databaseNames')[copyIndex('databases')])]"
                    }
                ]
            }
        }
    ],
    "outputs": {
    }
}
```

O elemento de cópia é uma matriz para que possa especificar mais do que uma propriedade para o recurso.

```json
{
  "type": "Microsoft.Network/loadBalancers",
  "apiVersion": "2017-10-01",
  "name": "exampleLB",
  "properties": {
    "copy": [
      {
        "name": "loadBalancingRules",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      },
      {
        "name": "probes",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      }
    ]
  }
}
```

Você pode usar recursos e iteração de propriedade juntos. Referência à iteração da propriedade pelo nome.

```json
{
  "type": "Microsoft.Network/virtualNetworks",
  "apiVersion": "2018-04-01",
  "name": "[concat(parameters('vnetname'), copyIndex())]",
  "copy":{
    "count": 2,
    "name": "vnetloop"
  },
  "location": "[resourceGroup().location]",
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "[parameters('addressPrefix')]"
      ]
    },
    "copy": [
      {
        "name": "subnets",
        "count": 2,
        "input": {
          "name": "[concat('subnet-', copyIndex('subnets'))]",
          "properties": {
            "addressPrefix": "[variables('subnetAddressPrefix')[copyIndex('subnets')]]"
          }
        }
      }
    ]
  }
}
```

## <a name="copy-limits"></a>Limites de cópia

A contagem não pode exceder 800.

A contagem não pode ser um número negativo. Se implementar um modelo com O Azure PowerShell 2.6 ou mais tarde, Azure CLI 2.0.74 ou mais tarde, ou rest API versão **2019-05-10** ou mais tarde, pode definir a contagem para zero. Versões anteriores de PowerShell, CLI e rest API não suportam zero para contagem.

## <a name="example-templates"></a>Modelos de exemplo

O exemplo que se segue mostra um cenário comum para a criação de mais do que um valor para um imóvel.

|Modelo  |Descrição  |
|---------|---------|
|[Implantação vm com um número variável de discos de dados](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Implementa vários discos de dados com uma máquina virtual. |

## <a name="next-steps"></a>Passos seguintes

* Para passar por um tutorial, consulte [Tutorial: crie múltiplas instâncias](template-tutorial-create-multiple-instances.md)de recursos utilizando modelos ARM .
* Para outras utilizações do elemento cópia, consulte:
  * [Iteração de recursos em modelos ARM](copy-resources.md)
  * [Iteração variável em modelos ARM](copy-variables.md)
  * [Iteração de saída em modelos ARM](copy-outputs.md)
* Se quiser saber sobre as secções de um modelo, consulte [os modelos ARM autores](template-syntax.md).
* Para aprender a implementar o seu modelo, consulte [implementar uma aplicação com modelo ARM](deploy-powershell.md).

