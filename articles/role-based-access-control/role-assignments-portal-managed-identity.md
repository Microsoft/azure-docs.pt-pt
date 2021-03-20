---
title: Atribuir funções de Azure a uma identidade gerida (Preview) - Azure RBAC
description: Aprenda a atribuir funções Azure começando com a identidade gerida e, em seguida, selecione o âmbito e o papel usando o portal Azure e o controlo de acesso baseado em funções Azure (Azure RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 02/15/2021
ms.author: rolyon
ms.openlocfilehash: 57c8c00a64996bc6223fbe7e514db9db38ccdcc2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100556848"
---
# <a name="assign-azure-roles-to-a-managed-identity-preview"></a>Atribuir funções de Azure a uma identidade gerida (Pré-visualização)

Pode atribuir uma função a uma identidade gerida utilizando a página **access control (IAM),** conforme descrito nas [funções De Atribuição Azure utilizando o portal Azure](role-assignments-portal.md). Quando utilizar a página 'Controlo de Acesso' (IAM), começa pelo âmbito e, em seguida, seleciona a identidade e função geridas. Este artigo descreve uma forma alternativa de atribuir funções para uma identidade gerida. Utilizando estes passos, começa-se com a identidade gerida e, em seguida, seleciona o âmbito e o papel.

> [!IMPORTANT]
> Atribuir uma função a uma identidade gerida usando estes passos alternativos está atualmente em pré-visualização.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="system-assigned-managed-identity"></a>Identidade gerida atribuída pelo sistema

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

   ![Adicionar painel de atribuição de funções para identidade gerida atribuída pelo sistema](./media/role-assignments-portal-managed-identity/add-role-assignment-with-scope.png)

1. Clique em **Guardar** para atribuir a função.

   Após alguns momentos, a identidade gerida é atribuída ao papel no âmbito selecionado.

## <a name="user-assigned-managed-identity"></a>Identidade gerida atribuída pelo utilizador

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

   ![Adicione painel de atribuição de funções para uma identidade gerida atribuída pelo utilizador](./media/role-assignments-portal-managed-identity/add-role-assignment-with-scope.png)

1. Clique em **Guardar** para atribuir a função.

   Após alguns momentos, a identidade gerida é atribuída ao papel no âmbito selecionado.

## <a name="next-steps"></a>Passos seguintes

- [Quais são as identidades geridas para os recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md)
- [Atribuir funções Azure usando o portal Azure](role-assignments-portal.md)
- [Liste atribuições de funções Azure usando o portal Azure](role-assignments-list-portal.md)
