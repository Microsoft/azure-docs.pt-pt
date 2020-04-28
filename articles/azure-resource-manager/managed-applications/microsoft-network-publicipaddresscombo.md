---
title: Elemento UI PublicIpAddressCombo
description: Descreve o elemento Microsoft.Network.PublicIpAddressCombo UI para o portal Azure.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 0393673663df8f3ca580ff34e16bee910b955f8d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75651920"
---
# <a name="microsoftnetworkpublicipaddresscombo-ui-element"></a>Microsoft.Network.PublicIpAddressCombo UI

Um grupo de controlos para selecionar um novo ou existente endereço IP público.

## <a name="ui-sample"></a>Amostra de UI

![Microsoft.Network.PublicIpAddressCombo](./media/managed-application-elements/microsoft.network.publicipaddresscombo.png)

- Se o utilizador selecionar "Nenhum" para endereço IP público, a caixa de texto de etiqueta de nome de domínio está escondida.
- Se o utilizador selecionar um endereço IP público existente, a caixa de texto de nome de domínio é desativada. O seu valor é o rótulo de nome de domínio do endereço IP selecionado.
- O sufixo de nome de domínio (por exemplo, westus.cloudapp.azure.com) atualiza automaticamente com base na localização selecionada.

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

Se o utilizador não selecionar nenhum endereço IP público, o controlo devolve a seguinte saída:

```json
{
  "newOrExistingOrNone": "none"
}
```

Se o utilizador selecionar um endereço IP novo ou existente, o controlo devolve a seguinte saída:

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

- Quando `options.hideNone` for especificado `newOrExistingOrNone` como **verdadeiro,** só terá um valor **novo** ou **existente.**
- Quando `options.hideDomainNameLabel` é especificado `domainNameLabel` como **verdadeiro,** não é declarado.

## <a name="remarks"></a>Observações

- Se `constraints.required.domainNameLabel` for definido como **verdadeiro,** o utilizador deve fornecer uma etiqueta de nome de domínio ao criar um novo endereço IP público. Os endereços IP públicos existentes sem etiqueta não estão disponíveis para seleção.
- Se `options.hideNone` for definido como **verdadeiro,** então a opção de selecionar **Nenhuma** para o endereço IP público está escondida. O valor predefinido é **falso**.
- Se `options.hideDomainNameLabel` for definido como **verdadeiro,** a caixa de texto para a etiqueta de nome de domínio está escondida. O valor predefinido é **falso**.
- Se `options.hideExisting` for verdade, então o utilizador não é capaz de escolher um endereço IP público existente. O valor predefinido é **falso**.
- Para `zone`, apenas estão disponíveis endereços IP públicos para a zona ou endereços IP públicos resilientes da zona especificada.

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à criação de definições de UI, consulte [Começar com CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns nos elementos UI, consulte [elementos CreateUiDefinition](create-uidefinition-elements.md).
