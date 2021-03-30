---
title: Elemento UI virtualNetworkCombo
description: Descreve o elemento UI microsoft.Network.VirtualNetworkCombo para o portal Azure.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 711f5293b205c1f500c6d9e08154342285ef959b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87033219"
---
# <a name="microsoftnetworkvirtualnetworkcombo-ui-element"></a>Microsoft.Network.VirtualNetworkCombo UI elemento

Um grupo de controlos para selecionar uma rede virtual nova ou existente.

## <a name="ui-sample"></a>Amostra de UI

Quando o utilizador escolhe uma nova rede virtual, o utilizador pode personalizar o nome e o prefixo de endereço de cada sub-rede. Configurar sub-redes é opcional.

![Microsoft.Network.VirtualNetworkCombo novo](./media/managed-application-elements/microsoft-network-virtualnetworkcombo-new.png)

Quando o utilizador escolhe uma rede virtual existente, o utilizador deve mapear cada sub-rede que o modelo de implementação requer para uma sub-rede existente. É necessário configurar sub-redes neste caso.

![Microsoft.Network.VirtualNetworkCombo existente](./media/managed-application-elements/microsoft-network-virtualnetworkcombo-existing.png)

## <a name="schema"></a>Esquema

```json
{
  "name": "element1",
  "type": "Microsoft.Network.VirtualNetworkCombo",
  "label": {
    "virtualNetwork": "Virtual network",
    "subnets": "Subnets"
  },
  "toolTip": {
    "virtualNetwork": "",
    "subnets": ""
  },
  "defaultValue": {
    "name": "vnet01",
    "addressPrefixSize": "/16"
  },
  "constraints": {
    "minAddressPrefixSize": "/16"
  },
  "options": {
    "hideExisting": false
  },
  "subnets": {
    "subnet1": {
      "label": "First subnet",
      "defaultValue": {
        "name": "subnet-1",
        "addressPrefixSize": "/24"
      },
      "constraints": {
        "minAddressPrefixSize": "/24",
        "minAddressCount": 12,
        "requireContiguousAddresses": true
      }
    },
    "subnet2": {
      "label": "Second subnet",
      "defaultValue": {
        "name": "subnet-2",
        "addressPrefixSize": "/26"
      },
      "constraints": {
        "minAddressPrefixSize": "/26",
        "minAddressCount": 8,
        "requireContiguousAddresses": true
      }
    }
  },
  "visible": true
}
```

## <a name="sample-output"></a>Saída de exemplo

```json
{
  "name": "vnet01",
  "resourceGroup": "rg01",
  "addressPrefixes": ["10.0.0.0/16"],
  "newOrExisting": "new",
  "subnets": {
    "subnet1": {
      "name": "subnet-1",
      "addressPrefix": "10.0.0.0/24",
      "startAddress": "10.0.0.1"
    },
    "subnet2": {
      "name": "subnet-2",
      "addressPrefix": "10.0.1.0/26",
      "startAddress": "10.0.1.1"
    }
  }
}
```

## <a name="remarks"></a>Observações

- Se especificado, o primeiro prefixo de endereço não sobreposto de tamanho `defaultValue.addressPrefixSize` é determinado automaticamente com base nas redes virtuais existentes na subscrição do utilizador.
- O valor `defaultValue.name` predefinido e `defaultValue.addressPrefixSize` é **nulo.**
- `constraints.minAddressPrefixSize` deve ser especificado. Quaisquer redes virtuais existentes com um espaço de endereço inferior ao valor especificado não estão disponíveis para seleção.
- `subnets` devem ser especificados e `constraints.minAddressPrefixSize` devem ser especificados para cada sub-rede.
- Ao criar uma nova rede virtual, o prefixo de endereço de cada sub-rede é calculado automaticamente com base no prefixo de endereço da rede virtual e no respetivo `addressPrefixSize` .
- Ao utilizar uma rede virtual existente, quaisquer subesí redes menores do que as respetivas `constraints.minAddressPrefixSize` não estão disponíveis para seleção. Além disso, se especificado, sub-redes que não tenham pelo menos `minAddressCount` endereços disponíveis não estão disponíveis para seleção. O valor predefinido é **0**. Para garantir que os endereços disponíveis são contíguos, especifique **o seguinte** para `requireContiguousAddresses` . O valor predefinido é **verdadeiro.**
- A criação de sub-redes numa rede virtual existente não é suportada.
- Se `options.hideExisting` for **verdade,** o utilizador não pode escolher uma rede virtual existente. O valor predefinido é **falso**.

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à criação de definições de UI, consulte [Começar com CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de UI, consulte [os elementos CreateUiDefinition](create-uidefinition-elements.md).
