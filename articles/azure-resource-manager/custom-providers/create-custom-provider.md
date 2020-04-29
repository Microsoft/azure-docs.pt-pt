---
title: Criar fornecedor de recursos
description: Descreve como criar um fornecedor de recursos e implementar os seus tipos de recursos personalizados.
author: MSEvanhi
ms.topic: tutorial
ms.date: 05/01/2019
ms.author: evanhi
ms.openlocfilehash: 393993a44c860525b9bd9a540ed7afff78e5b93c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "75649870"
---
# <a name="quickstart-create-custom-provider-and-deploy-custom-resources"></a>Quickstart: Criar fornecedor personalizado e implementar recursos personalizados

Neste arranque rápido, cria o seu próprio fornecedor de recursos e implementa tipos de recursos personalizados para esse fornecedor de recursos. Para mais informações sobre fornecedores personalizados, consulte a visão geral da [Pré-visualização dos Fornecedores Personalizados do Azure](overview.md).

## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos neste arranque rápido, precisa de ligar para as operações REST. Existem [diferentes formas de enviar pedidos de REST.](/rest/api/azure/) Se ainda não tiver uma ferramenta para operações REST, instale o [ARMClient.](https://github.com/projectkudu/ARMClient) É uma ferramenta de linha de comando de código aberto que simplifica a invocação da API do Gestor de Recursos Azure.

## <a name="deploy-custom-provider"></a>Implementar fornecedor personalizado

Para configurar o fornecedor personalizado, implemente um modelo de [exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/custom-providers/customprovider.json) para a sua subscrição Azure.

Depois de implementar o modelo, a sua subscrição tem os seguintes recursos:

* App de Funções com as operações para os recursos e ações.
* Conta de Armazenamento para armazenar utilizadores que são criados através do fornecedor personalizado.
* Fornecedor personalizado que define os tipos e ações de recursos personalizados. Utiliza o ponto final da aplicação de função para o envio de pedidos.
* Recurso personalizado do fornecedor personalizado.

Para implementar o fornecedor personalizado com a PowerShell, utilize:

```azurepowershell-interactive
$rgName = "<resource-group-name>"
$funcName = "<function-app-name>"

New-AzResourceGroup -Name $rgName -Location eastus
New-AzResourceGroupDeployment -ResourceGroupName $rgName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/custom-providers/customprovider.json `
  -funcname $funcName
```

Ou, pode implementar a solução com o seguinte botão:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-docs-json-samples%2Fmaster%2Fcustom-providers%2Fcustomprovider.json" target="_blank">
    <img src="https://azuredeploy.net/deploybutton.png"/>
</a>

## <a name="view-custom-provider-and-resource"></a>Ver fornecedor personalizado e recurso

No portal, o fornecedor personalizado é um tipo de recurso oculto. Para confirmar que o fornecedor de recursos foi implantado, navegue para o grupo de recursos. Selecione a opção de **mostrar tipos ocultos**.

![Mostrar tipos de recursos escondidos](./media/create-custom-provider/show-hidden.png)

Para ver o tipo de recurso personalizado que implementou, utilize a operação GET no seu tipo de recurso.

```
GET https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users?api-version=2018-09-01-preview
```

Com o ARMClient, use:

```powershell
$subID = (Get-AzContext).Subscription.Id
$requestURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users?api-version=2018-09-01-preview"

armclient GET $requestURI
```

Recebe a resposta:

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

O seu fornecedor personalizado também tem uma ação chamada **ping**. O código que processa o pedido é implementado na aplicação de funções. A ação do ping responde com uma saudação.

Para enviar um pedido de ping, utilize a operação POST no seu fornecedor personalizado.

```
POST https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/ping?api-version=2018-09-01-preview
```

Com o ARMClient, use:

```powershell
$pingURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/ping?api-version=2018-09-01-preview"

armclient POST $pingURI
```

Recebe a resposta:

```json
{
  "pingcontent": {
    "source": "<function-name>.azurewebsites.net"
  },
  "message": "hello <function-name>.azurewebsites.net"
}
```

## <a name="create-resource-type"></a>Criar tipo de recurso

Para criar o tipo de recurso personalizado, pode implantar o recurso num modelo. Esta abordagem é mostrada no modelo que implementou neste arranque rápido. Também pode enviar um pedido DE PUT para o tipo de recurso.

```
PUT https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/<resource-name>?api-version=2018-09-01-preview

{"properties":{"FullName": "Test User", "Location": "Earth"}}
```

Com o ARMClient, use:

```powershell
$addURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users/testuser?api-version=2018-09-01-preview"
$requestBody = "{'properties':{'FullName': 'Test User', 'Location': 'Earth'}}"

armclient PUT $addURI $requestBody
```

Recebe a resposta:

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

## <a name="next-steps"></a>Passos seguintes

Para uma introdução aos fornecedores personalizados, consulte a visão geral da [Pré-visualização dos fornecedores personalizados do Azure](overview.md).
