---
title: Erros de registo do fornecedor de recursos
description: Descreve como resolver os erros de registo do fornecedor de recursos Azure ao implementar recursos com o Gestor de Recursos Azure.
ms.topic: troubleshooting
ms.date: 02/15/2019
ms.openlocfilehash: a9182be53cc91240a62ab201efc53d674f7cf427
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79273777"
---
# <a name="resolve-errors-for-resource-provider-registration"></a>Resolver erros de registo do fornecedor de recursos

Este artigo descreve os erros que poderá encontrar ao utilizar um fornecedor de recursos que ainda não tenha utilizado na sua subscrição.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="symptom"></a>Sintoma

Durante a implantação de recursos, poderá receber o seguinte código de erro e a mensagem:

```
Code: NoRegisteredProviderFound
Message: No registered resource provider found for location {location}
and API version {api-version} for type {resource-type}.
```

Em alternativa, poderá receber uma mensagem semelhante que indica:

```
Code: MissingSubscriptionRegistration
Message: The subscription is not registered to use namespace {resource-provider-namespace}
```

A mensagem de erro deverá dar-lhe sugestões para as localizações suportadas e versões de API. Pode alterar o modelo para um dos valores sugeridos. A maioria dos fornecedores são registrados automaticamente pelo portal do Azure ou a interface de linha de comandos que está a utilizar, mas não todos. Se ainda não utilizou um fornecedor de recursos específico antes, se pretender registar esse fornecedor.

Ou, ao desativar o encerramento automático de máquinas virtuais, pode receber uma mensagem de erro semelhante a:

```
Code: AuthorizationFailed
Message: The client '<identifier>' with object id '<identifier>' does not have authorization to perform action 'Microsoft.Compute/virtualMachines/read' over scope ...
```

## <a name="cause"></a>Causa

Recebe estes erros por uma destas razões:

* O fornecedor de recursos necessário não foi registado para a sua subscrição
* Versão de API não suportada para o tipo de recurso
* Localização não é suportada para o tipo de recurso
* Para o encerramento automático de VMs, o fornecedor de recursos Microsoft.DevTestLab deve ser registado.

## <a name="solution-1---powershell"></a>Solução 1 - PowerShell

Para a PowerShell, utilize **o Get-AzResourceProvider** para ver o seu estado de registo.

```powershell
Get-AzResourceProvider -ListAvailable
```

Para registar um fornecedor, utilize o **Register-AzResourceProvider** e forneça o nome do fornecedor de recursos que pretende registar.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Cdn
```

Para obter as localizações suportadas para um determinado tipo de recurso, utilize:

```powershell
((Get-AzResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

Para obter as versões de API suportadas para um determinado tipo de recurso, utilize:

```powershell
((Get-AzResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
```

## <a name="solution-2---azure-cli"></a>Solução 2 - CLI do Azure

Para ver se o fornecedor está registado, utilize o comando `az provider list`.

```azurecli-interactive
az provider list
```

Para registar um fornecedor de recursos, utilize o comando `az provider register` e especifique o *espaço de nome* para se registar.

```azurecli-interactive
az provider register --namespace Microsoft.Cdn
```

Para ver as localizações suportadas e versões de API para um tipo de recurso, utilize:

```azurecli-interactive
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

## <a name="solution-3---azure-portal"></a>Solução de 3 - portal do Azure

Pode ver o estado de registo e registe-se de um espaço de nomes do fornecedor de recursos através do portal.

1. A partir do portal, selecione **Todos os serviços.**

   ![Selecione todos os serviços](./media/error-register-resource-provider/select-all-services.png)

1. Selecione **Subscrições**.

   ![Selecionar subscrições](./media/error-register-resource-provider/select-subscriptions.png)

1. Na lista de subscrições, selecione a subscrição que pretende utilizar para registar o fornecedor de recursos.

   ![Selecione a subscrição para registar o fornecedor de recursos](./media/error-register-resource-provider/select-subscription-to-register.png)

1. Para a sua subscrição, selecione **Fornecedores**de Recursos .

   ![Selecionar fornecedores de recursos](./media/error-register-resource-provider/select-resource-provider.png)

1. Veja a lista de fornecedores de recursos e, se necessário, selecione o link **Register** para registar o fornecedor de recursos do tipo que está a tentar implementar.

   ![Fornecedores de recursos de lista](./media/error-register-resource-provider/list-resource-providers.png)
