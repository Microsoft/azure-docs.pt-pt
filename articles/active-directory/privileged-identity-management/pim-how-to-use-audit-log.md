---
title: Exibir relatório de auditoria para funções do Azure AD no PIM – Azure AD | Microsoft Docs
description: Saiba como exibir o histórico de auditoria das funções do Azure AD no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 11/13/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: a275b08beac842c7d435d77d6b4c1338e817fbc7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75430111"
---
# <a name="view-audit-history-for-azure-ad-roles-in-pim"></a>Exibir histórico de auditoria para funções do Azure AD no PIM

Você pode usar o histórico de auditoria de Privileged Identity Management (PIM) para ver todas as atribuições de função e ativações nos últimos 30 dias para todas as funções privilegiadas. Se você quiser ver o histórico de auditoria completo de atividade na organização do Azure Active Directory (Azure AD), incluindo o administrador, o usuário final e a atividade de sincronização, poderá usar os [relatórios de segurança e atividade do Azure Active Directory](../reports-monitoring/overview-reports.md).

## <a name="determine-your-version-of-pim"></a>Determinar sua versão do PIM

A partir de novembro de 2019, a parte das funções do Azure AD da Privileged Identity Management está sendo atualizada para uma nova versão que corresponde às experiências de funções de recurso do Azure. Isso cria recursos adicionais, bem como [as alterações na API existente](azure-ad-roles-features.md#api-changes). Enquanto a nova versão está sendo distribuída, os procedimentos que você seguir neste artigo dependem da versão do Privileged Identity Management que você tem atualmente. Siga as etapas nesta seção para determinar qual versão do Privileged Identity Management você tem. Depois de saber sua versão do Privileged Identity Management, você pode selecionar os procedimentos neste artigo que correspondem a essa versão.

1. Entre no [portal do Azure](https://portal.azure.com/) com um usuário que esteja na função de [administrador de função com privilégios](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) .
1. Abra **Azure ad Privileged Identity Management**. Se você tiver uma faixa na parte superior da página Visão geral, siga as instruções na **nova guia versão** deste artigo. Caso contrário, siga as instruções na guia **versão anterior** .

    ![Nova versão das funções do Azure AD](./media/pim-how-to-add-role-to-user/pim-new-version.png)

# <a name="previous-versiontabprevious"></a>[Versão anterior](#tab/previous)

## <a name="view-audit-history"></a>Ver histórico de auditoria

Siga estas etapas para exibir o histórico de auditoria para funções do Azure AD.

1. Entre no [portal do Azure](https://portal.azure.com/) com um usuário que seja membro da função de [administrador de função com privilégios](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) .

1. Abra **Azure ad Privileged Identity Management**.

1. Selecione **funções do Azure ad**.

1. Selecione o **histórico de auditoria de funções de diretório**.

    Dependendo do histórico de auditoria, um gráfico de colunas é exibido junto com o total de ativações, Max ativações por dia e média de ativações por dia.

    ![Histórico de auditorias de funções de diretório](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

    Na parte inferior da página, é exibida uma tabela com informações sobre cada ação no histórico de auditoria disponível. As colunas têm os seguintes significados:

    | Column | Descrição |
    | --- | --- |
    | Tempo | Quando a ação ocorreu. |
    | Requerente | Usuário que solicitou a ativação ou alteração da função. Se o valor for **sistema do Azure**, verifique o histórico de auditoria do Azure para obter mais informações. |
    | Ação | Ações tomadas pelo solicitante. As ações podem incluir atribuir, Cancelar atribuição, ativar, desativar ou AddedOutsidePIM. |
    | Membro | Usuário que está ativando ou atribuído a uma função. |
    | Função | Função atribuída ou ativada pelo usuário. |
    | Reasoning | Texto inserido no campo motivo durante a ativação. |
    | Expiração | Quando uma função ativada expira. Aplica-se somente a atribuições de função qualificadas. |

1. Para classificar o histórico de auditoria, clique nos botões de **hora**, **ação**e **função** .

## <a name="filter-audit-history"></a>Filtrar histórico de auditoria

1. Na parte superior da página Histórico de auditoria, clique no botão **Filtrar** .

    O painel **atualizar parâmetros do gráfico** é exibido.

1. Em **intervalo de tempo**, selecione um intervalo de tempo.

1. Em **funções**, marque as caixas de seleção para indicar as funções que você deseja exibir.

    ![Atualizar painel de parâmetros do gráfico](media/pim-how-to-use-audit-log/update-chart-parameters.png)

1. Selecione **concluído** para exibir o histórico de auditoria filtrado.

# <a name="new-versiontabnew"></a>[Nova versão](#tab/new)

Siga estas etapas para exibir o histórico de auditoria para funções do Azure AD.

## <a name="view-resource-audit-history"></a>Exibir histórico de auditoria de recursos

A auditoria de recursos fornece uma exibição de todas as atividades associadas às suas funções do Azure AD.

1. Abra **Azure ad Privileged Identity Management**.

1. Selecione **funções do Azure ad**.

1. Selecione **auditoria de recurso**.

1. Filtre o histórico usando uma data predefinida ou um intervalo personalizado.

    ![Lista de auditoria de recursos com filtros](media/azure-pim-resource-rbac/rbac-resource-audit.png)

## <a name="view-my-audit"></a>Exibir minha auditoria

Minha auditoria permite que você exiba sua atividade de função pessoal.

1. Abra **Azure ad Privileged Identity Management**.

1. Selecione **funções do Azure ad**.

1. Selecione o recurso para o qual você deseja exibir o histórico de auditoria.

1. Selecione **minha auditoria**.

1. Filtre o histórico usando uma data predefinida ou um intervalo personalizado.

    ![Lista de auditoria para o usuário atual](media/azure-pim-resource-rbac/my-audit-time.png)

---

## <a name="next-steps"></a>Passos seguintes

- [Exibir a atividade e o histórico de auditoria das funções de recurso do Azure no Privileged Identity Management](azure-pim-resource-rbac.md)
