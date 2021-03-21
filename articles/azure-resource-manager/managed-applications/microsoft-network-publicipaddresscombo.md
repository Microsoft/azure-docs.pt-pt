---
title: Elemento de UI PublicIpAddressCombo
description: Descreve o elemento UI microsoft.Network.PublicIpAddressCombo para o portal Azure.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 5def6db9d551b3882204c9f997f164a0df7ac223
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87063302"
---
# <a name="microsoftnetworkpublicipaddresscombo-ui-element"></a>Microsoft.Network.PublicIpAddressCombo UI elemento

Um grupo de controlos para selecionar um endereço IP público novo ou existente.

## <a name="ui-sample"></a>Amostra de UI

![Microsoft.Network.PublicIpAddressCombo](./media/managed-application-elements/microsoft-network-publicipaddresscombo.png)

- Se o utilizador selecionar 'Nenhum' para endereço IP público, a caixa de texto do rótulo de domínio está ocultada.
- Se o utilizador selecionar um endereço IP público existente, a caixa de texto do rótulo de domínio está desativada. O seu valor é o rótulo de nome de domínio do endereço IP selecionado.
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

## <a name="sample-output"></a>Saída de exemplo

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

- Quando `options.hideNone` for especificado como **verdadeiro,** `newOrExistingOrNone` só terá um valor **novo** ou **existente.**
- Quando `options.hideDomainNameLabel` especificado como **verdadeiro,** `domainNameLabel` não é declarado.

## <a name="remarks"></a>Observações

- Se `constraints.required.domainNameLabel` estiver definido como **verdadeiro,** o utilizador deve fornecer uma etiqueta de nome de domínio ao criar um novo endereço IP público. Os endereços IP públicos existentes sem etiqueta não estão disponíveis para seleção.
- Se `options.hideNone` estiver definido como **verdadeiro,** então a opção de selecionar **Nenhum** para o endereço IP público está ocultada. O valor predefinido é **falso**.
- Se `options.hideDomainNameLabel` estiver definido como **verdadeiro,** então a caixa de texto para a etiqueta do nome de domínio está escondida. O valor predefinido é **falso**.
- Se `options.hideExisting` for verdade, então o utilizador não é capaz de escolher um endereço IP público existente. O valor predefinido é **falso**.
- Para `zone` , apenas endereços IP públicos para a zona especificada ou zona resiliente endereços IP públicos estão disponíveis.

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à criação de definições de UI, consulte [Começar com CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de UI, consulte [os elementos CreateUiDefinition](create-uidefinition-elements.md).
