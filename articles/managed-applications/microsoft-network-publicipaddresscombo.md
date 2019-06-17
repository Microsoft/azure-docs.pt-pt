---
title: Elemento de interface do Usuário de PublicIpAddressCombo do Azure | Documentos da Microsoft
description: Descreve o elemento de interface do Usuário de Microsoft.Network.PublicIpAddressCombo para o portal do Azure.
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
ms.openlocfilehash: c3e8c99f6648f0f4927140f3215978566afb9eb8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60251102"
---
# <a name="microsoftnetworkpublicipaddresscombo-ui-element"></a>Microsoft.Network.PublicIpAddressCombo UI element
Um grupo de controles para a seleção de um endereço IP público novo ou existente.

## <a name="ui-sample"></a>Exemplo de interface do Usuário
![Microsoft.Network.PublicIpAddressCombo](./media/managed-application-elements/microsoft.network.publicipaddresscombo.png)

- Se o utilizador selecionar 'None' para o endereço IP público, a caixa de texto de etiqueta de nome de domínio está oculta.
- Se o usuário seleciona um endereço IP público existente, a caixa de texto de etiqueta de nome de domínio está desativada. Seu valor é a etiqueta de nome de domínio do endereço IP selecionado.
- As atualizações de sufixo (por exemplo, westus.cloudapp.azure.com) de nome domínio automaticamente com base na localização selecionada.

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

## <a name="remarks"></a>Observações
- Se `constraints.required.domainNameLabel` está definido como **true**, o usuário deve fornecer uma etiqueta de nome de domínio, ao criar um novo endereço IP público. Sem uma etiqueta não estão disponíveis para seleção de endereços IP público existente.
- Se `options.hideNone` está definido como **verdadeiro**, em seguida, a opção de selecionar **nenhum** para o IP público está oculta o endereço. O valor predefinido é **false**.
- Se `options.hideDomainNameLabel` está definido como **true**, em seguida, a caixa de texto de etiqueta de nome de domínio está oculto. O valor predefinido é **false**.
- Se `options.hideExisting` seja verdadeira, o usuário não pode escolher um endereço IP público existente. O valor predefinido é **false**.
- Para `zone`, endereços de IP público único para a zona especificada ou zona resilientes os endereços IP públicos estão disponíveis.

## <a name="sample-output"></a>Resultado da amostra
Se o utilizador não seleciona nenhum endereço IP público, o controle retorna o resultado seguinte:

```json
{
  "newOrExistingOrNone": "none"
}
```

Se o usuário seleciona um endereço IP novo ou existente, o controle retorna o resultado seguinte:

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

- Quando `options.hideNone` é especificado como **verdadeira**, `newOrExistingOrNone` só terá um valor de **novo** ou **existente**.
- Quando `options.hideDomainNameLabel` é especificado como **verdadeira**, `domainNameLabel` é não declarado.

## <a name="next-steps"></a>Passos Seguintes
* Para obter uma introdução à criação de definições de interface do Usuário, consulte [introdução ao CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de interface do Usuário, consulte [CreateUiDefinition elementos](create-uidefinition-elements.md).
