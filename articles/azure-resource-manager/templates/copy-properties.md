---
title: Definir vários casos de uma propriedade
description: Utilize a operação de cópia num modelo do Gestor de Recursos Azure para iterar várias vezes ao criar uma propriedade num recurso.
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 61122b01889da832a73f729833ab0af676904d54
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84678465"
---
# <a name="property-iteration-in-arm-templates"></a>Iteração de propriedade em modelos ARM

Este artigo mostra-lhe como criar mais de um caso de propriedade no seu modelo Azure Resource Manager (ARM). Ao adicionar o elemento **de cópia** à secção de propriedades de um recurso no seu modelo, pode definir dinamicamente o número de itens para uma propriedade durante a implantação. Evite também ter de repetir a sintaxe do modelo.

Também pode utilizar cópia com [recursos,](copy-resources.md) [variáveis](copy-variables.md)e [saídas.](copy-outputs.md)

## <a name="syntax"></a>Syntax

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

Para **citar,** forneça o nome da propriedade de recursos que pretende criar.

A **propriedade conta** especifique o número de iterações que deseja para a propriedade.

A **propriedade de entrada** especifica as propriedades que pretende repetir. Cria-se uma série de elementos construídos a partir do valor na propriedade **de entrada.**

## <a name="copy-limits"></a>Limites de cópia

A contagem não pode ultrapassar os 800.

A contagem não pode ser um número negativo. Pode ser zero se implementar o modelo com uma versão recente de Azure CLI, PowerShell ou REST API. Especificamente, deve usar:

* Azure PowerShell **2.6** ou mais tarde
* Azure CLI **2.0.74** ou mais tarde
* REST Versão API **2019-05-10** ou posterior
* [As implementações ligadas](linked-templates.md) devem utilizar a versão API **2019-05-10** ou posterior para o tipo de recurso de implantação

Versões anteriores de PowerShell, CLI e REST API não suportam zero para contar.

## <a name="property-iteration"></a>Iteração imobiliária

O exemplo a seguir mostra como aplicar-se `copy` à propriedade dataDisks numa máquina virtual:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

Note que ao utilizar `copyIndex` dentro de uma iteração de propriedade, deve fornecer o nome da iteração. A iteração imobiliária também suporta um argumento de compensação. A compensação deve vir atrás do nome da iteração, tal como copyIndex ('dataDisks', 1).

O Gestor de Recursos expande a matriz durante a `copy` implantação. O nome da matriz torna-se o nome da propriedade. Os valores de entrada tornam-se propriedades do objeto. O modelo implantado torna-se:

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

A operação da cópia é útil quando se trabalha com matrizes porque pode iterar através de cada elemento da matriz. Utilize a `length` função na matriz para especificar a contagem de iterações e `copyIndex` para recuperar o índice atual na matriz.

O modelo de exemplo a seguir cria um grupo de failover para bases de dados que são passadas como uma matriz.

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

O elemento de cópia é um conjunto para que possa especificar mais do que uma propriedade para o recurso.

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

Pode utilizar a iteração de recursos e propriedades em conjunto. Referenciar a iteração da propriedade pelo nome.

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

## <a name="example-templates"></a>Modelos de exemplo

O exemplo a seguir mostra um cenário comum para criar mais do que um valor para uma propriedade.

|Modelo  |Descrição  |
|---------|---------|
|[Implementação de VM com um número variável de discos de dados](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Implementa vários discos de dados com uma máquina virtual. |

## <a name="next-steps"></a>Próximos passos

* Para passar por um tutorial, consulte [Tutorial: crie múltiplas instâncias de recursos utilizando modelos ARM](template-tutorial-create-multiple-instances.md).
* Para outras utilizações do elemento de cópia, consulte:
  * [Iteração de recursos em modelos ARM](copy-resources.md)
  * [Iteração variável em modelos ARM](copy-variables.md)
  * [Iteração de saída em modelos ARM](copy-outputs.md)
* Se quiser aprender sobre as secções de um modelo, consulte os [modelos de autoria DO ARM](template-syntax.md).
* Para aprender a implementar o seu modelo, consulte [implementar uma aplicação com o modelo ARM](deploy-powershell.md).

