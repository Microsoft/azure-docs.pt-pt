---
title: Elemento de interface do usuário VirtualNetworkCombo do Azure | Microsoft Docs
description: Descreve o elemento de interface do usuário Microsoft. Network. VirtualNetworkCombo para portal do Azure.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: d325230f603be4ccfe4fe42f1b58c6ad892fdb2c
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151473"
---
# <a name="microsoftnetworkvirtualnetworkcombo-ui-element"></a>Elemento de interface do usuário Microsoft. Network. VirtualNetworkCombo

Um grupo de controles para selecionar uma rede virtual nova ou existente.

## <a name="ui-sample"></a>Exemplo de interface do usuário

Quando o usuário escolhe uma nova rede virtual, o usuário pode personalizar o nome e o prefixo de endereço de cada sub-rede. A configuração de sub-redes é opcional.

![Microsoft. Network. VirtualNetworkCombo novo](./media/managed-application-elements/microsoft.network.virtualnetworkcombo-new.png)

Quando o usuário escolhe uma rede virtual existente, o usuário deve mapear cada sub-rede que o modelo de implantação requer para uma sub-rede existente. A configuração de sub-redes, nesse caso, é necessária.

![Microsoft. Network. VirtualNetworkCombo existente](./media/managed-application-elements/microsoft.network.virtualnetworkcombo-existing.png)

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

- Se especificado, o primeiro prefixo de endereço não sobreposto de tamanho `defaultValue.addressPrefixSize` será determinado automaticamente com base nas redes virtuais existentes na assinatura do usuário.
- O valor padrão para `defaultValue.name` e `defaultValue.addressPrefixSize` é **nulo**.
- `constraints.minAddressPrefixSize` deve ser especificado. Todas as redes virtuais existentes com um espaço de endereço menor do que o valor especificado não estarão disponíveis para seleção.
- `subnets` deve ser especificado e `constraints.minAddressPrefixSize` deve ser especificado para cada sub-rede.
- Ao criar uma nova rede virtual, cada prefixo de endereço de sub-rede é calculado automaticamente com base no prefixo de endereço da rede virtual e no respectivo `addressPrefixSize`.
- Ao usar uma rede virtual existente, qualquer sub-rede menor que a respectiva `constraints.minAddressPrefixSize` não estará disponível para seleção. Além disso, se especificado, as sub-redes que não tiverem pelo menos `minAddressCount` endereços disponíveis ficarão indisponíveis para seleção. O valor padrão é **0**. Para garantir que os endereços disponíveis sejam contíguos, especifique **true** para `requireContiguousAddresses`. O valor padrão é **true**.
- Não há suporte para a criação de sub-redes em uma rede virtual existente.
- Se `options.hideExisting` for **true**, o usuário não poderá escolher uma rede virtual existente. O valor predefinido é **false**.

## <a name="next-steps"></a>Passos seguintes

* Para obter uma introdução à criação de definições de interface do usuário, consulte [introdução ao CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns nos elementos da interface do usuário, consulte [elementos CreateUiDefinition](create-uidefinition-elements.md).
