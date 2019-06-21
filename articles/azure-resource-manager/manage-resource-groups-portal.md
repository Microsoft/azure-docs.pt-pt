---
title: Gerir grupos do Azure Resource Manager com o portal do Azure | Documentos da Microsoft
description: Utilize o portal do Azure para gerir os seus grupos do Azure Resource Manager.
services: azure-resource-manager,azure-portal
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: jgao
ms.openlocfilehash: bc3c1a05c64edea260bd177dd7eaefc003db5310
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296297"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-the-azure-portal"></a>Gerir grupos de recursos do Azure Resource Manager com o portal do Azure

Saiba como utilizar o [portal do Azure](https://portal.azure.com) com [do Azure Resource Manager](resource-group-overview.md) para gerir os seus grupos de recursos do Azure. Para gerir recursos do Azure, veja [recursos de gerir o Azure com o portal do Azure](./manage-resources-portal.md).

Outros artigos sobre a gestão de grupos de recursos:

- [Gerir grupos de recursos do Azure com a CLI do Azure](./manage-resources-cli.md)
- [Gerir grupos de recursos do Azure com o Azure PowerShell](./manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-resource-group"></a>O que é um grupo de recursos

Um grupo de recursos é um contentor que mantém recursos relacionados para uma solução do Azure. O grupo de recursos pode incluir todos os recursos para a solução ou apenas os recursos que pretende gerir como um grupo. Decida como pretende atribuir recursos a grupos de recursos com base no que é mais adequado para a sua organização. Em geral, adicione recursos que partilham o mesmo ciclo de vida no mesmo grupo de recursos para que possa facilmente implantar, atualizar e eliminá-los como um grupo.

O grupo de recursos armazena metadados sobre os recursos. Por conseguinte, quando especifica uma localização para o grupo de recursos, está a especificar onde esses metadados estão armazenados. Por motivos de conformidade, poderá ter de certificar que os dados estão armazenados numa determinada região.

O grupo de recursos armazena metadados sobre os recursos. Quando especificar uma localização para o grupo de recursos, especifica onde esses metadados estão armazenados.

## <a name="create-resource-groups"></a>Criar grupos de recursos

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **grupos de recursos**

    ![Adicionar grupo de recursos](./media/manage-resource-groups-portal/manage-resource-groups-add-group.png)
3. Selecione **Adicionar**.
4. Introduza os seguintes valores:

   - **Subscrição**: Selecione a sua subscrição do Azure. 
   - **Grupo de recursos**: Introduza um nome de grupo de recursos novo. 
   - **Região**: Selecione uma localização do Azure, como **E.U.A. Central**.

     ![Criar grupo de recursos](./media/manage-resource-groups-portal/manage-resource-groups-create-group.png)
5. Selecione **rever + criar**
6. Selecione **Criar**. Demora alguns segundos para criar um grupo de recursos.
7. Selecione **atualizar** no menu superior para atualizar a lista do grupo de recursos e, em seguida, selecione o grupo de recursos recentemente criado para abri-lo. Ou selecione **notificação**(o ícone de sino) na parte superior e, em seguida, selecione **vá para o grupo de recursos** para abrir o grupo de recursos recém-criado

    ![Ir para o grupo de recursos](./media/manage-resource-groups-portal/manage-resource-groups-add-group-go-to-resource-group.png)

## <a name="list-resource-groups"></a>Lista de grupos de recursos

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Para listar os grupos de recursos, selecione **grupos de recursos**

    ![procurar grupos de recursos](./media/manage-resource-groups-portal/manage-resource-groups-list-groups.png)

3. Para personalizar as informações apresentadas para os grupos de recursos, selecione **editar colunas**. Captura de ecrã seguinte mostra as colunas de adição, que poderia adicionar à apresentação de:

## <a name="open-resource-groups"></a>Grupos de recursos aberto

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **Grupos de recursos**.
3. Selecione o grupo de recursos que pretende abrir.

## <a name="delete-resource-groups"></a>Eliminar grupos de recursos

1. Abra o grupo de recursos que pretende eliminar.  Ver [abrir grupos de recursos](#open-resource-groups).
2. Selecione **Eliminar grupo de recursos**.

    ![Eliminar grupo de recursos do azure](./media/manage-resource-groups-portal/delete-group.png)

Para obter mais informações sobre como o Azure Resource Manager ordena a eliminação de recursos, consulte [eliminação do grupo de recursos do Azure Resource Manager](./resource-group-delete.md).

## <a name="deploy-resources-to-a-resource-group"></a>Implementar recursos para um grupo de recursos

Depois de criar um modelo do Resource Manager, pode utilizar o portal do Azure para implementar os recursos do Azure. Para criar um modelo, consulte [início rápido: Criar e implementar modelos Azure Resource Manager com o portal do Azure](./resource-manager-quickstart-create-templates-use-the-portal.md). Para implementar um modelo com o portal, consulte [implementar recursos com modelos do Resource Manager e o portal do Azure](resource-group-template-deploy-portal.md).

## <a name="move-to-another-resource-group-or-subscription"></a>Mover para outro grupo de recursos ou subscrição

Pode mover os recursos no grupo para outro grupo de recursos. Para obter mais informações, consulte [Mover recursos para um novo grupo de recursos ou subscrição](resource-group-move-resources.md).

## <a name="lock-resource-groups"></a>Grupos de recursos de bloqueio

Bloqueio evita que outros utilizadores na sua organização acidentalmente eliminem ou modifiquem recursos críticos, como o recurso, grupo de recursos ou subscrição do Azure. 

1. Abra o grupo de recursos que pretende eliminar.  Ver [abrir grupos de recursos](#open-resource-groups).
2. No painel esquerdo, selecione **bloqueia**.
3. Para adicionar um bloqueio para o grupo de recursos, selecione **adicionar**.
4. Introduza **nome de bloqueio**, **tipo de bloqueio**, e **notas**. Os tipos de bloqueio incluem **só de leitura**, e **eliminar**.

    ![grupo de recursos do azure de bloqueio](./media/manage-resource-groups-portal/manage-resource-groups-add-lock.png)

Para obter mais informações, consulte [bloquear recursos pode prevenir alterações inesperadas](./resource-group-lock-resources.md).

## <a name="tag-resource-groups"></a>Grupos de recursos de etiqueta

Pode aplicar etiquetas a grupos de recursos e recursos para organizar logicamente os recursos. Para obter informações, consulte [utilizar etiquetas para organizar os recursos do Azure](./resource-group-using-tags.md#portal).

## <a name="export-resource-groups-to-templates"></a>Grupos de recursos de exportação para modelos

Para obter informações sobre a exportação de modelos, consulte [exportação única e vários recurso para o modelo - Portal](export-template-portal.md).

## <a name="manage-access-to-resource-groups"></a>Gerir o acesso aos grupos de recursos

[O controlo de acesso baseado em funções (RBAC)](../role-based-access-control/overview.md) é a forma de gerir o acesso a recursos no Azure. Para obter mais informações, consulte [gerir o acesso com RBAC e o portal do Azure](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Passos Seguintes

- Para obter o Azure Resource Manager, veja [descrição geral do Azure Resource Manager](./resource-group-overview.md).
- Para obter a sintaxe do modelo do Resource Manager, veja [compreender a estrutura e a sintaxe de modelos Azure Resource Manager](./resource-group-authoring-templates.md).
- Para saber como desenvolver modelos, veja a [tutoriais passo a passo](/azure/azure-resource-manager/).
- Para ver os esquemas de modelo do Azure Resource Manager, consulte [referência de modelo](/azure/templates/).