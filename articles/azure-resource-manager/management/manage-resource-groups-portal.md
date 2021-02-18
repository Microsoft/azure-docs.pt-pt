---
title: Gerir grupos de recursos - Portal Azure
description: Utilize o portal Azure para gerir os seus grupos de recursos através do Azure Resource Manager. Mostra como criar, listar e eliminar grupos de recursos.
author: mumian
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: jgao
ms.openlocfilehash: aabaad54dce52d57aaa4689f591351e290b0f9a3
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "101093694"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-the-azure-portal"></a>Gerir grupos de recursos do Azure Resource Manager utilizando o portal Azure

Saiba como utilizar o [portal Azure](https://portal.azure.com) com [o Azure Resource Manager](overview.md) para gerir os seus grupos de recursos Azure. Para gerir os recursos da Azure, consulte [gerir os recursos do Azure utilizando o portal Azure.](manage-resources-portal.md)

Outros artigos sobre gestão de grupos de recursos:

- [Gerir grupos de recursos Azure utilizando o Azure CLI](manage-resources-cli.md)
- [Gerir grupos de recursos Azure utilizando a Azure PowerShell](manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-resource-group"></a>O que é um grupo de recursos

Um grupo de recursos é um contentor que detém recursos relacionados para uma solução do Azure. O grupo de recursos pode incluir todos os recursos para a solução ou apenas os recursos que pretende gerir como um grupo. Decida como pretende atribuir recursos a grupos de recursos com base no que é mais adequado para a sua organização. Geralmente, adicione recursos que partilham o mesmo ciclo de vida com o mesmo grupo de recursos para que possa facilmente implementar, atualizar e apagar como um grupo.

O grupo de recursos armazena metadados sobre os recursos. Por conseguinte, quando especifica uma localização para o grupo de recursos, está a especificar onde esses metadados estão armazenados. Por motivos de conformidade, poderá ter de certificar que os dados estão armazenados numa determinada região.


## <a name="create-resource-groups"></a>Criar grupos de recursos

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **grupos de recursos**

    ![adicionar grupo de recursos](./media/manage-resource-groups-portal/manage-resource-groups-add-group.png)
3. Selecione **Adicionar**.
4. Introduza os seguintes valores:

   - **Subscrição**: selecione a sua subscrição do Azure. 
   - **Grupo de recursos**: Introduza um novo nome de grupo de recursos. 
   - **Região**: Selecione uma localização Azure, como **a Central US**.

     ![criar grupo de recursos](./media/manage-resource-groups-portal/manage-resource-groups-create-group.png)
5. **Selecione Review + Criar**
6. Selecione **Criar**. Leva alguns segundos para criar um grupo de recursos.
7. Selecione **Refresh** do menu superior para refrescar a lista de grupos de recursos e, em seguida, selecione o grupo de recursos recém-criado para abri-la. Ou selecione **Notificação**(o ícone da campainha) a partir do topo e, em seguida, selecione Ir para o **grupo de recursos** para abrir o grupo de recursos recém-criado

    ![ir para o grupo de recursos](./media/manage-resource-groups-portal/manage-resource-groups-add-group-go-to-resource-group.png)

## <a name="list-resource-groups"></a>Grupos de recursos de lista

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Para listar os grupos de recursos, selecione **grupos de recursos**

    ![procurar grupos de recursos](./media/manage-resource-groups-portal/manage-resource-groups-list-groups.png)

3. Para personalizar as informações apresentadas para os grupos de recursos, **selecione Editar colunas**. A imagem que se segue mostra as colunas de adição que pode adicionar ao visor:

## <a name="open-resource-groups"></a>Grupos de recursos abertos

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **Grupos de recursos**.
3. Selecione o grupo de recursos que pretende abrir.

## <a name="delete-resource-groups"></a>Eliminar grupos de recursos

1. Abra o grupo de recursos que pretende eliminar.  Consulte [grupos de recursos abertos](#open-resource-groups).
2. Selecione **Eliminar grupo de recursos**.

    ![eliminar grupo de recursos azul](./media/manage-resource-groups-portal/delete-group.png)

Para obter mais informações sobre como o Azure Resource Manager ordena a supressão de recursos, consulte a eliminação do [grupo de recursos Azure Resource Manager](delete-resource-group.md).

## <a name="deploy-resources-to-a-resource-group"></a>Mobilizar recursos para um grupo de recursos

Depois de ter criado um modelo de Gestor de Recursos, pode utilizar o portal Azure para implementar os seus recursos Azure. Para criar um modelo, consulte [Quickstart: Crie e implemente os modelos do Gestor de Recursos Azure utilizando o portal Azure](../templates/quickstart-create-templates-use-the-portal.md). Para implementar um modelo utilizando o portal, consulte [implementar recursos com modelos de Gestor de Recursos e portal Azure](../templates/deploy-portal.md).

## <a name="move-to-another-resource-group-or-subscription"></a>Mude-se para outro grupo de recursos ou subscrição

Pode mover os recursos do grupo para outro grupo de recursos. Para obter mais informações, consulte [Mover recursos para um novo grupo de recursos ou subscrição](move-resource-group-and-subscription.md).

## <a name="lock-resource-groups"></a>Bloquear grupos de recursos

O bloqueio impede que outros utilizadores da sua organização apaguem ou modifiquem acidentalmente recursos críticos, tais como subscrição do Azure, grupo de recursos ou recursos. 

1. Abra o grupo de recursos que pretende bloquear.  Consulte [grupos de recursos abertos](#open-resource-groups).
2. No painel esquerdo, selecione **Fechaduras**.
3. Para adicionar uma fechadura ao grupo de recursos, **selecione Adicionar**.
4. Introduza **o nome de bloqueio,** **o tipo de bloqueio** e **as notas**. Os tipos de bloqueio incluem **Apenas leitura** e **excluir**.

    ![bloquear grupo de recursos azul](./media/manage-resource-groups-portal/manage-resource-groups-add-lock.png)

Para obter mais informações, consulte [os recursos de bloqueio para evitar alterações inesperadas](lock-resources.md).

## <a name="tag-resource-groups"></a>Grupos de recursos de etiqueta

Pode aplicar etiquetas a grupos de recursos e recursos para organizar logicamente os seus ativos. Para obter informações, consulte [a utilização de tags para organizar os seus recursos Azure.](tag-resources.md#portal)

## <a name="export-resource-groups-to-templates"></a>Grupos de recursos de exportação para modelos

Para obter informações sobre modelos de exportação, consulte [exportação única e multi-recursos para o modelo - Portal](../templates/export-template-portal.md).

## <a name="manage-access-to-resource-groups"></a>Gerir o acesso a grupos de recursos

[O controlo de acesso baseado em funções (Azure RBAC)](../../role-based-access-control/overview.md) é a forma como gere o acesso aos recursos em Azure. Para obter mais informações, consulte [as funções De Atribuição Azure utilizando o portal Azure](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Passos seguintes

- Para saber o Gestor de Recursos Azure, consulte [a visão geral do Azure Resource Manager](overview.md).
- Para aprender a sintaxe do modelo do Gestor de Recursos, consulte [compreender a estrutura e a sintaxe dos modelos do Gestor de Recursos Azure](../templates/template-syntax.md).
- Para aprender a desenvolver modelos, consulte os [tutoriais passo a passo.](../index.yml)
- Para ver os esquemas de modelo do Gestor de Recursos Azure, consulte [a referência do modelo](/azure/templates/).
