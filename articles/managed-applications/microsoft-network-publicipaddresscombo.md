---
title: Elemento de interface do usuário PublicIpAddressCombo do Azure | Microsoft Docs
description: Descreve o elemento de interface do usuário Microsoft. Network. PublicIpAddressCombo para portal do Azure.
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
ms.openlocfilehash: 069a8ee1f019d1b21be996084e4902f94076fbf7
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151501"
---
# <a name="microsoftnetworkpublicipaddresscombo-ui-element"></a>Microsoft.Network.PublicIpAddressCombo UI element

Um grupo de controles para selecionar um endereço IP público novo ou existente.

## <a name="ui-sample"></a>Exemplo de interface do usuário

![Microsoft.Network.PublicIpAddressCombo](./media/managed-application-elements/microsoft.network.publicipaddresscombo.png)

- Se o usuário selecionar "nenhum" para o endereço IP público, a caixa de texto rótulo de nome de domínio ficará oculta.
- Se o usuário selecionar um endereço IP público existente, a caixa de texto rótulo de nome de domínio será desabilitada. Seu valor é o rótulo de nome de domínio do endereço IP selecionado.
- O sufixo do nome de domínio (por exemplo, westus.cloudapp.azure.com) é atualizado automaticamente com base no local selecionado.

## <a name="schema"></a>Esquema

```json
{
  "name": "element1",
  "type": "Microsoft.Network.PublicIpAddressCombo",
  "label": {
    "publicIpAddress": "Public IP address",
    "domainNameLabel": "Domain name label"
  },
  "toolTip": {
    "publicIpAddress": "",
    "domainNameLabel": ""
  },
  "defaultValue": {
    "publicIpAddressName": "ip01",
    "domainNameLabel": "mydomain"
  },
  "constraints": {
    "required": {
      "domainNameLabel": true
    }
  },
  "options": {
    "hideNone": false,
    "hideDomainNameLabel": false,
    "hideExisting": false,
    "zone": 3
  },
  "visible": true
}
```

## <a name="sample-output"></a>Resultado da amostra

Se o usuário selecionar nenhum endereço IP público, o controle retornará a seguinte saída:

```json
{
  "newOrExistingOrNone": "none"
}
```

Se o usuário selecionar um endereço IP novo ou existente, o controle retornará a seguinte saída:

```json
{
  "name": "ip01",
  "resourceGroup": "rg01",
  "domainNameLabel": "mydomain",
  "publicIPAllocationMethod": "Dynamic",
  "sku": "Basic",
  "newOrExistingOrNone": "new"
}
```

- Quando `options.hideNone` for especificado como **true**, `newOrExistingOrNone` terá apenas um valor de **novo** ou **existente**.
- Quando `options.hideDomainNameLabel` é especificado como **true**, `domainNameLabel` não é declarado.

## <a name="remarks"></a>Observações

- Se `constraints.required.domainNameLabel` for definido como **true**, o usuário deverá fornecer um rótulo de nome de domínio ao criar um novo endereço IP público. Os endereços IP públicos existentes sem um rótulo não estão disponíveis para seleção.
- Se `options.hideNone` for definido como **true**, a opção para selecionar **nenhum** para o endereço IP público ficará oculta. O valor predefinido é **false**.
- Se `options.hideDomainNameLabel` for definido como **true**, a caixa de texto para o rótulo de nome de domínio ficará oculta. O valor predefinido é **false**.
- Se `options.hideExisting` for true, o usuário não poderá escolher um endereço IP público existente. O valor predefinido é **false**.
- Para `zone`, somente os endereços IP públicos para os endereços IP públicos resilientes de zona ou região especificada estão disponíveis.

## <a name="next-steps"></a>Passos seguintes

* Para obter uma introdução à criação de definições de interface do usuário, consulte [introdução ao CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns nos elementos da interface do usuário, consulte [elementos CreateUiDefinition](create-uidefinition-elements.md).
