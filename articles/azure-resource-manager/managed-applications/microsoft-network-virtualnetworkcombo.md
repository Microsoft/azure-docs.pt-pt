---
title: Elemento UI VirtualNetworkCombo
description: Descreve o elemento Microsoft.Network.VirtualNetworkCombo UI para o portal Azure.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 53c9653b44a6c9d26d49d37b351cf6000676e2d4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75651972"
---
# <a name="microsoftnetworkvirtualnetworkcombo-ui-element"></a>Microsoft.Network.VirtualNetworkCombo UI

Um grupo de controlos para selecionar uma rede virtual nova ou existente.

## <a name="ui-sample"></a>Amostra de UI

Quando o utilizador escolhe uma nova rede virtual, o utilizador pode personalizar o nome e o prefixo de endereço de cada subnet. Configurar subredes é opcional.

![Microsoft.Network.VirtualNetworkCombo novo](./media/managed-application-elements/microsoft.network.virtualnetworkcombo-new.png)

Quando o utilizador escolhe uma rede virtual existente, o utilizador deve mapear cada sub-rede que o modelo de implementação requer para uma sub-rede existente. Neste caso, é necessário configurar as subredes.

![Microsoft.Network.VirtualNetworkCombo existente](./media/managed-application-elements/microsoft.network.virtualnetworkcombo-existing.png)

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

## <a name="sample-output"></a>Resultado da amostra

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

- Se especificado, o primeiro prefixo de `defaultValue.addressPrefixSize` tamanho não sobreposto é determinado automaticamente com base nas redes virtuais existentes na subscrição do utilizador.
- O valor `defaultValue.name` predefinido para e `defaultValue.addressPrefixSize` é **nulo**.
- `constraints.minAddressPrefixSize`deve ser especificado. Quaisquer redes virtuais existentes com um espaço de endereço inferior ao valor especificado não estão disponíveis para seleção.
- `subnets`deve ser especificado `constraints.minAddressPrefixSize` e especificado para cada sub-rede.
- Ao criar uma nova rede virtual, o prefixo de endereço de cada subnet é calculado `addressPrefixSize`automaticamente com base no prefixo de endereço da rede virtual e no respetivo .
- Ao utilizar uma rede virtual existente, quaisquer `constraints.minAddressPrefixSize` subredes menores do que as respetivas não estão disponíveis para seleção. Além disso, se especificado, as subredes `minAddressCount` que não têm endereços disponíveis não estão disponíveis para seleção. O valor predefinido é **0**. Para garantir que os endereços disponíveis são contíguos, especifique **verdadeiramente** para `requireContiguousAddresses`. O valor padrão é **verdadeiro.**
- A criação de subredes numa rede virtual existente não é suportada.
- Se `options.hideExisting` for **verdade,** o utilizador não pode escolher uma rede virtual existente. O valor predefinido é **falso**.

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à criação de definições de UI, consulte [Começar com CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns nos elementos UI, consulte [elementos CreateUiDefinition](create-uidefinition-elements.md).
