---
title: Adicionar ou alterar os administradores de subscrições do Azure
description: Descreve como adicionar ou alterar um administrador de subscrições do Azure através do controlo de acesso baseado em funções do Azure (RBAC do Azure).
author: genlin
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 10956953f9ab3a9e32b9da4ab8a3501d38b0e2c3
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92369663"
---
# <a name="add-or-change-azure-subscription-administrators"></a>Adicionar ou alterar os administradores de subscrições do Azure


Para gerir o acesso aos recursos do Azure, tem de ter a função de administrador adequada. O Azure tem um sistema de autorização denominado [controlo de acesso baseado em funções do Azure (RBAC do Azure)](../../role-based-access-control/overview.md) que tem à sua disposição várias funções incorporadas. Pode atribuir estas funções em âmbitos diferentes, tais como grupo de gestão, subscrição ou grupo de recursos. Por predefinição, a pessoa que cria uma nova subscrição do Azure pode atribuir a outros utilizadores acesso administrativo a uma subscrição.

Este artigo descreve como adicionar ou alterar a função de administrador para um utilizador que utiliza o RBAC do Azure no âmbito da subscrição.

A Microsoft recomenda que faça a gestão do acesso aos recursos através do RBAC do Azure. No entanto, se ainda utiliza o modelo de implementação clássica e gere os recursos clássicos através do [Módulo do PowerShell da Gestão de Serviços do Azure](/powershell/module/servicemanagement/azure.service), terá de utilizar um administrador clássico.

> [!TIP]
> Se apenas utiliza o portal do Azure para gerir os recursos clássicos, não precisa de utilizar o administrador clássico.

Para obter mais informações, veja [Azure Resource Manager vs. implementação clássica](../../azure-resource-manager/management/deployment-models.md) e [Administradores de subscrições clássicos do Azure](../../role-based-access-control/classic-administrators.md).

<a name="add-an-admin-for-a-subscription"></a>

## <a name="assign-a-subscription-administrator"></a>Atribuir um administrador de subscrição

Para que um utilizador seja administrador de uma subscrição do Azure, um administrador existente tem de lhe atribuir a função [Proprietário](../../role-based-access-control/built-in-roles.md#owner) (uma função do Azure) no âmbito da subscrição. A função Proprietário dá ao utilizador acesso total a todos os recursos na subscrição, incluindo o direito de delegar o acesso a outras pessoas. Estes passos são iguais a qualquer outra atribuição de função.

Se não tiver a certeza de quem é o administrador de conta de uma subscrição, utilize os seguintes passos para descobrir.

1. Abra a [página Subscrições no portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Selecione a subscrição que pretende verificar e, em seguida, procure em **Definições** .
1. Selecione **Propriedades** . O administrador de conta da subscrição é apresentado na caixa **Administrador de Conta** .

### <a name="to-assign-a-user-as-an-administrator"></a>Atribuir a um utilizador a função de administrador

1. Inicie sessão no portal do Azure como proprietário da subscrição e abra [Subscrições](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

1. Clique na subscrição na qual pretende conceder acesso.

1. Clique em **Controlo de acesso (IAM)** .

1. Clique no separador **Atribuições de funções** para ver todas as atribuições de funções para esta subscrição.

    ![Captura de ecrã a mostrar as atribuições de funções](./media/add-change-subscription-administrator/role-assignments.png)

1. Clique em **Adicionar** > **Adicionar atribuição de funções** para abrir o painel **Adicionar atribuição de funções** .

    Se não tiver permissões para atribuir funções, a opção será desativada.

1. Na lista pendente **Função** , selecione a função **Proprietário** .

1. Na lista **Selecionar** , selecione um utilizador. Se não vir o utilizador na lista, pode escrever na caixa **Selecionar** para procurar no diretório os nomes a apresentar e endereços de e-mail.

    ![Captura de ecrã a mostrar a função de Proprietário selecionada](./media/add-change-subscription-administrator/add-role.png)

1. Clique em **Guardar** para atribuir a função.

    Após alguns instantes, é atribuída ao utilizador a função Proprietário no âmbito da subscrição.

## <a name="next-steps"></a>Passos seguintes

* [O que é o controlo de acesso baseado em funções do Azure (Azure RBAC)?](../../role-based-access-control/overview.md)
* [Compreender as diferentes funções no Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* [Associar ou adicionar uma subscrição do Azure ao inquilino do Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
* [Administrator role permissions in Azure Active Directory](../../active-directory/roles/permissions-reference.md) (Permissões de cargos de administrador no Azure Active Directory)

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contactar o suporte

Se ainda precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema rapidamente.
