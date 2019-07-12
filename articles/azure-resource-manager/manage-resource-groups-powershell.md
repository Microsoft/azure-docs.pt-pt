---
title: Gerir grupos do Azure Resource Manager com o Azure PowerShell | Documentos da Microsoft
description: Utilize o Azure PowerShell para gerir os seus grupos do Azure Resource Manager.
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 5197358e3bd8a3052fbf71cafc2f1e3acda46b26
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721140"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-powershell"></a>Gerir grupos de recursos do Azure Resource Manager com o Azure PowerShell

Saiba como utilizar o Azure PowerShell com [do Azure Resource Manager](resource-group-overview.md) para gerir os seus grupos de recursos do Azure. Para gerir recursos do Azure, veja [recursos de gerir o Azure com o Azure PowerShell](./manage-resources-powershell.md).

Outros artigos sobre a gestão de grupos de recursos:

- [Gerir grupos de recursos do Azure com o portal do Azure](./manage-resources-portal.md)
- [Gerir grupos de recursos do Azure com a CLI do Azure](./manage-resources-cli.md)

## <a name="what-is-a-resource-group"></a>O que é um grupo de recursos

Um grupo de recursos é um contentor que mantém recursos relacionados para uma solução do Azure. O grupo de recursos pode incluir todos os recursos para a solução ou apenas os recursos que pretende gerir como um grupo. Decida como pretende atribuir recursos a grupos de recursos com base no que é mais adequado para a sua organização. Em geral, adicione recursos que partilham o mesmo ciclo de vida no mesmo grupo de recursos para que possa facilmente implantar, atualizar e eliminá-los como um grupo.

O grupo de recursos armazena metadados sobre os recursos. Por conseguinte, quando especifica uma localização para o grupo de recursos, está a especificar onde esses metadados estão armazenados. Por motivos de conformidade, poderá ter de certificar que os dados estão armazenados numa determinada região.

O grupo de recursos armazena metadados sobre os recursos. Quando especificar uma localização para o grupo de recursos, especifica onde esses metadados estão armazenados.

## <a name="create-resource-groups"></a>Criar grupos de recursos

O seguinte script do PowerShell cria um grupo de recursos e, em seguida, mostra o grupo de recursos.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="list-resource-groups"></a>Lista de grupos de recursos

O script do PowerShell seguinte lista os grupos de recursos na sua subscrição.

```azurepowershell-interactive
Get-AzResourceGroup
```

Para obter um grupo de recursos:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="delete-resource-groups"></a>Eliminar grupos de recursos

O script do PowerShell seguinte elimina um grupo de recursos:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Remove-AzResourceGroup -Name $resourceGroupName
```

Para obter mais informações sobre como o Azure Resource Manager ordena a eliminação de recursos, consulte [eliminação do grupo de recursos do Azure Resource Manager](./resource-group-delete.md).

## <a name="deploy-resources-to-an-existing-resource-group"></a>Implementar recursos no grupo de recursos existente

Ver [implementar recursos no grupo de recursos existente](./manage-resources-powershell.md#deploy-resources-to-an-existing-resource-group).

Para validar uma implementação de grupo de recursos, consulte [teste AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/Az.Resources/Test-AzResourceGroupDeployment?view=azps-1.3.0).

## <a name="deploy-a-resource-group-and-resources"></a>Implementar um grupo de recursos e recursos

Pode criar um grupo de recursos e implementar recursos no grupo utilizando um modelo do Resource Manager. Para obter mais informações, consulte [criar o grupo de recursos e implementar recursos](./deploy-to-subscription.md#create-resource-group-and-deploy-resources).

## <a name="redeploy-when-deployment-fails"></a>Implementar novamente quando ocorre uma falha de implementação

Esta funcionalidade também é conhecido como *reversão com o erro*. Para obter mais informações, consulte [Reimplementar quando ocorre uma falha de implementação](./resource-group-template-deploy.md#redeploy-when-deployment-fails).

## <a name="move-to-another-resource-group-or-subscription"></a>Mover para outro grupo de recursos ou subscrição

Pode mover os recursos no grupo para outro grupo de recursos. Para obter mais informações, consulte [Mover recursos para um novo grupo de recursos ou subscrição](./resource-group-move-resources.md).

## <a name="lock-resource-groups"></a>Grupos de recursos de bloqueio

Bloqueio evita que outros utilizadores na sua organização acidentalmente eliminem ou modifiquem recursos críticos, como o recurso, grupo de recursos ou subscrição do Azure. 

O seguinte script bloqueia um grupo de recursos para que não é possível eliminar o grupo de recursos.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName $resourceGroupName 
```

O script seguinte obtém todos os bloqueios para um grupo de recursos:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceLock -ResourceGroupName $resourceGroupName 
```

Para obter mais informações, consulte [Bloquear recursos com o Azure Resource Manager](resource-group-lock-resources.md).

## <a name="tag-resource-groups"></a>Grupos de recursos de etiqueta

Pode aplicar etiquetas a grupos de recursos e recursos para organizar logicamente os recursos. Para obter informações, consulte [utilizar etiquetas para organizar os recursos do Azure](./resource-group-using-tags.md#powershell).

## <a name="export-resource-groups-to-templates"></a>Grupos de recursos de exportação para modelos

Depois de configurar o grupo de recursos, pode ver um modelo do Resource Manager para o grupo de recursos. Exportar o modelo oferece duas vantagens:

- Automatize implementações futuras da solução porque o modelo contém toda a infraestrutura.
- Aprenda a sintaxe do modelo ao procurar em JavaScript Object Notation (JSON) que representa a sua solução.

Para exportar todos os recursos num grupo de recursos, utilize o [Export-AzResourceGroup](/powershell/module/az.resources/Export-AzResourceGroup) cmdlet e forneça o nome do grupo de recursos.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Export-AzResourceGroup -ResourceGroupName $resourceGroupName
```

Ele salva o modelo como um ficheiro local.

Em vez de exportar todos os recursos no grupo de recursos, pode selecionar que recursos a exportar.

Para exportar um recurso, passar essa ID de recurso.

```azurepowershell-interactive
$resource = Get-AzResource `
  -ResourceGroupName <resource-group-name> `
  -ResourceName <resource-name> `
  -ResourceType <resource-type>
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource $resource.ResourceId
```

Para exportar mais do que um recurso, de transmitir o IDs de recurso numa matriz.

```azurepowershell-interactive
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource @($resource1.ResourceId, $resource2.ResourceId)
```

Ao exportar o modelo, pode especificar se os parâmetros são utilizados no modelo. Por predefinição, os parâmetros para nomes de recursos são incluídos, mas não têm um valor predefinido. Tem de passar esse valor de parâmetro durante a implementação.

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

O recurso, o parâmetro é utilizado para o nome.

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

Se utilizar o `-IncludeParameterDefaultValue` parâmetro ao exportar o modelo, o parâmetro de modelo inclui um valor predefinido que é definido como o valor atual. Pode utilizar esse valor de predefinição ou substituir o valor predefinido ao transmitir um valor diferente.

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

Se utilizar o `-SkipResourceNameParameterization` parâmetro ao exportar o modelo, os parâmetros para os nomes de recursos não estão incluídos no modelo. Em vez disso, o nome do recurso é definido diretamente no recurso para o valor atual. Não é possível personalizar o nome durante a implementação.

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

Para obter mais informações, consulte [exportação única e vários recurso para o modelo no portal do Azure](./export-template-portal.md).

## <a name="manage-access-to-resource-groups"></a>Gerir o acesso aos grupos de recursos

[O controlo de acesso baseado em funções (RBAC)](../role-based-access-control/overview.md) é a forma de gerir o acesso a recursos no Azure. Para obter mais informações, consulte [gerir o acesso com RBAC e o Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).

## <a name="next-steps"></a>Passos Seguintes

- Para obter o Azure Resource Manager, veja [descrição geral do Azure Resource Manager](./resource-group-overview.md).
- Para obter a sintaxe do modelo do Resource Manager, veja [compreender a estrutura e a sintaxe de modelos Azure Resource Manager](./resource-group-authoring-templates.md).
- Para saber como desenvolver modelos, veja a [tutoriais passo a passo](/azure/azure-resource-manager/).
- Para ver os esquemas de modelo do Azure Resource Manager, consulte [referência de modelo](/azure/templates/).