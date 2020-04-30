---
title: Criar e usar um fornecedor personalizado
description: Este tutorial mostra como criar e usar um Fornecedor Personalizado Azure. Utilize fornecedores personalizados para alterar fluxos de trabalho no Azure.
author: jjbfour
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 09df78955de6423244c2d8ec94e1e1c06ecab257
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "75650031"
---
# <a name="create-and-use-a-custom-provider"></a>Criar e usar um fornecedor personalizado

Um fornecedor personalizado é um contrato entre o Azure e um ponto final. Com fornecedores personalizados, pode alterar fluxos de trabalho no Azure. Este tutorial mostra o processo de criação de um fornecedor personalizado. Se não está familiarizado com os Fornecedores Personalizados Azure, consulte [a visão geral dos Fornecedores de Recursos Personalizados Azure](overview.md).

## <a name="create-a-custom-provider"></a>Criar um fornecedor personalizado

> [!NOTE]
> Este tutorial não mostra como ser autor de um ponto final. Se não tiver um ponto final repousante, siga o tutorial sobre a autoria de [pontos finais RESTful](./tutorial-custom-providers-function-authoring.md), que é a base para o tutorial atual.

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
**nome** | Sim | O nome da definição de ponto final. O Azure expõe este nome através da sua API em /subscrições/{subscriçãoId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders<br>/recursosProviders/{resourceProviderName}/{endpointDefinitionName}
**routingType** | Não | O tipo de contrato de ponto final. Se o valor não for especificado, não se importa com "Proxy".
**ponto final** | Sim | O ponto final para encaminhar os pedidos para. Este ponto final trata da resposta e quaisquer efeitos secundários do pedido.

O valor do **ponto final** é o URL de gatilho da aplicação de função Azure. O `<yourapp>` `<funcname>`, `<functionkey>` e os espaços reservados devem ser substituídos por valores para a sua aplicação de função criada.

## <a name="define-custom-actions-and-resources"></a>Definir ações e recursos personalizados

O fornecedor personalizado contém uma lista de definições de ponto final modeladas sob as propriedades de **ações** e **recursosTypes.** As **ações mapeiam** as ações personalizadas expostas pelo fornecedor personalizado, e a propriedade **resourceTypes** são os recursos personalizados. Neste tutorial, o fornecedor personalizado tem `myCustomAction` uma propriedade de `myCustomResources` **ações** nomeada e uma propriedade **resourceTypes** chamada .

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

Depois de criar um fornecedor personalizado, pode utilizar as novas APIs Azure. Os seguintes separadores explicam como ligar e utilizar um fornecedor personalizado.

### <a name="custom-actions"></a>Ações personalizadas

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

> [!NOTE]
> Deve substituir `{subscriptionId}` os `{resourceGroupName}` espaços reservados e reservados pelo grupo de subscrição e recursos de onde implementou o fornecedor personalizado.

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
*ação* | Sim | O nome da ação definida no fornecedor personalizado
*ids* | Sim | A identificação de recursos do fornecedor personalizado
*solicitar corpo* | Não | O órgão de pedido que será enviado para o ponto final

# <a name="template"></a>[Modelo](#tab/template)

Nenhum.

---

### <a name="custom-resources"></a>Recursos personalizados

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

> [!NOTE]
> Deve substituir `{subscriptionId}` os `{resourceGroupName}` espaços reservados e reservados pelo grupo de subscrição e recursos de onde implementou o fornecedor personalizado.

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

Parâmetro | Necessário | Descrição
---|---|---
*é objeto cheio* | Sim | Indica se o objeto de propriedades inclui outras opções como localização, tags, SKU ou plano.
*ID* | Sim | A identificação de recursos do recurso personalizado. Este ID é uma extensão do ID de recursos personalizados do fornecedor.
*propriedades* | Sim | O órgão de pedido que será enviado para o ponto final.

#### <a name="delete-a-custom-resource"></a>Eliminar um recurso personalizado

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

Parâmetro | Necessário | Descrição
---|---|---
*ID* | Sim | A identificação de recursos do recurso personalizado. Este ID é uma extensão do ID de recursos personalizados do fornecedor.

#### <a name="retrieve-a-custom-resource"></a>Recuperar um recurso personalizado

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

Parâmetro | Necessário | Descrição
---|---|---
*ID* | Sim | A identificação de recursos do recurso personalizado. Este ID é uma extensão do ID de recursos personalizados do fornecedor.

# <a name="template"></a>[Modelo](#tab/template)

Um modelo de Gestor de Recursos da amostra:

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
*recursoTypeName* | Sim | O `name` valor da propriedade **resourceTypes** define-se no fornecedor personalizado.
*nome fornecedor de recursos* | Sim | O nome da instância do fornecedor personalizado.
*nome personalizado DeRecursos* | Sim | O nome de recurso personalizado.

---

> [!NOTE]
> Depois de terminar de implementar e utilizar o fornecedor personalizado, lembre-se de limpar todos os recursos criados, incluindo a aplicação de função Azure.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu sobre fornecedores personalizados. Para obter mais informações, consulte:

- [Como: Adicionar ações personalizadas à API Do REST Azure](./custom-providers-action-endpoint-how-to.md)
- [Como: Adicionar recursos personalizados à API Do REST Azure](./custom-providers-resources-endpoint-how-to.md)
