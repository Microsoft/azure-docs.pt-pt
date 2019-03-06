---
title: Implementar várias instâncias de recursos do Azure | Documentos da Microsoft
description: Utilize a operação de cópia e matrizes de um modelo Azure Resource Manager para iterar várias vezes durante a implantação de recursos.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
editor: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2019
ms.author: tomfitz
ms.openlocfilehash: c60983dbbe72515fd8f0f4860e169ce1ba69ed45
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57407090"
---
# <a name="deploy-more-than-one-instance-of-a-resource-or-property-in-azure-resource-manager-templates"></a>Implementar mais de uma instância de um recurso ou uma propriedade nos modelos do Azure Resource Manager

Este artigo mostra como iterar no modelo do Azure Resource Manager para criar mais de uma instância de um recurso. Se tiver de especificar se um recurso está implementado em todos os, consulte [elemento condition](resource-group-authoring-templates.md#condition).

Para obter um tutorial, veja [Tutorial: criar várias instâncias de recursos com modelos do Resource Manager](./resource-manager-tutorial-create-multiple-instances.md).

## <a name="resource-iteration"></a>Iteração de recursos

Quando tem de decidir durante a implementação para criar uma ou mais instâncias de um recurso, adicione um `copy` elemento para o tipo de recurso. No elemento de cópia, especifique o número de iterações e um nome para este ciclo. O valor de contagem tem de ser um número inteiro positivo e não pode ser mais de 800. 

O recurso para criar várias vezes assume o formato seguinte:

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

Tenha em atenção que o nome de cada recurso inclui o `copyIndex()` função, que retorna a iteração atual no loop. `copyIndex()` é baseado em zero. Para isso, o exemplo seguinte:

```json
"name": "[concat('storage', copyIndex())]",
```

Cria estes nomes:

* storage0
* storage1
* storage2.

Para deslocar o valor de índice, pode passar um valor na função copyIndex(). O número de iterações para efetuar ainda é especificado no elemento de cópia, mas o valor de copyIndex é contrabalançado pelo valor especificado. Para isso, o exemplo seguinte:

```json
"name": "[concat('storage', copyIndex(1))]",
```

Cria estes nomes:

* storage1
* storage2
* storage3

A operação de cópia é útil ao trabalhar com matrizes, porque é possível iterar em cada elemento na matriz. Utilize o `length` função na matriz para especificar a contagem de iterações, e `copyIndex` para recuperar o índice atual na matriz. Para isso, o exemplo seguinte:

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

Por predefinição, o Gestor de recursos cria os recursos em paralelo. Não é garantida a ordem em que forem criadas. No entanto, poderá especificar que os recursos são implementados numa sequência. Por exemplo, ao atualizar um ambiente de produção, convém escalonar as atualizações por isso, apenas um determinado número são atualizadas ao mesmo tempo.

Para implementar em série mais de uma instância de um recurso, defina `mode` para **serial** e `batchSize` para o número de instâncias a implementar cada vez. Com o modo serial, o Resource Manager cria uma dependência em instâncias anteriores no loop, para que ele não começa um lote até que o lote anterior seja concluída.

Por exemplo, para implementar em série contas de armazenamento dois ao mesmo tempo, utilize:

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

A propriedade de modo também aceita **paralela**, que é o valor predefinido.

## <a name="property-iteration"></a>Iteração de propriedade

Para criar mais de um valor para uma propriedade num recurso, adicione um `copy` matriz no elemento de propriedades. Essa matriz contém objetos, e cada objeto tem as seguintes propriedades:

* nome – o nome da propriedade para criar vários valores para
* Contagem - o número de valores para criar. O valor de contagem tem de ser um número inteiro positivo e não pode ser mais de 800.
* introdução - um objeto que contém os valores a atribuir à propriedade  

O exemplo seguinte mostra como aplicar `copy` à propriedade dataDisks numa máquina virtual:

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

Tenha em atenção que, quando utilizar `copyIndex` dentro de uma iteração de propriedade, tem de fornecer o nome da iteração. Não precisa fornecer o nome quando utilizado com a iteração de recursos.

O Resource Manager expande o `copy` matriz durante a implementação. O nome da matriz torna-se o nome da propriedade. Os valores de entrada se tornam as propriedades do objeto. O modelo implementado torna-se:

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

O elemento de cópia é uma matriz, então, pode especificar mais de uma propriedade para o recurso. Adicione um objeto para cada propriedade criar.

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

Pode usar recursos e a propriedade iteração juntos. Referência a iteração de propriedade por nome.

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

## <a name="variable-iteration"></a>Iteração variável

Para criar várias instâncias de uma variável, utilize o `copy` propriedade na secção de variáveis. Criar uma matriz de elementos construído com o valor a `input` propriedade. Pode utilizar o `copy` propriedade dentro de uma variável ou ao nível superior da secção de variáveis. Quando utilizar `copyIndex` dentro de uma iteração de variável, tem de fornecer o nome da iteração.

Para obter um exemplo simples de criação de uma matriz de valores de cadeia de caracteres, consulte [modelo de matriz de cópia](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/copy-array/azuredeploy.json).

O exemplo seguinte mostra várias formas diferentes de criar variáveis de matriz com elementos construídos dinamicamente. Ele mostra como utilizar a cópia dentro de uma variável para criar matrizes de objetos e cadeias de caracteres. Ele também mostra como utilizar a cópia no nível superior para criar matrizes de objetos, cadeias de caracteres e inteiros.

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

O tipo de variável é criada depende do objeto de entrada. Por exemplo, a variável chamada **superior-nível--matriz de objeto** no exemplo anterior devolve:

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

E, a variável chamada **superior-nível--matriz de cadeia** devolve:

```json
[
  "myDataDisk1",
  "myDataDisk2",
  "myDataDisk3",
  "myDataDisk4",
  "myDataDisk5"
]
```

## <a name="depend-on-resources-in-a-loop"></a>Dependem de recursos num loop

Especificar que um recurso é implementado depois de outro recurso utilizando o `dependsOn` elemento. Para implementar um recurso que depende da coleção de recursos num loop, forneça o nome do ciclo de cópia no elemento dependsOn. O exemplo seguinte mostra como implementar três contas de armazenamento antes de implementar a Máquina Virtual. A definição completa da Máquina Virtual não é mostrada. Observe que o elemento de cópia tem o nome definido como `storagecopy` e o elemento de dependsOn para as máquinas virtuais também está definido como `storagecopy`.

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

## <a name="iteration-for-a-child-resource"></a>Iteração de um recurso subordinado
Não é possível usar um loop de cópia de um recurso de subordinados. Para criar mais de uma instância de um recurso que normalmente define como aninhada dentro de outro recurso, em vez disso, tem de criar esse recurso como um recurso de nível superior. É possível definir a relação com o recurso principal através das propriedades de tipo e nome.

Por exemplo, suponha que normalmente define um conjunto de dados como um recurso filho dentro de uma fábrica de dados.

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

Para criar mais de um conjunto de dados, movê-lo fora da fábrica de dados. O conjunto de dados tem de estar no mesmo nível que a fábrica de dados, mas ainda é um recurso de subordinados do data factory. Preservar a relação entre o conjunto de dados e o data factory através das propriedades de tipo e nome. Uma vez que o tipo já não pode ser inferido da sua posição no modelo, tem de fornecer o tipo completamente qualificado no formato: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`.

Para estabelecer uma relação de pai/filho com uma instância de fábrica de dados, forneça um nome para o conjunto de dados que inclui o nome de recurso principal. Utilize o formato: `{parent-resource-name}/{child-resource-name}`.  

O exemplo seguinte mostra a implementação:

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

Os exemplos seguintes apresentam cenários comuns para criar mais de uma instância de um recurso ou uma propriedade.

|Modelo  |Descrição  |
|---------|---------|
|[Armazenamento de cópia](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystorage.json) |Implementa mais de uma conta de armazenamento com um número de índice no nome. |
|[Armazenamento de cópia Serial](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/serialcopystorage.json) |Implementa várias contas de armazenamento um momento. O nome inclui o número de índice. |
|[Armazenamento de cópia com matriz](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystoragewitharray.json) |Implementa várias contas de armazenamento. O nome inclui um valor de uma matriz. |
|[Implementação de VMS com um número variável de discos de dados](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Implementa vários discos de dados com uma máquina virtual. |
|[Copie as variáveis](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) |Demonstra as diferentes formas de iteração em variáveis. |
|[Várias regras de segurança](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |Implementa várias regras de segurança de um grupo de segurança de rede. Ele constrói as regras de segurança de um parâmetro. Para o parâmetro, consulte [vários ficheiros de parâmetro NSG](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json). |

## <a name="next-steps"></a>Passos Seguintes

* Para seguir um tutorial, veja [Tutorial: criar várias instâncias de recursos com modelos do Resource Manager](./resource-manager-tutorial-create-multiple-instances.md).

* Se quiser saber mais sobre as secções de um modelo, veja [criação de modelos do Azure Resource Manager](resource-group-authoring-templates.md).
* Para saber como implementar o modelo, veja [implementar uma aplicação com o modelo do Azure Resource Manager](resource-group-template-deploy.md).

