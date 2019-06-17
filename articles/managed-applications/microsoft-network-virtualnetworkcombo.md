---
title: Elemento de interface do Usuário de VirtualNetworkCombo do Azure | Documentos da Microsoft
description: Descreve o elemento de interface do Usuário de Microsoft.Network.VirtualNetworkCombo para o portal do Azure.
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
ms.openlocfilehash: b0437338b403ff19761173d08be3938d07f13f55
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64708358"
---
# <a name="microsoftnetworkvirtualnetworkcombo-ui-element"></a>Elemento de interface do Usuário de Microsoft.Network.VirtualNetworkCombo
Um grupo de controlos para selecionar uma rede virtual nova ou existente.

## <a name="ui-sample"></a>Exemplo de interface do Usuário
Quando o usuário seleciona uma nova rede virtual, o utilizador pode personalizar o nome de cada sub-rede e o prefixo de endereço. A configuração de sub-redes é opcional.

![Microsoft.Network.VirtualNetworkCombo new](./media/managed-application-elements/microsoft.network.virtualnetworkcombo-new.png)

Quando o usuário seleciona uma rede virtual existente, o utilizador tem de mapear cada sub-rede que requer que o modelo de implementação a uma sub-rede existente. Configurar sub-redes nesse caso, é necessário.

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

## <a name="remarks"></a>Observações
- Se for especificado, o primeira não sobrepostos prefixo de endereço do tamanho `defaultValue.addressPrefixSize` é determinado automaticamente com base nas redes virtuais existentes na subscrição do utilizador.
- O valor predefinido para `defaultValue.name` e `defaultValue.addressPrefixSize` é **nulo**.
- `constraints.minAddressPrefixSize` tem de ser especificado. Quaisquer redes virtuais existentes com um espaço de endereços menor do que o valor especificado não estão disponíveis para seleção.
- `subnets` tem de ser especificado, e `constraints.minAddressPrefixSize` tem de ser especificado para cada sub-rede.
- Ao criar uma nova rede virtual, o prefixo de endereço de cada sub-rede é calculado automaticamente com base no prefixo de endereço da rede virtual e respetivos `addressPrefixSize`.
- Quando utilizar um existente virtual de rede, as sub-redes menores do que o respetivo `constraints.minAddressPrefixSize` não estão disponíveis para seleção. Além disso, se for especificado, sub-redes que não tenham, pelo menos, `minAddressCount` endereços disponíveis não estão disponíveis para seleção. O valor predefinido é **0**. Para garantir que os endereços disponíveis são contíguos, especifique **true** para `requireContiguousAddresses`. O valor predefinido é **true**.
- A criação de sub-redes numa rede virtual existente não é suportada.
- Se `options.hideExisting` é **true**, o utilizador não é possível escolher uma rede virtual existente. O valor predefinido é **false**.

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

## <a name="next-steps"></a>Passos Seguintes
* Para obter uma introdução à criação de definições de interface do Usuário, consulte [introdução ao CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de interface do Usuário, consulte [CreateUiDefinition elementos](create-uidefinition-elements.md).
