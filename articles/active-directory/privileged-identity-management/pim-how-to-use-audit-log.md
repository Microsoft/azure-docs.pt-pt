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
ms.date: 06/10/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3547ff218e9a15151e4abe2ceff53292d3f01ac0
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804317"
---
# <a name="view-audit-history-for-azure-ad-roles-in-pim"></a>Exibir histórico de auditoria para funções do Azure AD no PIM

Você pode usar o histórico de auditoria do Azure Active Directory (Azure AD) Privileged Identity Management (PIM) para ver todas as atribuições de função e ativações nos últimos 30 dias para todas as funções privilegiadas. Se você quiser ver o histórico de auditoria completo de atividade em seu diretório, incluindo administrador, usuário final e atividade de sincronização, você pode usar os [Azure Active Directory relatórios de segurança e atividade](../reports-monitoring/overview-reports.md).

## <a name="view-audit-history"></a>Ver histórico de auditoria

Siga estas etapas para exibir o histórico de auditoria para funções do Azure AD.

1. Entre no [portal do Azure](https://portal.azure.com/) com um usuário que seja membro da função de [administrador de função com privilégios](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) .

1. Abra **Azure ad Privileged Identity Management**.

1. Clique em **funções do Azure ad**.

1. Clique em **diretório funções histórico de auditoria**.

    Dependendo do histórico de auditoria, um gráfico de colunas é exibido junto com o total de ativações, Max ativações por dia e média de ativações por dia.

    ![Histórico de auditoria de funções de diretório](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

    Na parte inferior da página, é exibida uma tabela com informações sobre cada ação no histórico de auditoria disponível. As colunas têm os seguintes significados:

    | Coluna | Descrição |
    | --- | --- |
    | Time | Quando a ação ocorreu. |
    | Requerente | Usuário que solicitou a ativação ou alteração da função. Se o valor for **sistema do Azure**, verifique o histórico de auditoria do Azure para obter mais informações. |
    | Action | Ações tomadas pelo solicitante. As ações podem incluir atribuir, Cancelar atribuição, ativar, desativar ou AddedOutsidePIM. |
    | Membro | Usuário que está ativando ou atribuído a uma função. |
    | Role | Função atribuída ou ativada pelo usuário. |
    | Raciocínio | Texto inserido no campo motivo durante a ativação. |
    | Expiração | Quando uma função ativada expira. Aplica-se somente a atribuições de função qualificadas. |

1. Para classificar o histórico de auditoria, clique nos botões de **hora**, **ação**e **função** .

## <a name="filter-audit-history"></a>Filtrar histórico de auditoria

1. Na parte superior da página Histórico de auditoria, clique no botão **Filtrar** .

    O painel **atualizar parâmetros do gráfico** é exibido.

1. Em **intervalo de tempo**, selecione um intervalo de tempo.

1. Em **funções**, adicione marcas de seleção para as funções que você deseja exibir.

    ![Atualizar painel de parâmetros do gráfico](media/pim-how-to-use-audit-log/update-chart-parameters.png)

1. Clique em **concluído** para exibir o histórico de auditoria filtrado.

## <a name="next-steps"></a>Passos Seguintes

- [Exibir histórico de atividades e de auditoria para funções de recurso do Azure no PIM](azure-pim-resource-rbac.md)
