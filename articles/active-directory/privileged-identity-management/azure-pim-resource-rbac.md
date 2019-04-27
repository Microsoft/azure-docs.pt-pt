---
title: Ver histórico de atividade e auditoria para funções de recursos do Azure no PIM - Azure Active Directory | Documentos da Microsoft
description: Ver a atividade e auditar o histórico para funções de recursos do Azure no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74607f6a746558238ead65036d708b515d370035
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60441449"
---
# <a name="view-activity-and-audit-history-for-azure-resource-roles-in-pim"></a>Ver histórico de atividade e auditoria para funções de recursos do Azure no PIM

Com o Azure Active Directory (Azure AD) Privileged Identity Management (PIM), pode ver atividade, ativações e histórico de auditoria para funções de recursos do Azure na sua organização. Isto inclui as subscrições, grupos de recursos e até mesmo máquinas de virtuais. Qualquer recurso no portal do Azure, que aproveita a funcionalidade de controlo (RBAC) de acesso baseado em função do Azure pode aproveitar a segurança e o ciclo de vida das capacidades de gestão no PIM.

## <a name="view-activity-and-activations"></a>Ver a atividade e ativações

Para ver as ações realizou de um utilizador específico em vários recursos, pode ver a atividade de recursos do Azure que está associada um período de ativação fornecido.

1. Open **do Azure AD Privileged Identity Management**.

1. Clique em **recursos do Azure**.

1. Clique em recursos que pretende ver a atividade e ativações para.

1. Clique em **funções** ou **membros**.

1. Clique num utilizador.

    Consulte uma visão gráfica das ações do usuário em recursos do Azure por data. Ela também mostra as ativações de função recente no mesmo período de tempo.

    ![Detalhes do utilizador](media/azure-pim-resource-rbac/rbac-user-details.png)

1. Clique numa ativação de função específica para ver detalhes e a atividade de recursos do Azure correspondente que ocorreu enquanto que o utilizador esteve ativo.

    ![Selecione a ativação de função](media/azure-pim-resource-rbac/rbac-user-resource-activity.png)

## <a name="export-role-assignments-with-children"></a>Exportar as atribuições de função com subordinados

Talvez tenha um requisito de conformidade em que tem de fornecer uma lista completa das atribuições de funções para auditores. PIM permite-lhe as atribuições de funções de consulta num recurso específico, que inclui atribuições de funções para todos os recursos subordinados. Anteriormente, era difícil para os administradores obter uma lista completa das atribuições de funções para uma subscrição e teve de exportar as atribuições de função para cada recurso específico. Utilizar o PIM, pode consultar para todas as atribuições de função elegível e Active Directory numa subscrição incluindo atribuições de funções para todos os grupos de recursos e recursos.

1. Open **do Azure AD Privileged Identity Management**.

1. Clique em **recursos do Azure**.

1. Clique em recursos que pretende exportar as atribuições de funções para, por exemplo, uma subscrição.

1. Clique em **membros**.

1. Clique em **exportar** para abrir o painel de associação de exportação.

    ![Painel de associação de exportação](media/azure-pim-resource-rbac/export-membership.png)

1. Clique em **exportar todos os membros** para exportar todas as atribuições de função num arquivo CSV.

    ![Exportar o ficheiro CSV](media/azure-pim-resource-rbac/export-csv.png)

## <a name="view-resource-audit-history"></a>Ver histórico de auditoria do recurso

Auditoria de recurso dá-lhe uma vista de todas as atividades de função para um recurso.

1. Open **do Azure AD Privileged Identity Management**.

1. Clique em **recursos do Azure**.

1. Clique em recursos que pretende ver o histórico de auditoria para.

1. Clique em **auditoria de recurso**.

1. Filtre o histórico com uma data predefinida ou um intervalo personalizado.

    ![Auditoria de recurso de filtro](media/azure-pim-resource-rbac/rbac-resource-audit.png)

1. Para **tipo de auditoria**, selecione **Activate (atribuído + Activated)**.

    ![Detalhe da atividade](media/azure-pim-resource-rbac/rbac-audit-activity.png)

1. Sob **ação**, clique em **(atividade)** para um utilizador ver detalhes de atividade do usuário em recursos do Azure.

    ![Detalhe de atividade do utilizador](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

## <a name="view-my-audit"></a>Ver a minha auditoria

A minha auditoria permite-lhe ver a sua atividade de funções pessoais.

1. Open **do Azure AD Privileged Identity Management**.

1. Clique em **recursos do Azure**.

1. Clique em recursos que pretende ver o histórico de auditoria para.

1. Clique em **minha auditoria**.

1. Filtre o histórico com uma data predefinida ou um intervalo personalizado.

    ![Atividade de funções pessoais](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="next-steps"></a>Passos Seguintes

- [Atribuir funções de recursos do Azure no PIM](pim-resource-roles-assign-roles.md)
- [Aprovar ou recusar pedidos para funções de recursos do Azure no PIM](pim-resource-roles-approval-workflow.md)
- [Ver o histórico de auditoria para funções do Azure AD no PIM](pim-how-to-use-audit-log.md)
