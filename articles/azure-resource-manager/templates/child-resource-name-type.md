---
title: Recursos infantis em modelos
description: Descreve como definir o nome e o tipo para recursos infantis num modelo de Gestor de Recursos Azure.
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: 6de2b476fe19a057a62e4a54963dd8fde0d11579
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77207898"
---
# <a name="set-name-and-type-for-child-resources"></a>Definir nome e tipo para recursos infantis

Os recursos infantis são recursos que existem apenas no contexto de outro recurso. Por exemplo, uma [extensão virtual](/azure/templates/microsoft.compute/2019-03-01/virtualmachines/extensions) da máquina não pode existir sem uma [máquina virtual](/azure/templates/microsoft.compute/2019-03-01/virtualmachines). O recurso de extensão é uma criança da máquina virtual.

Num modelo de Manjedoura de Recursos, pode especificar o recurso da criança dentro do recurso-mãe ou fora do recurso-mãe. O exemplo seguinte mostra o recurso infantil incluído na propriedade de recursos do recurso-mãe.

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

O exemplo seguinte mostra o recurso infantil fora do recurso-mãe. Pode utilizar esta abordagem se o recurso-mãe não for implantado no mesmo modelo, ou se quiser usar [cópia](copy-resources.md) para criar mais do que um recurso infantil.

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

Os valores que fornece para o nome e o tipo de recursos variam em função de se o recurso da criança é definido dentro ou fora do recurso-mãe.

## <a name="within-parent-resource"></a>Dentro do recurso-mãe

Quando definido dentro do tipo de recurso-mãe, forme os valores do tipo e do nome como uma única palavra sem cortes.

```json
"type": "{child-resource-type}",
"name": "{child-resource-name}",
```

O exemplo seguinte mostra uma rede virtual e com uma sub-rede. Note que a subnet a subnet está incluída na matriz de recursos para a rede virtual. O nome está definido para **Subnet1** e o tipo está definido para **sub-redes**. O recurso da criança é marcado como dependente do recurso-mãe porque o recurso-mãe deve existir antes de o recurso da criança poder ser implantado.

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

O tipo completo de recursos ainda é **Microsoft.Network/virtualNetworks/subnets**. Não fornece **Microsoft.Network/virtualNetworks/** porque é assumido a partir do tipo de recurso principal.

O nome do recurso para crianças está definido para **Subnet1,** mas o nome completo inclui o nome principal. Não **forneces VNet1** porque é assumido a partir do recurso-mãe.

## <a name="outside-parent-resource"></a>Recurso dos pais externos

Quando definido fora do recurso-mãe, forme o tipo e com cortes para incluir o tipo e o nome dos pais.

```json
"type": "{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}",
"name": "{parent-resource-name}/{child-resource-name}",
```

O exemplo seguinte mostra uma rede virtual e uma sub-rede que são definidas ao nível da raiz. Note que a subnet não está incluída na matriz de recursos para a rede virtual. O nome está definido para **VNet1/Subnet1** e o tipo é definido para **Microsoft.Network/virtualNetworks/subnets**. O recurso da criança é marcado como dependente do recurso-mãe porque o recurso-mãe deve existir antes de o recurso da criança poder ser implantado.

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

* Para aprender sobre a criação de modelos de Gestor de Recursos Azure, consulte [os modelos de autor.](template-syntax.md)

* Para conhecer o formato do nome do recurso ao fazer referência ao recurso, consulte a [função de referência](template-functions-resource.md#reference).
