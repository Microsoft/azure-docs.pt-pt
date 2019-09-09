---
title: Exibir a atividade e o histórico de auditoria das funções de recurso do Azure no PIM-Azure Active Directory | Microsoft Docs
description: Exiba a atividade e o histórico de auditoria das funções de recurso do Azure no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
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
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40e1ed018f66d4f5eefd02d587504cf64969f47b
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804043"
---
# <a name="view-activity-and-audit-history-for-azure-resource-roles-in-pim"></a>Exibir histórico de atividades e de auditoria para funções de recurso do Azure no PIM

Com o Azure Active Directory (Azure AD) Privileged Identity Management (PIM), você pode exibir a atividade, ativações e o histórico de auditoria para funções de recursos do Azure em sua organização. Isso inclui assinaturas, grupos de recursos e até mesmo máquinas virtuais. Qualquer recurso dentro do portal do Azure que aproveita a funcionalidade RBAC (controle de acesso baseado em função) do Azure pode aproveitar os recursos de segurança e gerenciamento do ciclo de vida no PIM.

## <a name="view-activity-and-activations"></a>Exibir atividade e ativações

Para ver quais ações um usuário específico realizou em vários recursos, você pode exibir a atividade de recursos do Azure associada a um determinado período de ativação.

1. Abra **Azure ad Privileged Identity Management**.

1. Clique em **recursos do Azure**.

1. Clique no recurso para o qual você deseja exibir a atividade e as ativações.

1. Clique em **funções** ou **Membros**.

1. Clique em um usuário.

    Você vê uma exibição gráfica das ações do usuário nos recursos do Azure por data. Ele também mostra as ativações de função recentes no mesmo período de tempo.

    ![Detalhes do usuário com Resumo de atividade de recursos e ativações de função](media/azure-pim-resource-rbac/rbac-user-details.png)

1. Clique em uma ativação de função específica para ver os detalhes e a atividade de recurso do Azure correspondente que ocorreu enquanto esse usuário estava ativo.

    ![Ativação de função selecionada e detalhes da atividade exibidos por data](media/azure-pim-resource-rbac/rbac-user-resource-activity.png)

## <a name="export-role-assignments-with-children"></a>Exportar atribuições de função com filhos

Você pode ter um requisito de conformidade onde deve fornecer uma lista completa de atribuições de função para auditores. O PIM permite consultar atribuições de função em um recurso específico, que inclui atribuições de função para todos os recursos filho. Anteriormente, era difícil para os administradores obter uma lista completa de atribuições de função para uma assinatura e tinham de exportar atribuições de função para cada recurso específico. Usando o PIM, você pode consultar todas as atribuições de função ativas e qualificadas em uma assinatura, incluindo atribuições de função para todos os grupos de recursos e recursos.

1. Abra **Azure ad Privileged Identity Management**.

1. Clique em **recursos do Azure**.

1. Clique no recurso para o qual você deseja exportar atribuições de função, como uma assinatura.

1. Clique em **Membros**.

1. Clique em **Exportar** para abrir o painel Exportar associação.

    ![Exportar o painel de associação para exportar todos os membros](media/azure-pim-resource-rbac/export-membership.png)

1. Clique em **exportar todos os membros** para exportar todas as atribuições de função em um arquivo CSV.

    ![Atribuições de função exportadas em CSV Fil como exibidas no Excel](media/azure-pim-resource-rbac/export-csv.png)

## <a name="view-resource-audit-history"></a>Exibir histórico de auditoria de recursos

A auditoria de recursos fornece uma exibição de todas as atividades de função de um recurso.

1. Abra **Azure ad Privileged Identity Management**.

1. Clique em **recursos do Azure**.

1. Clique no recurso para o qual você deseja exibir o histórico de auditoria.

1. Clique em **auditoria de recurso**.

1. Filtre o histórico usando uma data predefinida ou um intervalo personalizado.

    ![Lista de auditoria de recursos com filtros](media/azure-pim-resource-rbac/rbac-resource-audit.png)

1. Para **tipo de auditoria**, selecione **Ativar (atribuído + ativado)** .

    ![Lista de auditoria de recursos filtrada por ativar tipo de auditoria](media/azure-pim-resource-rbac/rbac-audit-activity.png)

1. Em **ação**, clique em **(atividade)** para que um usuário veja os detalhes da atividade do usuário nos recursos do Azure.

    ![Detalhes da atividade do usuário para uma ação específica](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

## <a name="view-my-audit"></a>Exibir minha auditoria

Minha auditoria permite que você exiba sua atividade de função pessoal.

1. Abra **Azure ad Privileged Identity Management**.

1. Clique em **recursos do Azure**.

1. Clique no recurso para o qual você deseja exibir o histórico de auditoria.

1. Clique em **minha auditoria**.

1. Filtre o histórico usando uma data predefinida ou um intervalo personalizado.

    ![Lista de auditoria para o usuário atual](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="next-steps"></a>Passos seguintes

- [Atribuir funções de recurso do Azure no PIM](pim-resource-roles-assign-roles.md)
- [Aprovar ou negar solicitações para funções de recurso do Azure no PIM](pim-resource-roles-approval-workflow.md)
- [Exibir histórico de auditoria para funções do Azure AD no PIM](pim-how-to-use-audit-log.md)
