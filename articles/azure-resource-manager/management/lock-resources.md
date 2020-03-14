---
title: Bloquear recursos para evitar alterações
description: Impedir que os utilizadores atualizem ou abatam recursos críticos do Azure aplicando um bloqueio para todos os utilizadores e funções.
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 70fb189adb634b7ac24afe7cc8b94738117da5ef
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79274011"
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>Bloqueie recursos para evitar alterações inesperadas

Como administrador, pode precisar de bloquear uma subscrição, um grupo de recursos ou recursos, para impedir que outros utilizadores na sua organização eliminem ou modifiquem acidentalmente recursos importantes. Pode definir o nível do bloqueio para **CanNotDelete** ou **ReadOnly**. No portal, as fechaduras são chamadas **apagar** e **ler apenas** respectivamente.

* **CanNotDelete** significa que os utilizadores autorizados ainda podem ler e modificar um recurso, mas não podem apagar o recurso. 
* **LerApenas** significa que os utilizadores autorizados podem ler um recurso, mas não podem excluir ou atualizar o recurso. A aplicação deste bloqueio é semelhante a restringir todos os utilizadores autorizados às permissões concedidas pela função **Reader.**

## <a name="how-locks-are-applied"></a>Como as fechaduras são aplicadas

Quando se aplica um cadeado no âmbito dos pais, todos os recursos nesse âmbito herdam o mesmo cadeado. Até os recursos que adicionas mais tarde herdam o cadeado do progenitor. O bloqueio mais restritivo da herança tem precedência.

Ao contrário do controlo de acesso baseado em funções, utiliza bloqueios de gestão para aplicar uma restrição em todos os utilizadores e funções. Para aprender a definir permissões para utilizadores e funções, consulte [o Controlo de Acesso baseado em Papel Azure](../../role-based-access-control/role-assignments-portal.md).

Os bloqueios do Gestor de Recursos aplicam-se apenas às operações que ocorrem no plano de gestão, que consiste em operações enviadas para `https://management.azure.com`. As fechaduras não restringem o desempenho dos recursos. As alterações de recursos são restritas, mas as operações de recursos não são restritas. Por exemplo, um bloqueio De Leitura Apenas numa Base de Dados SQL impede-o de apagar ou modificar a base de dados. Não o impede de criar, atualizar ou eliminar dados na base de dados. As transações de dados são permitidas porque essas operações não são enviadas para `https://management.azure.com`.

Aplicar **a ReadOnly** pode levar a resultados inesperados porque algumas operações que não parecem modificar o recurso realmente requerem ações bloqueadas pelo bloqueio. O bloqueio **ReadOnly** pode ser aplicado ao recurso ou ao grupo de recursos que contém o recurso. Alguns exemplos comuns das operações que são bloqueadas por um bloqueio **ReadOnly** são:

* Um bloqueio **de leitura Apenas** numa conta de armazenamento impede todos os utilizadores de listar as chaves. A operação de chaves da lista é manuseada através de um pedido post porque as chaves devolvidas estão disponíveis para operações de escrita.

* Um bloqueio **De LeituraOnly** num recurso do Serviço de Aplicações impede o Visual Studio Server Explorer de apresentar ficheiros para o recurso, porque essa interação requer acesso por escrito.

* Um bloqueio **De Leitura Apenas** num grupo de recursos que contém uma máquina virtual impede que todos os utilizadores iniciem ou reiniciem a máquina virtual. Estas operações requerem um pedido de CORREIO.

## <a name="who-can-create-or-delete-locks"></a>Quem pode criar ou apagar fechaduras

Para criar ou eliminar fechaduras de gestão, deve ter acesso a ações `Microsoft.Authorization/*` ou `Microsoft.Authorization/locks/*`. Das funções incorporadas, apenas **Proprietário** e **Administrador de Acesso dos Utilizadores** têm acesso a essas ações.

## <a name="managed-applications-and-locks"></a>Aplicações e fechaduras geridas

Alguns serviços Azure, como o Azure Databricks, utilizam [aplicações geridas](../managed-applications/overview.md) para implementar o serviço. Nesse caso, o serviço cria dois grupos de recursos. Um grupo de recursos contém uma visão geral do serviço e não está bloqueado. O outro grupo de recursos contém a infraestrutura para o serviço e está bloqueado.

Se tentar eliminar o grupo de recursos de infraestrutura, obtém um erro afirmando que o grupo de recursos está bloqueado. Se tentar apagar o bloqueio para o grupo de recursos de infraestrutura, obtém um erro afirmando que o bloqueio não pode ser apagado porque é propriedade de uma aplicação do sistema.

Em vez disso, apague o serviço, que também elimina o grupo de recursos de infraestrutura.

Para aplicações geridas, selecione o serviço que implementou.

![Selecione o serviço](./media/lock-resources/select-service.png)

Note que o serviço inclui um link para um Grupo de **Recursos Geridos**. Este grupo de recursos detém a infraestrutura e está bloqueado. Não pode ser apagado diretamente.

![Mostrar grupo gerido](./media/lock-resources/show-managed-group.png)

Para eliminar tudo para o serviço, incluindo o grupo de recursos de infraestrutura bloqueado, **selecione Eliminar** para o serviço.

![Eliminar o serviço](./media/lock-resources/delete-service.png)

## <a name="azure-backups-and-locks"></a>Backups e fechaduras Azure

Se bloquear o grupo de recursos criado pelo Serviço de Backup Azure, as cópias de segurança começarão a falhar. O serviço suporta um máximo de 18 pontos de restauro. Com um bloqueio **CanNotDelete,** o serviço de backup não consegue limpar pontos de restauro. Para mais informações, consulte [frequentemente perguntas-Back up Azure VMs](../../backup/backup-azure-vm-backup-faq.md).

## <a name="portal"></a>Portal

[!INCLUDE [resource-manager-lock-resources](../../../includes/resource-manager-lock-resources.md)]

## <a name="template"></a>Modelo

Ao utilizar um modelo de Gestor de Recursos para implementar um bloqueio, utiliza valores diferentes para o nome e tipo, dependendo do âmbito do bloqueio.

Ao aplicar um bloqueio a um **recurso,** utilize os seguintes formatos:

* nome - `{resourceName}/Microsoft.Authorization/{lockName}`
* tipo - `{resourceProviderNamespace}/{resourceType}/providers/locks`

Ao aplicar um bloqueio a um grupo de **recursos** ou **subscrição,** utilize os seguintes formatos:

* nome - `{lockName}`
* tipo - `Microsoft.Authorization/locks`

O exemplo seguinte mostra um modelo que cria um plano de serviço de aplicações, um web site e um bloqueio no site. O tipo de recurso do bloqueio é o tipo de recurso do recurso para bloquear e **/fornecedores/fechaduras**. O nome do cadeado é criado concatenando o nome do recurso com **/Microsoft.Authorization/** e o nome do cadeado.

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

Para um exemplo de colocar um bloqueio num grupo de recursos, consulte Criar um grupo de [recursos e bloqueá-lo](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-rg-lock-role-assignment).

## <a name="powershell"></a>PowerShell
Bloqueia os recursos implantados com a Azure PowerShell utilizando o comando [New-AzResourceLock.](/powershell/module/az.resources/new-azresourcelock)

Para bloquear um recurso, forneça o nome do recurso, o seu tipo de recurso e o seu nome de grupo de recursos.

```azurepowershell-interactive
New-AzResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Para bloquear um grupo de recursos, forneça o nome do grupo de recursos.

```azurepowershell-interactive
New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName exampleresourcegroup
```

Para obter informações sobre um bloqueio, use [Get-AzResourceLock](/powershell/module/az.resources/get-azresourcelock). Para obter todas as fechaduras da sua subscrição, use:

```azurepowershell-interactive
Get-AzResourceLock
```

Para obter todas as fechaduras para um recurso, use:

```azurepowershell-interactive
Get-AzResourceLock -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Para obter todas as fechaduras para um grupo de recursos, use:

```azurepowershell-interactive
Get-AzResourceLock -ResourceGroupName exampleresourcegroup
```

Para eliminar um bloqueio, utilize:

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName exampleresourcegroup -ResourceName examplesite -ResourceType Microsoft.Web/sites).LockId
Remove-AzResourceLock -LockId $lockId
```

## <a name="azure-cli"></a>CLI do Azure

Bloqueia os recursos implantados com o Azure CLI utilizando o comando de criação de [bloqueio az.](/cli/azure/lock#az-lock-create)

Para bloquear um recurso, forneça o nome do recurso, o seu tipo de recurso e o seu nome de grupo de recursos.

```azurecli
az lock create --name LockSite --lock-type CanNotDelete --resource-group exampleresourcegroup --resource-name examplesite --resource-type Microsoft.Web/sites
```

Para bloquear um grupo de recursos, forneça o nome do grupo de recursos.

```azurecli
az lock create --name LockGroup --lock-type CanNotDelete --resource-group exampleresourcegroup
```

Para obter informações sobre um cadeado, use a lista de [bloqueio az](/cli/azure/lock#az-lock-list). Para obter todas as fechaduras da sua subscrição, use:

```azurecli
az lock list
```

Para obter todas as fechaduras para um recurso, use:

```azurecli
az lock list --resource-group exampleresourcegroup --resource-name examplesite --namespace Microsoft.Web --resource-type sites --parent ""
```

Para obter todas as fechaduras para um grupo de recursos, use:

```azurecli
az lock list --resource-group exampleresourcegroup
```

Para eliminar um bloqueio, utilize:

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup --resource-type Microsoft.Web/sites --resource-name examplesite --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="rest-api"></a>API REST
Pode bloquear os recursos implantados com a [API REST para fechaduras](https://docs.microsoft.com/rest/api/resources/managementlocks)de gestão . A API REST permite-lhe criar e eliminar fechaduras e recuperar informações sobre fechaduras existentes.

Para criar uma fechadura, corra:

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

O âmbito pode ser uma subscrição, um grupo de recursos ou um recurso. O nome da fechadura é o que quiser chamar de fechadura. Para a versão api, utilize **2016-09-01**.

No pedido, inclua um objeto JSON que especifica as propriedades para o bloqueio.

    {
      "properties": {
        "level": "CanNotDelete",
        "notes": "Optional text notes."
      }
    } 

## <a name="next-steps"></a>Passos seguintes
* Para aprender sobre a organização lógica dos seus recursos, consulte [Usar tags para organizar os seus recursos](tag-resources.md)
* Pode aplicar restrições e convenções em toda a sua subscrição com políticas personalizadas. Para obter mais informações, veja [What is Azure Policy?](../../governance/policy/overview.md) (O que é o Azure Policy?).
* Para obter documentação de orientação sobre como as empresas podem utilizar o Resource Manager para gerir subscrições de forma eficaz, consulte [Azure enterprise scaffold - prescriptive subscription governance (Andaime empresarial do Azure - governação de subscrições prescritivas)](/azure/architecture/cloud-adoption-guide/subscription-governance).

