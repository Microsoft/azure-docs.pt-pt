---
title: Gerir grupos de recursos - Azure CLI
description: Utilize o Azure CLI para gerir os seus grupos de recursos através do Azure Resource Manager. Mostra como criar, listar e eliminar grupos de recursos.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 7face572f545153ea92efbdb345bbaabda5dd126
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79248336"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-cli"></a>Gerir os grupos de recursos do Gestor de Recursos Azure utilizando o Azure CLI

Aprenda a utilizar o Azure CLI com o [Azure Resource Manager](overview.md) para gerir os seus grupos de recursos Azure. Para gerir os recursos do Azure, consulte [gerir os recursos do Azure utilizando o Azure CLI](manage-resources-cli.md).

Outros artigos sobre a gestão de grupos de recursos:

- [Gerir os grupos de recursos azure utilizando o portal Azure](manage-resources-portal.md)
- [Gerir grupos de recursos Azure utilizando o Azure PowerShell](manage-resources-powershell.md)

## <a name="what-is-a-resource-group"></a>O que é um grupo de recursos

Um grupo de recursos é um contentor que mantém recursos relacionados para uma solução do Azure. O grupo de recursos pode incluir todos os recursos para a solução ou apenas os recursos que pretende gerir como um grupo. Decida como pretende atribuir recursos a grupos de recursos com base no que é mais adequado para a sua organização. Geralmente, adicione recursos que partilham o mesmo ciclo de vida ao mesmo grupo de recursos para que possa facilmente implantá-los, atualizar e eliminá-los como um grupo.

O grupo de recursos armazena metadados sobre os recursos. Por conseguinte, quando especifica uma localização para o grupo de recursos, está a especificar onde esses metadados estão armazenados. Por motivos de conformidade, poderá ter de certificar que os dados estão armazenados numa determinada região.

O grupo de recursos armazena metadados sobre os recursos. Quando especifica uma localização para o grupo de recursos, está a especificar onde os metadados são armazenados.

## <a name="create-resource-groups"></a>Criar grupos de recursos

O seguinte script CLI cria um grupo de recursos, e em seguida, mostra o grupo de recursos.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location $location
```

## <a name="list-resource-groups"></a>Lista de grupos de recursos

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

Para obter mais informações sobre como o Gestor de Recursos azure ordena a eliminação de recursos, consulte a eliminação do grupo de recursos do Gestor de [Recursos do Azure.](delete-resource-group.md)

## <a name="deploy-resources-to-an-existing-resource-group"></a>Mobilizar recursos para um grupo de recursos existente

Consulte [os recursos de implantação para um grupo](manage-resources-cli.md#deploy-resources-to-an-existing-resource-group)de recursos existentes.

## <a name="deploy-a-resource-group-and-resources"></a>Implementar um grupo de recursos e recursos

Você pode criar um grupo de recursos e implementar recursos para o grupo usando um modelo de Gestor de Recursos. Para mais informações, consulte [Criar grupo de recursos e implementar recursos.](../templates/deploy-to-subscription.md#resource-group-and-resources)

## <a name="redeploy-when-deployment-fails"></a>Reimplantação quando a implantação falha

Esta funcionalidade também é conhecida como *Rollback no erro*. Para mais informações, consulte [Reimplantação quando a implementação falhar](../templates/rollback-on-error.md).

## <a name="move-to-another-resource-group-or-subscription"></a>Mude para outro grupo de recursos ou subscrição

Pode mover os recursos do grupo para outro grupo de recursos. Para mais informações, consulte [Mover recursos.](manage-resources-cli.md#move-resources)

## <a name="lock-resource-groups"></a>Bloquear grupos de recursos

O bloqueio impede que outros utilizadores da sua organização apagam ou modifiquem acidentalmente recursos críticos, tais como subscrição azure, grupo de recursos ou recursos. 

O seguinte script bloqueia um grupo de recursos para que o grupo de recursos não possa ser eliminado.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock create --name LockGroup --lock-type CanNotDelete --resource-group $resourceGroupName  
```

O seguinte script recebe todas as fechaduras para um grupo de recursos:

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

Pode aplicar etiquetas a grupos de recursos e recursos para organizar logicamente os seus ativos. Para obter informações, consulte [Utilizar tags para organizar os seus recursos Azure.](tag-resources.md#azure-cli)

## <a name="export-resource-groups-to-templates"></a>Grupos de recursos de exportação para modelos

Depois de configurar o seu grupo de recursos com sucesso, pode querer ver o modelo de Gestor de Recursos para o grupo de recursos. Exportar o modelo oferece dois benefícios:

- Automatizar futuras implementações da solução porque o modelo contém toda a infraestrutura completa.
- Aprenda a sintaxe do modelo olhando para a Notação de Objetos JavaScript (JSON) que representa a sua solução.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group export --name $resourceGroupName  
```

O script exibe o modelo na consola.  Copie o JSON e guarde como ficheiro.

O modelo de exportação não suporta a exportação de recursos da Azure Data Factory. Para saber como pode exportar recursos da Fábrica de Dados, consulte [copiar ou clonar uma fábrica](https://aka.ms/exportTemplateViaAdf)de dados na Azure Data Factory .

Para exportar recursos criados através de um modelo de implantação clássico, deve [emigrar para o modelo](https://aka.ms/migrateclassicresourcetoarm)de implantação do Gestor de Recursos.

Para mais informações, consulte [a exportação de monoe e multi-recursos para o modelo no portal Azure](../templates/export-template-portal.md).

## <a name="manage-access-to-resource-groups"></a>Gerir o acesso a grupos de recursos

[O controlo de acesso baseado em funções (RBAC)](../../role-based-access-control/overview.md) é a forma de gerir o acesso a recursos no Azure. Para mais informações, consulte [Gerir o acesso utilizando rBAC e Azure CLI](../../role-based-access-control/role-assignments-cli.md).

## <a name="next-steps"></a>Passos seguintes

- Para aprender O Gestor de Recursos Azure, consulte a [visão geral do Gestor de Recursos do Azure.](overview.md)
- Para aprender a sintaxe do modelo do Gestor de Recursos, consulte [Compreender a estrutura e a sintaxe dos modelos do Gestor](../templates/template-syntax.md)de Recursos Azure .
- Para aprender a desenvolver modelos, consulte os [tutoriais passo a passo.](/azure/azure-resource-manager/)
- Para ver os esquemas de modelo do Gestor de Recursos Azure, consulte a referência do [modelo](/azure/templates/).