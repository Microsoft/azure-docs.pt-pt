---
title: Adicione ou remova atribuições de funções Azure usando o portal Azure - Azure RBAC
description: Saiba como conceder acesso aos recursos Azure para utilizadores, grupos, diretores de serviços ou identidades geridas utilizando o portal Azure e o controlo de acesso baseado em funções Azure (Azure RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 09/30/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 7c58641f0039982f05be14d0f24ba89c62273d4b
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/07/2021
ms.locfileid: "97964308"
---
# <a name="add-or-remove-azure-role-assignments-using-the-azure-portal"></a>Adicionar ou remover atribuições de funções do Azure com o portal do Azure

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] Este artigo descreve como atribuir funções usando o portal Azure.

Se precisar de atribuir funções de administrador no Azure Ative Directory, consulte [Ver e atribuir funções de administrador no Azure Ative Directory](../active-directory/roles/manage-roles-portal.md).

## <a name="prerequisites"></a>Pré-requisitos

Para adicionar ou remover atribuições de funções, você deve ter:

- `Microsoft.Authorization/roleAssignments/write` e `Microsoft.Authorization/roleAssignments/delete` permissões, tais como [Administrador de Acesso ao Utilizador](built-in-roles.md#user-access-administrator) ou [Proprietário](built-in-roles.md#owner)

## <a name="access-control-iam"></a>Controlo de acesso (IAM)

**O controlo de acesso (IAM)** é a página que normalmente utiliza para atribuir funções para conceder acesso aos recursos do Azure. É também conhecido como gestão de identidade e acesso e aparece em vários locais do portal Azure. O seguinte mostra um exemplo da página access control (IAM) para uma subscrição.

![Página de controlo de acesso (IAM) para uma subscrição](./media/role-assignments-portal/access-control-subscription.png)

Para ser o mais eficaz com a página access control (IAM), ajuda a seguir estes passos para atribuir um papel.

1. Determinar quem precisa de acesso. Pode atribuir uma função a um utilizador, grupo, principal de serviço ou identidade gerida.

1. Encontre o papel apropriado. As permissões são agrupadas em papéis. Pode selecionar a partir de uma lista de várias [funções incorporadas do Azure](built-in-roles.md) ou pode usar as suas próprias funções personalizadas.

1. Identifique o âmbito necessário. O Azure fornece quatro níveis de âmbito: grupo de [gestão,](../governance/management-groups/overview.md)subscrição, [grupo de recursos](../azure-resource-manager/management/overview.md#resource-groups)e recursos. Para obter mais informações sobre o âmbito, consulte [o âmbito de aplicação.](scope-overview.md)

1. Execute os passos numa das seguintes secções para atribuir uma função.

## <a name="add-a-role-assignment"></a>Adicionar uma atribuição de função

No Azure RBAC, para conceder acesso a um recurso Azure, adiciona-se uma atribuição de funções. Siga estes passos para atribuir um papel.

1. No portal Azure, clique em **Todos os serviços** e, em seguida, selecione o âmbito a que pretende conceder acesso. Por exemplo, pode selecionar **grupos de Gestão,** **Assinaturas, Grupos** **de Recursos** ou um recurso.

1. Clique no recurso específico para esse âmbito.

1. Clique em **Controlo de acesso (IAM)**.

1. Clique no separador **atribuições de funções** para ver as atribuições de funções neste âmbito.

    ![Separador de atribuições de controlo de acesso (IAM) e atribuições de funções](./media/role-assignments-portal/role-assignments.png)

1. Clique **em Adicionar** Adicionar a atribuição de  >  **função**.

   Se não tiver permissões para atribuir funções, a opção de atribuição de funções Add será desativada.

   ![Adicionar menu de atribuição de funções](./media/shared/add-role-assignment-menu.png)

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

1. Clique **em Adicionar** Adicionar a atribuição de  >  **função**.

   Se não tiver permissões para atribuir funções, a opção de atribuição de funções Add será desativada.

   ![Adicione menu de atribuição de função para uma subscrição](./media/shared/add-role-assignment-menu.png)

    O painel Adicionar atribuição de função é aberto.

   ![Adicione painel de atribuição de funções para uma subscrição](./media/role-assignments-portal/add-role-assignment.png)

1. Na lista pendente **Função**, selecione a função **Proprietário**.

1. Na lista **Selecionar**, selecione um utilizador. Se não vir o utilizador na lista, pode escrever na caixa **Selecionar** para procurar no diretório os nomes a apresentar e endereços de e-mail.

1. Clique em **Guardar** para atribuir a função.

   Após alguns instantes, é atribuída ao utilizador a função Proprietário no âmbito da subscrição.

## <a name="add-a-role-assignment-for-a-managed-identity-preview"></a>Adicionar uma atribuição de função para uma identidade gerida (Pré-visualização)

Pode adicionar atribuições de funções para uma identidade gerida utilizando a página **de Controlo de Acesso (IAM),** conforme descrito anteriormente neste artigo. Quando utilizar a página 'Controlo de Acesso' (IAM), começa pelo âmbito e, em seguida, seleciona a identidade e função geridas. Esta secção descreve uma forma alternativa de adicionar atribuições de funções para uma identidade gerida. Utilizando estes passos, começa-se com a identidade gerida e, em seguida, seleciona o âmbito e o papel.

> [!IMPORTANT]
> A adição de uma atribuição de funções para uma identidade gerida utilizando estes passos alternativos está atualmente em pré-visualização.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="system-assigned-managed-identity"></a>Identidade gerida atribuída pelo sistema

Siga estes passos para atribuir um papel a uma identidade gerida atribuída pelo sistema, começando pela identidade gerida.

1. No portal Azure, abra uma identidade gerida atribuída pelo sistema.

1. No menu esquerdo, clique em **Identidade.**

    ![Identidade gerida atribuída pelo sistema](./media/shared/identity-system-assigned.png)

1. Em **Permissões,** clique nas **atribuições de funções Azure**.

    Se as funções já estiverem atribuídas à identidade gerida atribuída pelo sistema, consulte a lista de atribuições de funções. Esta lista inclui todas as atribuições de funções que tem permissão para ler.

    ![Atribuições de funções para uma identidade gerida atribuída pelo sistema](./media/shared/role-assignments-system-assigned.png)

1. Para alterar a subscrição, clique na lista **de Assinaturas.**

1. Clique **em Adicionar atribuição de função (Pré-visualização)**.

1. Utilize as listas de drop-down para selecionar o conjunto de recursos a que a atribuição de funções se aplica, tais como **Subscrição,** **Grupo de Recursos** ou recurso.

    Se não tiver a atribuição de funções, escreva permissões para o âmbito selecionado, será exibida uma mensagem inline. 

1. Na lista pendente **Função**, selecione uma função, como **Contribuidor de Máquina Virtual**.

   ![Adicionar painel de atribuição de funções para identidade gerida atribuída pelo sistema](./media/role-assignments-portal/add-role-assignment-with-scope.png)

1. Clique em **Guardar** para atribuir a função.

   Após alguns momentos, a identidade gerida é atribuída ao papel no âmbito selecionado.

### <a name="user-assigned-managed-identity"></a>Identidade gerida atribuída pelo utilizador

Siga estes passos para atribuir uma função a uma identidade gerida atribuída pelo utilizador, começando pela identidade gerida.

1. No portal Azure, abra uma identidade gerida atribuída ao utilizador.

1. No menu esquerdo, clique nas **atribuições de funções Azure**.

    Se as funções já estiverem atribuídas à identidade gerida atribuída pelo utilizador selecionado, consulte a lista de atribuições de funções. Esta lista inclui todas as atribuições de funções que tem permissão para ler.

    ![Atribuições de funções para uma identidade gerida atribuída pelo utilizador](./media/shared/role-assignments-user-assigned.png)

1. Para alterar a subscrição, clique na lista **de Assinaturas.**

1. Clique **em Adicionar atribuição de função (Pré-visualização)**.

1. Utilize as listas de drop-down para selecionar o conjunto de recursos a que a atribuição de funções se aplica, tais como **Subscrição,** **Grupo de Recursos** ou recurso.

    Se não tiver a atribuição de funções, escreva permissões para o âmbito selecionado, será exibida uma mensagem inline. 

1. Na lista pendente **Função**, selecione uma função, como **Contribuidor de Máquina Virtual**.

   ![Adicione painel de atribuição de funções para uma identidade gerida atribuída pelo utilizador](./media/role-assignments-portal/add-role-assignment-with-scope.png)

1. Clique em **Guardar** para atribuir a função.

   Após alguns momentos, a identidade gerida é atribuída ao papel no âmbito selecionado.

## <a name="remove-a-role-assignment"></a>Remover uma atribuição de função

No Azure RBAC, para remover o acesso de um recurso Azure, remove-se uma atribuição de funções. Siga estes passos para remover uma tarefa de função.

1. Controlo **de acesso aberto (IAM)** num âmbito, como grupo de gestão, subscrição, grupo de recursos ou recursos, onde pretende remover o acesso.

1. Clique no separador **Atribuições de funções** para ver todas as atribuições de funções para esta subscrição.

1. Na lista de atribuições de funções, adicione uma marca de verificação junto ao principal de segurança com a atribuição de função que pretende remover.

   ![Atribuição de funções selecionada para ser removida](./media/role-assignments-portal/remove-role-assignment-select.png)

1. Clique em **Remover**.

   ![Mensagem Remover atribuição de função](./media/role-assignments-portal/remove-role-assignment.png)

1. Na mensagem de atribuição de funções removida que aparece, clique em **Sim**.

    Se vir uma mensagem de que as atribuições de funções herdadas não podem ser removidas, está a tentar remover uma tarefa de papel no âmbito de uma criança. Deve abrir o controlo de acesso (IAM) no âmbito onde a função foi atribuída e tentar novamente. Uma forma rápida de abrir o controlo de acesso (IAM) no âmbito correto é olhar para a coluna **Scope** e clicar no link ao lado **de (Herdado)**.

   ![Remover mensagem de atribuição de funções para atribuições de funções herdadas](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>Passos seguintes

- [Liste atribuições de funções Azure usando o portal Azure](role-assignments-list-portal.md)
- [Tutorial: Conceder acesso a um utilizador aos recursos do Azure através do portal Azure](quickstart-assign-role-user-portal.md)
- [Resolução de problemas Azure RBAC](troubleshooting.md)
- [Organize your resources with Azure management groups](../governance/management-groups/overview.md) (Organizar os recursos com os grupos de gestão do Azure)
