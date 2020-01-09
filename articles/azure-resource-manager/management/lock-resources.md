---
title: Bloquear recursos para evitar alterações
description: Impedir que os usuários atualizem ou excluam recursos críticos do Azure aplicando um bloqueio para todos os usuários e funções.
ms.topic: conceptual
ms.date: 05/14/2019
ms.openlocfilehash: b7c6c7980f12e7f9015f4504f461733100b14ea8
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75644363"
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>Bloquear recursos para evitar alterações inesperadas

Como administrador, pode precisar de bloquear uma subscrição, um grupo de recursos ou recursos, para impedir que outros utilizadores na sua organização eliminem ou modifiquem acidentalmente recursos importantes. Pode definir o nível do bloqueio para **CanNotDelete** ou **ReadOnly**. No portal, os bloqueios são chamados **excluir** e **somente leitura** respectivamente.

* **CanNotDelete** significa que os usuários autorizados ainda podem ler e modificar um recurso, mas não podem excluir o recurso. 
* **ReadOnly** significa que os usuários autorizados podem ler um recurso, mas não podem excluir nem atualizar o recurso. A aplicação desse bloqueio é semelhante à restrição de todos os usuários autorizados às permissões concedidas pela função **leitor** . 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="how-locks-are-applied"></a>Como os bloqueios são aplicados

Quando você aplica um bloqueio a um escopo pai, todos os recursos dentro desse escopo herdam o mesmo bloqueio. Até mesmo os recursos adicionados posteriormente herdam o bloqueio do pai. O bloqueio mais restritivo na herança tem precedência.

Ao contrário do controlo de acesso baseado em funções, pode utilizar a gestão de bloqueios para aplicar uma restrição a todos os utilizadores e a todas as funções. Para saber mais sobre como definir permissões para usuários e funções, consulte [controle de acesso baseado em função do Azure](../../role-based-access-control/role-assignments-portal.md).

Os bloqueios do Resource Manager aplicam-se apenas a operações que ocorrem no painel de gestão, o que consiste em operações enviadas para `https://management.azure.com`. Os bloqueios não restringem a forma como os recursos desempenham as suas próprias funções. As alterações dos recursos são restritas, mas as operações dos recursos não são restritas. Por exemplo, um bloqueio somente leitura em um banco de dados SQL impede que você exclua ou modifique o banco de dados. Ele não impede que você crie, atualize ou exclua dados no banco de dado. As transações de dados são permitidas porque essas operações não são enviadas para `https://management.azure.com`.

A aplicação de **ReadOnly** pode levar a resultados inesperados porque algumas operações que não parecem modificar o recurso realmente exigem ações bloqueadas pelo bloqueio. O bloqueio **ReadOnly** pode ser aplicado ao recurso ou ao grupo de recursos que contém o recurso. Alguns exemplos comuns das operações que são bloqueadas por um bloqueio **somente leitura** são:

* Um bloqueio **ReadOnly** em uma conta de armazenamento impede que todos os usuários listem as chaves. A operação de listar chaves é processada através de um pedido POST porque as chaves devolvidas estão disponíveis para operações de escrita.

* Um bloqueio **ReadOnly** em um recurso do serviço de aplicativo impede que o Visual Studio Gerenciador de servidores exiba arquivos para o recurso, pois essa interação requer acesso de gravação.

* Um bloqueio **ReadOnly** em um grupo de recursos que contém uma máquina virtual impede que todos os usuários iniciem ou reiniciem a máquina virtual. Essas operações exigem uma solicitação POST.

## <a name="who-can-create-or-delete-locks"></a>Quem pode criar ou excluir bloqueios
Para criar ou excluir bloqueios de gerenciamento, você deve ter acesso a `Microsoft.Authorization/*` ou `Microsoft.Authorization/locks/*` ações. Das funções incorporadas, apenas **Proprietário** e **Administrador de Acesso dos Utilizadores** têm acesso a essas ações.

## <a name="managed-applications-and-locks"></a>Aplicativos e bloqueios gerenciados

Alguns serviços do Azure, como Azure Databricks, usam [aplicativos gerenciados](../managed-applications/overview.md) para implementar o serviço. Nesse caso, o serviço cria dois grupos de recursos. Um grupo de recursos contém uma visão geral do serviço e não está bloqueado. O outro grupo de recursos contém a infraestrutura para o serviço e está bloqueado.

Se você tentar excluir o grupo de recursos de infraestrutura, receberá um erro informando que o grupo de recursos está bloqueado. Se você tentar excluir o bloqueio para o grupo de recursos de infraestrutura, receberá um erro informando que o bloqueio não pode ser excluído porque ele é de propriedade de um aplicativo de sistema.

Em vez disso, exclua o serviço, que também exclui o grupo de recursos de infraestrutura.

Para aplicativos gerenciados, selecione o serviço que você implantou.

![Selecionar serviço](./media/lock-resources/select-service.png)

Observe que o serviço inclui um link para um **grupo de recursos gerenciado**. Esse grupo de recursos mantém a infraestrutura e está bloqueado. Ele não pode ser excluído diretamente.

![Mostrar grupo gerenciado](./media/lock-resources/show-managed-group.png)

Para excluir tudo para o serviço, incluindo o grupo de recursos de infraestrutura bloqueado, selecione **excluir** para o serviço.

![Excluir serviço](./media/lock-resources/delete-service.png)

## <a name="portal"></a>Portal
[!INCLUDE [resource-manager-lock-resources](../../../includes/resource-manager-lock-resources.md)]

## <a name="template"></a>Modelo

Ao usar um modelo do Resource Manager para implantar um bloqueio, você usa valores diferentes para o nome e tipo, dependendo do escopo do bloqueio.

Ao aplicar um bloqueio a um **recurso**, use os seguintes formatos:

* nome-`{resourceName}/Microsoft.Authorization/{lockName}`
* `{resourceProviderNamespace}/{resourceType}/providers/locks` de tipo

Ao aplicar um bloqueio a um **grupo de recursos** ou **assinatura**, use os seguintes formatos:

* nome-`{lockName}`
* `Microsoft.Authorization/locks` de tipo

O exemplo a seguir mostra um modelo que cria um plano de serviço de aplicativo, um site da Web e um bloqueio no site. O tipo de recurso do bloqueio é o tipo de recurso do recurso a ser bloqueado e **/Providers/Locks**. O nome do bloqueio é criado concatenando o nome do recurso com **/Microsoft.Authorization/** e o nome do bloqueio.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "hostingPlanName": {
            "type": "string"
        }
    },
    "variables": {
        "siteName": "[concat('ExampleSite', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "apiVersion": "2016-09-01",
            "type": "Microsoft.Web/serverfarms",
            "name": "[parameters('hostingPlanName')]",
            "location": "[resourceGroup().location]",
            "sku": {
                "tier": "Free",
                "name": "f1",
                "capacity": 0
            },
            "properties": {
                "targetWorkerCount": 1
            }
        },
        {
            "apiVersion": "2016-08-01",
            "name": "[variables('siteName')]",
            "type": "Microsoft.Web/sites",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
            ],
            "properties": {
                "serverFarmId": "[parameters('hostingPlanName')]"
            }
        },
        {
            "type": "Microsoft.Web/sites/providers/locks",
            "apiVersion": "2016-09-01",
            "name": "[concat(variables('siteName'), '/Microsoft.Authorization/siteLock')]",
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', variables('siteName'))]"
            ],
            "properties": {
                "level": "CanNotDelete",
                "notes": "Site should not be deleted."
            }
        }
    ]
}
```

Para obter um exemplo de como definir um bloqueio em um grupo de recursos, consulte [criar um grupo de recursos e bloqueá-lo](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-rg-lock-role-assignment).

## <a name="powershell"></a>PowerShell
Você bloqueia os recursos implantados com Azure PowerShell usando o comando [New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock) .

Para bloquear um recurso, forneça o nome do recurso, seu tipo de recurso e seu nome de grupo de recursos.

```azurepowershell-interactive
New-AzResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Para bloquear um grupo de recursos, forneça o nome do grupo de recursos.

```azurepowershell-interactive
New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName exampleresourcegroup
```

Para obter informações sobre um bloqueio, use [Get-AzResourceLock](/powershell/module/az.resources/get-azresourcelock). Para obter todos os bloqueios em sua assinatura, use:

```azurepowershell-interactive
Get-AzResourceLock
```

Para obter todos os bloqueios de um recurso, use:

```azurepowershell-interactive
Get-AzResourceLock -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Para obter todos os bloqueios para um grupo de recursos, use:

```azurepowershell-interactive
Get-AzResourceLock -ResourceGroupName exampleresourcegroup
```

Para excluir um bloqueio, use:

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName exampleresourcegroup -ResourceName examplesite -ResourceType Microsoft.Web/sites).LockId
Remove-AzResourceLock -LockId $lockId
```

## <a name="azure-cli"></a>CLI do Azure

Você bloqueia os recursos implantados com CLI do Azure usando o comando [AZ Lock Create](/cli/azure/lock#az-lock-create) .

Para bloquear um recurso, forneça o nome do recurso, seu tipo de recurso e seu nome de grupo de recursos.

```azurecli
az lock create --name LockSite --lock-type CanNotDelete --resource-group exampleresourcegroup --resource-name examplesite --resource-type Microsoft.Web/sites
```

Para bloquear um grupo de recursos, forneça o nome do grupo de recursos.

```azurecli
az lock create --name LockGroup --lock-type CanNotDelete --resource-group exampleresourcegroup
```

Para obter informações sobre um bloqueio, use [AZ Lock List](/cli/azure/lock#az-lock-list). Para obter todos os bloqueios em sua assinatura, use:

```azurecli
az lock list
```

Para obter todos os bloqueios de um recurso, use:

```azurecli
az lock list --resource-group exampleresourcegroup --resource-name examplesite --namespace Microsoft.Web --resource-type sites --parent ""
```

Para obter todos os bloqueios para um grupo de recursos, use:

```azurecli
az lock list --resource-group exampleresourcegroup
```

Para excluir um bloqueio, use:

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup --resource-type Microsoft.Web/sites --resource-name examplesite --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="rest-api"></a>API REST
Você pode bloquear os recursos implantados com a [API REST para bloqueios de gerenciamento](https://docs.microsoft.com/rest/api/resources/managementlocks). A API REST permite que você crie e exclua bloqueios e recupere informações sobre bloqueios existentes.

Para criar um bloqueio, execute:

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

O escopo pode ser uma assinatura, um grupo de recursos ou um recurso. O nome do bloqueio é o que você deseja chamar de bloqueio. Para a versão de API, use **2016-09-01**.

Na solicitação, inclua um objeto JSON que especifica as propriedades do bloqueio.

    {
      "properties": {
        "level": "CanNotDelete",
        "notes": "Optional text notes."
      }
    } 

## <a name="next-steps"></a>Passos seguintes
* Para saber mais sobre como organizar logicamente seus recursos, consulte [usando marcas para organizar seus recursos](tag-resources.md)
* Você pode aplicar restrições e convenções em sua assinatura com políticas personalizadas. Para obter mais informações, veja [What is Azure Policy?](../../governance/policy/overview.md) (O que é o Azure Policy?).
* Para obter documentação de orientação sobre como as empresas podem utilizar o Resource Manager para gerir subscrições de forma eficaz, consulte [Azure enterprise scaffold - prescriptive subscription governance (Andaime empresarial do Azure - governação de subscrições prescritivas)](/azure/architecture/cloud-adoption-guide/subscription-governance).

