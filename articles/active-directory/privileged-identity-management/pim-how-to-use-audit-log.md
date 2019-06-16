---
title: Ver histórico de auditoria para funções do Azure AD no PIM - Azure Active Directory | Documentos da Microsoft
description: Saiba como ver o histórico de auditoria para funções do Azure AD no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 06/10/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8061cff8d39db66cb22a5650c7688657aa8b3554
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67053918"
---
# <a name="view-audit-history-for-azure-ad-roles-in-pim"></a>Ver o histórico de auditoria para funções do Azure AD no PIM

Pode utilizar o histórico de auditoria do Azure Active Directory (Azure AD) Privileged Identity Management (PIM) para ver todas as atribuições de funções e ativações durante os últimos 30 dias para funções privilegiadas todos os. Se pretender ver o histórico de auditoria de atividade no seu diretório, incluindo administrador, o utilizador final e atividade de sincronização, pode utilizar o [relatórios de atividade e de segurança do Azure Active Directory](../reports-monitoring/overview-reports.md).

## <a name="view-audit-history"></a>Ver histórico de auditoria

Siga estes passos para ver o histórico de auditoria para funções do Azure AD.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) com um utilizador que seja um membro a [administrador com função privilegiada](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) função.

1. Open **do Azure AD Privileged Identity Management**.

1. Clique em **funções do Azure AD**.

1. Clique em **histórico de auditorias de funções de diretório**.

    Dependendo do seu histórico de auditoria, um gráfico de colunas é apresentado juntamente com o total de ativações, ativações máximas por dia e ativações média por dia.

    ![Histórico de auditorias de funções de diretório](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

    Na parte inferior da página, uma tabela é apresentada com informações sobre cada ação no histórico de auditoria disponíveis. As colunas têm o significado seguinte:

    | Coluna | Descrição |
    | --- | --- |
    | Hora | Quando a ação ocorreu. |
    | Requerente | Utilizador que pediu a ativação de função ou a alteração. Se o valor for **sistema de Azure**, verifique o histórico de auditoria do Azure para obter mais informações. |
    | Ação | Ações executadas pelo requerente. Ações podem incluir atribuir, Unassign, ativar, desativar ou AddedOutsidePIM. |
    | Membro | Utilizador que consiste em ativar ou atribuído a uma função. |
    | Função | Função atribuída ou ativada pelo utilizador. |
    | Reasoning | Texto que foi introduzido no campo motivo durante a ativação. |
    | expiração | Quando uma função ativada expira. Aplica-se apenas às atribuições de funções elegíveis. |

1. Para ordenar o histórico de auditoria, clique a **tempo**, **ação**, e **função** botões.

## <a name="filter-audit-history"></a>Histórico de auditoria de filtro

1. Na parte superior da página de histórico de auditoria, clique nas **filtro** botão.

    O **atualizar parâmetros do gráfico** é apresentado o painel.

1. Na **intervalo de tempo**, selecione um intervalo de tempo.

1. Na **funções**, adicionar marcas de verificação para as funções de que pretende visualizar.

    ![Atualizar o painel de parâmetros do gráfico](media/pim-how-to-use-audit-log/update-chart-parameters.png)

1. Clique em **feito** para ver o histórico de auditoria filtrado.

## <a name="next-steps"></a>Passos Seguintes

- [Ver histórico de atividade e auditoria para funções de recursos do Azure no PIM](azure-pim-resource-rbac.md)
