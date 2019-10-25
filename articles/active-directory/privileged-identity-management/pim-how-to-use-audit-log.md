---
title: Exibir histórico de auditoria para funções do Azure AD no PIM-Azure Active Directory | Microsoft Docs
description: Saiba como exibir o histórico de auditoria das funções do Azure AD no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/22/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9aa1c5121c723148884584ca3f00522c1c459859
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72808993"
---
# <a name="view-audit-history-for-azure-ad-roles-in-pim"></a>Exibir histórico de auditoria para funções do Azure AD no PIM

Você pode usar o histórico de auditoria de Privileged Identity Management (PIM) para ver todas as atribuições de função e ativações nos últimos 30 dias para todas as funções privilegiadas. Se você quiser ver o histórico de auditoria completo de atividade na organização do Azure Active Directory (Azure AD), incluindo o administrador, o usuário final e a atividade de sincronização, poderá usar os [relatórios de segurança e atividade do Azure Active Directory](../reports-monitoring/overview-reports.md).

## <a name="view-audit-history"></a>Ver histórico de auditoria

Siga estas etapas para exibir o histórico de auditoria para funções do Azure AD.

1. Entre no [portal do Azure](https://portal.azure.com/) com um usuário que seja membro da função de [administrador de função com privilégios](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) .

1. Abra **Azure ad Privileged Identity Management**.

1. Clique em **funções do Azure ad**.

1. Clique em **diretório funções histórico de auditoria**.

    Dependendo do histórico de auditoria, um gráfico de colunas é exibido junto com o total de ativações, Max ativações por dia e média de ativações por dia.

    ![Histórico de auditoria de funções de diretório](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

    Na parte inferior da página, é exibida uma tabela com informações sobre cada ação no histórico de auditoria disponível. As colunas têm os seguintes significados:

    | Column | Descrição |
    | --- | --- |
    | Tempo | Quando a ação ocorreu. |
    | Solicitante | Usuário que solicitou a ativação ou alteração da função. Se o valor for **sistema do Azure**, verifique o histórico de auditoria do Azure para obter mais informações. |
    | Ação | Ações tomadas pelo solicitante. As ações podem incluir atribuir, Cancelar atribuição, ativar, desativar ou AddedOutsidePIM. |
    | Membro | Usuário que está ativando ou atribuído a uma função. |
    | Função | Função atribuída ou ativada pelo usuário. |
    | Reasoning | Texto inserido no campo motivo durante a ativação. |
    | Ela | Quando uma função ativada expira. Aplica-se somente a atribuições de função qualificadas. |

1. Para classificar o histórico de auditoria, clique nos botões de **hora**, **ação**e **função** .

## <a name="filter-audit-history"></a>Filtrar histórico de auditoria

1. Na parte superior da página Histórico de auditoria, clique no botão **Filtrar** .

    O painel **atualizar parâmetros do gráfico** é exibido.

1. Em **intervalo de tempo**, selecione um intervalo de tempo.

1. Em **funções**, marque as caixas de seleção para indicar as funções que você deseja exibir.

    ![Atualizar painel de parâmetros do gráfico](media/pim-how-to-use-audit-log/update-chart-parameters.png)

1. Clique em **concluído** para exibir o histórico de auditoria filtrado.

## <a name="next-steps"></a>Passos seguintes

- [Exibir a atividade e o histórico de auditoria das funções de recurso do Azure no Privileged Identity Management](azure-pim-resource-rbac.md)
