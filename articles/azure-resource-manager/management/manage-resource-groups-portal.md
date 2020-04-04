---
title: Gerir grupos de recursos - Portal Azure
description: Utilize o portal Azure para gerir os seus grupos de recursos através do Azure Resource Manager. Mostra como criar, listar e eliminar grupos de recursos.
author: mumian
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: jgao
ms.openlocfilehash: 622c0cb8d58dddcdb13c7dc9412ada3f2e2d93e4
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632978"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-the-azure-portal"></a>Gerir os grupos de recursos do Gestor de Recursos Azure utilizando o portal Azure

Aprenda a utilizar o [portal Azure](https://portal.azure.com) com o Gestor de [Recursos Azure](overview.md) para gerir os seus grupos de recursos Azure. Para gerir os recursos do Azure, consulte [gerir os recursos do Azure utilizando o portal Azure.](manage-resources-portal.md)

Outros artigos sobre a gestão de grupos de recursos:

- [Gerir os grupos de recursos azure utilizando o Azure CLI](manage-resources-cli.md)
- [Gerir grupos de recursos Azure utilizando o Azure PowerShell](manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-resource-group"></a>O que é um grupo de recursos

Um grupo de recursos é um contentor que mantém recursos relacionados para uma solução do Azure. O grupo de recursos pode incluir todos os recursos para a solução ou apenas os recursos que pretende gerir como um grupo. Decida como pretende atribuir recursos a grupos de recursos com base no que é mais adequado para a sua organização. Geralmente, adicione recursos que partilham o mesmo ciclo de vida ao mesmo grupo de recursos para que possa facilmente implantá-los, atualizar e eliminá-los como um grupo.

O grupo de recursos armazena metadados sobre os recursos. Por conseguinte, quando especifica uma localização para o grupo de recursos, está a especificar onde esses metadados estão armazenados. Por motivos de conformidade, poderá ter de certificar que os dados estão armazenados numa determinada região.


## <a name="create-resource-groups"></a>Criar grupos de recursos

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Selecione **grupos de recursos**

    ![adicionar grupo de recursos](./media/manage-resource-groups-portal/manage-resource-groups-add-group.png)
3. Selecione **Adicionar**.
4. Introduza os seguintes valores:

   - **Subscrição**: selecione a sua subscrição do Azure. 
   - **Grupo de recursos**: Introduza um novo nome de grupo de recursos. 
   - **Região**: Selecione uma localização Azure, como **o Centro dos EUA**.

     ![criar grupo de recursos](./media/manage-resource-groups-portal/manage-resource-groups-create-group.png)
5. Selecione **Review + Criar**
6. Selecione **Criar**. Leva alguns segundos para criar um grupo de recursos.
7. Selecione **Refresh** do menu superior para refrescar a lista de grupos de recursos e, em seguida, selecione o grupo de recursos recém-criado para abri-la. Ou selecione **Notificação**(o ícone da campainha) a partir do topo e, em seguida, selecione **Ir para o grupo de recursos** para abrir o grupo de recursos recém-criado

    ![ir para grupo de recursos](./media/manage-resource-groups-portal/manage-resource-groups-add-group-go-to-resource-group.png)

## <a name="list-resource-groups"></a>Lista de grupos de recursos

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Para listar os grupos de recursos, selecione **grupos de recursos**

    ![navegar em grupos de recursos](./media/manage-resource-groups-portal/manage-resource-groups-list-groups.png)

3. Para personalizar as informações apresentadas para os grupos de recursos, selecione **colunas Editar**. A imagem a seguir mostra as colunas de adição que pode adicionar ao visor:

## <a name="open-resource-groups"></a>Grupos de recursos abertos

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Selecione **Grupos de recursos**.
3. Selecione o grupo de recursos que pretende abrir.

## <a name="delete-resource-groups"></a>Eliminar grupos de recursos

1. Abra o grupo de recursos que pretende eliminar.  Ver [Grupos de recursos abertos](#open-resource-groups).
2. Selecione **Eliminar grupo de recursos**.

    ![eliminar grupo de recursos azure](./media/manage-resource-groups-portal/delete-group.png)

Para obter mais informações sobre como o Gestor de Recursos azure ordena a eliminação de recursos, consulte a eliminação do grupo de recursos do Gestor de [Recursos do Azure.](delete-resource-group.md)

## <a name="deploy-resources-to-a-resource-group"></a>Implementar recursos para um grupo de recursos

Depois de ter criado um modelo de Gestor de Recursos, pode utilizar o portal Azure para implementar os seus recursos Azure. Para criar um modelo, consulte [Quickstart: Crie e implemente modelos](../templates/quickstart-create-templates-use-the-portal.md)do Gestor de Recursos Azure utilizando o portal Azure . Para implementar um modelo utilizando o portal, consulte [recursos de implantação com modelos de Gestor](../templates/deploy-portal.md)de Recursos e portal Azure .

## <a name="move-to-another-resource-group-or-subscription"></a>Mude para outro grupo de recursos ou subscrição

Pode mover os recursos do grupo para outro grupo de recursos. Para obter mais informações, consulte [Mover recursos para um novo grupo de recursos ou subscrição](move-resource-group-and-subscription.md).

## <a name="lock-resource-groups"></a>Bloquear grupos de recursos

O bloqueio impede que outros utilizadores da sua organização apagam ou modifiquem acidentalmente recursos críticos, tais como subscrição azure, grupo de recursos ou recursos. 

1. Abra o grupo de recursos que pretende eliminar.  Ver [Grupos de recursos abertos](#open-resource-groups).
2. No painel esquerdo, selecione **Locks**.
3. Para adicionar um bloqueio ao grupo de recursos, selecione **Adicionar**.
4. Introduza **o nome da fechadura,** **o tipo de bloqueio**e **as notas.** Os tipos de bloqueio incluem **leitura apenas**e **Eliminar**.

    ![lock grupo de recursos azure](./media/manage-resource-groups-portal/manage-resource-groups-add-lock.png)

Para mais informações, consulte [os recursos de bloqueio para evitar alterações inesperadas](lock-resources.md).

## <a name="tag-resource-groups"></a>Grupos de recursos de etiqueta

Pode aplicar etiquetas a grupos de recursos e recursos para organizar logicamente os seus ativos. Para obter informações, consulte [Utilizar tags para organizar os seus recursos Azure.](tag-resources.md#portal)

## <a name="export-resource-groups-to-templates"></a>Grupos de recursos de exportação para modelos

Para obter informações sobre modelos de exportação, consulte [exportação única e multi-recurso para o modelo - Portal](../templates/export-template-portal.md).

## <a name="manage-access-to-resource-groups"></a>Gerir o acesso a grupos de recursos

[O controlo de acesso baseado em funções (RBAC)](../../role-based-access-control/overview.md) é a forma como gere o acesso aos recursos em Azure. Para obter mais informações, veja [Gerir o acesso através do RBAC e do portal do Azure](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Passos seguintes

- Para aprender O Gestor de Recursos Azure, consulte a [visão geral do Gestor de Recursos do Azure.](overview.md)
- Para aprender a sintaxe do modelo do Gestor de Recursos, consulte [Compreender a estrutura e a sintaxe dos modelos do Gestor](../templates/template-syntax.md)de Recursos Azure .
- Para aprender a desenvolver modelos, consulte os [tutoriais passo a passo.](/azure/azure-resource-manager/)
- Para ver os esquemas de modelo do Gestor de Recursos Azure, consulte a referência do [modelo](/azure/templates/).
