---
title: Fornecedores de recursos e tipos de recursos
description: Descreve os fornecedores de recursos que suportam o Gestor de Recursos, os seus schemas e versões API disponíveis, e as regiões que podem acolher os recursos.
ms.topic: conceptual
ms.date: 08/29/2019
ms.openlocfilehash: 82b8251006a1a2d4edd198eca843489d3720f8d6
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2020
ms.locfileid: "78942080"
---
# <a name="azure-resource-providers-and-types"></a>Fornecedores e tipos de recursos Azure

Ao implementar recursos, precisa frequentemente de obter informações sobre os fornecedores e tipos de recursos. Por exemplo, se quiser armazenar chaves e segredos, trabalha com o fornecedor de recursos Microsoft.KeyVault. Este fornecedor de recursos oferece um tipo de recurso chamado cofres para criar o cofre chave.

O nome de um tipo de recurso está no formato: **{resource-provider}/{resource-type}** . O tipo de recurso para um cofre chave é **Microsoft.KeyVault/vaults**.

Neste artigo, vai aprender a:

* Ver todos os fornecedores de recursos em Azure
* Verifique o estado de registo de um fornecedor de recursos
* Registe um fornecedor de recursos
* Ver tipos de recursos para um fornecedor de recursos
* Ver locais válidos para um tipo de recurso
* Ver versões API válidas para um tipo de recurso

Pode fazer estes passos através do portal Azure, Azure PowerShell ou Azure CLI.

Para uma lista que mapeie os fornecedores de recursos para os serviços Azure, consulte os fornecedores de [recursos para os serviços Azure.](azure-services-resource-providers.md)

## <a name="azure-portal"></a>Portal do Azure

Para ver todos os fornecedores de recursos e o estado de registo da sua subscrição:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No menu do portal Azure, selecione **Todos os serviços.**

    ![selecionar subscrições](./media/resource-providers-and-types/select-all-services.png)

3. Na caixa **de todos os serviços,** introduza **a subscrição**, e, em seguida, selecione **Assinaturas**.
4. Selecione a subscrição da lista de subscrição para visualizar.
5. Selecione **fornecedores** de Recursos e veja a lista de fornecedores de recursos disponíveis.

    ![mostrar fornecedores de recursos](./media/resource-providers-and-types/show-resource-providers.png)

6. Registar um fornecedor de recursos configura a sua subscrição para trabalhar com o fornecedor de recursos. O âmbito de registo é sempre a subscrição. Por padrão, muitos fornecedores de recursos são automaticamente registados. No entanto, poderá ter de registar manualmente alguns fornecedores de recursos. Para registar um fornecedor de recursos, deve ter permissão para fazer a operação `/register/action` para o fornecedor de recursos. Esta operação está incluída nas funções de Contribuinte e Proprietário. Para registar um fornecedor de recursos, selecione **Register**. Na imagem anterior, o link **Register** é destacado para **Microsoft.Blueprint**.

    Não pode desregistar um fornecedor de recursos quando ainda tem tipos de recursos desse fornecedor de recursos na sua subscrição.

Para ver informações para um determinado fornecedor de recursos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No menu do portal Azure, selecione **Todos os serviços.**
3. Na caixa **de todos os serviços,** introduza o explorador de **recursos,** e, em seguida, selecione **Resource Explorer**.

    ![Selecione Todos os serviços](./media/resource-providers-and-types/select-resource-explorer.png)

4. Expandir **fornecedores** selecionando a seta direita.

    ![Selecione fornecedores](./media/resource-providers-and-types/select-providers.png)

5. Expanda um fornecedor de recursos e um tipo de recurso que pretende ver.

    ![Selecione o tipo de recurso](./media/resource-providers-and-types/select-resource-type.png)

6. O Gestor de Recursos é apoiado em todas as regiões, mas os recursos que você implanta podem não ser apoiados em todas as regiões. Além disso, pode haver limitações na sua subscrição que o impedem de usar algumas regiões que suportam o recurso. O explorador de recursos exibe locais válidos para o tipo de recurso.

    ![Mostrar locais](./media/resource-providers-and-types/show-locations.png)

7. A versão API corresponde a uma versão das operações REST API que são lançadas pelo fornecedor de recursos. Como um fornecedor de recursos permite novas funcionalidades, lança uma nova versão da API REST. O explorador de recursos apresenta versões API válidas para o tipo de recurso.

    ![Mostrar versões API](./media/resource-providers-and-types/show-api-versions.png)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Para ver todos os fornecedores de recursos em Azure, e o estado de registo da sua subscrição, utilize:

```azurepowershell-interactive
Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

Que devolve resultados semelhantes a:

```output
ProviderNamespace                RegistrationState
-------------------------------- ------------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

Registar um fornecedor de recursos configura a sua subscrição para trabalhar com o fornecedor de recursos. O âmbito de registo é sempre a subscrição. Por padrão, muitos fornecedores de recursos são automaticamente registados. No entanto, poderá ter de registar manualmente alguns fornecedores de recursos. Para registar um fornecedor de recursos, deve ter permissão para fazer a operação `/register/action` para o fornecedor de recursos. Esta operação está incluída nas funções de Contribuinte e Proprietário.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

Que devolve resultados semelhantes a:

```output
ProviderNamespace : Microsoft.Batch
RegistrationState : Registering
ResourceTypes     : {batchAccounts, operations, locations, locations/quotas}
Locations         : {West Europe, East US, East US 2, West US...}
```

Não pode desregistar um fornecedor de recursos quando ainda tem tipos de recursos desse fornecedor de recursos na sua subscrição.

Para ver informações para um determinado fornecedor de recursos, utilize:

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

Que devolve resultados semelhantes a:

```output
{ProviderNamespace : Microsoft.Batch
RegistrationState : Registered
ResourceTypes     : {batchAccounts}
Locations         : {West Europe, East US, East US 2, West US...}

...
```

Para ver os tipos de recursos para um fornecedor de recursos, utilize:

```azurepowershell-interactive
(Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes.ResourceTypeName
```

Que retorna:

```output
batchAccounts
operations
locations
locations/quotas
```

A versão API corresponde a uma versão das operações REST API que são lançadas pelo fornecedor de recursos. Como um fornecedor de recursos permite novas funcionalidades, lança uma nova versão da API REST.

Para obter as versões API disponíveis para um tipo de recurso, use:

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).ApiVersions
```

Que retorna:

```output
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

O Gestor de Recursos é apoiado em todas as regiões, mas os recursos que você implanta podem não ser apoiados em todas as regiões. Além disso, pode haver limitações na sua subscrição que o impedem de usar algumas regiões que suportam o recurso.

Para obter os locais suportados para um tipo de recurso, use.

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

Que retorna:

```output
West Europe
East US
East US 2
West US
...
```

## <a name="azure-cli"></a>CLI do Azure

Para ver todos os fornecedores de recursos em Azure, e o estado de registo da sua subscrição, utilize:

```azurecli
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

Que devolve resultados semelhantes a:

```output
Provider                         Status
-------------------------------- ----------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

Registar um fornecedor de recursos configura a sua subscrição para trabalhar com o fornecedor de recursos. O âmbito de registo é sempre a subscrição. Por padrão, muitos fornecedores de recursos são automaticamente registados. No entanto, poderá ter de registar manualmente alguns fornecedores de recursos. Para registar um fornecedor de recursos, deve ter permissão para fazer a operação `/register/action` para o fornecedor de recursos. Esta operação está incluída nas funções de Contribuinte e Proprietário.

```azurecli
az provider register --namespace Microsoft.Batch
```

O que devolve uma mensagem de que o registo está em curso.

Não pode desregistar um fornecedor de recursos quando ainda tem tipos de recursos desse fornecedor de recursos na sua subscrição.

Para ver informações para um determinado fornecedor de recursos, utilize:

```azurecli
az provider show --namespace Microsoft.Batch
```

Que devolve resultados semelhantes a:

```output
{
    "id": "/subscriptions/####-####/providers/Microsoft.Batch",
    "namespace": "Microsoft.Batch",
    "registrationsState": "Registering",
    "resourceTypes:" [
        ...
    ]
}
```

Para ver os tipos de recursos para um fornecedor de recursos, utilize:

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[*].resourceType" --out table
```

Que retorna:

```output
Result
---------------
batchAccounts
operations
locations
locations/quotas
```

A versão API corresponde a uma versão das operações REST API que são lançadas pelo fornecedor de recursos. Como um fornecedor de recursos permite novas funcionalidades, lança uma nova versão da API REST.

Para obter as versões API disponíveis para um tipo de recurso, use:

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].apiVersions | [0]" --out table
```

Que retorna:

```output
Result
---------------
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

O Gestor de Recursos é apoiado em todas as regiões, mas os recursos que você implanta podem não ser apoiados em todas as regiões. Além disso, pode haver limitações na sua subscrição que o impedem de usar algumas regiões que suportam o recurso.

Para obter os locais suportados para um tipo de recurso, use.

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" --out table
```

Que retorna:

```output
Result
---------------
West Europe
East US
East US 2
West US
...
```

## <a name="next-steps"></a>Passos seguintes

* Para aprender sobre a criação de modelos de Gestor de Recursos, consulte [os modelos de Gestor de Recursos Do Azure.](../templates/template-syntax.md) 
* Para ver os esquemas do modelo do fornecedor de recursos, consulte a referência do [modelo](/azure/templates/).
* Para uma lista que mapeie os fornecedores de recursos para os serviços Azure, consulte os fornecedores de [recursos para os serviços Azure.](azure-services-resource-providers.md)
* Para visualizar as operações para um fornecedor de recursos, consulte [a API Do REST Azure](/rest/api/).
