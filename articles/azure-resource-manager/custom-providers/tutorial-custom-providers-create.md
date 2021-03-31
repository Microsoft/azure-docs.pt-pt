---
title: Criar e utilizar um fornecedor personalizado
description: Este tutorial mostra como criar e usar um Fornecedor Personalizado Azure. Utilize fornecedores personalizados para alterar fluxos de trabalho no Azure.
author: jjbfour
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.custom: devx-track-azurecli
ms.openlocfilehash: 4f425af7681b666b42fbcc70ac0e4c31d9df6d49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87503757"
---
# <a name="create-and-use-a-custom-provider"></a>Criar e utilizar um fornecedor personalizado

Um fornecedor personalizado é um contrato entre a Azure e um ponto final. Com fornecedores personalizados, pode alterar fluxos de trabalho no Azure. Este tutorial mostra o processo de criação de um fornecedor personalizado. Se não estiver familiarizado com os Fornecedores Personalizados Azure, consulte [a visão geral dos Fornecedores de Recursos Personalizados Azure.](overview.md)

## <a name="create-a-custom-provider"></a>Criar um fornecedor personalizado

> [!NOTE]
> Este tutorial não mostra como autoriar um ponto final. Se não tiver um ponto final REPOUSANTE, siga o [tutorial sobre a autoria de pontos finais RESTful](./tutorial-custom-providers-function-authoring.md), que é a base para o tutorial atual.

Depois de criar um ponto final, pode criar um fornecedor personalizado para gerar um contrato entre o fornecedor e o ponto final. Com um fornecedor personalizado, pode especificar uma lista de definições de ponto final:

```JSON
{
  "name": "myEndpointDefinition",
  "routingType": "Proxy",
  "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
}
```

Propriedade | Necessário | Descrição
---|---|---
**nome** | Yes | O nome da definição de ponto final. O Azure expõe este nome através da sua API em /subscrições/{subscriçãoD}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders<br>/resourceProviders/{resourceProviderName}/{endpointDefinitionName}
**roteamentoType** | No | O tipo de contrato de ponto final. Se o valor não for especificado, é por defeito de "Proxy".
**ponto final** | Yes | O ponto final para encaminhar os pedidos para. Este ponto final trata da resposta e dos efeitos secundários do pedido.

O valor do **ponto final** é o URL de gatilho da aplicação de função Azure. O `<yourapp>` , e espaços `<funcname>` `<functionkey>` reservados deve ser substituído por valores para a sua aplicação de função criada.

## <a name="define-custom-actions-and-resources"></a>Definir ações e recursos personalizados

O fornecedor personalizado contém uma lista de definições de ponto final modeladas ao abrigo das propriedades de **ações** e **recursosTypes.** As **ações** mapeia as ações personalizadas expostas pelo fornecedor personalizado, e a propriedade **de recursosTypes** são os recursos personalizados. Neste tutorial, o fornecedor personalizado tem uma propriedade **de ações** nomeada `myCustomAction` e uma propriedade de **RecursosTypes** chamada `myCustomResources` .

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

## <a name="deploy-the-custom-provider"></a>Implementar o fornecedor personalizado

> [!NOTE]
> Deve substituir os valores do **ponto final** pelo URL do gatilho da aplicação de função criada no tutorial anterior.

Pode implementar o fornecedor personalizado anterior utilizando um modelo de Gestor de Recursos Azure:

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

## <a name="use-custom-actions-and-resources"></a>Use ações e recursos personalizados

Depois de criar um fornecedor personalizado, pode utilizar as novas APIs Azure. Os separadores seguintes explicam como ligar e usar um fornecedor personalizado.

### <a name="custom-actions"></a>Ações personalizadas

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

> [!NOTE]
> Deve substituir os `{subscriptionId}` espaços reservados e `{resourceGroupName}` os espaços reservados pelo grupo de subscrição e recursos do local onde implementou o fornecedor personalizado.

```azurecli-interactive
az resource invoke-action --action myCustomAction \
                          --ids /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider \
                          --request-body
                            '{
                                "hello": "world"
                            }'
```

Parâmetro | Obrigatório | Descrição
---|---|---
*ação* | Yes | O nome da ação definida no fornecedor personalizado
*ids* | Yes | O ID de recursos do fornecedor personalizado
*pedido-corpo* | No | O órgão de pedido que será enviado para o ponto final

# <a name="template"></a>[Modelo](#tab/template)

Nenhum.

---

### <a name="custom-resources"></a>Recursos personalizados

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

> [!NOTE]
> Deve substituir os `{subscriptionId}` espaços reservados e `{resourceGroupName}` os espaços reservados pelo grupo de subscrição e recursos do local onde implementou o fornecedor personalizado.

#### <a name="create-a-custom-resource"></a>Criar um recurso personalizado

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

Parâmetro | Obrigatório | Descrição
---|---|---
*é objeto completo* | Yes | Indica se o objeto de propriedades inclui outras opções, como localização, tags, SKU ou plano.
*id* | Yes | A identificação de recursos do recurso personalizado. Este ID é uma extensão do ID do fornecedor personalizado.
*propriedades* | Yes | O corpo de pedido que será enviado para o ponto final.

#### <a name="delete-a-custom-resource"></a>Excluir um recurso personalizado

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

Parâmetro | Obrigatório | Descrição
---|---|---
*id* | Yes | A identificação de recursos do recurso personalizado. Este ID é uma extensão do ID do fornecedor personalizado.

#### <a name="retrieve-a-custom-resource"></a>Recuperar um recurso personalizado

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

Parâmetro | Obrigatório | Descrição
---|---|---
*id* | Yes | A identificação de recursos do recurso personalizado. Este ID é uma extensão do ID do fornecedor personalizado.

# <a name="template"></a>[Modelo](#tab/template)

Um modelo de gestor de recursos de amostra:

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

Parâmetro | Obrigatório | Descrição
---|---|---
*nome de recursoTypeName* | Yes | O `name` valor da propriedade **recursosTypes** definida no fornecedor personalizado.
*nome de recursoProviderName* | Yes | O nome da instância do fornecedor personalizado.
*customResourceName* | Yes | O nome de recurso personalizado.

---

> [!NOTE]
> Depois de terminar a implementação e a utilização do fornecedor personalizado, lembre-se de limpar todos os recursos criados, incluindo a aplicação de função Azure.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu sobre fornecedores personalizados. Para obter mais informações, consulte:

- [Como: Adicionar ações personalizadas à Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [Como: Adicionar recursos personalizados à Azure REST API](./custom-providers-resources-endpoint-how-to.md)
