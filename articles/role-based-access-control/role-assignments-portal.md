---
title: Atribuir funções Azure usando o portal Azure - Azure RBAC
description: Saiba como conceder acesso aos recursos Azure para utilizadores, grupos, diretores de serviços ou identidades geridas utilizando o portal Azure e o controlo de acesso baseado em funções Azure (Azure RBAC).
services: active-directory
author: rolyon
manager: daveba
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 02/15/2021
ms.author: rolyon
ms.custom: contperf-fy21q3-portal
ms.openlocfilehash: e25bbe4e1a96e4efaaa13732aea571d26d4b006e
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100555270"
---
# <a name="assign-azure-roles-using-the-azure-portal"></a>Atribuir funções Azure usando o portal Azure

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] Este artigo descreve como atribuir funções usando o portal Azure.

Se precisar de atribuir funções de administrador no Azure Ative Directory, consulte [Ver e atribuir funções de administrador no Azure Ative Directory](../active-directory/roles/manage-roles-portal.md).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="step-1-identify-the-needed-scope"></a>Passo 1: Identificar o âmbito necessário

[!INCLUDE [Scope for Azure RBAC introduction](../../includes/role-based-access-control/scope-intro.md)]

[!INCLUDE [Scope for Azure RBAC least privilege](../../includes/role-based-access-control/scope-least.md)]Para obter mais informações sobre o âmbito, consulte [o âmbito de aplicação.](scope-overview.md)

![Níveis de âmbito para Azure RBAC](../../includes/role-based-access-control/media/scope-levels.png)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Na caixa 'Procurar' no topo, procure o âmbito a que pretende conceder acesso. Por exemplo, procure **grupos de Gestão,** **Assinaturas, Grupos** **de Recursos** ou um recurso específico.

    ![Pesquisa do portal Azure para grupo de recursos](./media/shared/rg-portal-search.png)

1. Clique no recurso específico para esse âmbito.

    O seguinte mostra um grupo de recursos exemplo.

    ![Visão geral do grupo de recursos](./media/shared/rg-overview.png)

## <a name="step-2-open-the-add-role-assignment-pane"></a>Passo 2: Abra o painel de atribuição de funções adicionar

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

## <a name="step-3-select-the-appropriate-role"></a>Passo 3: Selecione a função adequada

1. Na lista **De Função,** procure ou percorra para encontrar o papel que pretende atribuir.

    Para ajudá-lo a determinar o papel apropriado, pode pairar sobre o ícone da informação para mostrar uma descrição para o papel. Para mais informações, pode ver o artigo [de funções incorporadas do Azure.](built-in-roles.md)

   ![Selecione papel na atribuição de função Add](./media/role-assignments-portal/add-role-assignment-role.png)

1. Clique para selecionar o papel.

## <a name="step-4-select-who-needs-access"></a>Passo 4: Selecione quem precisa de acesso

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

## <a name="step-5-assign-role"></a>Passo 5: Atribuir função

1. Para atribuir o papel, clique em **Guardar**.

   Após alguns momentos, o diretor de segurança é atribuído o papel no âmbito selecionado.

1. No **separador Fun assignments,** verifique se vê a atribuição de funções na lista.

    ![Adicionar atribuição de função guardada](./media/role-assignments-portal/rg-role-assignments.png)

## <a name="next-steps"></a>Passos seguintes

- [Atribuir um utilizador como administrador de uma subscrição do Azure](role-assignments-portal-subscription-admin.md)
- [Remova atribuições de funções Azure](role-assignments-remove.md)
- [Resolução de problemas Azure RBAC](troubleshooting.md)
