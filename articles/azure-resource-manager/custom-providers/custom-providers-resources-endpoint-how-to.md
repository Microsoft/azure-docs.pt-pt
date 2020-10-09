---
title: Adicionar recursos personalizados à Azure REST API
description: Saiba como adicionar recursos personalizados à AZure REST API. Este artigo percorrerá os requisitos e as melhores práticas para os pontos finais que desejam implementar recursos personalizados.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: b6c5f5b8e437ad2dc2e8a3be3f3f2ed03a613b44
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75650529"
---
# <a name="adding-custom-resources-to-azure-rest-api"></a>Adicionar recursos personalizados à Azure REST API

Este artigo irá analisar os requisitos e as melhores práticas para a criação de pontos finais do Azure Custom Resource Provider que implemente recursos personalizados. Se não estiver familiarizado com os Fornecedores de Recursos Personalizados Azure, consulte [a visão geral dos fornecedores de recursos personalizados.](overview.md)

## <a name="how-to-define-a-resource-endpoint"></a>Como definir um ponto final de recurso

Um **ponto final** é um URL que aponta para um serviço, que implementa o contrato subjacente entre ele e a Azure. O ponto final é definido no fornecedor de recursos personalizados e pode ser qualquer URL acessível ao público. A amostra abaixo tem um **recurso chamadoType** implementado `myCustomResource` por `endpointURL` .

Fonte **de recursos da amostra:**

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

## <a name="building-a-resource-endpoint"></a>Construção de um ponto final de recursos

Um **ponto final** que implemente um recurso **OType** deve lidar com o pedido e resposta para a nova API em Azure. Quando um fornecedor de recursos personalizado com um **recursoType** é criado, irá gerar um novo conjunto de APIs em Azure. Neste caso, o **recursoType** gerará uma nova API de recurso Azure `PUT` `GET` para, e `DELETE` executar a CRUD num único recurso, bem como `GET` para recuperar todos os recursos existentes:

Manipular o Único Recurso `PUT` (, `GET` , `DELETE` e):

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource/{myCustomResourceName}
```

Recuperar todos os recursos `GET` (

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource
```

Para recursos personalizados, os fornecedores de recursos personalizados oferecem dois tipos de **roteamentoTypes**: `Proxy` " " " e " `Proxy, Cache`

### <a name="proxy-routing-type"></a>tipo de encaminhamento proxy

O `Proxy` " " **encaminhamentoType** proxies todos os métodos de pedido para o **ponto final** especificado no fornecedor de recursos personalizados. Quando usar `Proxy` " ":

- É necessário um controlo total sobre a resposta.
- Integração de sistemas com recursos existentes.

Para saber mais sobre " `Proxy` recursos", consulte [a referência proxy de recursos personalizados](proxy-resource-endpoint-reference.md)

### <a name="proxy-cache-routing-type"></a>tipo de encaminhamento de cache proxy

O `Proxy, Cache` " " **" encaminhamentoType** proxies apenas `PUT` e solicitar `DELETE` métodos para o ponto **final** especificado no fornecedor de recursos personalizados. O fornecedor de recursos personalizados devolverá automaticamente `GET` os pedidos com base no que guardou na sua cache. Se um recurso personalizado for marcado com cache, o fornecedor de recursos personalizados também adicionará/substituirá campos na resposta para tornar as APIs Azure compatíveis. Quando usar `Proxy, Cache` " ":

- Criar um novo sistema que não tenha recursos existentes.
- Trabalhe com o ecossistema Azure existente.

Para saber mais sobre " `Proxy, Cache` recursos", consulte [a referência de cache de recursos personalizados](proxy-cache-resource-endpoint-reference.md)

## <a name="creating-a-custom-resource"></a>Criar um recurso personalizado

Existem duas formas principais de criar um recurso personalizado a partir de um fornecedor de recursos personalizados:

- CLI do Azure
- Modelos do Azure Resource Manager

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

Parâmetro | Obrigatório | Descrição
---|---|---
é objeto completo | *Sim, o que é* | Indica que o objeto de propriedades inclui outras opções, tais como localização, tags, sku e/ou plano.
ID | *Sim, o que é* | A identificação de recursos do recurso personalizado. Isto deve existir fora do **ResourceProvider**
propriedades | *Sim, o que é* | O órgão de pedido que será enviado para o **ponto final.**

Eliminar um recurso Azure Custom:

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

Parâmetro | Obrigatório | Descrição
---|---|---
ID | *Sim, o que é* | A identificação de recursos do recurso personalizado. Isto deve existir fora do **ResourceProvider**.

Recupere um recurso personalizado Azure:

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

Parâmetro | Obrigatório | Descrição
---|---|---
ID | *Sim, o que é* | A identificação de recursos do recurso personalizado. Isto deve existir fora do **ResourceProvider**

### <a name="azure-resource-manager-template"></a>Modelo do Azure Resource Manager

> [!NOTE]
> Os recursos exigem que a resposta contenha um `id` ponto `name` final adequado, e a partir do `type` ponto **final.**

Os modelos do Gestor de Recursos Azure exigem `id` `name` isso, e `type` são devolvidos corretamente a partir do ponto final a jusante. Uma resposta de recurso devolvida deve estar no formulário:

Resposta **do ponto final** da amostra:

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

Modelo de gestor de recursos Azure:

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

Parâmetro | Obrigatório | Descrição
---|---|---
nome de recursoTypeName | *Sim, o que é* | O **nome** do **recursoType** definido no fornecedor personalizado.
nome de recursoProviderName | *Sim, o que é* | O nome da instância do fornecedor de recursos personalizado.
customResourceName | *Sim, o que é* | O nome de recurso personalizado.

## <a name="next-steps"></a>Passos seguintes

- [Visão geral sobre fornecedores de recursos personalizados Azure](overview.md)
- [Quickstart: Criar Fornecedor de Recursos Personalizados Azure e implementar recursos personalizados](./create-custom-provider.md)
- [Tutorial: Criar ações e recursos personalizados em Azure](./tutorial-get-started-with-custom-providers.md)
- [Como: Adicionar ações personalizadas à Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [Referência: Referência de procuração de recursos personalizados](proxy-resource-endpoint-reference.md)
- [Referência: Referência de cache de recursos personalizados](proxy-cache-resource-endpoint-reference.md)
