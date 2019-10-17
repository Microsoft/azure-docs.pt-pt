---
title: Erros de registro do provedor de recursos do Azure | Microsoft Docs
description: Descreve como resolver erros de registro do provedor de recursos do Azure ao implantar recursos com o Azure Resource Manager.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 02/15/2019
ms.author: tomfitz
ms.openlocfilehash: fcdcfdfe736f29f18ea2dc240a66fd7fa6bc404b
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390268"
---
# <a name="resolve-errors-for-resource-provider-registration"></a>Resolver erros de registro do provedor de recursos

Este artigo descreve os erros que você pode encontrar ao usar um provedor de recursos que você não usou anteriormente em sua assinatura.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="symptom"></a>Sintoma

Ao implantar o recurso, você pode receber o seguinte código de erro e mensagem:

```
Code: NoRegisteredProviderFound
Message: No registered resource provider found for location {location}
and API version {api-version} for type {resource-type}.
```

Ou, você pode receber uma mensagem semelhante que diz:

```
Code: MissingSubscriptionRegistration
Message: The subscription is not registered to use namespace {resource-provider-namespace}
```

A mensagem de erro deve fornecer sugestões para os locais com suporte e as versões de API. Você pode alterar seu modelo para um dos valores sugeridos. A maioria dos provedores são registrados automaticamente pelo portal do Azure ou pela interface de linha de comando que você está usando, mas nem todos. Se você ainda não usou um provedor de recursos específico antes, talvez seja necessário registrar esse provedor.

Ou, ao desabilitar o desligamento automático para máquinas virtuais, você pode receber uma mensagem de erro semelhante a:

```
Code: AuthorizationFailed
Message: The client '<identifier>' with object id '<identifier>' does not have authorization to perform action 'Microsoft.Compute/virtualMachines/read' over scope ...
```

## <a name="cause"></a>Causa

Você recebe esses erros por um destes motivos:

* O provedor de recursos necessário não foi registrado para sua assinatura
* Versão de API sem suporte para o tipo de recurso
* Local sem suporte para o tipo de recurso
* Para o desligamento automático de VMs, o provedor de recursos Microsoft. DevTestLab deve ser registrado.

## <a name="solution-1---powershell"></a>Solução 1-PowerShell

Para o PowerShell, use **Get-AzResourceProvider** para ver seu status de registro.

```powershell
Get-AzResourceProvider -ListAvailable
```

Para registrar um provedor, use **Register-AzResourceProvider** e forneça o nome do provedor de recursos que você deseja registrar.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Cdn
```

Para obter os locais com suporte para um determinado tipo de recurso, use:

```powershell
((Get-AzResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

Para obter as versões de API com suporte para um determinado tipo de recurso, use:

```powershell
((Get-AzResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
```

## <a name="solution-2---azure-cli"></a>Solução 2-CLI do Azure

Para ver se o provedor está registrado, use o comando `az provider list`.

```azurecli-interactive
az provider list
```

Para registrar um provedor de recursos, use o comando `az provider register` e especifique o *namespace* a ser registrado.

```azurecli-interactive
az provider register --namespace Microsoft.Cdn
```

Para ver os locais com suporte e as versões de API para um tipo de recurso, use:

```azurecli-interactive
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

## <a name="solution-3---azure-portal"></a>Solução 3-portal do Azure

Você pode ver o status do registro e registrar um namespace do provedor de recursos por meio do Portal.

1. No portal, selecione **todos os serviços**.

   ![Selecionar todos os serviços](./media/resource-manager-register-provider-errors/select-all-services.png)

1. Selecione **Subscrições**.

   ![Selecionar assinaturas](./media/resource-manager-register-provider-errors/select-subscriptions.png)

1. Na lista de assinaturas, selecione a assinatura que você deseja usar para registrar o provedor de recursos.

   ![Selecione a assinatura para registrar o provedor de recursos](./media/resource-manager-register-provider-errors/select-subscription-to-register.png)

1. Para sua assinatura, selecione **provedores de recursos**.

   ![Selecionar provedores de recursos](./media/resource-manager-register-provider-errors/select-resource-provider.png)

1. Examine a lista de provedores de recursos e, se necessário, selecione o link **registrar** para registrar o provedor de recursos do tipo que você está tentando implantar.

   ![Listar provedores de recursos](./media/resource-manager-register-provider-errors/list-resource-providers.png)
