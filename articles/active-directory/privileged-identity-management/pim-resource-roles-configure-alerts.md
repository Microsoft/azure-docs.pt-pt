---
title: Configurar alertas de segurança para funções de recurso do Azure no Privileged Identity Management-Azure Active Directory | Microsoft Docs
description: Saiba como configurar alertas de segurança para funções de recurso do Azure no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 10/23/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f191eac841bd9f79844a09cd25725de7ea8b154
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72895782"
---
# <a name="configure-security-alerts-for-azure-resource-roles-in-privileged-identity-management"></a>Configurar alertas de segurança para funções de recurso do Azure no Privileged Identity Management

Privileged Identity Management (PIM) gera alertas quando há atividade suspeita ou não segura em sua organização do Azure Active Directory (AD do Azure). Quando um alerta é disparado, ele aparece na página alertas.

![Recursos do Azure-alerta de listagem de página de alertas, nível de risco e contagem](media/pim-resource-roles-configure-alerts/rbac-alerts-page.png)

## <a name="review-alerts"></a>Examinar alertas

Selecione um alerta para ver um relatório que lista os usuários ou funções que dispararam o alerta, juntamente com diretrizes de correção.

![Relatório de alertas mostrando a hora da última verificação, descrição, etapas de mitigação, tipo, gravidade, impacto de segurança e como evitar a próxima vez](media/pim-resource-roles-configure-alerts/rbac-alert-info.png)

## <a name="alerts"></a>Alertas

| Alerta | Gravidade | Acionador | Recomendação |
| --- | --- | --- | --- |
| **Muitos proprietários atribuídos a um recurso** |Médio |Muitos usuários têm a função de proprietário. |Examine os usuários na lista e reatribua alguns a funções com menos privilégios. |
| **Muitos proprietários permanentes atribuídos a um recurso** |Médio |Muitos usuários são permanentemente atribuídos a uma função. |Examine os usuários na lista e reatribua alguns para exigir a ativação para uso de função. |
| **Função duplicada criada** |Médio |Várias funções têm os mesmos critérios. |Use apenas uma dessas funções. |

### <a name="severity"></a>Gravidade

- **Alto**: requer ação imediata devido a uma violação de política. 
- **Médio**: não requer ação imediata, mas sinaliza uma possível violação de política.
- **Baixo**: não requer ação imediata, mas sugere uma alteração de política preferida.

## <a name="configure-security-alert-settings"></a>Definir configurações de alerta de segurança

Na página alertas, vá para **configurações**.

![Página de alertas com configurações realçadas](media/pim-resource-roles-configure-alerts/rbac-navigate-settings.png)

Personalize as configurações nos diferentes alertas para trabalhar com suas metas de ambiente e de segurança.

![Página de configuração de um alerta para habilitar e definir configurações](media/pim-resource-roles-configure-alerts/rbac-alert-settings.png)

## <a name="next-steps"></a>Passos seguintes

- [Definir configurações de função de recurso do Azure no Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
