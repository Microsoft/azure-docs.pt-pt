---
title: Referência de proxy de recurso personalizado
description: Referência de procuração de recursos personalizados para fornecedores de recursos personalizados Azure. Este artigo irá analisar os requisitos para os pontos finais que implementam recursos personalizados de procuração.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 46b38686b39836f3d4bfb80686d514f932a79bf3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "75650464"
---
# <a name="custom-resource-proxy-reference"></a>Referência de procuração de recursos personalizados

Este artigo irá analisar os requisitos para os pontos finais que implementam recursos personalizados de procuração. Se não estiver familiarizado com os Fornecedores de Recursos Personalizados Azure, consulte [a visão geral dos fornecedores de recursos personalizados.](overview.md)

## <a name="how-to-define-a-proxy-resource-endpoint"></a>Como definir um ponto final de recursos de procuração

Um recurso proxy pode ser criado especificando o **RoteamentoType** para "Proxy".

Fornecedor de recursos personalizados da amostra:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
  "name": "{resourceProviderName}"
}
```

## <a name="building-proxy-resource-endpoint"></a>Ponto final de recursos de procuração de edifício

Um **ponto final** que implemente um ponto **final** de recursos "Proxy" deve lidar com o pedido e resposta para a nova API em Azure. Neste caso, o **recursoType** gerará uma nova API de recurso Azure `PUT` para , e para executar `GET` `DELETE` CRUD em um único recurso, bem como `GET` para recuperar todos os recursos existentes.

> [!NOTE]
> Os `id` `name` campos e campos não `type` são necessários, mas são necessários para integrar o recurso personalizado com o ecossistema Azure existente.

Recurso de amostra:

``` JSON
{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

Referência de parâmetro:

Propriedade | Sample | Descrição
---|---|---
name | '{myCustomResourceName}' | O nome do recurso personalizado.
tipo | 'Microsoft.CustomProviders/resourceProviders/{resourceTypeName}' | O espaço de nome tipo de recurso.
ID | '/subscrições/{subscriçãoId}/resourceGroups/{resourceGroupName}/<br>fornecedores/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{myCustomResourceName}' | A identificação de recursos.

### <a name="create-a-custom-resource"></a>Criar um recurso personalizado

Pedido de entrada da Azure API:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resource-provider-name}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

Este pedido será então encaminhado para o **ponto final** sob a forma:

``` HTTP
PUT https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

Da mesma forma, a resposta do **ponto final** é então reencaminhada para o cliente. A resposta do ponto final deve regressar:

- Um documento válido de objeto JSON. Todas as matrizes e cordas devem ser aninhadas sob um objeto superior.
- O `Content-Type` cabeçalho deve ser definido como "aplicação/json; charset=utf-8".

**Ponto final** Resposta:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

Resposta do fornecedor de recursos personalizados Azure:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

### <a name="remove-a-custom-resource"></a>Remover um recurso personalizado

Pedido de entrada da Azure API:

``` HTTP
Delete https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Este pedido será então encaminhado para o **ponto final** sob a forma:

``` HTTP
Delete https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

Da mesma forma, a resposta do **ponto final** é então reencaminhada para o cliente. A resposta do ponto final deve regressar:

- Documento válido do objeto JSON. Todas as matrizes e cordas devem ser aninhadas sob um objeto superior.
- O `Content-Type` cabeçalho deve ser definido como "aplicação/json; charset=utf-8".

**Ponto final** Resposta:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

Resposta do fornecedor de recursos personalizados Azure:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

### <a name="retrieve-a-custom-resource"></a>Recuperar um recurso personalizado

Pedido de entrada da Azure API:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Este pedido será então encaminhado para o **ponto final** sob a forma:

``` HTTP
GET https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

Da mesma forma, a resposta do **ponto final** é então reencaminhada para o cliente. A resposta do ponto final deve regressar:

- Um documento válido de objeto JSON. Todas as matrizes e cordas devem ser aninhadas sob um objeto superior.
- O `Content-Type` cabeçalho deve ser definido como "aplicação/json; charset=utf-8".

**Ponto final** Resposta:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

Resposta do fornecedor de recursos personalizados Azure:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

### <a name="enumerate-all-custom-resources"></a>Enumerar todos os recursos personalizados

Pedido de entrada da Azure API:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Este pedido será então encaminhado para o **ponto final** sob a forma:

``` HTTP
GET https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources
```

Da mesma forma, a resposta do **ponto final** é então reencaminhada para o cliente. A resposta do ponto final deve regressar:

- Um documento válido de objeto JSON. Todas as matrizes e cordas devem ser aninhadas sob um objeto superior.
- O `Content-Type` cabeçalho deve ser definido como "aplicação/json; charset=utf-8".
- A lista de recursos deve ser colocada sob a propriedade de alto `value` nível.

**Ponto final** Resposta:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "value" : [
        {
            "name": "{myCustomResourceName}",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
            "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
            "properties": {
                "myProperty1": "myPropertyValue1",
                "myProperty2": {
                    "myProperty3" : "myPropertyValue3"
                }
            }
        }
    ]
}
```

Resposta do fornecedor de recursos personalizados Azure:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "value" : [
        {
            "name": "{myCustomResourceName}",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
            "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
            "properties": {
                "myProperty1": "myPropertyValue1",
                "myProperty2": {
                    "myProperty3" : "myPropertyValue3"
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Passos seguintes

- [Visão geral sobre fornecedores de recursos personalizados Azure](overview.md)
- [Quickstart: Criar Fornecedor de Recursos Personalizados Azure e implementar recursos personalizados](./create-custom-provider.md)
- [Tutorial: Criar ações e recursos personalizados em Azure](./tutorial-get-started-with-custom-providers.md)
- [Como: Adicionar ações personalizadas à Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [Referência: Referência de cache de recursos personalizados](proxy-cache-resource-endpoint-reference.md)
