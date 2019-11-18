---
title: Gerenciar grupos de recursos-portal do Azure
description: Use portal do Azure para gerenciar seus grupos de recursos por meio de Azure Resource Manager. Mostra como criar, listar e excluir grupos de recursos.
author: mumian
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: jgao
ms.openlocfilehash: e3402f496371fe89c832dc22c8288a1b94378c82
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74147151"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-the-azure-portal"></a>Gerenciar Azure Resource Manager grupos de recursos usando o portal do Azure

Saiba como usar o [portal do Azure](https://portal.azure.com) com [Azure Resource Manager](resource-group-overview.md) para gerenciar seus grupos de recursos do Azure. Para gerenciar recursos do Azure, consulte [gerenciar recursos do Azure usando o portal do Azure](./manage-resources-portal.md).

Outros artigos sobre como gerenciar grupos de recursos:

- [Gerenciar grupos de recursos do Azure usando o CLI do Azure](./manage-resources-cli.md)
- [Gerenciar grupos de recursos do Azure usando o Azure PowerShell](./manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-resource-group"></a>O que é um grupo de recursos

Um grupo de recursos é um contentor que mantém recursos relacionados para uma solução do Azure. O grupo de recursos pode incluir todos os recursos para a solução ou apenas os recursos que pretende gerir como um grupo. Decida como pretende atribuir recursos a grupos de recursos com base no que é mais adequado para a sua organização. Em geral, adicione recursos que compartilham o mesmo ciclo de vida ao mesmo grupo de recursos para que você possa implantá-los, atualizá-los e excluí-los facilmente como um grupo.

O grupo de recursos armazena metadados sobre os recursos. Por conseguinte, quando especifica uma localização para o grupo de recursos, está a especificar onde esses metadados estão armazenados. Por motivos de conformidade, poderá ter de certificar que os dados estão armazenados numa determinada região.

O grupo de recursos armazena metadados sobre os recursos. Ao especificar um local para o grupo de recursos, você está especificando onde os metadados são armazenados.

## <a name="create-resource-groups"></a>Criar grupos de recursos

1. Iniciar sessão no [portal do Azure](https://portal.azure.com).
2. Selecionar **grupos de recursos**

    ![Adicionar grupo de recursos](./media/manage-resource-groups-portal/manage-resource-groups-add-group.png)
3. Selecione **Adicionar**.
4. Introduza os seguintes valores:

   - **Subscrição**: selecione a sua subscrição do Azure. 
   - **Grupo de recursos**: Insira um novo nome de grupo de recursos. 
   - **Região**: selecione um local do Azure, como **EUA Central**.

     ![Criar grupo de recursos](./media/manage-resource-groups-portal/manage-resource-groups-create-group.png)
5. Selecione **revisão + criar**
6. Selecione **Criar**. Leva alguns segundos para criar um grupo de recursos.
7. Selecione **Atualizar** no menu superior para atualizar a lista grupo de recursos e, em seguida, selecione o grupo de recursos recém-criado para abri-lo. Ou selecione **notificação**(o ícone de sino) na parte superior e, em seguida, selecione **ir para o grupo de recursos** para abrir o grupo de recursos recém-criado

    ![ir para o grupo de recursos](./media/manage-resource-groups-portal/manage-resource-groups-add-group-go-to-resource-group.png)

## <a name="list-resource-groups"></a>Listar grupos de recursos

1. Iniciar sessão no [portal do Azure](https://portal.azure.com).
2. Para listar os grupos de recursos, selecione **grupos de recursos**

    ![procurar grupos de recursos](./media/manage-resource-groups-portal/manage-resource-groups-list-groups.png)

3. Para personalizar as informações exibidas para os grupos de recursos, selecione **Editar colunas**. A captura de tela a seguir mostra as colunas de adição que você pode adicionar à exibição:

## <a name="open-resource-groups"></a>Abrir grupos de recursos

1. Iniciar sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **Grupos de recursos**.
3. Selecione o grupo de recursos que você deseja abrir.

## <a name="delete-resource-groups"></a>Excluir grupos de recursos

1. Abra o grupo de recursos que você deseja excluir.  Consulte [abrir grupos de recursos](#open-resource-groups).
2. Selecione **Eliminar grupo de recursos**.

    ![excluir grupo de recursos do Azure](./media/manage-resource-groups-portal/delete-group.png)

Para obter mais informações sobre como Azure Resource Manager ordena a exclusão de recursos, consulte [Azure Resource Manager exclusão de grupo de recursos](./resource-group-delete.md).

## <a name="deploy-resources-to-a-resource-group"></a>Implantar recursos em um grupo de recursos

Depois de criar um modelo do Resource Manager, você pode usar o portal do Azure para implantar os recursos do Azure. Para criar um modelo, consulte [início rápido: criar e implantar modelos de Azure Resource Manager usando o portal do Azure](./resource-manager-quickstart-create-templates-use-the-portal.md). Para implantar um modelo usando o portal, consulte [implantar recursos com modelos do Resource Manager e portal do Azure](resource-group-template-deploy-portal.md).

## <a name="move-to-another-resource-group-or-subscription"></a>Mover para outro grupo de recursos ou assinatura

Você pode mover os recursos do grupo para outro grupo de recursos. Para obter mais informações, consulte [Mover recursos para um novo grupo de recursos ou subscrição](resource-group-move-resources.md).

## <a name="lock-resource-groups"></a>Bloquear grupos de recursos

O bloqueio impede que outros usuários em sua organização excluam ou modifiquem acidentalmente recursos críticos, como assinatura do Azure, grupo de recursos ou recurso. 

1. Abra o grupo de recursos que você deseja excluir.  Consulte [abrir grupos de recursos](#open-resource-groups).
2. No painel esquerdo, selecione **bloqueios**.
3. Para adicionar um bloqueio ao grupo de recursos, selecione **Adicionar**.
4. Insira o **nome do bloqueio**, o tipo de **bloqueio**e as **observações**. Os tipos de bloqueio incluem **somente leitura**e **excluir**.

    ![bloquear grupo de recursos do Azure](./media/manage-resource-groups-portal/manage-resource-groups-add-lock.png)

Para obter mais informações, consulte [Bloquear recursos para evitar alterações inesperadas](./resource-group-lock-resources.md).

## <a name="tag-resource-groups"></a>Marcar grupos de recursos

Você pode aplicar marcas a recursos e grupos de recursos para organizar logicamente seus ativos. Para obter informações, consulte [usando marcas para organizar os recursos do Azure](./resource-group-using-tags.md#portal).

## <a name="export-resource-groups-to-templates"></a>Exportar grupos de recursos para modelos

Para obter informações sobre como exportar modelos, consulte [exportação única e de vários recursos para o portal de modelos](export-template-portal.md).

## <a name="manage-access-to-resource-groups"></a>Gerenciar o acesso a grupos de recursos

[O controlo de acesso baseado em funções (RBAC)](../role-based-access-control/overview.md) é a forma de gerir o acesso a recursos no Azure. Para obter mais informações, veja [Gerir o acesso através do RBAC e do portal do Azure](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Passos seguintes

- Para saber Azure Resource Manager, consulte [Azure Resource Manager visão geral](./resource-group-overview.md).
- Para saber mais sobre a sintaxe do modelo do Resource Manager, consulte [entender a estrutura e a sintaxe dos modelos de Azure Resource Manager](./resource-group-authoring-templates.md).
- Para saber como desenvolver modelos, consulte os tutoriais passo a [passo](/azure/azure-resource-manager/).
- Para exibir os esquemas de modelo de Azure Resource Manager, consulte [referência de modelo](/azure/templates/).