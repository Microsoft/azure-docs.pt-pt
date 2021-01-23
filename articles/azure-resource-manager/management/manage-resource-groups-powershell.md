---
title: Gerir grupos de recursos - Azure PowerShell
description: Utilize o Azure PowerShell para gerir os seus grupos de recursos através do Azure Resource Manager. Mostra como criar, listar e eliminar grupos de recursos.
author: mumian
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: jgao
ms.openlocfilehash: fb8cd45842e2f9b64048aa939f7b6adc9b0e6e0a
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98703687"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-powershell"></a>Gerir os grupos de recursos do Azure Resource Manager utilizando o Azure PowerShell

Saiba como utilizar o Azure PowerShell com [o Azure Resource Manager](overview.md) para gerir os seus grupos de recursos Azure. Para gerir os recursos da Azure, consulte [gerir os recursos do Azure utilizando a Azure PowerShell](manage-resources-powershell.md).

Outros artigos sobre gestão de grupos de recursos:

- [Gerir grupos de recursos Azure utilizando o portal Azure](manage-resources-portal.md)
- [Gerir grupos de recursos Azure utilizando o Azure CLI](manage-resources-cli.md)

## <a name="what-is-a-resource-group"></a>O que é um grupo de recursos

Um grupo de recursos é um contentor que detém recursos relacionados para uma solução do Azure. O grupo de recursos pode incluir todos os recursos para a solução ou apenas os recursos que pretende gerir como um grupo. Decida como pretende atribuir recursos a grupos de recursos com base no que é mais adequado para a sua organização. Geralmente, adicione recursos que partilham o mesmo ciclo de vida com o mesmo grupo de recursos para que possa facilmente implementar, atualizar e apagar como um grupo.

O grupo de recursos armazena metadados sobre os recursos. Por conseguinte, quando especifica uma localização para o grupo de recursos, está a especificar onde esses metadados estão armazenados. Por motivos de conformidade, poderá ter de certificar que os dados estão armazenados numa determinada região.

O grupo de recursos armazena metadados sobre os recursos. Quando especificar uma localização para o grupo de recursos, está a especificar onde esses metadados são armazenados.

## <a name="create-resource-groups"></a>Criar grupos de recursos

O seguinte script PowerShell cria um grupo de recursos.

```azurepowershell-interactive
New-AzResourceGroup -Name demoResourceGroup -Location westus
```

## <a name="list-resource-groups"></a>Grupos de recursos de lista

O seguinte script PowerShell lista os grupos de recursos sob a sua subscrição.

```azurepowershell-interactive
Get-AzResourceGroup
```

Para obter um grupo de recursos:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="delete-resource-groups"></a>Eliminar grupos de recursos

O seguinte script PowerShell elimina um grupo de recursos:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Remove-AzResourceGroup -Name $resourceGroupName
```

Para obter mais informações sobre como o Azure Resource Manager ordena a supressão de recursos, consulte a eliminação do [grupo de recursos Azure Resource Manager](delete-resource-group.md).

## <a name="deploy-resources-to-an-existing-resource-group"></a>Mobilizar recursos para um grupo de recursos existente

Consulte [recursos de implantação para um grupo de recursos existente.](manage-resources-powershell.md#deploy-resources-to-an-existing-resource-group)

Para validar uma implementação de grupo de recursos, consulte [Test-AzResourceGroupDeployment](/powershell/module/Az.Resources/Test-AzResourceGroupDeployment).

## <a name="deploy-a-resource-group-and-resources"></a>Implementar um grupo de recursos e recursos

Pode criar um grupo de recursos e implementar recursos para o grupo utilizando um modelo de Gestor de Recursos. Para obter mais informações, consulte [criar grupo de recursos e implementar recursos.](../templates/deploy-to-subscription.md#resource-groups)

## <a name="redeploy-when-deployment-fails"></a>Reimplantação quando a implementação falha

Esta funcionalidade também é conhecida como *Rollback on error*. Para obter mais informações, consulte [Reposicionar-se quando a implementação falhar](../templates/rollback-on-error.md).

## <a name="move-to-another-resource-group-or-subscription"></a>Mude-se para outro grupo de recursos ou subscrição

Pode mover os recursos do grupo para outro grupo de recursos. Para obter mais informações, consulte [Mover recursos para um novo grupo de recursos ou subscrição](move-resource-group-and-subscription.md).

## <a name="lock-resource-groups"></a>Bloquear grupos de recursos

O bloqueio impede que outros utilizadores da sua organização apaguem ou modifiquem acidentalmente recursos críticos, tais como subscrição do Azure, grupo de recursos ou recursos. 

O seguinte script bloqueia um grupo de recursos para que o grupo de recursos não possa ser eliminado.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName $resourceGroupName 
```

O seguinte script obtém todas as fechaduras para um grupo de recursos:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceLock -ResourceGroupName $resourceGroupName 
```

Para obter mais informações, consulte [Bloquear recursos com o Azure Resource Manager](lock-resources.md).

## <a name="tag-resource-groups"></a>Grupos de recursos de etiqueta

Pode aplicar etiquetas a grupos de recursos e recursos para organizar logicamente os seus ativos. Para obter informações, consulte [a utilização de tags para organizar os seus recursos Azure.](tag-resources.md#powershell)

## <a name="export-resource-groups-to-templates"></a>Grupos de recursos de exportação para modelos

Depois de configurar o seu grupo de recursos, pode ver um modelo de Gestor de Recursos para o grupo de recursos. Exportar o modelo oferece dois benefícios:

- Automatizar futuras implementações da solução porque o modelo contém a infraestrutura completa.
- Aprenda a sintaxe do modelo olhando para a Notação de Objeto JavaScript (JSON) que representa a sua solução.

Para exportar todos os recursos num grupo de recursos, utilize o cmdlet [Export-AzResourceGroup](/powershell/module/az.resources/Export-AzResourceGroup) e forneça o nome do grupo de recursos.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Export-AzResourceGroup -ResourceGroupName $resourceGroupName
```

Guarda o modelo como um ficheiro local.

Em vez de exportar todos os recursos do grupo de recursos, pode selecionar quais os recursos para exportar.

Para exportar um recurso, passe o ID de recurso.

```azurepowershell-interactive
$resource = Get-AzResource `
  -ResourceGroupName <resource-group-name> `
  -ResourceName <resource-name> `
  -ResourceType <resource-type>
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource $resource.ResourceId
```

Para exportar mais do que um recurso, passe os IDs de recursos numa matriz.

```azurepowershell-interactive
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource @($resource1.ResourceId, $resource2.ResourceId)
```

Ao exportar o modelo, pode especificar se os parâmetros são usados no modelo. Por predefinição, os parâmetros para nomes de recursos estão incluídos, mas não têm um valor padrão. Tem de passar o valor do parâmetro durante a implantação.

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "defaultValue": null,
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "defaultValue": null,
    "type": "String"
  }
}
```

No recurso, o parâmetro é usado para o nome.

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "[parameters('serverfarms_demoHostPlan_name')]",
    ...
  }
]
```

Se utilizar o `-IncludeParameterDefaultValue` parâmetro ao exportar o modelo, o parâmetro do modelo inclui um valor padrão definido para o valor atual. Pode utilizar esse valor predefinido ou substituir o valor predefinido ao passar num valor diferente.

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "defaultValue": "demoHostPlan",
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "defaultValue": "webSite3bwt23ktvdo36",
    "type": "String"
  }
}
```

Se utilizar o `-SkipResourceNameParameterization` parâmetro ao exportar o modelo, os parâmetros para nomes de recursos não estão incluídos no modelo. Em vez disso, o nome do recurso é definido diretamente no recurso para o seu valor atual. Não é possível personalizar o nome durante a implantação.

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "demoHostPlan",
    ...
  }
]
```

A funcionalidade do modelo de exportação não suporta a exportação de recursos da Azure Data Factory. Para saber como pode exportar recursos da Data Factory, consulte [Copy ou clone uma fábrica de dados na Azure Data Factory.](../../data-factory/copy-clone-data-factory.md)

Para exportar recursos criados através de um modelo de implementação clássico, deve [migrar para o modelo de implementação do Gestor de Recursos.](../../virtual-machines/migration-classic-resource-manager-overview.md)

Para obter mais informações, consulte [exportação única e multi-recursos para o modelo no portal Azure](../templates/export-template-portal.md).

## <a name="manage-access-to-resource-groups"></a>Gerir o acesso a grupos de recursos

[O controlo de acesso baseado em funções (Azure RBAC)](../../role-based-access-control/overview.md) é a forma como gere o acesso aos recursos em Azure. Para obter mais informações, consulte [Adicionar ou remover atribuições de funções Azure utilizando a Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

## <a name="next-steps"></a>Próximos passos

- Para saber o Gestor de Recursos Azure, consulte [a visão geral do Azure Resource Manager](overview.md).
- Para aprender a sintaxe do modelo do Gestor de Recursos, consulte [compreender a estrutura e a sintaxe dos modelos do Gestor de Recursos Azure](../templates/template-syntax.md).
- Para aprender a desenvolver modelos, consulte os [tutoriais passo a passo.](../index.yml)
- Para ver os esquemas de modelo do Gestor de Recursos Azure, consulte [a referência do modelo](/azure/templates/).