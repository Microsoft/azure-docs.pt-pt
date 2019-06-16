---
title: Bloquear recursos do Azure para impedir alterações | Documentos da Microsoft
description: Impedi os utilizadores de atualização e a eliminação de recursos do Azure críticos ao aplicar um bloqueio para todos os utilizadores e funções.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: tomfitz
ms.openlocfilehash: a6c7983d22eed4a4232fbb2db490c1743684a04c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65813391"
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>Bloquear recursos pode prevenir alterações inesperadas 

Como administrador, terá de bloquear uma subscrição, grupo de recursos ou recursos para impedir que outros utilizadores na sua organização acidentalmente eliminem ou modifiquem recursos críticos. Pode definir o nível do bloqueio para **CanNotDelete** ou **ReadOnly**. No portal, os bloqueios são chamados **elimine** e **só de leitura** , respetivamente.

* **CanNotDelete** significa que os utilizadores autorizados ainda podem ler e modificar um recurso, mas eles não é possível eliminar o recurso. 
* **Só de leitura** significa que os utilizadores autorizados podem ler um recurso, mas não é possível eliminar ou atualizar o recurso. Aplicar esse bloqueio é semelhante ao restringir todos os utilizadores autorizados para as permissões concedidas pela **leitor** função. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="how-locks-are-applied"></a>Como os bloqueios são aplicados

Quando aplica um bloqueio num âmbito principal, todos os recursos dentro daquele escopo herdam o mesmo bloqueio. Recursos até mesmo que adicionar posteriormente herdam o bloqueio do pai. O bloqueio mais restritivo na herança tem precedência.

Ao contrário do controlo de acesso baseado em funções, usar os bloqueios de gestão para aplicar uma restrição em todos os utilizadores e funções. Para saber mais sobre como definir permissões para utilizadores e funções, veja [controlo de acesso baseado em funções do Azure](../role-based-access-control/role-assignments-portal.md).

Bloqueios de Gestor de recursos aplicam-se apenas às operações que ocorrem no plano de gestão, que consiste em operações enviadas para `https://management.azure.com`. Os bloqueios não restringem como recursos executam suas próprias funções. As alterações de recursos são restritas, mas as operações de recursos não restritas. Por exemplo, um bloqueio só de leitura numa base de dados SQL impede-eliminem ou modifiquem o banco de dados. Ele não impede de criar, atualizar ou eliminar dados na base de dados. Transações de dados são permitidas porque essas operações não são enviadas para `https://management.azure.com`.

Aplicando **só de leitura** pode levar a resultados inesperados, uma vez que algumas operações que parecem modificar o recurso não exigem realmente ações que são bloqueadas pelo bloqueio. O **só de leitura** bloqueio pode ser aplicado ao recurso ou para o grupo de recursos que contém o recurso. Alguns exemplos comuns das operações que estão bloqueadas por uma **só de leitura** bloqueio são:

* R **só de leitura** bloqueio numa conta de armazenamento impede que todos os utilizadores de listar as chaves. A lista de operação de chaves é tratada por meio de um pedido POST porque as chaves retornadas estão disponíveis para operações de escrita.

* R **só de leitura** bloqueio num recurso de serviço de aplicações impede que o Visual Studio Server Explorer de apresentar ficheiros para o recurso porque essa interação requer acesso de escrita.

* R **só de leitura** bloqueio no grupo de recursos que contém uma máquina virtual impede que todos os utilizadores de iniciar ou reiniciar a máquina virtual. Estas operações exigem um pedido POST.

## <a name="who-can-create-or-delete-locks"></a>Quem pode criar ou eliminar bloqueios
Para criar ou eliminar bloqueios de gestão, tem de ter acesso ao `Microsoft.Authorization/*` ou `Microsoft.Authorization/locks/*` ações. Das funções incorporadas, apenas **Proprietário** e **Administrador de Acesso dos Utilizadores** têm acesso a essas ações.

## <a name="managed-applications-and-locks"></a>Aplicações e bloqueios

Alguns serviços do Azure, como o Azure Databricks, utilizam [aplicativos gerenciados](../managed-applications/overview.md) para implementar o serviço. Nesse caso, o serviço cria dois grupos de recursos. Um grupo de recursos contém uma descrição geral do serviço e não está bloqueado. O grupo de recursos contém a infraestrutura para o serviço e é bloqueado.

Se tentar eliminar o grupo de recursos de infraestrutura, obterá um erro a indicar que o grupo de recursos está bloqueado. Se tentar eliminar o bloqueio para o grupo de recursos de infraestrutura, obterá um erro a indicar que o bloqueio não pode ser eliminado porque o proprietário é uma aplicação de sistema.

Em vez disso, elimine o serviço, que também elimina o grupo de recursos de infraestrutura.

Para aplicativos gerenciados, selecione o serviço que implementou.

![Selecionar serviço](./media/resource-group-lock-resources/select-service.png)

Observe que o serviço inclui uma ligação para uma **grupo de recursos gerido**. Grupo de recursos possui a infra-estrutura e está bloqueado. Não é possível eliminá-lo diretamente.

![Mostrar grupo gerido por](./media/resource-group-lock-resources/show-managed-group.png)

Para eliminar tudo para o serviço, incluindo o grupo de recursos de infraestrutura bloqueado, selecione **eliminar** para o serviço.

![Eliminar serviço](./media/resource-group-lock-resources/delete-service.png)

## <a name="portal"></a>Portal
[!INCLUDE [resource-manager-lock-resources](../../includes/resource-manager-lock-resources.md)]

## <a name="template"></a>Modelo

Ao utilizar um modelo do Resource Manager para implementar um bloqueio, utilizar valores diferentes para o nome e tipo, dependendo do escopo do bloqueio.

Ao aplicar um bloqueio para um **recursos**, utilize os seguintes formatos:

* name - `{resourceName}/Microsoft.Authorization/{lockName}`
* tipo de- `{resourceProviderNamespace}/{resourceType}/providers/locks`

Ao aplicar um bloqueio para um **grupo de recursos** ou **subscrição**, utilize os seguintes formatos:

* name - `{lockName}`
* tipo de- `Microsoft.Authorization/locks`

O exemplo seguinte mostra um modelo que cria um plano do serviço de aplicações, um web site e um bloqueio no web site. O tipo de recurso do bloqueio é o tipo de recurso do recurso para bloquear e **/fornecedores/bloqueios**. O nome do bloqueio é criado pela concatenação com o nome do recurso **/Microsoft.Authorization/** e o nome do bloqueio.

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

Para obter um exemplo de configuração de um bloqueio num grupo de recursos, consulte [criar um grupo de recursos e bloqueá-lo](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-rg-lock-role-assignment).

## <a name="powershell"></a>PowerShell
O bloqueio é implementada recursos com o Azure PowerShell com o [New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock) comando.

Para bloquear um recurso, forneça o nome do recurso, o respetivo tipo de recurso e o seu nome de grupo de recursos.

```azurepowershell-interactive
New-AzResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Para bloquear um grupo de recursos, forneça o nome do grupo de recursos.

```azurepowershell-interactive
New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName exampleresourcegroup
```

Para obter informações sobre um bloqueio, utilize [Get-AzureRmResourceLock](/powershell/module/az.resources/get-azresourcelock). Para obter todos os bloqueios na sua subscrição, utilize:

```azurepowershell-interactive
Get-AzResourceLock
```

Para obter todos os bloqueios de um recurso, utilize:

```azurepowershell-interactive
Get-AzResourceLock -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Para obter todos os bloqueios para um grupo de recursos, utilize:

```azurepowershell-interactive
Get-AzResourceLock -ResourceGroupName exampleresourcegroup
```

Para eliminar um bloqueio, utilize:

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName exampleresourcegroup -ResourceName examplesite -ResourceType Microsoft.Web/sites).LockId
Remove-AzResourceLock -LockId $lockId
```

## <a name="azure-cli"></a>CLI do Azure

O bloqueio é implementada recursos com a CLI do Azure com o [bloqueio az criar](/cli/azure/lock#az-lock-create) comando.

Para bloquear um recurso, forneça o nome do recurso, o respetivo tipo de recurso e o seu nome de grupo de recursos.

```azurecli
az lock create --name LockSite --lock-type CanNotDelete --resource-group exampleresourcegroup --resource-name examplesite --resource-type Microsoft.Web/sites
```

Para bloquear um grupo de recursos, forneça o nome do grupo de recursos.

```azurecli
az lock create --name LockGroup --lock-type CanNotDelete --resource-group exampleresourcegroup
```

Para obter informações sobre um bloqueio, utilize [lista de bloqueio de az](/cli/azure/lock#az-lock-list). Para obter todos os bloqueios na sua subscrição, utilize:

```azurecli
az lock list
```

Para obter todos os bloqueios de um recurso, utilize:

```azurecli
az lock list --resource-group exampleresourcegroup --resource-name examplesite --namespace Microsoft.Web --resource-type sites --parent ""
```

Para obter todos os bloqueios para um grupo de recursos, utilize:

```azurecli
az lock list --resource-group exampleresourcegroup
```

Para eliminar um bloqueio, utilize:

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup --resource-type Microsoft.Web/sites --resource-name examplesite --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="rest-api"></a>API REST
É possível bloquear recursos implementados com o [API REST para bloqueios de gestão](https://docs.microsoft.com/rest/api/resources/managementlocks). A API REST permite-lhe criar e eliminar bloqueios e obter informações sobre bloqueios existentes.

Para criar um bloqueio, execute:

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

O âmbito pode ser uma subscrição, grupo de recursos ou recurso. O nome de bloqueio é tudo o que deseja chamar o bloqueio. Para a versão de api, utilize **2016-09-01**.

Na solicitação, incluem um objeto JSON que especifica as propriedades para o bloqueio.

    {
      "properties": {
        "level": "CanNotDelete",
        "notes": "Optional text notes."
      }
    } 

## <a name="next-steps"></a>Passos Seguintes
* Para saber mais sobre como organizar logicamente os recursos, veja [utilizando etiquetas para organizar os recursos](resource-group-using-tags.md)
* Pode aplicar restrições e convenções na sua subscrição com políticas personalizadas. Para obter mais informações, veja [What is Azure Policy?](../governance/policy/overview.md) (O que é o Azure Policy?).
* Para obter documentação de orientação sobre como as empresas podem utilizar o Resource Manager para gerir subscrições de forma eficaz, consulte [Azure enterprise scaffold - prescriptive subscription governance (Andaime empresarial do Azure - governação de subscrições prescritivas)](/azure/architecture/cloud-adoption-guide/subscription-governance).

