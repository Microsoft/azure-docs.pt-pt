---
title: Criar provedor de recursos
description: Descreve como criar um provedor de recursos e implantar seus tipos de recursos personalizados.
author: MSEvanhi
ms.topic: tutorial
ms.date: 05/01/2019
ms.author: evanhi
ms.openlocfilehash: 393993a44c860525b9bd9a540ed7afff78e5b93c
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75649870"
---
# <a name="quickstart-create-custom-provider-and-deploy-custom-resources"></a>Início rápido: criar um provedor personalizado e implantar recursos personalizados

Neste guia de início rápido, você cria seu próprio provedor de recursos e implanta tipos de recursos personalizados para esse provedor de recursos. Para obter mais informações sobre provedores personalizados, consulte [visão geral de visualização de provedores personalizados do Azure](overview.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas neste guia de início rápido, você precisa chamar as operações REST. Há [diferentes maneiras de enviar solicitações REST](/rest/api/azure/). Se você ainda não tiver uma ferramenta para operações REST, instale o [ARMClient](https://github.com/projectkudu/ARMClient). É uma ferramenta de linha de comando de software livre que simplifica a invocação da API de Azure Resource Manager.

## <a name="deploy-custom-provider"></a>Implantar provedor personalizado

Para configurar o provedor personalizado, implante um [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/custom-providers/customprovider.json) em sua assinatura do Azure.

Depois de implantar o modelo, sua assinatura tem os seguintes recursos:

* Aplicativo de funções com as operações para os recursos e ações.
* Conta de armazenamento para armazenar usuários criados por meio do provedor personalizado.
* Provedor personalizado que define as ações e os tipos de recursos personalizados. Ele usa o ponto de extremidade do aplicativo de funções para enviar solicitações.
* Recurso personalizado do provedor personalizado.

Para implantar o provedor personalizado com o PowerShell, use:

```azurepowershell-interactive
$rgName = "<resource-group-name>"
$funcName = "<function-app-name>"

New-AzResourceGroup -Name $rgName -Location eastus
New-AzResourceGroupDeployment -ResourceGroupName $rgName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/custom-providers/customprovider.json `
  -funcname $funcName
```

Ou, você pode implantar a solução com o seguinte botão:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-docs-json-samples%2Fmaster%2Fcustom-providers%2Fcustomprovider.json" target="_blank">
    <img src="https://azuredeploy.net/deploybutton.png"/>
</a>

## <a name="view-custom-provider-and-resource"></a>Exibir provedor personalizado e recurso

No portal, o provedor personalizado é um tipo de recurso oculto. Para confirmar que o provedor de recursos foi implantado, navegue até o grupo de recursos. Selecione a opção para **Mostrar tipos ocultos**.

![Mostrar tipos de recursos ocultos](./media/create-custom-provider/show-hidden.png)

Para ver o tipo de recurso personalizado que você implantou, use a operação GET no seu tipo de recurso.

```
GET https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users?api-version=2018-09-01-preview
```

Com o ARMClient, use:

```powershell
$subID = (Get-AzContext).Subscription.Id
$requestURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users?api-version=2018-09-01-preview"

armclient GET $requestURI
```

Você recebe a resposta:

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

Seu provedor personalizado também tem uma ação chamada **ping**. O código que processa a solicitação é implementado no aplicativo de funções. A ação ping responde com uma saudação.

Para enviar uma solicitação de ping, use a operação POST em seu provedor personalizado.

```
POST https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/ping?api-version=2018-09-01-preview
```

Com o ARMClient, use:

```powershell
$pingURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/ping?api-version=2018-09-01-preview"

armclient POST $pingURI
```

Você recebe a resposta:

```json
{
  "pingcontent": {
    "source": "<function-name>.azurewebsites.net"
  },
  "message": "hello <function-name>.azurewebsites.net"
}
```

## <a name="create-resource-type"></a>Criar tipo de recurso

Para criar o tipo de recurso personalizado, você pode implantar o recurso em um modelo. Essa abordagem é mostrada no modelo implantado neste guia de início rápido. Você também pode enviar uma solicitação PUT para o tipo de recurso.

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

Você recebe a resposta:

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

Para obter uma introdução aos provedores personalizados, consulte [visão geral de visualização de provedores personalizados do Azure](overview.md).
