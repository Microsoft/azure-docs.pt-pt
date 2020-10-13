---
title: Gerir grupos de recursos - Azure CLI
description: Utilize o Azure CLI para gerir os seus grupos de recursos através do Azure Resource Manager. Mostra como criar, listar e eliminar grupos de recursos.
author: mumian
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: jgao
ms.custom: devx-track-azurecli
ms.openlocfilehash: abab1a6a65f3cc45bcd88dc7aed89e18e4e28d5b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91371240"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-cli"></a>Gerir grupos de recursos do Azure Resource Manager utilizando o Azure CLI

Saiba como utilizar o Azure CLI com [o Azure Resource Manager](overview.md) para gerir os seus grupos de recursos Azure. Para gerir os recursos da Azure, consulte [gerir os recursos do Azure utilizando o Azure CLI](manage-resources-cli.md).

Outros artigos sobre gestão de grupos de recursos:

- [Gerir grupos de recursos Azure utilizando o portal Azure](manage-resources-portal.md)
- [Gerir grupos de recursos Azure utilizando a Azure PowerShell](manage-resources-powershell.md)

## <a name="what-is-a-resource-group"></a>O que é um grupo de recursos

Um grupo de recursos é um contentor que detém recursos relacionados para uma solução do Azure. O grupo de recursos pode incluir todos os recursos para a solução ou apenas os recursos que pretende gerir como um grupo. Decida como pretende atribuir recursos a grupos de recursos com base no que é mais adequado para a sua organização. Geralmente, adicione recursos que partilham o mesmo ciclo de vida com o mesmo grupo de recursos para que possa facilmente implementar, atualizar e apagar como um grupo.

O grupo de recursos armazena metadados sobre os recursos. Por conseguinte, quando especifica uma localização para o grupo de recursos, está a especificar onde esses metadados estão armazenados. Por motivos de conformidade, poderá ter de certificar que os dados estão armazenados numa determinada região.

O grupo de recursos armazena metadados sobre os recursos. Quando especificar uma localização para o grupo de recursos, está a especificar onde esses metadados são armazenados.

## <a name="create-resource-groups"></a>Criar grupos de recursos

O seguinte comando CLI cria um grupo de recursos.

```azurecli-interactive
az group create --name demoResourceGroup --location westus
```

## <a name="list-resource-groups"></a>Grupos de recursos de lista

O seguinte script CLI lista os grupos de recursos sob a sua subscrição.

```azurecli-interactive
az group list
```

Para obter um grupo de recursos:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group show --name $resourceGroupName
```

## <a name="delete-resource-groups"></a>Eliminar grupos de recursos

O seguinte script CLI elimina um grupo de recursos:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

Para obter mais informações sobre como o Azure Resource Manager ordena a supressão de recursos, consulte a eliminação do [grupo de recursos Azure Resource Manager](delete-resource-group.md).

## <a name="deploy-resources-to-an-existing-resource-group"></a>Mobilizar recursos para um grupo de recursos existente

Consulte [recursos de implantação para um grupo de recursos existente.](manage-resources-cli.md#deploy-resources-to-an-existing-resource-group)

## <a name="deploy-a-resource-group-and-resources"></a>Implementar um grupo de recursos e recursos

Pode criar um grupo de recursos e implementar recursos para o grupo utilizando um modelo de Gestor de Recursos. Para obter mais informações, consulte [criar grupo de recursos e implementar recursos.](../templates/deploy-to-subscription.md#resource-groups)

## <a name="redeploy-when-deployment-fails"></a>Reimplantação quando a implementação falha

Esta funcionalidade também é conhecida como *Rollback on error*. Para obter mais informações, consulte [Reposicionar-se quando a implementação falhar](../templates/rollback-on-error.md).

## <a name="move-to-another-resource-group-or-subscription"></a>Mude-se para outro grupo de recursos ou subscrição

Pode mover os recursos do grupo para outro grupo de recursos. Para mais informações, consulte [os recursos de Move.](manage-resources-cli.md#move-resources)

## <a name="lock-resource-groups"></a>Bloquear grupos de recursos

O bloqueio impede que outros utilizadores da sua organização apaguem ou modifiquem acidentalmente recursos críticos, tais como subscrição do Azure, grupo de recursos ou recursos. 

O seguinte script bloqueia um grupo de recursos para que o grupo de recursos não possa ser eliminado.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock create --name LockGroup --lock-type CanNotDelete --resource-group $resourceGroupName  
```

O seguinte script obtém todas as fechaduras para um grupo de recursos:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock list --resource-group $resourceGroupName  
```

O seguinte script elimina uma fechadura:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the lock name:" &&
read lockName &&
az lock delete --name $lockName --resource-group $resourceGroupName
```

Para obter mais informações, consulte [Bloquear recursos com o Azure Resource Manager](lock-resources.md).

## <a name="tag-resource-groups"></a>Grupos de recursos de etiqueta

Pode aplicar etiquetas a grupos de recursos e recursos para organizar logicamente os seus ativos. Para obter informações, consulte [a utilização de tags para organizar os seus recursos Azure.](tag-resources.md#azure-cli)

## <a name="export-resource-groups-to-templates"></a>Grupos de recursos de exportação para modelos

Depois de configurar o seu grupo de recursos com sucesso, pode querer visualizar o modelo de Gestor de Recursos para o grupo de recursos. Exportar o modelo oferece dois benefícios:

- Automatizar futuras implementações da solução porque o modelo contém toda a infraestrutura completa.
- Aprenda a sintaxe do modelo olhando para a Notação de Objeto JavaScript (JSON) que representa a sua solução.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group export --name $resourceGroupName  
```

O script apresenta o modelo na consola.  Copie o JSON e guarde como um ficheiro.

A funcionalidade do modelo de exportação não suporta a exportação de recursos da Azure Data Factory. Para saber como pode exportar recursos da Data Factory, consulte [Copy ou clone uma fábrica de dados na Azure Data Factory.](https://aka.ms/exportTemplateViaAdf)

Para exportar recursos criados através de um modelo de implementação clássico, deve [migrar para o modelo de implementação do Gestor de Recursos.](https://aka.ms/migrateclassicresourcetoarm)

Para obter mais informações, consulte [exportação única e multi-recursos para o modelo no portal Azure](../templates/export-template-portal.md).

## <a name="manage-access-to-resource-groups"></a>Gerir o acesso a grupos de recursos

[O controlo de acesso baseado em funções (Azure RBAC)](../../role-based-access-control/overview.md) é a forma como gere o acesso aos recursos em Azure. Para obter mais informações, consulte [Adicionar ou remover atribuições de funções Azure utilizando O Azure CLI](../../role-based-access-control/role-assignments-cli.md).

## <a name="next-steps"></a>Passos seguintes

- Para saber o Gestor de Recursos Azure, consulte [a visão geral do Azure Resource Manager](overview.md).
- Para aprender a sintaxe do modelo do Gestor de Recursos, consulte [compreender a estrutura e a sintaxe dos modelos do Gestor de Recursos Azure](../templates/template-syntax.md).
- Para aprender a desenvolver modelos, consulte os [tutoriais passo a passo.](../index.yml)
- Para ver os esquemas de modelo do Gestor de Recursos Azure, consulte [a referência do modelo](/azure/templates/).
