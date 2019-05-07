---
title: Criar o fornecedor de recursos com a pré-visualização de fornecedores de personalizado do Azure
description: Descreve como criar um fornecedor de recursos e implementar os tipos de recursos personalizados.
services: managed-applications
author: MSEvanhi
ms.service: managed-applications
ms.topic: tutorial
ms.date: 05/01/2019
ms.author: evanhi
ms.openlocfilehash: 34dd5efda2c9f6cc9a7b5ddcde06e8f7d27de901
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65158428"
---
# <a name="tutorial-create-custom-provider-and-deploy-custom-resources"></a>Tutorial: Criar fornecedor personalizado e implementar recursos personalizados

Neste tutorial, crie o seu próprio fornecedor de recursos e implementar tipos de recurso personalizado para esse fornecedor de recursos. Para obter mais informações sobre fornecedores personalizados, consulte [descrição geral de visualização de provedores personalizados do Azure](custom-providers-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir os passos neste tutorial, terá de chamar as operações REST. Existem [diferentes maneiras de enviar pedidos REST](/rest/api/azure/). Se ainda não tiver uma ferramenta para operações REST, instale [ARMClient](https://github.com/projectkudu/ARMClient). É uma ferramenta de linha de comandos de código-fonte aberto que simplifica a invocar a API do Azure Resource Manager.

## <a name="deploy-custom-provider"></a>Implementar um fornecedor personalizado

Para configurar o provedor personalizado, implementar um [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/custom-providers/customprovider.json) à sua subscrição do Azure.

Depois de implementar o modelo, a sua subscrição tem os seguintes recursos:

* Aplicação de função com as operações que os recursos e ações.
* Conta de armazenamento para armazenar os utilizadores que forem criados com o provedor personalizado.
* Fornecedor personalizado que define os tipos de recurso personalizado e ações. Ele usa o ponto final de aplicação de função para enviar pedidos.
* Recurso personalizado do fornecedor personalizado.

Para implementar o provedor personalizado com o PowerShell, utilize:

```azurepowershell-interactive
$rgName = "<resource-group-name>"
$funcName = "<function-app-name>"

New-AzResourceGroup -Name $rgName -Location eastus
New-AzResourceGroupDeployment -ResourceGroupName $rgName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/custom-providers/customprovider.json `
  -funcname $funcName
```

Em alternativa, pode implementar a solução com o botão seguinte:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-docs-json-samples%2Fmaster%2Fcustom-providers%2Fcustomprovider.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

## <a name="view-custom-provider-and-resource"></a>Provedor de exibição personalizado e recursos

No portal, o provedor personalizado é um tipo de recurso oculto. Para confirmar que o fornecedor de recursos foi implementado, navegue para o grupo de recursos. Selecione a opção para **mostrar tipos ocultos**.

![Mostrar tipos ocultos de recursos](./media/create-custom-providers/show-hidden.png)

Para ver o tipo de recurso personalizado que implementou, utilize a operação GET no seu tipo de recurso.

```
GET https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users?api-version=2018-09-01-preview
```

Com ARMClient, utilize:

```powershell
$subID = (Get-AzContext).Subscription.Id
$requestURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users?api-version=2018-09-01-preview"

armclient GET $requestURI
```

Receber a resposta:

```json
{
  "value": [
    {
      "properties": {
        "provisioningState": "Succeeded",
        "FullName": "Santa Claus",
        "Location": "NorthPole"
      },
      "id": "/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/santa",
      "name": "santa",
      "type": "Microsoft.CustomProviders/resourceProviders/users"
    }
  ]
}
```

## <a name="call-action"></a>Ação de chamada

O provedor personalizado também tem uma ação chamada **ping**. O código que processa o pedido é implementado na aplicação de função. A ação de ping responde com uma saudação.

Para enviar um pedido de ping, utilize a operação de publicação no seu fornecedor personalizado.

```
POST https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/ping?api-version=2018-09-01-preview
```

Com ARMClient, utilize:

```powershell
$pingURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/ping?api-version=2018-09-01-preview"

armclient POST $pingURI
```

Receber a resposta:

```json
{
  "pingcontent": {
    "source": "<function-name>.azurewebsites.net"
  },
  "message": "hello <function-name>.azurewebsites.net"
}
```

## <a name="create-resource-type"></a>Criar tipo de recurso

Para criar o tipo de recurso personalizado, pode implementar o recurso num modelo. Essa abordagem é mostrada no modelo que implementou neste tutorial. Também pode enviar um pedido PUT para o tipo de recurso.

```
PUT https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/<resource-name>?api-version=2018-09-01-preview

{"properties":{"FullName": "Test User", "Location": "Earth"}}
```

Com ARMClient, utilize:

```powershell
$addURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users/testuser?api-version=2018-09-01-preview"
$requestBody = "{'properties':{'FullName': 'Test User', 'Location': 'Earth'}}"

armclient PUT $addURI $requestBody
```

Receber a resposta:

```json
{
  "properties": {
    "provisioningState": "Succeeded",
    "FullName": "Test User",
    "Location": "Earth"
  },
  "id": "/subscriptions/<sub-ID>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/testuser",
  "name": "testuser",
  "type": "Microsoft.CustomProviders/resourceProviders/users"
}
```

## <a name="next-steps"></a>Passos Seguintes

Para obter uma introdução para fornecedores personalizados, consulte [descrição geral de visualização de provedores personalizados do Azure](custom-providers-overview.md).
