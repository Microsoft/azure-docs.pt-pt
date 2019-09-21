---
title: Criar e usar um provedor personalizado
description: Este tutorial vai sobre como criar e usar um provedor personalizado.
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 053cf9fca03bf58cf10c313ae2569ce1918a46b9
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71172895"
---
# <a name="create-and-use-a-custom-provider"></a>Criar e usar um provedor personalizado

Um provedor personalizado é um contrato entre o Azure e um ponto de extremidade. Com provedores personalizados, você pode alterar os fluxos de trabalho no Azure. Este tutorial mostra o processo de criação de um provedor personalizado. Se você não estiver familiarizado com os provedores personalizados do Azure, consulte [a visão geral dos provedores de recursos personalizados do Azure](./custom-providers-overview.md).

## <a name="create-a-custom-provider"></a>Criar um provedor personalizado

> [!NOTE]
> Este tutorial não mostra como criar um ponto de extremidade. Se você não tiver um ponto de extremidade RESTFUL, siga o [tutorial sobre como criar pontos de extremidades RESTful](./tutorial-custom-providers-function-authoring.md), que é a base para o tutorial atual.

Depois de criar um ponto de extremidade, você pode criar um provedor personalizado para gerar um contrato entre o provedor e o ponto de extremidade. Com um provedor personalizado, você pode especificar uma lista de definições de ponto de extremidade:

```JSON
{
  "name": "myEndpointDefinition",
  "routingType": "Proxy",
  "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
}
```

Propriedade | Requerido | Descrição
---|---|---
**name** | Sim | O nome da definição do ponto de extremidade. O Azure expõe esse nome por meio de sua API em/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders<br>/resourceProviders/{resourceProviderName}/{endpointDefinitionName}
**roteamentotype** | Não | O tipo de contrato do ponto de extremidade. Se o valor não for especificado, o padrão será "proxy".
**extremidade** | Sim | O ponto de extremidade para o qual rotear as solicitações. Esse ponto de extremidade manipula a resposta e quaisquer efeitos colaterais da solicitação.

O valor do **ponto de extremidade** é a URL do gatilho do aplicativo de funções do Azure. Os `<yourapp>`espaços `<funcname>`reservados, `<functionkey>` e devem ser substituídos por valores para seu aplicativo de funções criado.

## <a name="define-custom-actions-and-resources"></a>Definir ações e recursos personalizados

O provedor personalizado contém uma lista de definições de ponto de extremidade modeladas nas propriedades **Actions** e **ResourceTypes** . A propriedade **Actions** é mapeada para as ações personalizadas expostas pelo provedor personalizado, e a propriedade **ResourceTypes** são os recursos personalizados. Neste tutorial, o provedor personalizado tem uma propriedade **Actions** chamada `myCustomAction` e uma propriedade **ResourceTypes** chamada `myCustomResources`.

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

## <a name="deploy-the-custom-provider"></a>Implantar o provedor personalizado

> [!NOTE]
> Você deve substituir os valores do **ponto de extremidade** pela URL do gatilho do aplicativo de funções criado no tutorial anterior.

Você pode implantar o provedor personalizado anterior usando um modelo de Azure Resource Manager:

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

## <a name="use-custom-actions-and-resources"></a>Usar ações e recursos personalizados

Depois de criar um provedor personalizado, você pode usar as novas APIs do Azure. As guias a seguir explicam como chamar e usar um provedor personalizado.

### <a name="custom-actions"></a>Ações personalizadas

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

> [!NOTE]
> Você deve substituir os `{subscriptionId}` espaços `{resourceGroupName}` reservados e pela assinatura e grupo de recursos de onde você implantou o provedor personalizado.

```azurecli-interactive
az resource invoke-action --action myCustomAction \
                          --ids /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider \
                          --request-body
                            '{
                                "hello": "world"
                            }'
```

Parâmetro | Requerido | Descrição
---|---|---
*Action* | Sim | O nome da ação definida no provedor personalizado
*identidade* | Sim | A ID de recurso do provedor personalizado
*corpo da solicitação* | Não | O corpo da solicitação que será enviado ao ponto de extremidade

# <a name="templatetabtemplate"></a>[Modelo](#tab/template)

Nenhum.

---

### <a name="custom-resources"></a>Recursos personalizados

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

> [!NOTE]
> Você deve substituir os `{subscriptionId}` espaços `{resourceGroupName}` reservados e pela assinatura e grupo de recursos de onde você implantou o provedor personalizado.

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

Parâmetro | Requerido | Descrição
---|---|---
*é-Full-Object* | Sim | Indica se o objeto de propriedades inclui outras opções, como localização, marcas, SKU ou plano.
*id* | Sim | A ID de recurso do recurso personalizado. Essa ID é uma extensão da ID de recurso do provedor personalizado.
*Propriedades* | Sim | O corpo da solicitação que será enviado ao ponto de extremidade.

#### <a name="delete-a-custom-resource"></a>Excluir um recurso personalizado

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

Parâmetro | Requerido | Descrição
---|---|---
*id* | Sim | A ID de recurso do recurso personalizado. Essa ID é uma extensão da ID de recurso do provedor personalizado.

#### <a name="retrieve-a-custom-resource"></a>Recuperar um recurso personalizado

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

Parâmetro | Requerido | Descrição
---|---|---
*id* | Sim | A ID de recurso do recurso personalizado. Essa ID é uma extensão da ID de recurso do provedor personalizado.

# <a name="templatetabtemplate"></a>[Modelo](#tab/template)

Um modelo do Resource Manager de exemplo:

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

Parâmetro | Requerido | Descrição
---|---|---
*resourceTypename* | Sim | O `name` valor da propriedade **ResourceTypes** definida no provedor personalizado.
*resourceProviderName* | Sim | O nome da instância do provedor personalizado.
*customResourceName* | Sim | O nome do recurso personalizado.

---

> [!NOTE]
> Depois de concluir a implantação e o uso do provedor personalizado, lembre-se de limpar todos os recursos criados, incluindo o aplicativo de funções do Azure.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu sobre provedores personalizados. Para obter mais informações, consulte:

- [How to: Adicionando ações personalizadas à API REST do Azure](./custom-providers-action-endpoint-how-to.md)
- [How to: Adicionando recursos personalizados à API REST do Azure](./custom-providers-resources-endpoint-how-to.md)
