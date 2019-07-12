---
title: Adicionar ações personalizadas à API REST do Azure
description: Saiba como adicionar ações personalizadas para a API de REST do Azure. Este artigo irá guiá-os requisitos e melhores práticas para pontos de extremidade que desejarem implementar ações personalizadas.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 6fbd20c201e1b141b7276e3283599b00cdefd118
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795302"
---
# <a name="adding-custom-actions-to-azure-rest-api"></a>Adicionar ações personalizadas a API REST do Azure

Este artigo tratará de requisitos e melhores práticas para a criação de pontos de extremidade de provedor de recurso personalizado do Azure que implementam ações personalizadas. Se não estiver familiarizado com fornecedores de recursos personalizado do Azure, veja [a visão geral sobre provedores de recursos personalizados](./custom-providers-overview.md).

## <a name="how-to-define-an-action-endpoint"></a>Como definir um ponto de extremidade de ação

Uma **ponto final** é um URL que aponta para um serviço, que implementa o contrato subjacente entre ele e o Azure. O ponto de extremidade é definido no fornecedor de recursos personalizado e pode ser qualquer URL acessível publicamente. O exemplo abaixo tem um **ação** chamado `myCustomAction` implementado pelo `endpointURL`.

Exemplo **ResourceProvider**:

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "myCustomAction",
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

## <a name="building-an-action-endpoint"></a>Criação de um ponto de extremidade de ação

Uma **ponto final** que implementa um **ação** tem de processar o pedido e resposta para a nova API do Azure. Quando um fornecedor de recursos personalizado com uma **ação** é criada, irá gerar um novo conjunto de APIs no Azure. Neste caso, a ação irá gerar uma nova ação de Azure API para `POST` chamadas:

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction
```

Pedido de entrada de API do Azure:

``` HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

Este pedido, em seguida, será reencaminhado para o **ponto final** sob a forma:

``` HTTP
POST https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

Da mesma forma, a resposta dos **ponto final** , em seguida, é reencaminhado para o customer. Deverá devolver a resposta do ponto final:

- Um documento de objeto JSON válido. Todas as matrizes e seqüências de caracteres devem ser aninhadas num objeto superior.
- O `Content-Type` cabeçalho deve ser definido como "application/json; charset = utf-8 ".

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

Resposta do fornecedor de recursos personalizado do Azure:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

## <a name="calling-a-custom-action"></a>Chamar uma ação personalizada

Existem duas formas de chamar uma ação personalizada num provedor de recursos personalizado:

- CLI do Azure
- Modelos Azure Resource Manager

### <a name="azure-cli"></a>CLI do Azure

```azurecli-interactive
az resource invoke-action --action {actionName} \
                          --ids /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName} \
                          --request-body \
                            '{
                                "myProperty1": "myPropertyValue1",
                                "myProperty2": {
                                    "myProperty3": "myPropertyValue3"
                                }
                            }'
```

Parâmetro | Necessário | Descrição
---|---|---
ação | *Sim* | O nome da ação definida no **ResourceProvider**.
ids | *Sim* | O ID de recurso do **ResourceProvider**.
corpo do pedido | *no* | O corpo do pedido que será enviado para o **ponto final**.

### <a name="azure-resource-manager-template"></a>Modelo do Azure Resource Manager

> [!NOTE]
> Ações de limitaram suporte nos modelos do Azure Resource Manager. Por ordem para a ação a ser chamado dentro de um modelo, tem de conter o [ `list` ](../azure-resource-manager/resource-group-template-functions-resource.md#list) prefixo no nome.

Exemplo **ResourceProvider** com ação de lista:

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "listMyCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus"
}
```

Exemplo modelo do Resource Manager do Azure:

``` JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "resourceIdentifier": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
        "apiVersion": "2018-09-01-preview",
        "functionValues": {
            "myProperty1": "myPropertyValue1",
            "myProperty2": {
                "myProperty3": "myPropertyValue3"
            }
        }
    },
    "resources": [],
    "outputs": {
        "myCustomActionOutput": {
            "type": "object",
            "value": "[listMyCustomAction(variables('resourceIdentifier'), variables('apiVersion'), variables('functionValues'))]"
        }
    }
}
```

Parâmetro | Necessário | Descrição
---|---|---
resourceIdentifier | *Sim* | O ID de recurso do **ResourceProvider**.
apiVersion | *Sim* | A versão de API do tempo de execução do recurso. Isso deve ser sempre "2018-09-01-preview".
functionValues | *no* | O corpo do pedido que será enviado para o **ponto final**.

## <a name="next-steps"></a>Passos Seguintes

- [Descrição geral sobre provedores de recurso personalizado do Azure](./custom-providers-overview.md)
- [Quickstart: Criar o fornecedor de recursos personalizado do Azure e implementar recursos personalizados](./create-custom-provider.md)
- [Tutorial: Criar ações personalizadas e os recursos no Azure](./tutorial-custom-providers-101.md)
- [How To: Adicionar recursos personalizados à API REST do Azure](./custom-providers-resources-endpoint-how-to.md)
