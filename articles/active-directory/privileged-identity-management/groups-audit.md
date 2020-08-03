---
title: Ver relatório de auditoria para atribuições de grupos de acesso privilegiado em Gestão de Identidade Privilegiada (PIM) - Azure AD ! Microsoft Docs
description: Ver histórico de atividades e auditorias para atribuições privilegiadas de grupos de acesso em Azure AD Gestão de Identidade Privilegiada (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: pim
ms.date: 07/27/2020
ms.author: curtand
ms.reviewer: shaunliu
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9bbc90776ca007b84d5f67c50f8550ee9c881c7
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87506046"
---
# <a name="audit-activity-history-for-privileged-access-group-assignments-preview-in-privileged-identity-management"></a>Histórico de atividades de auditoria para atribuições privilegiadas de grupos de acesso (pré-visualização) em Gestão de Identidade Privilegiada

Com Gestão de Identidade Privilegiada (PIM), pode visualizar atividade, ativações e histórico de auditoria para membros privilegiados do grupo de acesso Azure e proprietários dentro da sua organização Azure Ative Directory (Azure AD).

> [!NOTE]
> Se a sua organização tiver funções de gestão subcontratadas a um prestador de serviços que utilize [a Azure delegada em gestão de recursos,](../../lighthouse/concepts/azure-delegated-resource-management.md)as atribuições de funções autorizadas por esse prestador de serviços não serão mostradas aqui.

Siga estes passos para ver o histórico de auditoria para grupos de acesso privilegiados.

## <a name="view-resource-audit-history"></a>Ver histórico de auditoria de recursos

**A auditoria de recursos** dá-lhe uma visão de toda a atividade associada aos seus grupos de acesso privilegiados.

1. Open **Azure AD Gestão de Identidade Privilegiada.**

1. Selecione **acesso privilegiado (Pré-visualização)**.

1. No âmbito **da Atividade**, selecione a auditoria **de Recursos.**

1. Filtrar o histórico utilizando uma data ou um intervalo personalizado predefinidos.

    ![Lista de auditoria de recursos com filtros](media/groups-audit/groups-resource-audit.png)

## <a name="view-my-audit"></a>Ver a minha auditoria

A minha auditoria permite-lhe ver a sua atividade de papel pessoal.

1. Open **Azure AD Gestão de Identidade Privilegiada.**

1. Selecione **acesso privilegiado (Pré-visualização)**.

1. Selecione o membro ou grupo para o quais deseja visualizar o histórico de auditoria.

1. Selecione **A minha auditoria.**

1. Filtrar o histórico utilizando uma data ou um intervalo personalizado predefinidos.

    ![Lista de auditoria para o utilizador atual](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="next-steps"></a>Passos seguintes

- [Atribuir a filiação e a propriedade do grupo na Gestão de Identidade Privilegiada](groups-assign-member-owner.md)
- [Aprovar ou negar pedidos de grupos de acesso privilegiados em PIM](groups-approval-workflow.md)
- [Ver histórico de auditoria para funções de Azure AD em PIM](groups-audit.md)
