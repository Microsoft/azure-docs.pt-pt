---
title: Adicione ou remova atribuições de funções Azure usando o portal Azure - Azure RBAC
description: Saiba como conceder acesso aos recursos Azure para utilizadores, grupos, diretores de serviços ou identidades geridas utilizando o portal Azure e o controlo de acesso baseado em funções Azure (Azure RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 01/11/2021
ms.author: rolyon
ms.openlocfilehash: f1753e7bc50fa9ff2c5512696a37dae7578f23b4
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98117449"
---
# <a name="add-or-remove-azure-role-assignments-using-the-azure-portal"></a>Adicionar ou remover atribuições de funções do Azure com o portal do Azure

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] Este artigo descreve como atribuir funções usando o portal Azure.

Se precisar de atribuir funções de administrador no Azure Ative Directory, consulte [Ver e atribuir funções de administrador no Azure Ative Directory](../active-directory/roles/manage-roles-portal.md).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="add-a-role-assignment"></a>Adicionar uma atribuição de função

No Azure RBAC, para conceder acesso a um recurso Azure, adiciona-se uma atribuição de funções. Siga estes passos para atribuir um papel. Para obter uma visão geral de etapas de alto nível, consulte [Passos para adicionar uma atribuição de funções](role-assignments-steps.md).

### <a name="step-1-identify-the-needed-scope"></a>Passo 1: Identificar o âmbito necessário

[!INCLUDE [Scope for Azure RBAC introduction](../../includes/role-based-access-control/scope-intro.md)]

[!INCLUDE [Scope for Azure RBAC least privilege](../../includes/role-based-access-control/scope-least.md)]Para obter mais informações sobre o âmbito, consulte [o âmbito de aplicação.](scope-overview.md)

![Níveis de âmbito para Azure RBAC](../../includes/role-based-access-control/media/scope-levels.png)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Na caixa 'Procurar' no topo, procure o âmbito a que pretende conceder acesso. Por exemplo, procure **grupos de Gestão,** **Assinaturas, Grupos** **de Recursos** ou um recurso específico.

    ![Pesquisa do portal Azure para grupo de recursos](./media/shared/rg-portal-search.png)

1. Clique no recurso específico para esse âmbito.

    O seguinte mostra um grupo de recursos exemplo.

    ![Visão geral do grupo de recursos](./media/shared/rg-overview.png)

### <a name="step-2-open-the-add-role-assignment-pane"></a>Passo 2: Abra o painel de atribuição de funções adicionar

**O controlo de acesso (IAM)** é a página que normalmente utiliza para atribuir funções para conceder acesso aos recursos do Azure. É também conhecido como gestão de identidade e acesso (IAM) e aparece em vários locais do portal Azure.

1. Clique em **Controlo de acesso (IAM)**.

    O seguinte mostra um exemplo da página access control (IAM) para um grupo de recursos.

    ![Página de controlo de acesso (IAM) para um grupo de recursos](./media/shared/rg-access-control.png)

1. Clique no separador **atribuições de funções** para ver as atribuições de funções neste âmbito.

1. Clique **em Adicionar** Adicionar a atribuição de  >  **função**.
   Se não tiver permissões para atribuir funções, a opção de atribuição de funções Add será desativada.

   ![Adicionar menu de atribuição de funções](./media/shared/add-role-assignment-menu.png)

    O painel Adicionar atribuição de função é aberto.

   ![Adicione painel de atribuição de funções](./media/shared/add-role-assignment.png)

### <a name="step-3-select-the-appropriate-role"></a>Passo 3: Selecione a função adequada

1. Na lista **De Função,** procure ou percorra para encontrar o papel que pretende atribuir.

    Para ajudá-lo a determinar o papel apropriado, pode pairar sobre o ícone da informação para mostrar uma descrição para o papel. Para mais informações, pode ver o artigo [de funções incorporadas do Azure.](built-in-roles.md)

   ![Selecione papel na atribuição de função Add](./media/role-assignments-portal/add-role-assignment-role.png)

1. Clique para selecionar o papel.

### <a name="step-4-select-who-needs-access"></a>Passo 4: Selecione quem precisa de acesso

1. No acesso à lista **de atribuições,** selecione o tipo de princípio de segurança para atribuir acesso.

    | Tipo | Descrição |
    | --- | --- |
    | **Utilizador, grupo ou principal de serviço** | Se pretender atribuir a função a um utilizador, grupo ou principal de serviço (aplicação), selecione este tipo. |
    | **Identidade gerida atribuída pelo utilizador** | Se pretender atribuir a função a uma [identidade gerida atribuída pelo utilizador,](../active-directory/managed-identities-azure-resources/overview.md)selecione este tipo. |
    | *Identidade gerida atribuída ao sistema* | Se pretender atribuir a função a uma identidade gerida atribuída ao [sistema,](../active-directory/managed-identities-azure-resources/overview.md)selecione a instância de serviço Azure onde a identidade gerida está localizada. |

   ![Selecione o tipo principal de segurança na atribuição de função Adicionar](./media/role-assignments-portal/add-role-assignment-type.png)

1. Se selecionar uma identidade gerida atribuída pelo utilizador ou uma identidade gerida atribuída ao sistema, selecione a **Subscrição** onde está localizada a identidade gerida.

1. Na secção **Selecione,** procure o principal de segurança introduzindo uma cadeia ou percorrendo a lista.

   ![Selecione o utilizador na atribuição de função Adicionar](./media/role-assignments-portal/add-role-assignment-user.png)

1. Assim que encontrar o principal de segurança, clique para selecioná-lo.

### <a name="step-5-assign-role"></a>Passo 5: Atribuir função

1. Para atribuir o papel, clique em **Guardar**.

   Após alguns momentos, o diretor de segurança é atribuído o papel no âmbito selecionado.

1. No **separador Fun assignments,** verifique se vê a atribuição de funções na lista.

    ![Adicionar atribuição de função guardada](./media/role-assignments-portal/rg-role-assignments.png)

## <a name="remove-a-role-assignment"></a>Remover uma atribuição de função

No Azure RBAC, para remover o acesso de um recurso Azure, remove-se uma atribuição de funções. Siga estes passos para remover uma tarefa de função.

1. Controlo **de acesso aberto (IAM)** num âmbito, como grupo de gestão, subscrição, grupo de recursos ou recursos, onde pretende remover o acesso.

1. Clique no **separador atribuições de funções** para ver todas as atribuições de funções neste âmbito.

1. Na lista de atribuições de funções, adicione uma marca de verificação junto ao principal de segurança com a atribuição de função que pretende remover.

   ![Atribuição de funções selecionada para ser removida](./media/role-assignments-portal/rg-role-assignments-select.png)

1. Clique em **Remover**.

   ![Mensagem Remover atribuição de função](./media/role-assignments-portal/remove-role-assignment.png)

1. Na mensagem de atribuição de funções removida que aparece, clique em **Sim**.

    Se vir uma mensagem de que as atribuições de funções herdadas não podem ser removidas, está a tentar remover uma tarefa de papel no âmbito de uma criança. Deve abrir o controlo de acesso (IAM) no âmbito onde a função foi atribuída e tentar novamente. Uma forma rápida de abrir o controlo de acesso (IAM) no âmbito correto é olhar para a coluna **Scope** e clicar no link ao lado **de (Herdado)**.

   ![Remover mensagem de atribuição de funções para atribuições de funções herdadas](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>Passos seguintes

- [Atribuir um utilizador como administrador de uma subscrição do Azure](role-assignments-portal-subscription-admin.md)
- [Adicione uma atribuição de papel para uma identidade gerida](role-assignments-portal-managed-identity.md)
- [Resolução de problemas Azure RBAC](troubleshooting.md)
