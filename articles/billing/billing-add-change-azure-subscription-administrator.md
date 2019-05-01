---
title: Adicionar ou alterar os administradores de subscrição do Azure | Documentos da Microsoft
description: Descreve como adicionar ou alterar um administrador de subscrição do Azure através de controlo de acesso baseado em funções (RBAC).
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
ms.openlocfilehash: 6cc965f8e775e02e9dec9f610516739a9a2c1936
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127722"
---
# <a name="add-or-change-azure-subscription-administrators"></a>Adicionar ou alterar os administradores de subscrição do Azure

Para gerir o acesso aos recursos do Azure, tem de ter a função de administrador adequada. O Azure tem um sistema de autorização chamado controlo de acesso baseado em funções (RBAC) com várias funções incorporadas que pode escolher. Pode atribuir essas funções em escopos diferentes, como o grupo de gestão, subscrição ou grupo de recursos.

A Microsoft recomenda que gerencie o acesso aos recursos através do RBAC. No entanto, se ainda estiver usando o modelo de implementação clássica e gerir os recursos clássicos com [módulo do PowerShell de gestão de serviço de Azure](https://docs.microsoft.com/en-us/powershell/module/servicemanagement/azure), precisará usar um administrador clássico. 

> [!TIP]
> Se usar apenas o portal do Azure para gerir os recursos clássicos, não precisará usar o administrador clássico.

Para obter mais informações, consulte [vs. de implementação clássica do Azure Resource Manager](../azure-resource-manager/resource-manager-deployment-model.md) e [administradores de subscrição clássica do Azure](../role-based-access-control/classic-administrators.md).

Este artigo descreve como adicionar ou alterar a função de administrador para um utilizador com o RBAC no âmbito da subscrição.

<a name="add-an-admin-for-a-subscription"></a>

## <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Atribuir um utilizador como administrador de uma subscrição

Para tornar um utilizador administrador de uma subscrição do Azure, atribuí-los a [proprietário](../role-based-access-control/built-in-roles.md#owner) função (uma função RBAC) no âmbito da subscrição. A função de proprietário dá ao usuário acesso total a todos os recursos na subscrição, incluindo o direito de delegar o acesso a outras pessoas. Estes passos são os mesmos como qualquer outra atribuição de função.

1. No portal do Azure, abra [subscrições](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

1. Clique a subscrição em que pretende conceder acesso.

1. Clique em **controlo de acesso (IAM)**.

1. Clique nas **atribuições de funções** separador para ver todas as atribuições de função para esta subscrição.

    ![Captura de ecrã que mostra as atribuições de funções](./media/billing-add-change-azure-subscription-administrator/role-assignments.png)

1. Clique em **Add** > **adicionar atribuição de função** para abrir o **adicionar atribuição de função** painel.

    Se não tiver permissões para atribuir funções, a opção estará desativada.

1. Na **função** na lista pendente, selecione a **proprietário** função.

1. Na **selecione** , selecione um utilizador. Se não vir o utilizador na lista, pode digitar o **selecione** caixa para procurar o diretório para os nomes a apresentar e endereços de e-mail.

    ![Captura de ecrã que mostra a função de proprietário selecionada](./media/billing-add-change-azure-subscription-administrator/add-role.png)

1. Clique em **guardar** para atribuir a função.

    Após alguns instantes, o utilizador tem atribuída a função de proprietário no âmbito da subscrição.

## <a name="next-steps"></a>Passos Seguintes

* [O que é o controlo de acesso baseado em funções (RBAC)?](../role-based-access-control/overview.md)
* [Compreender as diferentes funções no Azure](../role-based-access-control/rbac-and-directory-admin-roles.md)
* [How to: Associar ou adicionar uma subscrição do Azure ao Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
* [Administrator role permissions in Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md) (Permissões de cargos de administrador no Azure Active Directory)

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contactar o suporte

Se precisar de ajuda, ainda [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para a sua questão resolvidos rapidamente.
