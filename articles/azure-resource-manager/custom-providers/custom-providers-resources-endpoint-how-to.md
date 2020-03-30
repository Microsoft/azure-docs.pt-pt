---
title: Adicionar recursos personalizados à API Do REST Azure
description: Saiba como adicionar recursos personalizados à API Azure REST. Este artigo irá percorrer os requisitos e as melhores práticas para pontos finais que desejem implementar recursos personalizados.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: b6c5f5b8e437ad2dc2e8a3be3f3f2ed03a613b44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650529"
---
# <a name="adding-custom-resources-to-azure-rest-api"></a>Adicionar recursos personalizados à API do REST Azure

Este artigo irá analisar os requisitos e as melhores práticas para criar pontos finais do Azure Custom Resource Provider que implementa recursos personalizados. Se não está familiarizado com os Fornecedores de Recursos Personalizados Da Azure, consulte a visão geral sobre os fornecedores de [recursos personalizados.](overview.md)

## <a name="how-to-define-a-resource-endpoint"></a>Como definir um ponto final de recurso

Um **ponto final** é um URL que aponta para um serviço, que implementa o contrato subjacente entre ele e o Azure. O ponto final é definido no fornecedor de recursos personalizados e pode ser qualquer URL acessível ao público. A amostra abaixo tem `myCustomResource` um **recursoType** chamado implementado por `endpointURL`.

**Provedor de Recursos**da Amostra:

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

## <a name="building-a-resource-endpoint"></a>Construção de um ponto final de recurso

Um **ponto final** que implemente um **recursoType** deve lidar com o pedido e resposta para a nova API em Azure. Quando um fornecedor de recursos personalizados com um **recursoType** é criado, irá gerar um novo conjunto de APIs em Azure. Neste caso, o **resourceType** gerará uma nova `PUT`API de recursos Azure para, `GET` `DELETE` e `GET` para executar CRUD num único recurso, bem como para recuperar todos os recursos existentes:

Manipular um`PUT`recurso `GET`único `DELETE`( e ):

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource/{myCustomResourceName}
```

Recuperar todos`GET`os recursos ():

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource
```

Para recursos personalizados, os fornecedores de recursos`Proxy`personalizados`Proxy, Cache`oferecem dois tipos de tipos de tipos de **encaminhamento**: " e " ".

### <a name="proxy-routing-type"></a>tipo de encaminhamento proxy

O`Proxy`" **routingType** proxies todos os métodos de pedido para o **ponto final** especificado no fornecedor de recursos personalizados. Quando usar`Proxy`" ":

- É necessário controlo total sobre a resposta.
- Integração de sistemas com recursos existentes.

Para saber mais`Proxy`sobre " recursos ", consulte [a referência de procuração](proxy-resource-endpoint-reference.md) de recursos personalizados

### <a name="proxy-cache-routing-type"></a>tipo de encaminhamento de cache proxy

Os`Proxy, Cache`proxies " **routtype** `PUT` `DELETE` apenas e solicitar métodos para o **ponto final** especificado no fornecedor de recursos personalizados. O fornecedor de recursos `GET` personalizados devolverá automaticamente pedidos com base no que armazenou na sua cache. Se um recurso personalizado for marcado com cache, o fornecedor de recursos personalizados também adicionará/substituirá campos na resposta para tornar o APIs Azure conforme. Quando usar`Proxy, Cache`" ":

- Criar um novo sistema que não tenha recursos existentes.
- Trabalhe com o ecossistema Azure existente.

Para saber mais`Proxy, Cache`sobre " recursos ", consulte [a referência de cache de recursos personalizados](proxy-cache-resource-endpoint-reference.md)

## <a name="creating-a-custom-resource"></a>Criar um recurso personalizado

Existem duas formas principais de criar um recurso personalizado a partir de um fornecedor de recursos personalizados:

- CLI do Azure
- Modelos de gestor de recursos azure

### <a name="azure-cli"></a>CLI do Azure

Criar um recurso personalizado:

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
é objeto cheio | *Sim,* | Indica que o objeto de propriedades inclui outras opções, tais como localização, tags, sku e/ou plano.
ID | *Sim,* | A identificação de recursos do recurso personalizado. Isto deve existir fora do **ResourceProvider**
propriedades | *Sim,* | O órgão de pedido que será enviado para o **ponto final.**

Eliminar um recurso personalizado Azure:

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

Parâmetro | Necessário | Descrição
---|---|---
ID | *Sim,* | A identificação de recursos do recurso personalizado. Isto deve existir fora do **ResourceProvider**.

Recupere um recurso personalizado Azure:

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

Parâmetro | Necessário | Descrição
---|---|---
ID | *Sim,* | A identificação de recursos do recurso personalizado. Isto deve existir fora do **ResourceProvider**

### <a name="azure-resource-manager-template"></a>Modelo do Azure Resource Manager

> [!NOTE]
> Os recursos exigem que `id` `name`a `type` resposta contenha um adequado , e do **ponto final**.

Os modelos de gestor `id` `name`de `type` recursos Azure requerem isso, e são devolvidos corretamente a partir do ponto final a jusante. Uma resposta de recursos devolvido deve estar na forma:

Resposta **final** da amostra:

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

Modelo de gestor de recursos azure da amostra:

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
recursoTypeName | *Sim,* | O **nome** do **recursoType** definido no fornecedor personalizado.
nome fornecedor de recursos | *Sim,* | O nome da instância do fornecedor de recursos personalizados.
nome personalizado DeRecursos | *Sim,* | O nome de recurso personalizado.

## <a name="next-steps"></a>Passos seguintes

- [Visão geral sobre fornecedores de recursos personalizados Azure](overview.md)
- [Quickstart: Criar o Fornecedor de Recursos Personalizados Azure e implementar recursos personalizados](./create-custom-provider.md)
- [Tutorial: Criar ações e recursos personalizados em Azure](./tutorial-get-started-with-custom-providers.md)
- [Como: Adicionar ações personalizadas à API Do REST Azure](./custom-providers-action-endpoint-how-to.md)
- [Referência: Referência de procuração de recursos personalizados](proxy-resource-endpoint-reference.md)
- [Referência: Referência de cache de recursos personalizados](proxy-cache-resource-endpoint-reference.md)
