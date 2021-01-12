---
title: Adicionar uma atribuição de função para uma identidade gerida (Preview) - Azure RBAC
description: Aprenda a adicionar uma atribuição de função começando com a identidade gerida e, em seguida, selecione o âmbito e o papel usando o portal Azure e o controlo de acesso baseado em funções Azure (Azure RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 01/11/2021
ms.author: rolyon
ms.openlocfilehash: a01246c0cf35653f4d13262183cf9df28b056c69
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98122497"
---
# <a name="add-a-role-assignment-for-a-managed-identity-preview"></a>Adicionar uma atribuição de função para uma identidade gerida (Pré-visualização)

Pode adicionar atribuições de funções para uma identidade gerida utilizando a página **access control (IAM)** como descrito no [Add ou remover atribuições de funções Azure utilizando o portal Azure](role-assignments-portal.md). Quando utilizar a página 'Controlo de Acesso' (IAM), começa pelo âmbito e, em seguida, seleciona a identidade e função geridas. Este artigo descreve uma forma alternativa de adicionar atribuições de funções para uma identidade gerida. Utilizando estes passos, começa-se com a identidade gerida e, em seguida, seleciona o âmbito e o papel.

> [!IMPORTANT]
> A adição de uma atribuição de funções para uma identidade gerida utilizando estes passos alternativos está atualmente em pré-visualização.
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
- [Adicionar ou remover atribuições de funções do Azure com o portal do Azure](role-assignments-portal.md)
- [Liste atribuições de funções Azure usando o portal Azure](role-assignments-list-portal.md)
