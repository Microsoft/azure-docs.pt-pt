---
title: Implantar várias instâncias de recursos do Azure | Microsoft Docs
description: Use a operação de cópia e as matrizes em um modelo de Azure Resource Manager para iterar várias vezes ao implantar recursos.
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: tomfitz
ms.openlocfilehash: f97f9dac76ac29cf295b5cedc08f916e85c4e317
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/30/2019
ms.locfileid: "71675093"
---
# <a name="resource-property-or-variable-iteration-in-azure-resource-manager-templates"></a>Recurso, propriedade ou iteração de variável em modelos de Azure Resource Manager

Este artigo mostra como criar mais de uma instância de um recurso, variável ou propriedade em seu modelo de Azure Resource Manager. Para criar várias instâncias, adicione o objeto `copy` ao seu modelo.

Quando usado com um recurso, o objeto de cópia tem o seguinte formato:

```json
"copy": {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "mode": "serial" <or> "parallel",
    "batchSize": <number-to-deploy-serially>
}
```

Quando usado com uma variável ou propriedade, o objeto de cópia tem o seguinte formato:

```json
"copy": [
  {
      "name": "<name-of-loop>",
      "count": <number-of-iterations>,
      "input": <values-for-the-property-or-variable>
  }
]
```

Ambos os usos são descritos mais detalhadamente neste artigo. Para obter um tutorial, consulte [tutorial: criar várias instâncias de recursos usando modelos do Resource Manager](./resource-manager-tutorial-create-multiple-instances.md).

Se você precisar especificar se um recurso é implantado, consulte [elemento Condition](conditional-resource-deployment.md).

## <a name="copy-limits"></a>Limites de cópia

Para especificar o número de iterações, você fornece um valor para a propriedade Count. A contagem não pode exceder 800.

A contagem não pode ser um número negativo. Se você implantar um modelo com Azure PowerShell 2,6 ou posterior, CLI do Azure 2.0.74 ou posterior, ou a API REST versão **2019-05-10** ou posterior, poderá definir Count como zero. As versões anteriores do PowerShell, da CLI e da API REST não dão suporte a zero para contagem.

Tenha cuidado ao usar a [implantação do modo completo](deployment-modes.md) com a cópia. Se você reimplantar com o modo completo em um grupo de recursos, todos os recursos que não forem especificados no modelo após a resolução do loop de cópia serão excluídos.

Os limites para a contagem são os mesmos, se usados com um recurso, uma variável ou uma propriedade.

## <a name="resource-iteration"></a>Iteração de recurso

Se você quiser criar mais de uma instância de um recurso em uma implantação, adicione um elemento `copy` ao tipo de recurso. No elemento copiar, especifique o número de iterações e um nome para esse loop.

O recurso para criar várias vezes usa o seguinte formato:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": 3
      }
    }
  ],
  "outputs": {}
}
```

Observe que o nome de cada recurso inclui a função `copyIndex()`, que retorna a iteração atual no loop. `copyIndex()` é baseado em zero. Portanto, o exemplo a seguir:

```json
"name": "[concat('storage', copyIndex())]",
```

Cria estes nomes:

* storage0
* storage1
* storage2.

Para deslocar o valor de índice, pode passar um valor na função copyIndex(). O número de iterações ainda é especificado no elemento Copy, mas o valor de copyIndex é offset pelo valor especificado. Portanto, o exemplo a seguir:

```json
"name": "[concat('storage', copyIndex(1))]",
```

Cria estes nomes:

* storage1
* storage2
* storage3

A operação de cópia é útil ao trabalhar com matrizes porque você pode iterar em cada elemento na matriz. Use a função `length` na matriz para especificar a contagem de iterações e `copyIndex` para recuperar o índice atual na matriz. Portanto, o exemplo a seguir:

```json
"parameters": {
  "org": {
    "type": "array",
    "defaultValue": [
      "contoso",
      "fabrikam",
      "coho"
    ]
  }
},
"resources": [
  {
    "name": "[concat('storage', parameters('org')[copyIndex()])]",
    "copy": {
      "name": "storagecopy",
      "count": "[length(parameters('org'))]"
    },
    ...
  }
]
```

Cria estes nomes:

* storagecontoso
* storagefabrikam
* storagecoho

Por padrão, o Resource Manager cria os recursos em paralelo. Ele não aplica nenhum limite ao número de recursos implantados em paralelo, além do limite total de 800 recursos no modelo. A ordem na qual eles são criados não é garantida.

No entanto, talvez você queira especificar que os recursos sejam implantados em sequência. Por exemplo, ao atualizar um ambiente de produção, talvez você queira escalonar as atualizações para que apenas um determinado número seja atualizado a qualquer momento. Para implantar em série mais de uma instância de um recurso, defina `mode` como **serial** e `batchSize` como o número de instâncias a serem implantadas de cada vez. Com o modo Serial, o Resource Manager cria uma dependência em instâncias anteriores no loop, portanto, ele não inicia um lote até que o lote anterior seja concluído.

Por exemplo, para implantar em série duas contas de armazenamento de cada vez, use:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": 4,
        "mode": "serial",
        "batchSize": 2
      }
    }
  ],
  "outputs": {}
}
```

A propriedade Mode também aceita **Parallel**, que é o valor padrão.

Para obter informações sobre como usar a cópia com modelos aninhados, consulte [usando copiar](resource-group-linked-templates.md#using-copy).

## <a name="property-iteration"></a>Iteração de propriedade

Para criar mais de um valor para uma propriedade em um recurso, adicione uma matriz `copy` no elemento Properties. Essa matriz contém objetos e cada objeto tem as seguintes propriedades:

* nome-o nome da propriedade para a qual criar vários valores
* Count – o número de valores a serem criados.
* entrada-um objeto que contém os valores a serem atribuídos à propriedade

O exemplo a seguir mostra como aplicar `copy` à propriedade datadisks em uma máquina virtual:

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2017-03-30",
  "properties": {
    "storageProfile": {
      "copy": [{
        "name": "dataDisks",
        "count": 3,
        "input": {
          "lun": "[copyIndex('dataDisks')]",
          "createOption": "Empty",
          "diskSizeGB": "1023"
        }
      }],
      ...
```

Observe que, ao usar `copyIndex` dentro de uma iteração de propriedade, você deve fornecer o nome da iteração. Você não precisa fornecer o nome quando usado com a iteração de recurso.

O Resource Manager expande a matriz `copy` durante a implantação. O nome da matriz se torna o nome da propriedade. Os valores de entrada se tornam as propriedades do objeto. O modelo implantado se torna:

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
          "diskSizeGB": "1023"
        },
        {
          "lun": 1,
          "createOption": "Empty",
          "diskSizeGB": "1023"
        },
        {
          "lun": 2,
          "createOption": "Empty",
          "diskSizeGB": "1023"
        }
      ],
      ...
```

O elemento Copy é uma matriz para que você possa especificar mais de uma propriedade para o recurso. Adicione um objeto para cada propriedade a ser criada.

```json
{
  "name": "string",
  "type": "Microsoft.Network/loadBalancers",
  "apiVersion": "2017-10-01",
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

Você pode usar a iteração de recurso e Propriedade juntas. Referencie a iteração de propriedade por nome.

```json
{
  "type": "Microsoft.Network/virtualNetworks",
  "name": "[concat(parameters('vnetname'), copyIndex())]",
  "apiVersion": "2018-04-01",
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

## <a name="variable-iteration"></a>Iteração de variável

Para criar várias instâncias de uma variável, use a propriedade `copy` na seção variáveis. Você cria uma matriz de elementos construídos com base no valor na propriedade `input`. Você pode usar a propriedade `copy` dentro de uma variável ou no nível superior da seção de variáveis. Ao usar `copyIndex` dentro de uma iteração de variável, você deve fornecer o nome da iteração.

Para obter um exemplo simples de criação de uma matriz de valores de cadeia de caracteres, consulte [copiar modelo de matriz](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/copy-array/azuredeploy.json).

O exemplo a seguir mostra várias maneiras diferentes de criar variáveis de matriz com elementos construídos dinamicamente. Ele mostra como usar Copy dentro de uma variável para criar matrizes de objetos e cadeias de caracteres. Ele também mostra como usar Copy no nível superior para criar matrizes de objetos, cadeias de caracteres e inteiros.

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

O tipo de variável que é criado depende do objeto de entrada. Por exemplo, a variável chamada **Top-Level-Object-array** no exemplo anterior retorna:

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

E, a variável chamada de **nível superior-String-array** retorna:

```json
[
  "myDataDisk1",
  "myDataDisk2",
  "myDataDisk3",
  "myDataDisk4",
  "myDataDisk5"
]
```

## <a name="depend-on-resources-in-a-loop"></a>Depender de recursos em um loop

Você especifica que um recurso é implantado após outro recurso usando o elemento `dependsOn`. Para implantar um recurso que depende da coleção de recursos em um loop, forneça o nome do loop de cópia no elemento depende. O exemplo a seguir mostra como implantar três contas de armazenamento antes de implantar a máquina virtual. A definição completa da máquina virtual não é mostrada. Observe que o elemento Copy tem o nome definido como `storagecopy` e o elemento depende para as máquinas virtuais também é definido como `storagecopy`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": 3
      }
    },
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[concat('VM', uniqueString(resourceGroup().id))]",
      "dependsOn": ["storagecopy"],
      ...
    }
  ],
  "outputs": {}
}
```

<a id="looping-on-a-nested-resource" />

## <a name="iteration-for-a-child-resource"></a>Iteração para um recurso filho
Você não pode usar um loop de cópia para um recurso filho. Para criar mais de uma instância de um recurso que você normalmente define como aninhado em outro recurso, você deve criar esse recurso como um recurso de nível superior. Você define a relação com o recurso pai por meio das propriedades Type e Name.

Por exemplo, suponha que você normalmente defina um conjunto de um como um recurso filho dentro de um data factory.

```json
"resources": [
{
  "type": "Microsoft.DataFactory/datafactories",
  "name": "exampleDataFactory",
  ...
  "resources": [
    {
      "type": "datasets",
      "name": "exampleDataSet",
      "dependsOn": [
        "exampleDataFactory"
      ],
      ...
    }
  ]
```

Para criar mais de um conjunto de dados, mova-o para fora do data factory. O conjunto de recursos deve estar no mesmo nível que o data factory, mas ainda é um recurso filho do data factory. Você preserva a relação entre o conjunto de dados e data factory pelas propriedades Type e Name. Como o tipo não pode mais ser inferido de sua posição no modelo, você deve fornecer o tipo totalmente qualificado no formato: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`.

Para estabelecer uma relação pai/filho com uma instância do data factory, forneça um nome para o conjunto de dados que inclui o nome do recurso pai. Use o formato: `{parent-resource-name}/{child-resource-name}`.

O exemplo a seguir mostra a implementação:

```json
"resources": [
{
  "type": "Microsoft.DataFactory/datafactories",
  "name": "exampleDataFactory",
  ...
},
{
  "type": "Microsoft.DataFactory/datafactories/datasets",
  "name": "[concat('exampleDataFactory', '/', 'exampleDataSet', copyIndex())]",
  "dependsOn": [
    "exampleDataFactory"
  ],
  "copy": {
    "name": "datasetcopy",
    "count": "3"
  },
  ...
}]
```

## <a name="example-templates"></a>Modelos de exemplo

Os exemplos a seguir mostram cenários comuns para criar mais de uma instância de um recurso ou propriedade.

|Modelo  |Descrição  |
|---------|---------|
|[Armazenamento de cópia](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystorage.json) |Implanta mais de uma conta de armazenamento com um número de índice no nome. |
|[Armazenamento de cópia serial](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/serialcopystorage.json) |Implanta várias contas de armazenamento uma de cada vez. O nome inclui o número de índice. |
|[Copiar armazenamento com matriz](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystoragewitharray.json) |Implanta várias contas de armazenamento. O nome inclui um valor de uma matriz. |
|[Implantação de VM com um número variável de discos de dados](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Implanta vários discos de dados com uma máquina virtual. |
|[Copie as variáveis](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) |Demonstra as diferentes maneiras de iterar em variáveis. |
|[Várias regras de segurança](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |Implanta várias regras de segurança em um grupo de segurança de rede. Ele constrói as regras de segurança a partir de um parâmetro. Para o parâmetro, consulte [vários arquivos de parâmetro NSG](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json). |

## <a name="next-steps"></a>Passos seguintes

* Para percorrer um tutorial, consulte [tutorial: criar várias instâncias de recursos usando modelos do Resource Manager](./resource-manager-tutorial-create-multiple-instances.md).

* Se você quiser saber mais sobre as seções de um modelo, consulte [criando modelos de Azure Resource Manager](resource-group-authoring-templates.md).
* Para saber como implantar seu modelo, consulte [implantar um aplicativo com Azure Resource Manager modelo](resource-group-template-deploy.md).

