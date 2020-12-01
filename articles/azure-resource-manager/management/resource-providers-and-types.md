---
title: Fornecedores de recursos e tipos de recursos
description: Descreve os fornecedores de recursos que suportam o Gestor de Recursos Azure. Descreve os seus esquemas, versões API disponíveis e as regiões que podem acolher os recursos.
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: a8adbce80d5e8f9ee9df2050d8f43363cbf57dc3
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352104"
---
# <a name="azure-resource-providers-and-types"></a>Tipos e fornecedores de recursos do Azure

Ao utilizar recursos, é frequente que recupere informações sobre os fornecedores e tipos de recursos. Por exemplo, se pretende armazenar chaves e segredos, trabalha com o fornecedor de recursos Microsoft.KeyVault. Este fornecedor de recursos oferece um tipo de recurso denominado cofres para a criação do cofre de chaves.

O nome de um tipo de recurso está no formato: **{resource-provider}/{resource-type}**. O tipo de recurso para um cofre chave é **Microsoft.KeyVault/vaults**.

Neste artigo, vai aprender a:

* Ver todos os fornecedores de recursos em Azure
* Verifique o estado de registo de um fornecedor de recursos
* Registar um fornecedor de recursos
* Ver tipos de recursos para um fornecedor de recursos
* Ver locais válidos para um tipo de recurso
* Ver ver versões API válidas para um tipo de recurso

Pode fazer estes passos através do portal Azure PowerShell ou Azure CLI.

Para obter uma lista que mapeia os fornecedores de recursos para os serviços Azure, consulte [os fornecedores de recursos para os serviços Azure](azure-services-resource-providers.md).

## <a name="register-resource-provider"></a>Registar o fornecedor de recursos

Antes de utilizar um fornecedor de recursos, a sua assinatura Azure deve ser registada para o fornecedor de recursos. O registo configura a sua subscrição para trabalhar com o fornecedor de recursos. Alguns fornecedores de recursos estão registados por padrão. Outros fornecedores de recursos são registados automaticamente quando toma determinadas ações. Por exemplo, quando cria um recurso através do portal, o fornecedor de recursos é normalmente registado para si. Para outros cenários, poderá ter de registar manualmente um fornecedor de recursos.

Este artigo mostra-lhe como verificar o estado de registo de um fornecedor de recursos e registá-lo conforme necessário. Tem de ter autorização para fazer a `/register/action` operação para o fornecedor de recursos. A permissão está incluída nas funções de Contribuinte e Proprietário.

> [!IMPORTANT]
> Só registe um fornecedor de recursos quando estiver pronto para o utilizar. O passo de registo permite-lhe manter os privilégios mínimos dentro da sua subscrição. Um utilizador malicioso não pode usar fornecedores de recursos que não estejam registados.

O seu código de aplicação não deve bloquear a criação de recursos para um fornecedor de recursos que se encontra no estado **de registo.** Quando regista o fornecedor de recursos, a operação é feita individualmente para cada região apoiada. Para criar recursos numa região, o registo só precisa de ser concluído naquela região. Ao não bloquear o fornecedor de recursos no estado de registo, a sua aplicação pode continuar muito mais cedo do que esperar que todas as regiões completem.

Não é possível desregistar um fornecedor de recursos quando ainda tem tipos de recursos desse fornecedor de recursos na sua subscrição.

## <a name="azure-portal"></a>Portal do Azure

Para ver todos os fornecedores de recursos e o estado de registo da sua subscrição:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No menu do portal do Azure, selecione **Todos os serviços**.

    ![selecionar subscrições](./media/resource-providers-and-types/select-all-services.png)

3. Na caixa **de todos os serviços,** **introduza a subscrição** e, em seguida, selecione **Subscrições**.
4. Selecione a subscrição da lista de subscrição para visualizar.
5. Selecione **fornecedores de recursos** e veja a lista de fornecedores de recursos disponíveis.

    ![mostrar fornecedores de recursos](./media/resource-providers-and-types/show-resource-providers.png)

6. Para registar um fornecedor de recursos, **selecione Registar-se.** Na imagem anterior, o link **Register** é destacado para **microsoft.Blueprint**. Para manter os privilégios mínimos na sua subscrição, registe apenas os fornecedores de recursos que está pronto a utilizar.

Para ver informações para um determinado fornecedor de recursos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No menu do portal do Azure, selecione **Todos os serviços**.
3. Na caixa **de todos os serviços,** insira **o explorador de recursos** e, em seguida, selecione **Resource Explorer**.

    ![selecionar Todos os serviços](./media/resource-providers-and-types/select-resource-explorer.png)

4. Expandir **fornecedores** selecionando a seta direita.

    ![Selecione fornecedores](./media/resource-providers-and-types/select-providers.png)

5. Expanda um fornecedor de recursos e um tipo de recurso que pretende visualizar.

    ![Selecione o tipo de recurso](./media/resource-providers-and-types/select-resource-type.png)

6. O Gestor de Recursos é apoiado em todas as regiões, mas os recursos que você implanta podem não ser suportados em todas as regiões. Além disso, pode haver limitações na sua subscrição que o impedem de usar algumas regiões que suportam o recurso. O explorador de recursos apresenta locais válidos para o tipo de recurso.

    ![Mostrar locais](./media/resource-providers-and-types/show-locations.png)

7. A versão API corresponde a uma versão das operações rest API que são lançadas pelo fornecedor de recursos. Como fornecedor de recursos permite novas funcionalidades, lança uma nova versão da API REST. O explorador de recursos apresenta versões API válidas para o tipo de recurso.

    ![Mostrar versões API](./media/resource-providers-and-types/show-api-versions.png)

## <a name="azure-powershell"></a>Azure PowerShell

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

Para consultar todos os fornecedores de recursos registados para a sua subscrição, utilize:

```azurepowershell-interactive
 Get-AzResourceProvider -ListAvailable | Where-Object RegistrationState -eq "Registered" | Select-Object ProviderNamespace, RegistrationState | Sort-Object ProviderNamespace
```

Para manter os privilégios mínimos na sua subscrição, registe apenas os fornecedores de recursos que está pronto a utilizar. Para registar um fornecedor de recursos, utilize:

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

Para obter informações para um determinado fornecedor de recursos, utilize:

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

Para ver os tipos de recursos de um fornecedor de recursos, utilize:

```azurepowershell-interactive
(Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes.ResourceTypeName
```

Que devolve:

```output
batchAccounts
operations
locations
locations/quotas
```

A versão API corresponde a uma versão das operações rest API que são lançadas pelo fornecedor de recursos. Como fornecedor de recursos permite novas funcionalidades, lança uma nova versão da API REST.

Para obter as versões API disponíveis para um tipo de recurso, utilize:

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).ApiVersions
```

Que devolve:

```output
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

O Gestor de Recursos é apoiado em todas as regiões, mas os recursos que você implanta podem não ser suportados em todas as regiões. Além disso, pode haver limitações na sua subscrição que o impedem de usar algumas regiões que suportam o recurso.

Para obter os locais suportados para um tipo de recurso, use.

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

Que devolve:

```output
West Europe
East US
East US 2
West US
...
```

## <a name="azure-cli"></a>CLI do Azure

Para ver todos os fornecedores de recursos em Azure, e o estado de registo da sua subscrição, utilize:

```azurecli-interactive
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

Para consultar todos os fornecedores de recursos registados para a sua subscrição, utilize:

```azurecli-interactive
az provider list --query "sort_by([?registrationState=='Registered'].{Provider:namespace, Status:registrationState}, &Provider)" --out table
```

Para manter os privilégios mínimos na sua subscrição, registe apenas os fornecedores de recursos que está pronto a utilizar. Para registar um fornecedor de recursos, utilize:

```azurecli-interactive
az provider register --namespace Microsoft.Batch
```

Que devolve uma mensagem de que o registo está em andamento.

Para obter informações para um determinado fornecedor de recursos, utilize:

```azurecli-interactive
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

Para ver os tipos de recursos de um fornecedor de recursos, utilize:

```azurecli-interactive
az provider show --namespace Microsoft.Batch --query "resourceTypes[*].resourceType" --out table
```

Que devolve:

```output
Result
---------------
batchAccounts
operations
locations
locations/quotas
```

A versão API corresponde a uma versão das operações rest API que são lançadas pelo fornecedor de recursos. Como fornecedor de recursos permite novas funcionalidades, lança uma nova versão da API REST.

Para obter as versões API disponíveis para um tipo de recurso, utilize:

```azurecli-interactive
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].apiVersions | [0]" --out table
```

Que devolve:

```output
Result
---------------
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

O Gestor de Recursos é apoiado em todas as regiões, mas os recursos que você implanta podem não ser suportados em todas as regiões. Além disso, pode haver limitações na sua subscrição que o impedem de usar algumas regiões que suportam o recurso.

Para obter os locais suportados para um tipo de recurso, use.

```azurecli-interactive
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" --out table
```

Que devolve:

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

* Para saber sobre a criação de modelos de Gestor de Recursos, consulte os modelos do [Gestor de Recursos Azure.](../templates/template-syntax.md) 
* Para ver os esquemas de modelo do fornecedor de recursos, consulte [a referência do modelo](/azure/templates/).
* Para obter uma lista que mapeia os fornecedores de recursos para os serviços Azure, consulte [os fornecedores de recursos para os serviços Azure](azure-services-resource-providers.md).
* Para visualizar as operações de um fornecedor de recursos, consulte [a AZure REST API](/rest/api/).
