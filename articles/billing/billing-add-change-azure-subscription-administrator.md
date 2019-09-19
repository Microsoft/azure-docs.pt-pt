---
title: Adicionar ou alterar os administradores de subscrições do Azure | Microsoft Docs
description: Descreve como adicionar ou alterar um administrador de subscrições do Azure através do controlo de acesso baseado em funções (RBAC).
services: ''
documentationcenter: ''
author: genlin
manager: adpick
editor: ''
tags: billing
ms.assetid: 13a72d76-e043-4212-bcac-a35f4a27ee26
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: 000315a2d751a05d3e401ee1bb9f593c6e321194
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "64922904"
---
# <a name="add-or-change-azure-subscription-administrators"></a>Adicionar ou alterar os administradores de subscrições do Azure

Para gerir o acesso aos recursos do Azure, tem de ter a função de administrador adequada. O Azure tem um sistema de autorização denominado controlo de acesso baseado em funções (RBAC) que tem à sua disposição várias funções incorporadas. Pode atribuir estas funções em âmbitos diferentes, tais como grupo de gestão, subscrição ou grupo de recursos.

A Microsoft recomenda que faça a gestão do acesso aos recursos através do RBAC. No entanto, se ainda utiliza o modelo de implementação clássica e gere os recursos clássicos através do [Módulo do PowerShell da Gestão de Serviços do Azure](https://docs.microsoft.com/powershell/module/servicemanagement/azure), terá de utilizar um administrador clássico. 

> [!TIP]
> Se utiliza apenas o portal do Azure para gerir os recursos clássicos, não precisará de utilizar um administrador clássico.

Para obter mais informações, veja [Azure Resource Manager vs. implementação clássica](../azure-resource-manager/resource-manager-deployment-model.md) e [Administradores de subscrições clássicos do Azure](../role-based-access-control/classic-administrators.md).

Este artigo descreve como adicionar ou alterar a função de administrador para um utilizador que utiliza o RBAC no âmbito da subscrição.

<a name="add-an-admin-for-a-subscription"></a>

## <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Atribuir um utilizador como administrador de uma subscrição

Para que um utilizador seja administrador de uma subscrição do Azure, atribua-lhe a função [Proprietário](../role-based-access-control/built-in-roles.md#owner) (uma função RBAC) no âmbito da subscrição. A função Proprietário dá ao utilizador acesso total a todos os recursos na subscrição, incluindo o direito de delegar o acesso a outras pessoas. Estes passos são iguais a qualquer outra atribuição de função.

1. No portal do Azure, abra as [Subscrições](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

1. Clique na subscrição na qual pretende conceder acesso.

1. Clique em **Controlo de acesso (IAM)** .

1. Clique no separador **Atribuições de funções** para ver todas as atribuições de funções para esta subscrição.

    ![Captura de ecrã a mostrar as atribuições de funções](./media/billing-add-change-azure-subscription-administrator/role-assignments.png)

1. Clique em **Adicionar** > **Adicionar atribuição de funções** para abrir o painel **Adicionar atribuição de funções**.

    Se não tiver permissões para atribuir funções, a opção será desativada.

1. Na lista pendente **Função**, selecione a função **Proprietário**.

1. Na lista **Selecionar**, selecione um utilizador. Se não vir o utilizador na lista, pode escrever na caixa **Selecionar** para procurar no diretório os nomes a apresentar e endereços de e-mail.

    ![Captura de ecrã a mostrar a função de Proprietário selecionada](./media/billing-add-change-azure-subscription-administrator/add-role.png)

1. Clique em **Guardar** para atribuir a função.

    Após alguns instantes, é atribuída ao utilizador a função Proprietário no âmbito da subscrição.

## <a name="next-steps"></a>Passos seguintes

* [O que é o controlo de acesso baseado em funções (RBAC)?](../role-based-access-control/overview.md)
* [Compreender as diferentes funções no Azure](../role-based-access-control/rbac-and-directory-admin-roles.md)
* [How to: Associar ou adicionar uma subscrição do Azure ao Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
* [Administrator role permissions in Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md) (Permissões de cargos de administrador no Azure Active Directory)

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contactar o suporte

Se ainda precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema rapidamente.
