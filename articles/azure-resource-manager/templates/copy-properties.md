---
title: Definir vários casos de uma propriedade
description: Utilize a operação de cópia num modelo de Gestor de Recursos Azure (modelo ARM) para iterar várias vezes ao criar uma propriedade num recurso.
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: 94bc153a49f80694ab9b2d5b04fdf57e8a12e8c8
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2021
ms.locfileid: "106385756"
---
# <a name="property-iteration-in-arm-templates"></a>Iteração de propriedade em modelos ARM

Este artigo mostra-lhe como criar mais de um exemplo de uma propriedade no seu modelo de Gestor de Recursos Azure (modelo ARM). Ao adicionar o loop de cópia à secção de propriedades de um recurso no seu modelo, pode definir dinamicamente o número de itens para uma propriedade durante a implantação. Evite também ter de repetir a sintaxe do modelo.

Só pode utilizar o ciclo de cópia com recursos de alto nível, mesmo quando se aplica o ciclo de cópias a uma propriedade. Para aprender a mudar um recurso para uma criança para um recurso de alto nível, consulte [a Iteração para um recurso infantil](copy-resources.md#iteration-for-a-child-resource).

Também pode utilizar o ciclo de cópias com [recursos,](copy-resources.md) [variáveis](copy-variables.md)e [saídas.](copy-outputs.md)

## <a name="syntax"></a>Syntax

# <a name="json"></a>[JSON](#tab/json)

Adicione o `copy` elemento à secção de recursos do seu modelo para definir o número de itens para uma propriedade. O elemento de cópia tem o seguinte formato geral:

```json
"copy": [
  {
    "name": "<name-of-property>",
    "count": <number-of-iterations>,
    "input": <values-for-the-property>
  }
]
```

Para `name` , fornecer o nome da propriedade de recursos que pretende criar.

A `count` propriedade especifica o número de iterações que deseja para a propriedade.

A `input` propriedade especifica as propriedades que pretende repetir. Cria-se uma série de elementos construídos a partir do valor da `input` propriedade.

# <a name="bicep"></a>[Bicep](#tab/bicep)

Loops podem ser usados declarando múltiplas propriedades por:

- Iterando sobre uma matriz:

  ```bicep
  <property-name>: [for <item> in <collection>: {
    <properties>
  }
  ```

- Iterando sobre os elementos de uma matriz

  ```bicep
  <property-name>: [for (<item>, <index>) in <collection>: {
    <properties>
  }
  ```

- Usando índice de loop

  ```bicep
  <property-name>: [for <index> in range(<start>, <stop>): {
    <properties>
  }
  ```

---

## <a name="copy-limits"></a>Limites de cópia

A contagem não pode ultrapassar os 800.

A contagem não pode ser um número negativo. Pode ser zero se implementar o modelo com uma versão recente de Azure CLI, PowerShell ou REST API. Especificamente, deve usar:

- Azure PowerShell **2.6** ou mais tarde
- Azure CLI **2.0.74** ou mais tarde
- REST Versão API **2019-05-10** ou posterior
- [As implementações ligadas](linked-templates.md) devem utilizar a versão API **2019-05-10** ou posterior para o tipo de recurso de implantação

Versões anteriores de PowerShell, CLI e REST API não suportam zero para contar.

## <a name="property-iteration"></a>Iteração imobiliária

O exemplo a seguir mostra como aplicar o ciclo de cópia à `dataDisks` propriedade numa máquina virtual:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numberOfDataDisks": {
      "type": "int",
      "minValue": 0,
      "maxValue": 16,
      "defaultValue": 3,
      "metadata": {
        "description": "The number of dataDisks to create."
      }
    },
    ...
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2020-06-01",
      ...
      "properties": {
        "storageProfile": {
          ...
          "copy": [
            {
              "name": "dataDisks",
              "count": "[parameters('numberOfDataDisks')]",
              "input": {
                "lun": "[copyIndex('dataDisks')]",
                "createOption": "Empty",
                "diskSizeGB": 1023
              }
            }
          ]
        }
        ...
      }
    }
  ]
}
```

Note que ao utilizar `copyIndex` dentro de uma iteração de propriedade, deve fornecer o nome da iteração. A iteração imobiliária também suporta um argumento de compensação. A compensação deve vir atrás do nome da iteração, tais como `copyIndex('dataDisks', 1)` .

O modelo implantado torna-se:

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2020-06-01",
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

O `copy` elemento é uma matriz para que possa especificar mais do que uma propriedade para o recurso.

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

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minValue(0)
@maxValue(16)
@description('The number of dataDisks to be returned in the output array.')
param numberOfDataDisks int = 16

resource vmName 'Microsoft.Compute/virtualMachines@2020-06-01' = {
  ...
  properties: {
    storageProfile: {
      ...
      dataDisks: [for i in range(0, numberOfDataDisks): {
        lun: i
        createOption: 'Empty'
        diskSizeGB: 1023
      }]
    }
    ...
  }
}
```

O modelo implantado torna-se:

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2020-06-01",
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

---

Pode utilizar a iteração de recursos e propriedades em conjunto. Referenciar a iteração da propriedade pelo nome.

# <a name="json"></a>[JSON](#tab/json)

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

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource vnetname_resource 'Microsoft.Network/virtualNetworks@2018-04-01' = [for i in range(0, 2): {
  name: concat(vnetname, i)
  location: resourceGroup().location
  properties: {
    addressSpace: {
      addressPrefixes: [
        addressPrefix
      ]
    }
    subnets: [for j in range(0, 2): {
      name: 'subnet-${j}'
      properties: {
        addressPrefix: subnetAddressPrefix[j]
      }
    }]
  }
}]
```

---

## <a name="example-templates"></a>Modelos de exemplo

O exemplo a seguir mostra um cenário comum para criar mais do que um valor para uma propriedade.

|Modelo  |Descrição  |
|---------|---------|
|[Implementação de VM com um número variável de discos de dados](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Implementa vários discos de dados com uma máquina virtual. |

## <a name="next-steps"></a>Passos seguintes

- Para passar por um tutorial, consulte [Tutorial: Crie múltiplas instâncias de recursos com modelos ARM](template-tutorial-create-multiple-instances.md).
- Para outras utilizações do ciclo de cópia, consulte:
  - [Iteração de recursos em modelos ARM](copy-resources.md)
  - [Iteração variável em modelos ARM](copy-variables.md)
  - [Iteração de saída em modelos ARM](copy-outputs.md)
- Se quiser aprender sobre as secções de um modelo, consulte [compreender a estrutura e a sintaxe dos modelos ARM](template-syntax.md).
- Para aprender a implementar o seu modelo, consulte [implementar recursos com modelos ARM e Azure PowerShell](deploy-powershell.md).
