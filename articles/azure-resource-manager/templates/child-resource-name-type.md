---
title: Recursos infantis em modelos
description: Descreve como definir o nome e o tipo para recursos infantis num modelo de Gestor de Recursos Azure (modelo ARM).
ms.topic: conceptual
ms.date: 12/21/2020
ms.openlocfilehash: a950d72751b829c0a2aa3ba5ca27316a0544d9cc
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/07/2021
ms.locfileid: "97963917"
---
# <a name="set-name-and-type-for-child-resources"></a>Definir nome e tipo para recursos infantis

Os recursos infantis são recursos que existem apenas no contexto de outro recurso. Por exemplo, uma [extensão de máquina virtual](/azure/templates/microsoft.compute/virtualmachines/extensions) não pode existir sem uma [máquina virtual](/azure/templates/microsoft.compute/virtualmachines). O recurso de extensão é uma criança da máquina virtual.

Cada recurso dos pais aceita apenas certos tipos de recursos como recursos infantis. O tipo de recurso para o recurso da criança inclui o tipo de recurso para o recurso principal. Por exemplo, `Microsoft.Web/sites/config` e ambos são recursos `Microsoft.Web/sites/extensions` infantis do `Microsoft.Web/sites` . Os tipos de recursos aceites são especificados no esquema de [modelo](https://github.com/Azure/azure-resource-manager-schemas) do recurso principal.

Num modelo de Gestor de Recursos Azure (modelo ARM), pode especificar o recurso da criança dentro do recurso principal ou fora do recurso principal. O exemplo a seguir mostra o recurso da criança incluído na propriedade dos recursos do recurso principal.

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

Os recursos infantis só podem ser definidos com cinco níveis de profundidade.

O exemplo seguinte mostra o recurso da criança fora do recurso principal. Pode utilizar esta abordagem se o recurso dos pais não for implantado no mesmo modelo, ou se quiser usar a [cópia](copy-resources.md) para criar mais do que um recurso infantil.

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

Os valores que fornece para o nome e o tipo de recurso variam em função do facto de o recurso da criança ser definido dentro ou fora do recurso principal.

## <a name="within-parent-resource"></a>Dentro do recurso principal

Quando definido dentro do tipo de recurso principal, você forma o tipo e os valores de nome como uma única palavra sem cortes.

```json
"type": "{child-resource-type}",
"name": "{child-resource-name}",
```

O exemplo a seguir mostra uma rede virtual e com uma sub-rede. Note que a sub-rede está incluída no conjunto de recursos da rede virtual. O nome está definido para **Subnet1** e o tipo está definido para **sub-redes**. O recurso da criança é marcado como dependente do recurso principal porque o recurso principal deve existir antes de o recurso da criança poder ser implantado.

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

O tipo completo de recursos ainda `Microsoft.Network/virtualNetworks/subnets` está. Não `Microsoft.Network/virtualNetworks/` forneces porque é assumido do tipo de recursos dos pais.

O nome do recurso para crianças está definido para **Subnet1,** mas o nome completo inclui o nome principal. Não **forneces o VNet1** porque é assumido pelo recurso dos pais.

## <a name="outside-parent-resource"></a>Recurso de pai externo

Quando definido fora do recurso principal, você forma o tipo e com cortes para incluir o tipo e nome do progenitor.

```json
"type": "{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}",
"name": "{parent-resource-name}/{child-resource-name}",
```

O exemplo a seguir mostra uma rede virtual e uma sub-rede que são ambas definidas ao nível da raiz. Note que a sub-rede não está incluída no conjunto de recursos da rede virtual. O nome está definido para **VNet1/Subnet1** e o tipo está definido para `Microsoft.Network/virtualNetworks/subnets` . O recurso da criança é marcado como dependente do recurso principal porque o recurso principal deve existir antes de o recurso da criança poder ser implantado.

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

* Para aprender a criar modelos ARM, consulte [compreender a estrutura e a sintaxe dos modelos ARM](template-syntax.md).
* Para saber mais sobre o formato do nome do recurso ao fazer referência ao recurso, consulte a [função de referência](template-functions-resource.md#reference).
