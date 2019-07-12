---
title: Referência de proxy de recurso personalizado
description: Referência de proxy de recurso personalizado para fornecedores de recursos do Azure personalizadas. Este artigo irá percorrer os requisitos para implementar recursos personalizados do proxy de pontos de extremidade.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: fe470165d11b24ffb1df704ecaa9804663fc9e5c
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67796038"
---
# <a name="custom-resource-proxy-reference"></a>Referência de Proxy de recurso personalizado

Este artigo irá percorrer os requisitos para implementar recursos personalizados do proxy de pontos de extremidade. Se não estiver familiarizado com fornecedores de recursos personalizado do Azure, veja [a visão geral sobre provedores de recursos personalizados](./custom-providers-overview.md).

## <a name="how-to-define-a-proxy-resource-endpoint"></a>Como definir um ponto de final de recurso de proxy

Um recurso de proxy pode ser criado, especificando o **routingType** para "Proxy".

Exemplo de provedor de recurso personalizado:

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

## <a name="building-proxy-resource-endpoint"></a>Ponto final de recurso do proxy de criação

Uma **ponto final** que implementa um recurso de "Proxy" **endpoint** tem de processar o pedido e resposta para a nova API do Azure. Neste caso, o **resourceType** irá gerar um novo recurso de Azure API para `PUT`, `GET`, e `DELETE` efetuar CRUD num único recurso que `GET` para recuperar todos os recursos existentes.

> [!NOTE]
> O `id`, `name`, e `type` campos não são necessários, mas são necessários para integrar o recurso personalizado com o ecossistema do Azure existente.

Recurso de exemplo:

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
type | 'Microsoft.CustomProviders/resourceProviders/{resourceTypeName}' | O espaço de nomes do tipo de recurso.
id | '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{myCustomResourceName}' | O ID de recurso.

### <a name="create-a-custom-resource"></a>Criar um recurso personalizado

Pedido de entrada de API do Azure:

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

**Ponto final** resposta:

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

- Documento de objeto JSON válido. Todas as matrizes e seqüências de caracteres devem ser aninhadas num objeto superior.
- O `Content-Type` cabeçalho deve ser definido como "application/json; charset = utf-8 ".

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

Este pedido, em seguida, será reencaminhado para o **ponto final** sob a forma:

``` HTTP
GET https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

Da mesma forma, a resposta dos **ponto final** , em seguida, é reencaminhado para o customer. Deverá devolver a resposta do ponto final:

- Um documento de objeto JSON válido. Todas as matrizes e seqüências de caracteres devem ser aninhadas num objeto superior.
- O `Content-Type` cabeçalho deve ser definido como "application/json; charset = utf-8 ".

**Ponto final** resposta:

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

Este pedido, em seguida, será reencaminhado para o **ponto final** sob a forma:

``` HTTP
GET https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources
```

Da mesma forma, a resposta dos **ponto final** , em seguida, é reencaminhado para o customer. Deverá devolver a resposta do ponto final:

- Um documento de objeto JSON válido. Todas as matrizes e seqüências de caracteres devem ser aninhadas num objeto superior.
- O `Content-Type` cabeçalho deve ser definido como "application/json; charset = utf-8 ".
- A lista de recursos deve ser colocada sob o nível superior `value` propriedade.

**Ponto final** resposta:

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
- [Quickstart: Criar o fornecedor de recursos personalizado do Azure e implementar recursos personalizados](./create-custom-provider.md)
- [Tutorial: Criar ações personalizadas e os recursos no Azure](./tutorial-custom-providers-101.md)
- [How To: Adicionar ações personalizadas a API REST do Azure](./custom-providers-action-endpoint-how-to.md)
- [Referência: Referência de Cache do recurso personalizado](./custom-providers-proxy-cache-resource-endpoint-reference.md)
