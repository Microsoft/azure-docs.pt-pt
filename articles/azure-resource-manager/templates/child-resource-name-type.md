---
title: Recursos filho em modelos
description: Descreve como definir o nome e o tipo de recursos filho em um modelo de Azure Resource Manager.
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: 7d8a7a39bab3340b6f5c9e66d54b7398fa70ee3e
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122020"
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

O exemplo a seguir mostra o recurso filho fora do recurso pai. Você pode usar essa abordagem se o recurso pai não estiver implantado no mesmo modelo ou se quiser usar [copiar](create-multiple-instances.md) para criar mais de um recurso filho.

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
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2018-10-01",
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
        "type": "subnets",
        "apiVersion": "2018-10-01",
        "name": "Subnet1",
        "location": "[parameters('location')]",
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
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2018-10-01",
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
    "type": "Microsoft.Network/virtualNetworks/subnets",
    "apiVersion": "2018-10-01",
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

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre como criar modelos de Azure Resource Manager, consulte Criando [modelos](template-syntax.md).

* Para saber mais sobre o formato do nome do recurso ao referenciar o recurso, consulte a [função de referência](template-functions-resource.md#reference).
