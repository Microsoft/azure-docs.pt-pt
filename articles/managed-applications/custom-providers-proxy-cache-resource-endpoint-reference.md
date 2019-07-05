---
title: Referência de cache de recurso personalizado
description: Referência de cache de recurso personalizado para fornecedores de recursos do Azure personalizadas. Este artigo irá percorrer os requisitos para implementar recursos personalizados da cache de pontos de extremidade.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 6a3d570d9695516a293b601b3d34c2bcba6b058d
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478891"
---
# <a name="custom-resource-cache-reference"></a>Referência de Cache do recurso personalizado

Este artigo irá percorrer os requisitos para implementar recursos personalizados da cache de pontos de extremidade. Se não estiver familiarizado com fornecedores de recursos personalizado do Azure, veja [a visão geral sobre provedores de recursos personalizados](./custom-providers-overview.md).

## <a name="how-to-define-a-cache-resource-endpoint"></a>Como definir um ponto de final de recurso de cache

Um recurso de proxy pode ser criado, especificando o **routingType** a "Proxy Cache".

Exemplo de provedor de recurso personalizado:

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

## <a name="building-proxy-resource-endpoint"></a>Ponto final de recurso do proxy de criação

Uma **ponto final** que implementa um recurso de "Proxy, Cache" **endpoint** tem de processar o pedido e resposta para a nova API do Azure. Neste caso, o **resourceType** irá gerar um novo recurso de Azure API para `PUT`, `GET`, e `DELETE` efetuar CRUD num único recurso que `GET` para recuperar todos os recursos existentes:

> [!NOTE]
> A API do Azure irá gerar os métodos de pedido `PUT`, `GET`, e `DELETE`, mas a cache **ponto final** apenas precisa lidar com `PUT` e `DELETE`.
> Recomendamos que o **ponto final** também implementa `GET`.

### <a name="create-a-custom-resource"></a>Criar um recurso personalizado

Pedido de entrada de API do Azure:

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

Este pedido, em seguida, será reencaminhado para o **ponto final** sob a forma:

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

Da mesma forma, a resposta dos **ponto final** , em seguida, é reencaminhado para o customer. Deverá devolver a resposta do ponto final:

- Um documento de objeto JSON válido. Todas as matrizes e seqüências de caracteres devem ser aninhadas num objeto superior.
- O `Content-Type` cabeçalho deve ser definido como "application/json; charset = utf-8 ".
- O fornecedor de recursos personalizado irá substituir a `name`, `type`, e `id` campos para o pedido.
- O fornecedor de recursos personalizados só irá devolver campos sob o `properties` objeto para um ponto final da cache.

**Ponto final** resposta:

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

O `name`, `id`, e `type` campos serão automaticamente gerados para o recurso personalizado pelo fornecedor de recursos personalizado.

Resposta do fornecedor de recursos personalizado do Azure:

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

Pedido de entrada de API do Azure:

``` HTTP
Delete https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Este pedido, em seguida, será reencaminhado para o **ponto final** sob a forma:

``` HTTP
Delete https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

Da mesma forma, a resposta dos **ponto final** , em seguida, é reencaminhado para o customer. Deverá devolver a resposta do ponto final:

- Um documento de objeto JSON válido. Todas as matrizes e seqüências de caracteres devem ser aninhadas num objeto superior.
- O `Content-Type` cabeçalho deve ser definido como "application/json; charset = utf-8 ".
- O fornecedor de recursos do Azure personalizado apenas removerá o item de seu cache se uma resposta de nível 200 é retornada. Mesmo que o recurso não existir, o **ponto final** deverá devolver 204.

**Ponto final** resposta:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

Resposta do fornecedor de recursos personalizado do Azure:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

### <a name="retrieve-a-custom-resource"></a>Obter um recurso personalizado

Pedido de entrada de API do Azure:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

O pedido será **não** reencaminhados para o **endpoint**.

Resposta do fornecedor de recursos personalizado do Azure:

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

Pedido de entrada de API do Azure:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Este pedido será **não** reencaminhados para o **endpoint**.

Resposta do fornecedor de recursos personalizado do Azure:

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

## <a name="next-steps"></a>Passos Seguintes

- [Descrição geral sobre provedores de recurso personalizado do Azure](./custom-providers-overview.md)
- [Tutorial: Criar o fornecedor de recursos personalizado do Azure e implementar recursos personalizados](./create-custom-provider.md)
- [How To: Adicionar ações personalizadas a API REST do Azure](./custom-providers-action-endpoint-how-to.md)
- [Referência: Referência de Proxy de recurso personalizado](./custom-providers-proxy-resource-endpoint-reference.md)
