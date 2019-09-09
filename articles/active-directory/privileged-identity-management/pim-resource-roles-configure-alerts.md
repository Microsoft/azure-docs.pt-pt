---
title: Configurar alertas de segurança para funções de recurso do Azure em PIM-Azure Active Directory | Microsoft Docs
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
ms.date: 04/02/2018
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 177f61392c3e441c891ba1b531301b3dae8c0db2
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804229"
---
# <a name="configure-security-alerts-for-azure-resource-roles-in-pim"></a>Configurar alertas de segurança para funções de recurso do Azure no PIM
O Azure Active Directory (Azure AD) Privileged Identity Management (PIM) gera alertas quando há atividade suspeita ou não segura em seu ambiente. Quando um alerta é disparado, ele aparece na página alertas. 

![Recursos do Azure-alerta de listagem de página de alertas, nível de risco e contagem](media/pim-resource-roles-configure-alerts/rbac-alerts-page.png)

## <a name="review-alerts"></a>Examinar alertas
Selecione um alerta para ver um relatório que lista os usuários ou funções que dispararam o alerta, juntamente com o aviso de correção.

![Relatório de alertas mostrando a hora da última verificação, descrição, etapas de mitigação, tipo, gravidade, impacto de segurança e como evitar a próxima vez](media/pim-resource-roles-configure-alerts/rbac-alert-info.png)

## <a name="alerts"></a>Alertas
| Alerta | Severity | Acionador | Recomendação |
| --- | --- | --- | --- |
| **Muitos proprietários atribuídos a um recurso** |Médio |Muitos usuários têm a função de proprietário. |Examine os usuários na lista e reatribua alguns a funções com menos privilégios. |
| **Muitos proprietários permanentes atribuídos a um recurso** |Médio |Muitos usuários são permanentemente atribuídos a uma função. |Examine os usuários na lista e reatribua alguns para exigir a ativação para uso de função. |
| **Função duplicada criada** |Médio |Várias funções têm os mesmos critérios. |Use apenas uma dessas funções. |


### <a name="severity"></a>Severity
* **Alta**: Requer ação imediata devido a uma violação de política. 
* **Médio**: Não requer ação imediata, mas sinaliza uma possível violação de política.
* **Baixo**: Não requer ação imediata, mas sugere uma alteração de política preferida.

## <a name="configure-security-alert-settings"></a>Definir configurações de alerta de segurança
Na página alertas, vá para **configurações**.

![Página de alertas com configurações realçadas](media/pim-resource-roles-configure-alerts/rbac-navigate-settings.png)

Personalize as configurações nos diferentes alertas para trabalhar com suas metas de ambiente e de segurança.

![Página de configuração de um alerta para habilitar e definir configurações](media/pim-resource-roles-configure-alerts/rbac-alert-settings.png)

## <a name="next-steps"></a>Passos seguintes

- [Definir configurações de função de recurso do Azure no PIM](pim-resource-roles-configure-role-settings.md)
