---
title: Ver relatório de auditoria para funções de recursos da Azure em Gestão de Identidade Privilegiada (PIM) - Azure AD ! Microsoft Docs
description: Ver histórico de atividade e auditoria para funções de recursos Azure em Azure AD Gestão de Identidade Privilegiada (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 01/10/2020
ms.author: curtand
ms.reviewer: shaunliu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45144f64789a19390984c3f9f6a660e3c3300215
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002498"
---
# <a name="view-activity-and-audit-history-for-azure-resource-roles-in-privileged-identity-management"></a>Ver histórico de atividades e auditorias para funções de recursos da Azure em Gestão de Identidade Privilegiada

Com o Azure Ative Directory (Azure AD) Privileged Identity Management (PIM), pode visualizar atividade, ativações e histórico de auditoria para funções de recursos Azure dentro da sua organização. Isto inclui subscrições, grupos de recursos e até máquinas virtuais. Qualquer recurso dentro do portal Azure que aproveite a funcionalidade de controlo de acesso baseado em funções do Azure pode tirar partido das capacidades de gestão de segurança e ciclo de vida na Gestão de Identidade Privilegiada.

> [!NOTE]
> Se a sua organização tiver funções de gestão subcontratadas a um prestador de serviços que utilize [a Azure delegada em gestão de recursos,](../../lighthouse/concepts/azure-delegated-resource-management.md)as atribuições de funções autorizadas por esse prestador de serviços não serão mostradas aqui.

## <a name="view-activity-and-activations"></a>Ver atividade e ativações

Para ver que ações um utilizador específico tomou em vários recursos, você pode ver a atividade de recursos Azure que está associada a um determinado período de ativação.

1. Open **Azure AD Gestão de Identidade Privilegiada.**

1. Selecione **recursos Azure**.

1. Selecione o recurso para o quais pretende visualizar a atividade e as ativações.

1. Selecione **Funções** ou **Membros**.

1. Selecione um utilizador.

    Você vê um resumo das ações do utilizador em recursos Azure por data. Mostra também as recentes ativações de funções durante o mesmo período de tempo.

    ![Detalhes do utilizador com resumo da atividade de recursos e ativações de funções](media/azure-pim-resource-rbac/rbac-user-details.png)

1. Selecione uma ativação de função específica para ver detalhes e correspondente atividade de recursos Azure que ocorreram enquanto esse utilizador estava ativo.

    [![Ativação de funções selecionada e detalhes de atividade](media/azure-pim-resource-rbac/export-membership.png "Ativação de funções selecionada e detalhes de atividade")](media/azure-pim-resource-rbac/export-membership.png)

## <a name="export-role-assignments-with-children"></a>Atribuição de funções de exportação com crianças

Pode ter um requisito de conformidade onde deve fornecer uma lista completa de atribuições de funções aos auditores. A Gestão de Identidade Privilegiada permite-lhe consultar atribuições de funções num recurso específico, que inclui atribuições de funções para todos os recursos infantis. Anteriormente, era difícil para os administradores obter uma lista completa de atribuições de funções para uma subscrição e tinham de exportar atribuições de funções para cada recurso específico. Utilizando a Gestão de Identidade Privilegiada, pode consultar todas as atribuições de papéis ativos e elegíveis numa subscrição, incluindo atribuições de funções para todos os grupos de recursos e recursos.

1. Open **Azure AD Gestão de Identidade Privilegiada.**

1. Selecione **recursos Azure**.

1. Selecione o recurso para o quais pretende exportar atribuições de funções, como uma subscrição.

1. Selecione **Membros**.

1. Selecione **Export** para abrir o painel de adesão à Exportação.

    [![Painel de adesão à exportação para exportação de todos os membros](media/azure-pim-resource-rbac/export-membership.png "Página de adesão à exportação para exportação de todos os membros")](media/azure-pim-resource-rbac/export-membership.png)

1. Selecione **Exporte todos os membros** para exportar todas as atribuições de funções num ficheiro CSV.

    ![Atribuições de funções exportadas em ficheiro CSV como exibição no Excel](media/azure-pim-resource-rbac/export-csv.png)

## <a name="view-resource-audit-history"></a>Ver histórico de auditoria de recursos

A auditoria de recursos dá-lhe uma visão de toda a atividade de papel para um recurso.

1. Open **Azure AD Gestão de Identidade Privilegiada.**

1. Selecione **recursos Azure**.

1. Selecione o recurso para o quais pretende visualizar o histórico de auditoria.

1. Selecione **a auditoria de recursos.**

1. Filtrar o histórico utilizando uma data ou um intervalo personalizado predefinidos.

    [![Lista de auditoria de recursos com filtros](media/azure-pim-resource-rbac/rbac-resource-audit.png "Lista de auditoria de recursos com filtros")](media/azure-pim-resource-rbac/rbac-resource-audit.png)

1. Para **o tipo de auditoria,** selecione **Ativar (Atribuído + Ativado)**.

    [![Lista de auditoria de recursos filtrada pelo tipo](media/azure-pim-resource-rbac/rbac-audit-activity.png "Lista de auditoria de recursos filtrada por Activate")](media/azure-pim-resource-rbac/rbac-audit-activity.png) ![ de auditoria Activate Lista de auditoria de recursos que é filtrada pelo tipo de auditoria Ativa](media/azure-pim-resource-rbac/rbac-audit-activity.png)

1. Em **Ação**, clique **(atividade)** para um utilizador ver o detalhe de atividade desse utilizador nos recursos Azure.

    ![Detalhes da atividade do utilizador para uma determinada ação](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

## <a name="view-my-audit"></a>Ver a minha auditoria

A minha auditoria permite-lhe ver a sua atividade de papel pessoal.

1. Open **Azure AD Gestão de Identidade Privilegiada.**

1. Selecione **recursos Azure**.

1. Selecione o recurso para o quais pretende visualizar o histórico de auditoria.

1. Selecione **A minha auditoria.**

1. Filtrar o histórico utilizando uma data ou um intervalo personalizado predefinidos.

    [![Lista de auditoria para o utilizador atual](media/azure-pim-resource-rbac/my-audit-time.png "Lista de auditoria para o utilizador atual")](media/azure-pim-resource-rbac/my-audit-time.png)

> [!NOTE]
> O acesso ao histórico de auditoria requer um papel de Administrador Global ou Administrador privilegiado.

## <a name="get-reason-approver-and-ticket-number-for-approval-events"></a>Obtenha razão, aprovação e número de bilhete para eventos de aprovação

1. Inscreva-se no [portal Azure](https://aad.portal.azure.com) com permissões de função de administrador de função privilegiada e abra a Azure AD.
1. Selecione **Registos de auditoria**.
1. Utilize o filtro **De serviço** para exibir apenas eventos de auditoria para o serviço de Gestão de Identidade Privilegiada. Na página **de registos de auditoria,** pode:

    - Consulte o motivo de um evento de auditoria na coluna **Status Reason.**
    - Consulte o aprovador na coluna **Iniciada por (ator)** para o evento "adicionar membro ao pedido de função aprovado".

    [![Filtrar o registo de auditoria para o serviço PIM](media/azure-pim-resource-rbac/filter-audit-logs.png "Filtrar o registo de auditoria para o serviço PIM")](media/azure-pim-resource-rbac/filter-audit-logs.png)

1. Selecione um evento de registo de auditoria para ver o número do bilhete no separador **Atividade** do painel **De detalhes.**
  
    [![Verifique o número do bilhete para o evento de auditoria](media/azure-pim-resource-rbac/audit-event-ticket-number.png "Verifique o número do bilhete para o evento de auditoria")](media/azure-pim-resource-rbac/audit-event-ticket-number.png)]

1. Pode ver o solicitador (pessoa que ativa a função) no **separador Alvos** do painel **De detalhes** para um evento de auditoria. Existem três tipos-alvo para funções de recursos Azure:

    - O papel **(Tipo** = Função)
    - O solicitador **(Tipo** = Outros)
    - O aprovador **(Tipo** = Utilizador)

    [![Verifique o tipo de alvo](media/azure-pim-resource-rbac/audit-event-target-type.png "Verifique o tipo de alvo")](media/azure-pim-resource-rbac/audit-event-target-type.png)

Normalmente, o evento de log imediatamente acima do evento de aprovação é um evento para "Adicionar membro ao papel concluído" onde o **Iniciado por (ator)** é o solicitador. Na maioria dos casos, não precisará de encontrar o solicitador no pedido de aprovação numa perspetiva de auditoria.

## <a name="next-steps"></a>Passos seguintes

- [Atribuir funções de recursos da Azure na Gestão de Identidade Privilegiada](pim-resource-roles-assign-roles.md)
- [Aprovar ou negar pedidos de funções de recursos da Azure na Gestão de Identidade Privilegiada](pim-resource-roles-approval-workflow.md)
- [Ver histórico de auditoria para funções de Azure AD em Gestão de Identidade Privilegiada](pim-how-to-use-audit-log.md)
