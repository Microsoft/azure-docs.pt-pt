---
title: Ver relatório de registo de auditoria para as funções de Azure AD em Azure AD PIM | Microsoft Docs
description: Saiba como ver o histórico de registos de auditoria para as funções de Azure AD em Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 01/07/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8481c562ecbab1f26e877e55a5a9454695ddf4c0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92370836"
---
# <a name="view-audit-history-for-azure-ad-roles-in-privileged-identity-management"></a>Ver histórico de auditoria para funções de Azure AD em Gestão de Identidade Privilegiada

Pode utilizar o histórico de auditoria da Gestão de Identidade Privilegiada (PIM) para ver todas as atribuições e ativações de papéis nos últimos 30 dias para todas as funções privilegiadas. Se quiser ver o histórico completo de auditoria da sua organização Azure Ative Directory (Azure AD), incluindo administrador, utilizador final e atividade de sincronização, pode utilizar os relatórios de segurança e atividade do [Azure Ative Directory](../reports-monitoring/overview-reports.md).

## <a name="determine-your-version-of-pim"></a>Determine a sua versão de PIM

A partir de novembro de 2019, a parte de funções da AZure AD da Gestão de Identidade Privilegiada está a ser atualizada para uma nova versão que corresponde às experiências para funções de recursos Azure. Isto cria funcionalidades adicionais, bem como [alterações à API existente.](azure-ad-roles-features.md#api-changes) Enquanto a nova versão está a ser lançada, quais os procedimentos que segue neste artigo dependem da versão de Gestão de Identidade Privilegiada que tem atualmente. Siga os passos nesta secção para determinar qual a versão da Gestão de Identidade Privilegiada que tem. Depois de conhecer a sua versão de Gestão de Identidade Privilegiada, pode selecionar os procedimentos deste artigo que correspondam a essa versão.

1. Inscreva-se no [portal Azure](https://portal.azure.com/) com um utilizador que esteja na [função de administrador](../roles/permissions-reference.md#privileged-role-administrator) privilegiado.
1. Open **Azure AD Gestão de Identidade Privilegiada.** Se tiver um banner no topo da página geral, siga as instruções no separador **Nova versão** deste artigo. Caso contrário, siga as instruções no separador **versão anterior.**

    [![Screenshot que mostra a página "Azure AD roles - Directory roles audit history".](media/pim-how-to-use-audit-log/directory-roles-audit-history.png "Selecione o separador para a sua versão")](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

# <a name="new-version"></a>[Nova versão](#tab/new)

Siga estes passos para ver o histórico de auditoria para as funções de Azure AD.

## <a name="view-resource-audit-history"></a>Ver histórico de auditoria de recursos

A auditoria de recursos dá-lhe uma visão de toda a atividade associada às suas funções Azure AD.

1. Open **Azure AD Gestão de Identidade Privilegiada.**

1. Selecione **funções AD Azure**.

1. Selecione **a auditoria de recursos.**

1. Filtrar o histórico utilizando uma data ou um intervalo personalizado predefinidos.

    ![Lista de auditoria de recursos com filtros](media/azure-pim-resource-rbac/rbac-resource-audit.png)

## <a name="view-my-audit"></a>Ver a minha auditoria

A minha auditoria permite-lhe ver a sua atividade de papel pessoal.

1. Open **Azure AD Gestão de Identidade Privilegiada.**

1. Selecione **funções AD Azure**.

1. Selecione o recurso para o quais pretende visualizar o histórico de auditoria.

1. Selecione **A minha auditoria.**

1. Filtrar o histórico utilizando uma data ou um intervalo personalizado predefinidos.

    ![Lista de auditoria para o utilizador atual](media/azure-pim-resource-rbac/my-audit-time.png)

# <a name="previous-version"></a>[Versão anterior](#tab/previous)

## <a name="view-audit-history"></a>Ver histórico de auditoria

Siga estes passos para ver o histórico de auditoria para as funções de Azure AD.

1. Inscreva-se no [portal Azure](https://portal.azure.com/) com um utilizador que é membro da função de Administrador de [Função Privilegiada.](../roles/permissions-reference.md#privileged-role-administrator)

1. Open **Azure AD Gestão de Identidade Privilegiada.**

1. Selecione **funções AD Azure**.

1. Selecione **o histórico de auditoria de funções de diretório**.

    Dependendo do histórico da sua auditoria, é apresentado um gráfico de colunas juntamente com as ativações totais, ativações máximas por dia e ativações médias por dia.

    [![Azure AD funções nova versão](media/pim-how-to-use-audit-log/directory-roles-audit-history.png "Ver histórico de auditoria de funções de diretório")](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

    Na parte inferior da página, é apresentada uma tabela com informações sobre cada ação no histórico de auditoria disponível. As colunas têm os seguintes significados:

    | Coluna | Descrição |
    | --- | --- |
    | Hora | Quando a ação ocorreu. |
    | Requerente | Utilizador que solicitou a ativação ou alteração da função. Se o valor for **O Sistema Azure,** consulte o histórico de auditoria do Azure para obter mais informações. |
    | Ação | Ações tomadas pelo solicitador. As ações podem incluir Atribuir, Não Assinar, Ativar, Desativar ou Adicionar OutsidePIM. |
    | Membro | Utilizador que está a ativar ou a atribuir a uma função. |
    | Função | Função atribuída ou ativada pelo utilizador. |
    | Raciocínio | Texto que foi introduzido no campo da razão durante a ativação. |
    | Expiração | Quando uma função ativa expira. Aplica-se apenas a atribuições de funções elegíveis. |

1. Para classificar o histórico de auditoria, clique nos botões **Tempo,** **Ação** e **Fun.**

## <a name="filter-audit-history"></a>Histórico de auditoria de filtro

1. No topo da página de histórico de auditoria, clique no botão **Filtro.**

    Aparece o painel de parâmetros do **gráfico de atualização.**

1. No **intervalo de tempo,** selecione um intervalo de tempo.

1. Em **Roles**, selecione as caixas de verificação para indicar as funções que pretende visualizar.

    ![Painel de parâmetros de gráfico de atualização](media/pim-how-to-use-audit-log/update-chart-parameters.png)

1. Selecione **Feito** para ver o histórico de auditoria filtrado.

## <a name="get-reason-approver-and-ticket-number-for-approval-events"></a>Obtenha razão, aprovação e número de bilhete para eventos de aprovação

1. Inscreva-se no [portal Azure](https://aad.portal.azure.com) com permissões de função de administrador de função privilegiada e abra a Azure AD.
1. Selecione **Registos de auditoria**.
1. Utilize o filtro **De serviço** para exibir apenas eventos de auditoria para o serviço de Gestão de Identidade Privilegiada. Na página **de registos de auditoria,** pode:

    - Consulte o motivo de um evento de auditoria na coluna **Status Reason.**
    - Consulte o aprovador na coluna **Iniciada por (ator)** para o evento "adicionar membro ao pedido de função aprovado".

    [![Screenshot que mostra a página "registos de auditoria" com o menu "Iniciado por (ator) aberto e "PIM" selecionado.](media/pim-how-to-use-audit-log/filter-audit-logs.png "Filtrar o registo de auditoria para o serviço PIM")](media/pim-how-to-use-audit-log/filter-audit-logs.png)

1. Selecione um evento de registo de auditoria para ver o número do bilhete no separador **Atividade** do painel **De detalhes.**
  
    [![Screenshot que mostra a página "Registos de Auditoria" com o número do bilhete realçado no painel "Detalhes".](media/pim-how-to-use-audit-log/audit-event-ticket-number.png "Verifique o número do bilhete para o evento de auditoria")](media/pim-how-to-use-audit-log/audit-event-ticket-number.png)

1. Pode ver o solicitador (pessoa que ativa a função) no **separador Alvos** do painel **De detalhes** para um evento de auditoria. Existem dois tipos-alvo para funções AD Azure:

    - O papel **(Tipo** = Função)
    - O solicitador **(Tipo** = Utilizador)

Normalmente, o evento de registo de auditoria imediatamente acima do evento de aprovação é um evento para "Adicionar membro ao papel concluído" onde o **Iniciado por (ator)** é o solicitador. Na maioria dos casos, não precisará de encontrar o solicitador no pedido de aprovação numa perspetiva de auditoria.

---

## <a name="next-steps"></a>Passos seguintes

- [Ver histórico de atividades e auditorias para funções de recursos da Azure em Gestão de Identidade Privilegiada](azure-pim-resource-rbac.md)
