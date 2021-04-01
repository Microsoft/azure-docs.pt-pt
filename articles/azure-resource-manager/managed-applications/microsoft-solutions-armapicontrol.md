---
title: Elemento de UI ArmApiControl
description: Descreve o elemento Microsoft.Solutions.ArmApiControl UI para o portal Azure. Usado para chamar operações da API.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: tomfitz
ms.openlocfilehash: bbe36e072d10b81c421331b2212d8b161afd2693
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87098154"
---
# <a name="microsoftcommonarmapicontrol-ui-element"></a>Microsoft.Common.ArmApiControl UI elemento

O ArmApiControl permite-lhe obter resultados de uma operação API do Gestor de Recursos Azure. Utilize os resultados para preencher o conteúdo dinâmico noutros controlos.

## <a name="ui-sample"></a>Amostra de UI

Não há UI para este controlo.

## <a name="schema"></a>Esquema

O exemplo a seguir mostra o esquema para este controlo:

```json
{
    "name": "testApi",
    "type": "Microsoft.Solutions.ArmApiControl",
    "request": {
        "method": "{HTTP-method}",
        "path": "{path-for-the-URL}",
        "body": {
            "key1": "val1",
            "key2": "val2"
        }
    }
}
```

## <a name="sample-output"></a>Saída de exemplo

A saída do controlo não é apresentada ao utilizador. Em vez disso, o resultado da operação é utilizado noutros controlos.

## <a name="remarks"></a>Observações

- A `request.method` propriedade especifica o método HTTP. Apenas `GET` ou `POST` são permitidos.
- A `request.path` propriedade especifica o caminho relativo do URL. Pode ser um caminho estático ou pode ser construído dinamicamente referindo valores de saída dos outros controlos.
- A `request.body` propriedade é opcional. Utilize-o para especificar um corpo JSON que é enviado com o pedido. O corpo pode ser conteúdo estático ou construído dinamicamente, referindo-se aos valores de saída de outros controlos.

## <a name="example"></a>Exemplo

No exemplo seguinte, o `providersApi` elemento chama uma API para obter uma série de objetos de provedor.

A `allowedValues` propriedade do elemento está `providersDropDown` configurada para obter os nomes dos fornecedores. Mostra-os na lista de abandono.

```json
{
    "name": "providersApi",
    "type": "Microsoft.Solutions.ArmApiControl",
    "request": {
        "method": "GET",
        "path": "[concat(subscription().id, '/providers/Microsoft.Network/expressRouteServiceProviders?api-version=2019-02-01')]"
    }
},
{
    "name": "providerDropDown",
    "type": "Microsoft.Common.DropDown",
    "label": "Provider",
    "toolTip": "The provider that offers the express route connection.",
    "constraints": {
        "allowedValues": "[map(steps('settings').providersApi.value, (item) => parse(concat('{\"label\":\"', item.name, '\",\"value\":\"', item.name, '\"}')))]",
        "required": true
    },
    "visible": true
}
```

Para um exemplo de utilização do ArmApiControl para verificar a disponibilidade de um nome de recurso, consulte [Microsoft.Common.TextBox](microsoft-common-textbox.md).

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à criação de definições de UI, consulte [Começar com CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de UI, consulte [os elementos CreateUiDefinition](create-uidefinition-elements.md).
