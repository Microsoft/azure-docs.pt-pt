---
title: Adicione ou remova atribuições de funções Azure usando o portal Azure - Azure RBAC
description: Saiba como conceder acesso aos recursos Azure para utilizadores, grupos, diretores de serviços ou identidades geridas utilizando o portal Azure e o controlo de acesso baseado em funções Azure (Azure RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/25/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 3e4d2dca6817951f2f06a86c4338106f194b7751
ms.sourcegitcommit: 6571e34e609785e82751f0b34f6237686470c1f3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/15/2020
ms.locfileid: "84790964"
---
# <a name="add-or-remove-azure-role-assignments-using-the-azure-portal"></a>Adicione ou remova atribuições de funções Azure usando o portal Azure

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]Este artigo descreve como atribuir funções usando o portal Azure.

Se precisar de atribuir funções de administrador no Azure Ative Directory, consulte [Ver e atribuir funções de administrador no Azure Ative Directory](../active-directory/users-groups-roles/directory-manage-roles-portal.md).

## <a name="prerequisites"></a>Pré-requisitos

Para adicionar ou remover atribuições de funções, você deve ter:

- `Microsoft.Authorization/roleAssignments/write`e `Microsoft.Authorization/roleAssignments/delete` permissões, tais como [Administrador de Acesso ao Utilizador](built-in-roles.md#user-access-administrator) ou [Proprietário](built-in-roles.md#owner)

## <a name="access-control-iam"></a>Controlo de acesso (IAM)

**O controlo de acesso (IAM)** é a lâmina que utiliza para atribuir funções para conceder acesso aos recursos da Azure. É também conhecido como gestão de identidade e acesso e aparece em vários locais do portal Azure. O seguinte mostra um exemplo da lâmina do controlo de acesso (IAM) para uma subscrição.

![Lâmina de controlo de acesso (IAM) para uma subscrição](./media/role-assignments-portal/access-control-subscription.png)

Para ser o mais eficaz com a lâmina do controlo de acesso (IAM), ajuda se conseguir responder às seguintes três perguntas quando está a tentar atribuir uma função:

1. **Quem precisa de acesso?**

    Que se refere a um utilizador, grupo, principal de serviço ou identidade gerida. Isto também é chamado de diretor de *segurança.*

1. **De que papel precisam?**

    As permissões são agrupadas em papéis. Pode selecionar a partir de uma lista de várias [funções incorporadas](built-in-roles.md) ou pode usar as suas próprias funções personalizadas.

1. **Onde precisam de acesso?**

    Quando se refere ao conjunto de recursos a que o acesso se aplica. Onde pode ser um grupo de gestão, subscrição, grupo de recursos ou um único recurso, como uma conta de armazenamento. Isto chama-se *o âmbito.*

## <a name="add-a-role-assignment"></a>Adicionar uma atribuição de função

No Azure RBAC, para conceder acesso a um recurso Azure, adiciona-se uma atribuição de funções. Siga estes passos para atribuir um papel.

1. No portal Azure, clique em **Todos os serviços** e, em seguida, selecione o âmbito a que pretende conceder acesso. Por exemplo, pode selecionar **grupos de Gestão,** **Assinaturas, Grupos** **de Recursos**ou um recurso.

1. Clique no recurso específico para esse âmbito.

1. Clique em **Controlo de acesso (IAM)** .

1. Clique no separador **atribuições de funções** para ver as atribuições de funções neste âmbito.

    ![Separador de atribuições de controlo de acesso (IAM) e atribuições de funções](./media/role-assignments-portal/role-assignments.png)

1. Clique **em Adicionar**Adicionar a atribuição de  >  **função**.

   Se não tiver permissões para atribuir funções, a opção de atribuição de funções Add será desativada.

   ![Adicionar menu](./media/role-assignments-portal/add-menu.png)

    O painel Adicionar atribuição de função é aberto.

   ![Adicione painel de atribuição de funções](./media/role-assignments-portal/add-role-assignment.png)

1. Na lista pendente **Função**, selecione uma função, como **Contribuidor de Máquina Virtual**.

1. Na lista **Select,** selecione um utilizador, grupo, principal de serviço ou identidade gerida. Se não vir o principal de segurança na lista, pode escrever na caixa **Selecionar** para procurar no diretório os nomes a apresentar, endereços de e-mail e identificadores de objetos.

1. Clique em **Guardar** para atribuir a função.

   Após alguns momentos, o diretor de segurança é atribuído o papel no âmbito selecionado.

    ![Adicionar atribuição de função guardada](./media/role-assignments-portal/add-role-assignment-save.png)

## <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Atribuir um utilizador como administrador de uma subscrição

Para tornar um utilizador um administrador de uma subscrição Azure, atribua-lhes a função [De Proprietário](built-in-roles.md#owner) no âmbito de subscrição. A função Proprietário dá ao utilizador acesso total a todos os recursos da subscrição, incluindo a permissão para conceder acesso a outros. Estes passos são iguais a qualquer outra atribuição de função.

1. No portal Azure, clique em **Todos os serviços** e, em seguida, **Em Assinaturas**.

1. Clique na subscrição na qual pretende conceder acesso.

1. Clique em **Controlo de acesso (IAM)** .

1. Clique no **separador atribuições de funções** para ver as atribuições de funções para esta subscrição.

    ![Separador de atribuições de controlo de acesso (IAM) e atribuições de funções](./media/role-assignments-portal/role-assignments.png)

1. Clique **em Adicionar**Adicionar a atribuição de  >  **função**.

   Se não tiver permissões para atribuir funções, a opção de atribuição de funções Add será desativada.

   ![Adicionar menu](./media/role-assignments-portal/add-menu.png)

    O painel Adicionar atribuição de função é aberto.

   ![Adicione painel de atribuição de funções](./media/role-assignments-portal/add-role-assignment.png)

1. Na lista pendente **Função**, selecione a função **Proprietário**.

1. Na lista **Selecionar**, selecione um utilizador. Se não vir o utilizador na lista, pode escrever na caixa **Selecionar** para procurar no diretório os nomes a apresentar e endereços de e-mail.

1. Clique em **Guardar** para atribuir a função.

   Após alguns instantes, é atribuída ao utilizador a função Proprietário no âmbito da subscrição.

## <a name="remove-a-role-assignment"></a>Remover uma atribuição de função

No Azure RBAC, para remover o acesso de um recurso Azure, remove-se uma atribuição de funções. Siga estes passos para remover uma tarefa de função.

1. Controlo **de acesso aberto (IAM)** num âmbito, como grupo de gestão, subscrição, grupo de recursos ou recursos, onde pretende remover o acesso.

1. Clique no separador **Atribuições de funções** para ver todas as atribuições de funções para esta subscrição.

1. Na lista de atribuições de funções, adicione uma marca de verificação junto ao principal de segurança com a atribuição de função que pretende remover.

   ![Mensagem Remover atribuição de função](./media/role-assignments-portal/remove-role-assignment-select.png)

1. Clique em **Remover**.

   ![Mensagem Remover atribuição de função](./media/role-assignments-portal/remove-role-assignment.png)

1. Na mensagem de atribuição de funções removida que aparece, clique em **Sim**.

    Se vir uma mensagem de que as atribuições de funções herdadas não podem ser removidas, está a tentar remover uma tarefa de papel no âmbito de uma criança. Deve abrir o controlo de acesso (IAM) no âmbito onde a função foi atribuída e tentar novamente. Uma forma rápida de abrir o controlo de acesso (IAM) no âmbito correto é olhar para a coluna **Scope** e clicar no link ao lado **de (Herdado)**.

   ![Mensagem Remover atribuição de função](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>Passos seguintes

- [Liste atribuições de funções Azure usando o portal Azure](role-assignments-list-portal.md)
- [Tutorial: Conceder acesso a um utilizador aos recursos do Azure através do portal Azure](quickstart-assign-role-user-portal.md)
- [Resolução de problemas Azure RBAC](troubleshooting.md)
- [Organize your resources with Azure management groups](../governance/management-groups/overview.md) (Organizar os recursos com os grupos de gestão do Azure)
