---
title: Gerir grupos de recursos - Azure PowerShell
description: Utilize o Azure PowerShell para gerir os seus grupos de recursos através do Azure Resource Manager. Mostra como criar, listar e eliminar grupos de recursos.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 25e001927d5c06b4a7e5639faaa054ae18b12bb7
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78384997"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-powershell"></a>Gerencie os grupos de recursos do Gestor de Recursos Azure utilizando o Azure PowerShell

Aprenda a utilizar o Azure PowerShell com o [Azure Resource Manager](overview.md) para gerir os seus grupos de recursos Azure. Para gerir os recursos do Azure, consulte os recursos do Manage Azure utilizando o [Azure PowerShell.](manage-resources-powershell.md)

Outros artigos sobre a gestão de grupos de recursos:

- [Gerir os grupos de recursos azure utilizando o portal Azure](manage-resources-portal.md)
- [Gerir os grupos de recursos azure utilizando o Azure CLI](manage-resources-cli.md)

## <a name="what-is-a-resource-group"></a>O que é um grupo de recursos

Um grupo de recursos é um contentor que mantém recursos relacionados para uma solução do Azure. O grupo de recursos pode incluir todos os recursos para a solução ou apenas os recursos que pretende gerir como um grupo. Decida como pretende atribuir recursos a grupos de recursos com base no que é mais adequado para a sua organização. Geralmente, adicione recursos que partilham o mesmo ciclo de vida ao mesmo grupo de recursos para que possa facilmente implantá-los, atualizar e eliminá-los como um grupo.

O grupo de recursos armazena metadados sobre os recursos. Por conseguinte, quando especifica uma localização para o grupo de recursos, está a especificar onde esses metadados estão armazenados. Por motivos de conformidade, poderá ter de certificar que os dados estão armazenados numa determinada região.

O grupo de recursos armazena metadados sobre os recursos. Quando especifica uma localização para o grupo de recursos, está a especificar onde os metadados são armazenados.

## <a name="create-resource-groups"></a>Criar grupos de recursos

O seguinte script PowerShell cria um grupo de recursos, e depois mostra o grupo de recursos.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="list-resource-groups"></a>Lista de grupos de recursos

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

Para obter mais informações sobre como o Gestor de Recursos azure ordena a eliminação de recursos, consulte a eliminação do grupo de recursos do Gestor de [Recursos do Azure.](delete-resource-group.md)

## <a name="deploy-resources-to-an-existing-resource-group"></a>Mobilizar recursos para um grupo de recursos existente

Consulte [os recursos de implantação para um grupo](manage-resources-powershell.md#deploy-resources-to-an-existing-resource-group)de recursos existentes.

Para validar uma implementação de um grupo de recursos, consulte [Test-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/Az.Resources/Test-AzResourceGroupDeployment?view=azps-1.3.0).

## <a name="deploy-a-resource-group-and-resources"></a>Implementar um grupo de recursos e recursos

Você pode criar um grupo de recursos e implementar recursos para o grupo usando um modelo de Gestor de Recursos. Para mais informações, consulte [Criar grupo de recursos e implementar recursos.](../templates/deploy-to-subscription.md#resource-group-and-resources)

## <a name="redeploy-when-deployment-fails"></a>Reimplantação quando a implantação falha

Esta funcionalidade também é conhecida como *Rollback no erro*. Para mais informações, consulte [Reimplantação quando a implementação falhar](../templates/rollback-on-error.md).

## <a name="move-to-another-resource-group-or-subscription"></a>Mude para outro grupo de recursos ou subscrição

Pode mover os recursos do grupo para outro grupo de recursos. Para obter mais informações, consulte [Mover recursos para um novo grupo de recursos ou subscrição](move-resource-group-and-subscription.md).

## <a name="lock-resource-groups"></a>Bloquear grupos de recursos

O bloqueio impede que outros utilizadores da sua organização apagam ou modifiquem acidentalmente recursos críticos, tais como subscrição azure, grupo de recursos ou recursos. 

O seguinte script bloqueia um grupo de recursos para que o grupo de recursos não possa ser eliminado.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName $resourceGroupName 
```

O seguinte script recebe todas as fechaduras para um grupo de recursos:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceLock -ResourceGroupName $resourceGroupName 
```

Para obter mais informações, consulte [Bloquear recursos com o Azure Resource Manager](lock-resources.md).

## <a name="tag-resource-groups"></a>Grupos de recursos de etiqueta

Pode aplicar etiquetas a grupos de recursos e recursos para organizar logicamente os seus ativos. Para obter informações, consulte [Utilizar tags para organizar os seus recursos Azure.](tag-resources.md#powershell)

## <a name="export-resource-groups-to-templates"></a>Grupos de recursos de exportação para modelos

Depois de configurar o seu grupo de recursos, pode ver um modelo de Gestor de Recursos para o grupo de recursos. Exportar o modelo oferece dois benefícios:

- Automatizar futuras implementações da solução porque o modelo contém a infraestrutura completa.
- Aprenda a sintaxe do modelo olhando para a Notação de Objetos JavaScript (JSON) que representa a sua solução.

Para exportar todos os recursos num grupo de recursos, utilize o cmdlet [export-AzResourceGroup](/powershell/module/az.resources/Export-AzResourceGroup) e forneça o nome do grupo de recursos.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Export-AzResourceGroup -ResourceGroupName $resourceGroupName
```

Guarda o modelo como um ficheiro local.

Em vez de exportar todos os recursos do grupo de recursos, pode selecionar quais os recursos para exportar.

Para exportar um recurso, passe a identificação do recurso.

```azurepowershell-interactive
$resource = Get-AzResource `
  -ResourceGroupName <resource-group-name> `
  -ResourceName <resource-name> `
  -ResourceType <resource-type>
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource $resource.ResourceId
```

Para exportar mais do que um recurso, passe as iDs de recurso numa matriz.

```azurepowershell-interactive
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource @($resource1.ResourceId, $resource2.ResourceId)
```

Ao exportar o modelo, pode especificar se os parâmetros são usados no modelo. Por padrão, os parâmetros para nomes de recursos estão incluídos, mas não têm um valor predefinido. Deve passar o valor do parâmetro durante a implantação.

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

No recurso, o parâmetro é utilizado para o nome.

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

Se utilizar o parâmetro `-IncludeParameterDefaultValue` ao exportar o modelo, o parâmetro do modelo inclui um valor predefinido que é definido para o valor atual. Pode utilizar esse valor predefinido ou substituir o valor predefinido, passando num valor diferente.

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

Se utilizar o parâmetro `-SkipResourceNameParameterization` ao exportar o modelo, os parâmetros para nomes de recursos não estão incluídos no modelo. Em vez disso, o nome do recurso é definido diretamente no recurso para o seu valor atual. Não pode personalizar o nome durante a implantação.

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

O modelo de exportação não suporta a exportação de recursos da Azure Data Factory. Para saber como pode exportar recursos da Fábrica de Dados, consulte [copiar ou clonar uma fábrica](https://aka.ms/exportTemplateViaAdf)de dados na Azure Data Factory .

Para exportar recursos criados através de um modelo de implantação clássico, deve [emigrar para o modelo](https://aka.ms/migrateclassicresourcetoarm)de implantação do Gestor de Recursos.

Para mais informações, consulte [a exportação de monoe e multi-recursos para o modelo no portal Azure](../templates/export-template-portal.md).

## <a name="manage-access-to-resource-groups"></a>Gerir o acesso a grupos de recursos

[O controlo de acesso baseado em funções (RBAC)](../../role-based-access-control/overview.md) é a forma de gerir o acesso a recursos no Azure. Para mais informações, consulte [Gerir o acesso utilizando o RBAC e o Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

## <a name="next-steps"></a>Passos seguintes

- Para aprender O Gestor de Recursos Azure, consulte a [visão geral do Gestor de Recursos do Azure.](overview.md)
- Para aprender a sintaxe do modelo do Gestor de Recursos, consulte [Compreender a estrutura e a sintaxe dos modelos do Gestor](../templates/template-syntax.md)de Recursos Azure .
- Para aprender a desenvolver modelos, consulte os [tutoriais passo a passo.](/azure/azure-resource-manager/)
- Para ver os esquemas de modelo do Gestor de Recursos Azure, consulte a referência do [modelo](/azure/templates/).