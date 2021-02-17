---
title: Atribuir um utilizador como administrador de uma subscrição do Azure - Azure RBAC
description: Saiba como tornar um utilizador um administrador de uma subscrição Azure utilizando o portal Azure e o controlo de acesso baseado em funções Azure (Azure RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 01/11/2021
ms.author: rolyon
ms.openlocfilehash: dec5888127ed1fc291bec244a44cfb71e343e3bb
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100556834"
---
# <a name="assign-a-user-as-an-administrator-of-an-azure-subscription"></a>Atribuir um utilizador como administrador de uma subscrição do Azure

Para tornar um utilizador um administrador de uma subscrição Azure, atribua-lhes a função [De Proprietário](built-in-roles.md#owner) no âmbito de subscrição. A função Proprietário dá ao utilizador acesso total a todos os recursos da subscrição, incluindo a permissão para conceder acesso a outros. Estes passos são iguais a qualquer outra atribuição de função.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="step-1-open-the-subscription"></a>Passo 1: Abra a subscrição

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Na caixa 'Procurar' no topo, procure por subscrições.

    ![Pesquisa do portal Azure para grupo de recursos](./media/shared/sub-portal-search.png)

1. Clique na subscrição que pretende utilizar.

    O seguinte mostra uma assinatura de exemplo.

    ![Visão geral do grupo de recursos](./media/shared/sub-overview.png)

## <a name="step-2-open-the-add-role-assignment-pane"></a>Passo 2: Abra o painel de atribuição de funções adicionar

**O controlo de acesso (IAM)** é a página que normalmente utiliza para atribuir funções para conceder acesso aos recursos do Azure. É também conhecido como gestão de identidade e acesso (IAM) e aparece em vários locais do portal Azure.

1. Clique em **Controlo de acesso (IAM)**.

    O seguinte mostra um exemplo da página access control (IAM) para uma subscrição.

    ![Página de controlo de acesso (IAM) para um grupo de recursos](./media/shared/sub-access-control.png)

1. Clique no separador **atribuições de funções** para ver as atribuições de funções neste âmbito.

1. Clique **em Adicionar** Adicionar a atribuição de  >  **função**.
   Se não tiver permissões para atribuir funções, a opção de atribuição de funções Add será desativada.

   ![Adicionar menu de atribuição de funções](./media/shared/add-role-assignment-menu.png)

    O painel Adicionar atribuição de função é aberto.

   ![Adicione painel de atribuição de funções](./media/shared/add-role-assignment.png)

## <a name="step-3-select-the-owner-role"></a>Passo 3: Selecione a função proprietário

A função [do Proprietário](built-in-roles.md#owner) concede acesso total à gestão de todos os recursos, incluindo a capacidade de atribuir funções no Azure RBAC. Deverá ter um máximo de 3 proprietários de subscrições para reduzir o potencial de violação por parte de um proprietário comprometido.

- Na lista **role,** selecione a função **Proprietário.**

   ![Selecione a função do Proprietário no painel de atribuição de funções adicionar](./media/role-assignments-portal-subscription-admin/add-role-assignment-role-owner.png)

## <a name="step-4-select-who-needs-access"></a>Passo 4: Selecione quem precisa de acesso

1. No acesso à **lista,** selecione **Utilizador, grupo ou principal de serviço.**

1. Na secção **Selecione,** procure o utilizador introduzindo uma cadeia ou percorrendo a lista.

   ![Selecione o utilizador na atribuição de função Adicionar](./media/role-assignments-portal-subscription-admin/add-role-assignment-user-admin.png)

1. Uma vez encontrado o utilizador, clique para selecioná-lo.

## <a name="step-5-assign-role"></a>Passo 5: Atribuir função

1. Para atribuir o papel, clique em **Guardar**.

   Após alguns momentos, o utilizador é atribuído ao papel no âmbito selecionado.

1. No **separador Fun assignments,** verifique se vê a atribuição de funções na lista.

    ![Adicionar atribuição de função guardada](./media/role-assignments-portal-subscription-admin/sub-role-assignments-owner.png)

## <a name="next-steps"></a>Passos seguintes

- [Atribuir funções Azure usando o portal Azure](role-assignments-portal.md)
- [Liste atribuições de funções Azure usando o portal Azure](role-assignments-list-portal.md)
- [Organize your resources with Azure management groups](../governance/management-groups/overview.md) (Organizar os recursos com os grupos de gestão do Azure)
