---
title: Referência de cache de recurso personalizado
description: Referência de cache de recursos personalizado para fornecedores de recursos personalizados Azure. Este artigo irá analisar os requisitos para os pontos finais que implementam recursos personalizados em cache.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: e1b8c44f020d18066423eed236018308fe88b607
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650386"
---
# <a name="custom-resource-cache-reference"></a>Referência de cache de recursos personalizados

Este artigo irá analisar os requisitos para os pontos finais que implementam recursos personalizados em cache. Se não está familiarizado com os Fornecedores de Recursos Personalizados Da Azure, consulte a visão geral sobre os fornecedores de [recursos personalizados.](overview.md)

## <a name="how-to-define-a-cache-resource-endpoint"></a>Como definir um ponto final de recurso de cache

Um recurso proxy pode ser criado especificando o **routtype** para "Proxy, Cache".

Provedor de recursos personalizados da amostra:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy, Cache",
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

## <a name="building-proxy-resource-endpoint"></a>Ponto final do recurso proxy de construção

Um **ponto final** que implemente um **ponto final** de recurso "Proxy, Cache" deve lidar com o pedido e resposta para a nova API em Azure. Neste caso, o **recursoType** gerará uma nova `PUT`API de recursos Azure para , `GET`e `DELETE` para executar CRUD num único recurso, bem como `GET` para recuperar todos os recursos existentes:

> [!NOTE]
> A API Azure gerará `PUT` `GET`os `DELETE`métodos de pedido, e, `PUT` `DELETE`mas o **ponto final** da cache só precisa de manusear e .
> Recomendamos que o **ponto** `GET`final também implemente.

### <a name="create-a-custom-resource"></a>Criar um recurso personalizado

Pedido de entrada da API Azure:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
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

Da mesma forma, a resposta do **ponto final** é então reencaminhada para o cliente. A resposta do ponto final deve voltar:

- Um documento válido de objeto JSON. Todas as matrizes e cordas devem ser aninhadas sob um objeto superior.
- O `Content-Type` cabeçalho deve ser definido para "aplicação/json; charset=utf-8".
- O fornecedor de recursos `name`personalizados irá substituir os , `type`e `id` campos para o pedido.
- O fornecedor de recursos personalizados `properties` só devolverá campos sob o objeto para um ponto final de cache.

**Ponto final** Resposta:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

Os `name` `id`campos `type` serão automaticamente gerados para o recurso personalizado pelo fornecedor de recursos personalizados.

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

### <a name="remove-a-custom-resource"></a>Remova um recurso personalizado

Pedido de entrada da API Azure:

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

Da mesma forma, a resposta do **ponto final** é então reencaminhada para o cliente. A resposta do ponto final deve voltar:

- Um documento válido de objeto JSON. Todas as matrizes e cordas devem ser aninhadas sob um objeto superior.
- O `Content-Type` cabeçalho deve ser definido para "aplicação/json; charset=utf-8".
- O Fornecedor de Recursos Personalizados Azure só removerá o artigo da sua cache se uma resposta de 200 níveis for devolvida. Mesmo que o recurso não exista, o **ponto final** deve devolver 204.

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

Pedido de entrada da API Azure:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

O pedido **não** será reencaminhado para o **ponto final.**

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

Pedido de entrada da API Azure:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Este pedido **não** será reencaminhado para o **ponto final.**

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
- [Quickstart: Criar o Fornecedor de Recursos Personalizados Azure e implementar recursos personalizados](./create-custom-provider.md)
- [Tutorial: Criar ações e recursos personalizados em Azure](./tutorial-get-started-with-custom-providers.md)
- [Como: Adicionar ações personalizadas à API Do REST Azure](./custom-providers-action-endpoint-how-to.md)
- [Referência: Referência de procuração de recursos personalizados](proxy-resource-endpoint-reference.md)
