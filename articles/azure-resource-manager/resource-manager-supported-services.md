---
title: Provedores de recursos do Azure e tipos de recursos | Microsoft Docs
description: Descreve os provedores de recursos que oferecem suporte ao Gerenciador de recursos, seus esquemas e versões de API disponíveis e as regiões que podem hospedar os recursos.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: 3c7a6fe4-371a-40da-9ebe-b574f583305b
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2019
ms.author: tomfitz
ms.openlocfilehash: 2cbc8843d41b760c52b9ca5ccfb6d940bd454136
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70164810"
---
# <a name="azure-resource-providers-and-types"></a>Tipos e provedores de recursos do Azure

Ao implantar recursos, com frequência você precisa recuperar informações sobre os provedores de recursos e os tipos. Por exemplo, se você quiser armazenar chaves e segredos, trabalhe com o provedor de recursos Microsoft. keyvault. Este provedor de recursos oferece um tipo de recurso chamado cofres para criar o cofre de chaves.

O nome de um tipo de recurso está no formato: **{resource-provider}/{resource-type}** . O tipo de recurso para um cofre de chaves é **Microsoft. keyvault/cofres**.

Neste artigo, vai aprender a:

* Exibir todos os provedores de recursos no Azure
* Verificar o status do registro de um provedor de recursos
* Registrar um provedor de recursos
* Exibir tipos de recursos para um provedor de recursos
* Exibir locais válidos para um tipo de recurso
* Exibir versões de API válidas para um tipo de recurso

Você pode executar essas etapas por meio do portal do Azure, Azure PowerShell ou CLI do Azure.

Para obter uma lista que mapeia os provedores de recursos para os serviços do Azure, consulte [provedores de recursos para serviços do Azure](azure-services-resource-providers.md).

## <a name="azure-portal"></a>Portal do Azure

Para ver todos os provedores de recursos e o status de registro para sua assinatura:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **Todos os serviços**.

    ![selecionar assinaturas](./media/resource-manager-supported-services/select-subscriptions.png)
3. Na caixa **todos os serviços** , insira **assinatura**e, em seguida, selecione **assinaturas**.
4. Selecione a assinatura na lista assinatura para exibir.
5. Selecione **provedores de recursos** e exiba a lista de provedores de recursos disponíveis.

    ![Mostrar provedores de recursos](./media/resource-manager-supported-services/show-resource-providers.png)

6. O registro de um provedor de recursos configura sua assinatura para trabalhar com o provedor de recursos. O âmbito de registo é sempre a subscrição. Por padrão, muitos provedores de recursos são automaticamente registrados. No entanto, talvez seja necessário registrar manualmente alguns provedores de recursos. Para registrar um provedor de recursos, você deve ter permissão para realizar `/register/action` a operação para o provedor de recursos. Esta operação está incluída nas funções de Contribuinte e Proprietário. Para registrar um provedor de recursos, selecione **registrar**. Na captura de tela anterior, o link **registrar** é realçado para **Microsoft. Blueprint**.

    Não é possível cancelar o registro de um provedor de recursos quando você ainda tem tipos de recursos desse provedor de recursos em sua assinatura.

Para ver informações para um provedor de recursos específico:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **Todos os serviços**.

    ![selecionar todos os serviços](./media/resource-manager-supported-services/more-services.png)

3. Na caixa **todos os serviços** , insira **Gerenciador de recursos**e, em seguida, selecione **Gerenciador de recursos**.
4. Expanda **provedores** selecionando a seta para a direita.

    ![Selecionar provedores](./media/resource-manager-supported-services/select-providers.png)

5. Expanda um provedor de recursos e um tipo de recurso que você deseja exibir.

    ![Selecionar tipo de recurso](./media/resource-manager-supported-services/select-resource-type.png)

6. O Gerenciador de recursos tem suporte em todas as regiões, mas os recursos implantados podem não ter suporte em todas as regiões. Além disso, pode haver limitações na sua assinatura que impeçam o uso de algumas regiões que dão suporte ao recurso. O Gerenciador de recursos exibe locais válidos para o tipo de recurso.

    ![Mostrar locais](./media/resource-manager-supported-services/show-locations.png)

7. A versão da API corresponde a uma versão das operações da API REST liberadas pelo provedor de recursos. Como um provedor de recursos habilita novos recursos, ele libera uma nova versão da API REST. O Gerenciador de recursos exibe versões de API válidas para o tipo de recurso.

    ![Mostrar versões de API](./media/resource-manager-supported-services/show-api-versions.png)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para ver todos os provedores de recursos no Azure e o status de registro para sua assinatura, use:

```azurepowershell-interactive
Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

Que retorna resultados semelhantes a:

```powershell
ProviderNamespace                RegistrationState
-------------------------------- ------------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

O registro de um provedor de recursos configura sua assinatura para trabalhar com o provedor de recursos. O âmbito de registo é sempre a subscrição. Por padrão, muitos provedores de recursos são automaticamente registrados. No entanto, talvez seja necessário registrar manualmente alguns provedores de recursos. Para registrar um provedor de recursos, você deve ter permissão para realizar `/register/action` a operação para o provedor de recursos. Esta operação está incluída nas funções de Contribuinte e Proprietário.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

Que retorna resultados semelhantes a:

```powershell
ProviderNamespace : Microsoft.Batch
RegistrationState : Registering
ResourceTypes     : {batchAccounts, operations, locations, locations/quotas}
Locations         : {West Europe, East US, East US 2, West US...}
```

Não é possível cancelar o registro de um provedor de recursos quando você ainda tem tipos de recursos desse provedor de recursos em sua assinatura.

Para ver informações de um provedor de recursos específico, use:

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

Que retorna resultados semelhantes a:

```powershell
{ProviderNamespace : Microsoft.Batch
RegistrationState : Registered
ResourceTypes     : {batchAccounts}
Locations         : {West Europe, East US, East US 2, West US...}

...
```

Para ver os tipos de recursos para um provedor de recursos, use:

```azurepowershell-interactive
(Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes.ResourceTypeName
```

Que retorna:

```powershell
batchAccounts
operations
locations
locations/quotas
```

A versão da API corresponde a uma versão das operações da API REST liberadas pelo provedor de recursos. Como um provedor de recursos habilita novos recursos, ele libera uma nova versão da API REST.

Para obter as versões de API disponíveis para um tipo de recurso, use:

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).ApiVersions
```

Que retorna:

```powershell
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

O Gerenciador de recursos tem suporte em todas as regiões, mas os recursos implantados podem não ter suporte em todas as regiões. Além disso, pode haver limitações na sua assinatura que impeçam o uso de algumas regiões que dão suporte ao recurso.

Para obter os locais com suporte para um tipo de recurso, use.

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

Que retorna:

```powershell
West Europe
East US
East US 2
West US
...
```

## <a name="azure-cli"></a>CLI do Azure

Para ver todos os provedores de recursos no Azure e o status de registro para sua assinatura, use:

```azurecli
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

Que retorna resultados semelhantes a:

```azurecli
Provider                         Status
-------------------------------- ----------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

O registro de um provedor de recursos configura sua assinatura para trabalhar com o provedor de recursos. O âmbito de registo é sempre a subscrição. Por padrão, muitos provedores de recursos são automaticamente registrados. No entanto, talvez seja necessário registrar manualmente alguns provedores de recursos. Para registrar um provedor de recursos, você deve ter permissão para realizar `/register/action` a operação para o provedor de recursos. Esta operação está incluída nas funções de Contribuinte e Proprietário.

```azurecli
az provider register --namespace Microsoft.Batch
```

Que retorna uma mensagem informando que o registro está em andamento.

Não é possível cancelar o registro de um provedor de recursos quando você ainda tem tipos de recursos desse provedor de recursos em sua assinatura.

Para ver informações de um provedor de recursos específico, use:

```azurecli
az provider show --namespace Microsoft.Batch
```

Que retorna resultados semelhantes a:

```azurecli
{
    "id": "/subscriptions/####-####/providers/Microsoft.Batch",
    "namespace": "Microsoft.Batch",
    "registrationsState": "Registering",
    "resourceTypes:" [
        ...
    ]
}
```

Para ver os tipos de recursos para um provedor de recursos, use:

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[*].resourceType" --out table
```

Que retorna:

```azurecli
Result
---------------
batchAccounts
operations
locations
locations/quotas
```

A versão da API corresponde a uma versão das operações da API REST liberadas pelo provedor de recursos. Como um provedor de recursos habilita novos recursos, ele libera uma nova versão da API REST.

Para obter as versões de API disponíveis para um tipo de recurso, use:

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].apiVersions | [0]" --out table
```

Que retorna:

```azurecli
Result
---------------
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

O Gerenciador de recursos tem suporte em todas as regiões, mas os recursos implantados podem não ter suporte em todas as regiões. Além disso, pode haver limitações na sua assinatura que impeçam o uso de algumas regiões que dão suporte ao recurso.

Para obter os locais com suporte para um tipo de recurso, use.

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" --out table
```

Que retorna:

```azurecli
Result
---------------
West Europe
East US
East US 2
West US
...
```

## <a name="next-steps"></a>Passos Seguintes

* Para saber mais sobre como criar modelos do Resource Manager, confira [criação de modelos de Azure Resource Manager](resource-group-authoring-templates.md). 
* Para exibir os esquemas de modelo de provedor de recursos, consulte [referência de modelo](/azure/templates/).
* Para obter uma lista que mapeia os provedores de recursos para os serviços do Azure, consulte [provedores de recursos para serviços do Azure](azure-services-resource-providers.md).
* Para exibir as operações de um provedor de recursos, consulte [API REST do Azure](/rest/api/).
