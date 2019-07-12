---
title: Criar e utilizar um fornecedor personalizado do Azure
description: Este tutorial irá abordar como criar e utilizar um fornecedor personalizado.
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 65a8e60d8216e1da16af987c9e699e24ecaec3ec
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800002"
---
# <a name="authoring-a-restful-endpoint-for-custom-providers"></a>Criação de um ponto de extremidade RESTful para fornecedores personalizados

Fornecedores personalizados permitem-lhe personalizar fluxos de trabalho no Azure. Um provedor personalizado é um contrato entre o Azure e um `endpoint`. Este tutorial irá percorrer o processo de criação de um fornecedor personalizado. Se não estiver familiarizado com fornecedores personalizados do Azure, veja [a visão geral sobre provedores de recursos personalizados](./custom-providers-overview.md).

Este tutorial é dividido nos seguintes passos:

- O que é um provedor personalizado
- Definição de ações personalizadas e recursos
- Implementar o provedor personalizado

Neste tutorial, irá criar nos tutoriais seguintes:

- [Criação de um ponto de extremidade RESTful para fornecedores personalizados](./tutorial-custom-providers-function-authoring.md)

## <a name="creating-a-custom-provider"></a>Criar um provedor personalizado

> [!NOTE]
> Este tutorial não será transmitido pela criação de um ponto de extremidade. Siga os [tutorial sobre a criação de pontos de extremidade RESTful](./tutorial-custom-providers-function-authoring.md) se não tiver um ponto de extremidade RESTful.

Uma vez a `endpoint` é criado, pode gerar o criar um provedor personalizado para gerar um contrato entre ele e o `endpoint`. Um provedor personalizado permite-lhe especificar uma lista de definições de ponto final.

```JSON
{
  "name": "myEndpointDefinition",
  "routingType": "Proxy",
  "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
}
```

Propriedade | Necessário | Descrição
---|---|---
name | *Sim* | O nome da definição do ponto final. Azure irá expor este nome por meio de sua API em ' /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/<br>resourceProviders/{resourceProviderName}/{endpointDefinitionName}'
routingType | *no* | Determina o tipo de contrato com o `endpoint`. Se não for especificado, será predefinido para "Proxy".
endpoint | *Sim* | O ponto final para encaminhar os pedidos para. Isto irá processar a resposta, bem como quaisquer efeitos colaterais do pedido.

Neste caso, o `endpoint` é o URL do acionador da função do Azure. O `<yourapp>`, `<funcname>`, e `<functionkey>` devem ser substituídas por valores para a função criada.

## <a name="defining-custom-actions-and-resources"></a>Definição de ações personalizadas e recursos

O provedor personalizado contém uma lista de definições de ponto final modelada sob `actions` e `resourceTypes`. `actions` o mapa para as ações personalizadas exposto pelo fornecedor personalizado, embora `resourceTypes` são os recursos personalizados. Para este tutorial, iremos definir um provedor personalizado com uma `action` chamado `myCustomAction` e uma `resourceType` chamado `myCustomResources`.

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "myCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
      }
    ],
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy",
        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
      }
    ]
  },
  "location": "eastus"
}
```

Substitua `endpoint` com o URL de Acionador a partir da função criada anteriormente no tutorial anterior.

## <a name="deploying-the-custom-provider"></a>Implementar o provedor personalizado

> [!NOTE]
> O `endpoint` deve ser substituído com o URL da função.

O provedor personalizado acima pode ser implementado com um modelo de Gestor de recursos do Azure.

```JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.CustomProviders/resourceProviders",
            "name": "myCustomProvider",
            "apiVersion": "2018-09-01-preview",
            "location": "eastus",
            "properties": {
                "actions": [
                    {
                        "name": "myCustomAction",
                        "routingType": "Proxy",
                        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
                    }
                ],
                "resourceTypes": [
                    {
                        "name": "myCustomResources",
                        "routingType": "Proxy",
                        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
                    }
                ]
            }
        }
    ]
}
```

## <a name="using-custom-actions-and-resources"></a>Utilizar ações personalizadas e recursos

Depois criamos um provedor personalizado, podemos pode utilizar as novas APIs do Azure. A secção seguinte irá explicar como chamar e utilizar um fornecedor personalizado.

### <a name="custom-actions"></a>Ações personalizadas

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

> [!NOTE]
> O `{subscriptionId}` e `{resourceGroupName}` deve ser substituído com a subscrição e o resourceGroup em que o provedor personalizado foi implementado.

```azurecli-interactive
az resource invoke-action --action myCustomAction \
                          --ids /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider \
                          --request-body
                            '{
                                "hello": "world"
                            }'
```

Parâmetro | Necessário | Descrição
---|---|---
ação | *Sim* | O nome da ação definida no fornecedor personalizado criado.
ids | *Sim* | O ID de recurso do fornecedor personalizado criado.
corpo do pedido | *no* | O corpo do pedido que será enviado para o `endpoint`.

# <a name="templatetabtemplate"></a>[Modelo](#tab/template)

Nenhum.

---

### <a name="custom-resources"></a>Recursos personalizados

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

> [!NOTE]
> O `{subscriptionId}` e `{resourceGroupName}` deve ser substituído com a subscrição e o resourceGroup em que o provedor personalizado foi implementado.

Crie um recurso personalizado:

```azurecli-interactive
az resource create --is-full-object \
                   --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1 \
                   --properties
                    '{
                        "location": "eastus",
                        "properties": {
                            "hello" : "world"
                        }
                    }'
```

Parâmetro | Necessário | Descrição
---|---|---
is-full-object | *Sim* | Indica que o objeto de propriedades inclui outras opções, como a localização, marcas, sku e/ou plano.
id | *Sim* | O ID de recurso do recurso personalizado. Isso deve existir fora o provedor personalizado criado.
properties | *Sim* | O corpo do pedido que será enviado para o `endpoint`.

Elimine um recurso personalizado do Azure:

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

Parâmetro | Necessário | Descrição
---|---|---
id | *Sim* | O ID de recurso do recurso personalizado. Isso deve existir fora o provedor personalizado criado.

Obter um recurso personalizado do Azure:

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

Parâmetro | Necessário | Descrição
---|---|---
id | *Sim* | O ID de recurso do recurso personalizado. Isso deve existir fora o provedor personalizado criado.

# <a name="templatetabtemplate"></a>[Modelo](#tab/template)

Exemplo modelo do Resource Manager do Azure:

```JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
            "name": "myCustomProvider/myTestResourceName1",
            "apiVersion": "2018-09-01-preview",
            "location": "eastus",
            "properties": {
                "hello": "world"
            }
        }
    ]
}
```

Parâmetro | Necessário | Descrição
---|---|---
resourceTypeName | *Sim* | O `name` das *resourceType* definidas no fornecedor personalizado.
resourceProviderName | *Sim* | O nome da instância de fornecedor personalizado.
customResourceName | *Sim* | O nome de recurso personalizado.

---

> [!NOTE]
> Depois de o implementar e utilizar o provedor personalizado, lembre-se de limpar quaisquer recursos criados, incluindo a função do Azure.

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu sobre fornecedores personalizados. Vá para o artigo seguinte para criar um provedor personalizado.

- [How To: Adicionar ações personalizadas a API REST do Azure](./custom-providers-action-endpoint-how-to.md)
- [How To: Adicionar recursos personalizados à API REST do Azure](./custom-providers-resources-endpoint-how-to.md)
