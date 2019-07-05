---
title: Adicionar recursos personalizados à API REST do Azure
description: Saiba como adicionar recursos personalizados para a API de REST do Azure. Este artigo irá guiá-os requisitos e melhores práticas para pontos de extremidade que desejarem implementar recursos personalizados.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: b94d59b55a62797e142768dc84ec499d714bd067
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/29/2019
ms.locfileid: "67479021"
---
# <a name="adding-custom-resources-to-azure-rest-api"></a>Adicionar recursos personalizados à API REST do Azure

Este artigo tratará de requisitos e melhores práticas para a criação de pontos finais de fornecedor de recursos personalizado do Azure que implementa os recursos personalizados. Se não estiver familiarizado com fornecedores de recursos personalizado do Azure, veja [a visão geral sobre provedores de recursos personalizados](./custom-providers-overview.md).

## <a name="how-to-define-a-resource-endpoint"></a>Como definir um ponto de final de recurso

Uma **ponto final** é um URL que aponta para um serviço, que implementa o contrato subjacente entre ele e o Azure. O ponto de extremidade é definido no fornecedor de recursos personalizado e pode ser qualquer URL acessível publicamente. O exemplo abaixo tem um **resourceType** chamado `myCustomResource` implementado pelo `endpointURL`.

Exemplo **ResourceProvider**:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResource",
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

## <a name="building-a-resource-endpoint"></a>Criação de um ponto de final de recurso

Uma **ponto final** que implementa um **resourceType** tem de processar o pedido e resposta para a nova API do Azure. Quando um fornecedor de recursos personalizado com uma **resourceType** é criada, irá gerar um novo conjunto de APIs no Azure. Neste caso, o **resourceType** irá gerar um novo recurso de Azure API para `PUT`, `GET`, e `DELETE` efetuar CRUD num único recurso que `GET` para recuperar todos os recursos existentes:

Manipular o único recurso (`PUT`, `GET`, e `DELETE`):

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource/{myCustomResourceName}
```

Obter todos os recursos (`GET`):

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource
```

Para obter recursos personalizados, provedores de recursos personalizados oferecem dois tipos de **routingTypes**: "`Proxy`"e"`Proxy, Cache`".

### <a name="proxy-routing-type"></a>tipo de encaminhamento de proxy

A "`Proxy`" **routingType** proxies solicitar todos os métodos para o **ponto de extremidade** especificado no fornecedor de recursos personalizado. Quando utilizar "`Proxy`":

- Controlo total sobre a resposta é necessário.
- Integração de sistemas com os recursos existentes.

Para saber mais sobre "`Proxy`" os recursos, consulte [a referência de proxy de recurso personalizado](./custom-providers-proxy-resource-endpoint-reference.md)

### <a name="proxy-cache-routing-type"></a>tipo de encaminhamento de cache de proxy

A "`Proxy, Cache`" **routingType** proxies apenas `PUT` e `DELETE` pedir métodos para o **endpoint** especificado no fornecedor de recursos personalizado. O fornecedor de recursos personalizado automaticamente irá devolver `GET` pedidos com base no que tem armazenados em seu cache. Se um recurso personalizado é marcado com a cache, o fornecedor de recursos personalizado também irá adicionar / substituir campos na resposta ao que o Azure de APIs fique em conformidade. Quando utilizar "`Proxy, Cache`":

- Criar um novo sistema que tem não existem recursos existentes.
- Trabalhar com o ecossistema do Azure existente.

Para saber mais sobre "`Proxy, Cache`" os recursos, consulte [a referência de cache do recurso personalizado](./custom-providers-proxy-cache-resource-endpoint-reference.md)

## <a name="creating-a-custom-resource"></a>Criar um recurso personalizado

Existem duas formas principais de criação de um recurso personalizado num provedor de recursos personalizado:

- CLI do Azure
- Modelos Azure Resource Manager

### <a name="azure-cli"></a>CLI do Azure

Crie um recurso personalizado:

```azurecli-interactive
az resource create --is-full-object \
                   --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName} \
                   --properties \
                    '{
                        "location": "eastus",
                        "properties": {
                            "myProperty1": "myPropertyValue1",
                            "myProperty2": {
                                "myProperty3": "myPropertyValue3"
                            }
                        }
                    }'
```

Parâmetro | Necessário | Descrição
---|---|---
is-full-object | *Sim* | Indica que o objeto de propriedades inclui outras opções, como a localização, marcas, sku e/ou plano.
id | *Sim* | O ID de recurso do recurso personalizado. Isso deve existir fora do **ResourceProvider**
properties | *Sim* | O corpo do pedido que será enviado para o **ponto final**.

Elimine um recurso personalizado do Azure:

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

Parâmetro | Necessário | Descrição
---|---|---
id | *Sim* | O ID de recurso do recurso personalizado. Isso deve existir fora dos **ResourceProvider**.

Obter um recurso personalizado do Azure:

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

Parâmetro | Necessário | Descrição
---|---|---
id | *Sim* | O ID de recurso do recurso personalizado. Isso deve existir fora do **ResourceProvider**

### <a name="azure-resource-manager-template"></a>Modelo do Azure Resource Manager

> [!NOTE]
> Recursos requerem que a resposta contém apropriadas `id`, `name`, e `type` da **ponto de extremidade**.

Modelos Azure Resource Manager requer que `id`, `name`, e `type` são devolvidos corretamente a partir do ponto final downstream. Uma resposta do recurso devolvida deve estar no formato:

Exemplo **ponto final** resposta:

``` JSON
{
  "properties": {
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3": "myPropertyValue3"
    }
  },
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{customResourceName}",
  "name": "{customResourceName}",
  "type": "Microsoft.CustomProviders/resourceProviders/{resourceTypeName}"
}
```

Exemplo modelo do Resource Manager do Azure:

```JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.CustomProviders/resourceProviders/{resourceTypeName}",
            "name": "{resourceProviderName}/{customResourceName}",
            "apiVersion": "2018-09-01-preview",
            "location": "eastus",
            "properties": {
                "myProperty1": "myPropertyValue1",
                "myProperty2": {
                    "myProperty3": "myPropertyValue3"
                }
            }
        }
    ]
}
```

Parâmetro | Necessário | Descrição
---|---|---
resourceTypeName | *Sim* | O **name** da **resourceType** definidas no fornecedor personalizado.
resourceProviderName | *Sim* | O nome de instância do fornecedor de recursos personalizado.
customResourceName | *Sim* | O nome de recurso personalizado.

## <a name="next-steps"></a>Passos Seguintes

- [Descrição geral sobre provedores de recurso personalizado do Azure](./custom-providers-overview.md)
- [Tutorial: Criar o fornecedor de recursos personalizado do Azure e implementar recursos personalizados](./create-custom-provider.md)
- [How To: Adicionar ações personalizadas a API REST do Azure](./custom-providers-action-endpoint-how-to.md)
- [Referência: Referência de Proxy de recurso personalizado](./custom-providers-proxy-resource-endpoint-reference.md)
- [Referência: Referência de Cache do recurso personalizado](./custom-providers-proxy-cache-resource-endpoint-reference.md)
