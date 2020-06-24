---
title: Criar fornecedor de recursos
description: Descreve como criar um fornecedor de recursos e implementar os seus tipos de recursos personalizados.
author: MSEvanhi
ms.topic: tutorial
ms.date: 06/19/2020
ms.author: evanhi
ms.openlocfilehash: ce547c010d3cc814d4e6f6182c19572248228fc3
ms.sourcegitcommit: 398fecceba133d90aa8f6f1f2af58899f613d1e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/21/2020
ms.locfileid: "85125016"
---
# <a name="quickstart-create-custom-provider-and-deploy-custom-resources"></a>Quickstart: Criar fornecedor personalizado e implementar recursos personalizados

Neste quickstart, cria o seu próprio fornecedor de recursos e implementa tipos de recursos personalizados para esse fornecedor de recursos. Para obter mais informações sobre fornecedores personalizados, consulte [a visão geral do Pré-visualização do Azure Custom Providers](overview.md).

## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos neste arranque rápido, precisa de chamar `REST` as operações. Existem [diferentes formas de enviar pedidos de DESCANSO.](/rest/api/azure/)

Para executar comandos Azure CLI, use [Bash in Azure Cloud Shell](/azure/cloud-shell/quickstart). Os [comandos de fornecedores personalizados](/cli/azure/ext/custom-providers/custom-providers/resource-provider) requerem uma extensão. Para obter mais informações, consulte [as extensões de utilização com O Azure CLI](/cli/azure/azure-cli-extensions-overview).

Para executar os comandos PowerShell localmente, utilize o PowerShell 7 ou mais tarde e os módulos Azure PowerShell. Para obter mais informações, consulte [instalar a Azure PowerShell](/powershell/azure/install-az-ps). Se ainda não tiver uma ferramenta para `REST` operações, instale o [ARMClient](https://github.com/projectkudu/ARMClient). É uma ferramenta de linha de comando de código aberto que simplifica a invocação da API do Gestor de Recursos Azure.

## <a name="deploy-custom-provider"></a>Implementar fornecedor personalizado

Para configurar o fornecedor personalizado, implemente um [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/custom-providers/customprovider.json) para a sua subscrição Azure.

Após a implementação do modelo, a sua subscrição tem os seguintes recursos:

* App de função com as operações para os recursos e ações.
* Conta de Armazenamento para armazenar utilizadores que são criados através do fornecedor personalizado.
* Fornecedor personalizado que define os tipos e ações de recursos personalizados. Utiliza o ponto final da aplicação de função para o envio de pedidos.
* Recurso personalizado do fornecedor personalizado.

Para implementar o fornecedor personalizado, utilize o Azure CLI ou o PowerShell:

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
read -p "Enter a resource group name:" rgName &&
read -p "Enter the location (i.e. eastus):" location &&
read -p "Enter the provider's function app name:" funcName &&
templateUri="https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/custom-providers/customprovider.json" &&
az group create --name $rgName --location "$location" &&
az deployment group create --resource-group $rgName --template-uri $templateUri --parameters funcName=$funcName &&
echo "Press [ENTER] to continue ..." &&
read
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
$rgName = Read-Host -Prompt "Enter a resource group name"
$location = Read-Host -Prompt "Enter the location (i.e. eastus)"
$funcName = Read-Host -Prompt "Enter the provider's function app name"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/custom-providers/customprovider.json"
New-AzResourceGroup -Name $rgName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $rgName -TemplateUri $templateUri -funcName $funcName
Read-Host -Prompt "Press [ENTER] to continue ..."
```

---

Ou, pode implantar a solução a partir do portal Azure com o seguinte botão:

[![Implementar no Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-docs-json-samples%2Fmaster%2Fcustom-providers%2Fcustomprovider.json)

## <a name="view-custom-provider-and-resource"></a>Ver fornecedor personalizado e recurso

No portal, o fornecedor personalizado é um tipo de recurso oculto. Para confirmar que o fornecedor de recursos foi implantado, navegue para o grupo de recursos. Selecione a opção para **mostrar os tipos ocultos**.

![Mostrar tipos de recursos ocultos](./media/create-custom-provider/show-hidden.png)

Para ver o tipo de recurso personalizado que implementou, utilize a `GET` operação no seu tipo de recurso.

```http
GET https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users?api-version=2018-09-01-preview
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
subID=$(az account show --query id --output tsv)
requestURI="https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users?api-version=2018-09-01-preview"
az rest --method get --uri $requestURI
```

Recebe a resposta:

```json
{
  "value": [
    {
      "id": "/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/santa",
      "name": "santa",
      "properties": {
        "FullName": "Santa Claus",
        "Location": "NorthPole",
        "provisioningState": "Succeeded"
      },
      "resourceGroup": "<rg-name>",
      "type": "Microsoft.CustomProviders/resourceProviders/users"
    }
  ]
}
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

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

---

## <a name="call-action"></a>Ação de chamada

O seu fornecedor personalizado também tem uma ação chamada `ping` . O código que processa o pedido é implementado na aplicação de função. A `ping` ação responde com uma saudação.

Para enviar um `ping` pedido, utilize a `POST` operação no seu fornecedor personalizado.

```http
POST https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/ping?api-version=2018-09-01-preview
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
pingURI="https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/ping?api-version=2018-09-01-preview"
az rest --method post --uri $pingURI
```

Recebe a resposta:

```json
{
  "message": "hello <function-name>.azurewebsites.net",
  "pingcontent": {
    "source": "<function-name>.azurewebsites.net"
  }
}
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

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

---

## <a name="create-a-resource-type"></a>Criar um tipo de recurso

Para criar o tipo de recurso personalizado, pode implementar o recurso num modelo. Esta abordagem é mostrada no modelo que implementou neste arranque rápido. Também pode enviar um `PUT` pedido para o tipo de recurso.

```http
PUT https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/<resource-name>?api-version=2018-09-01-preview

{"properties":{"FullName": "Test User", "Location": "Earth"}}
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
addURI="https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users/testuser?api-version=2018-09-01-preview"
az rest --method put --uri $addURI --body "{'properties':{'FullName': 'Test User', 'Location': 'Earth'}}"
```

Recebe a resposta:

```json
{
  "id": "/subscriptions/<sub-ID>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/testuser",
  "name": "testuser",
  "properties": {
    "FullName": "Test User",
    "Location": "Earth",
    "provisioningState": "Succeeded"
  },
  "resourceGroup": "<rg-name>",
  "type": "Microsoft.CustomProviders/resourceProviders/users"
}
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

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

---

## <a name="custom-resource-provider-commands"></a>Comandos personalizados do fornecedor de recursos

Utilize os [comandos de fornecedores personalizados](/cli/azure/ext/custom-providers/custom-providers/resource-provider) para trabalhar com o seu fornecedor de recursos personalizados.

### <a name="list-custom-resource-providers"></a>Listar fornecedores de recursos personalizados

Listar todos os fornecedores de recursos personalizados numa subscrição. O padrão lista os fornecedores de recursos personalizados para a subscrição atual, ou pode especificar o `--subscription` parâmetro. Para listar um grupo de recursos, utilize o `--resource-group` parâmetro.

```azurecli-interactive
az custom-providers resource-provider list --subscription $subID
```

```json
[
  {
    "actions": [
      {
        "endpoint": "https://<provider-name>.azurewebsites.net/api/{requestPath}",
        "name": "ping",
        "routingType": "Proxy"
      }
    ],
    "id": "/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceproviders/<provider-name>",
    "location": "eastus",
    "name": "<provider-name>",
    "provisioningState": "Succeeded",
    "resourceGroup": "<rg-name>",
    "resourceTypes": [
      {
        "endpoint": "https://<provider-name>.azurewebsites.net/api/{requestPath}",
        "name": "users",
        "routingType": "Proxy, Cache"
      }
    ],
    "tags": {},
    "type": "Microsoft.CustomProviders/resourceproviders",
    "validations": null
  }
]
```

### <a name="show-the-properties"></a>Mostrar as propriedades

Mostre as propriedades de um fornecedor de recursos personalizado. O formato de saída assemelha-se à `list` saída.

```azurecli-interactive
az custom-providers resource-provider show --resource-group $rgName --name $funcName
```

### <a name="create-a-new-resource"></a>Criar um novo recurso

Utilize o `create` comando para criar ou atualizar um fornecedor de recursos personalizado. Este exemplo atualiza o `actions` e `resourceTypes` .

```azurecli-interactive
az custom-providers resource-provider create --resource-group $rgName --name $funcName \
--action name=ping endpoint=https://myTestSite.azurewebsites.net/api/{requestPath} routing_type=Proxy \
--resource-type name=users endpoint=https://myTestSite.azurewebsites.net/api{requestPath} routing_type="Proxy, Cache"
```

```json
"actions": [
  {
    "endpoint": "https://myTestSite.azurewebsites.net/api/{requestPath}",
    "name": "ping",
    "routingType": "Proxy"
  }
],

"resourceTypes": [
  {
    "endpoint": "https://myTestSite.azurewebsites.net/api{requestPath}",
    "name": "users",
    "routingType": "Proxy, Cache"
  }
],
```

### <a name="update-the-providers-tags"></a>Atualizar as etiquetas do fornecedor

O `update` comando apenas atualiza as etiquetas para um fornecedor de recursos personalizado. No portal Azure, o serviço de aplicações do fornecedor de recursos personalizados mostra a etiqueta.

```azurecli-interactive
az custom-providers resource-provider update --resource-group $rgName --name $funcName --tags new=tag
```

```json
"tags": {
  "new": "tag"
},
```

### <a name="delete-a-custom-resource-provider"></a>Excluir um fornecedor de recursos personalizado

O `delete` comando solicita-lhe e elimina apenas o fornecedor de recursos personalizado. A conta de armazenamento, o serviço de aplicações e o plano de serviço de aplicações não são eliminados. Depois de o fornecedor ser apagado, é devolvido a um pedido de comando.

```azurecli-interactive
az custom-providers resource-provider delete --resource-group $rgName --name $funcName
```

## <a name="next-steps"></a>Passos seguintes

Para uma introdução a fornecedores personalizados, consulte o seguinte artigo:

> [!div class="nextstepaction"]
> [Visão geral do pré-visualização dos fornecedores personalizados Azure](overview.md)
