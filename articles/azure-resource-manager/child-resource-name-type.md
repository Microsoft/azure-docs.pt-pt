---
title: Nome e tipo de recursos filho em modelos de Azure Resource Manager
description: Descreve como definir o nome e o tipo de recursos filho em um modelo de Azure Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: tomfitz
ms.openlocfilehash: bd1c38547866c34eb5c4808ed5b185b50fa44a01
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035570"
---
# <a name="set-name-and-type-for-child-resources"></a>Definir nome e tipo para recursos filho

Recursos filho são recursos que existem somente dentro do contexto de outro recurso. Por exemplo, uma [extensão de máquina virtual](/azure/templates/microsoft.compute/2019-03-01/virtualmachines/extensions) não pode existir sem uma [máquina virtual](/azure/templates/microsoft.compute/2019-03-01/virtualmachines). O recurso de extensão é um filho da máquina virtual.

Em um modelo do Resource Manager, você pode especificar o recurso filho dentro do recurso pai ou fora do recurso pai. O exemplo a seguir mostra o recurso filho incluído na propriedade Resources do recurso pai.

```json
"resources": [
  {
    <parent-resource>
    "resources": [
      <child-resource>
    ]
  }
]
```

O exemplo a seguir mostra o recurso filho fora do recurso pai. Você pode usar essa abordagem se o recurso pai não estiver implantado no mesmo modelo ou se quiser usar [copiar](resource-group-create-multiple.md) para criar mais de um recurso filho.

```json
"resources": [
  {
    <parent-resource>
  },
  {
    <child-resource>
  }
]
```

Os valores que você fornece para o nome e tipo do recurso variam de acordo com a definição do recurso filho dentro ou fora do recurso pai.

## <a name="within-parent-resource"></a>Dentro do recurso pai

Quando definido dentro do tipo de recurso pai, você formata os valores de tipo e nome como uma única palavra sem barras.

```json
"type": "{child-resource-type}",
"name": "{child-resource-name}",
```

O exemplo a seguir mostra uma rede virtual e uma sub-rede. Observe que a sub-rede está incluída na matriz de recursos para a rede virtual. O nome é definido como **Subnet1** e o tipo é definido como **sub-redes**. O recurso filho é marcado como dependente do recurso pai porque o recurso pai deve existir antes que o recurso filho possa ser implantado.

```json
"resources": [
  {
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "VNet1",
    "location": "[parameters('location')]",
    "properties": {
      "addressSpace": {
        "addressPrefixes": [
          "10.0.0.0/16"
        ]
      }
    },
    "resources": [
      {
        "apiVersion": "2018-10-01",
        "type": "subnets",
        "location": "[parameters('location')]",
        "name": "Subnet1",
        "dependsOn": [
          "VNet1"
        ],
        "properties": {
          "addressPrefix": "10.0.0.0/24"
        }
      }
    ]
  }
]
```

O tipo de recurso completo ainda é **Microsoft. Network/virtualNetworks/sub-redes**. Você não fornece **Microsoft. Network/virtualNetworks/** porque ele é assumido do tipo de recurso pai.

O nome do recurso filho é definido como **Subnet1** , mas o nome completo inclui o nome do pai. Você não fornece **VNet1** porque ele é assumido do recurso pai.

## <a name="outside-parent-resource"></a>Recurso pai externo

Quando definido fora do recurso pai, você formata o tipo e com barras para incluir o tipo e o nome pai.

```json
"type": "{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}",
"name": "{parent-resource-name}/{child-resource-name}",
```

O exemplo a seguir mostra uma rede virtual e uma sub-rede que são definidas no nível raiz. Observe que a sub-rede não está incluída na matriz de recursos para a rede virtual. O nome é definido como **VNet1/Subnet1** e o tipo é definido como **Microsoft. Network/virtualNetworks/sub-redes**. O recurso filho é marcado como dependente do recurso pai porque o recurso pai deve existir antes que o recurso filho possa ser implantado.

```json
"resources": [
  {
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "VNet1",
    "location": "[parameters('location')]",
    "properties": {
      "addressSpace": {
        "addressPrefixes": [
          "10.0.0.0/16"
        ]
      }
    }
  },
  {
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Network/virtualNetworks/subnets",
    "location": "[parameters('location')]",
    "name": "VNet1/Subnet1",
    "dependsOn": [
      "VNet1"
    ],
    "properties": {
      "addressPrefix": "10.0.0.0/24"
    }
  }
]
```

## <a name="next-steps"></a>Passos Seguintes

* Para saber mais sobre como criar modelos de Azure Resource Manager, consulte Criando [modelos](resource-group-authoring-templates.md). 

* Para saber mais sobre o formato do nome do recurso ao referenciar o recurso, consulte a [função de referência](resource-group-template-functions-resource.md#reference).
