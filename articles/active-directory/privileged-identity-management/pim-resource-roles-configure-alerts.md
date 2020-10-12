---
title: Configure alertas de segurança para funções de recursos da Azure em Gestão de Identidade Privilegiada - Diretório Ativo Azure Microsoft Docs
description: Saiba como configurar alertas de segurança para funções de recursos Azure em Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0bfca096eb49ee9f1807935de1dac49151cc8ac3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84743767"
---
# <a name="configure-security-alerts-for-azure-resource-roles-in-privileged-identity-management"></a>Configure alertas de segurança para funções de recursos Azure na Gestão de Identidade Privilegiada

A Gestão privilegiada de Identidade (PIM) gera alertas quando há atividade suspeita ou insegura na sua organização Azure Ative Directory (Azure AD). Quando um alerta é acionado, aparece na página Alertas.

![Recursos Azure - Alerta de listagem de página, nível de risco e contagem](media/pim-resource-roles-configure-alerts/rbac-alerts-page.png)

## <a name="review-alerts"></a>Consultar alertas

Selecione um alerta para ver um relatório que lista os utilizadores ou funções que desencadearam o alerta, juntamente com orientações de reparação.

![Relatório de alerta mostrando a última hora de digitalização, descrição, etapas de mitigação, tipo, gravidade, impacto de segurança, e como prevenir da próxima vez](media/pim-resource-roles-configure-alerts/rbac-alert-info.png)

## <a name="alerts"></a>Alertas

| Alerta | Gravidade | Acionador | Recomendação |
| --- | --- | --- | --- |
| **Demasiados proprietários atribuídos a um recurso** |Médio |Muitos utilizadores têm o papel de proprietário. |Reveja os utilizadores da lista e reatribua alguns para papéis menos privilegiados. |
| **Demasiados proprietários permanentes atribuídos a um recurso** |Médio |Muitos utilizadores são permanentemente designados para um papel. |Reveja os utilizadores da lista e re-atribua alguns para exigir ativação para uso de funções. |
| **Papel duplicado criado** |Médio |Várias funções têm os mesmos critérios. |Use apenas um destes papéis. |

### <a name="severity"></a>Gravidade

- **High**: Requer ação imediata por causa de uma violação de política. 
- **Médio**: Não requer ação imediata, mas sinaliza uma potencial violação da política.
- **Baixo**: Não requer ação imediata, mas sugere uma mudança de política preferida.

## <a name="configure-security-alert-settings"></a>Configurar configurações de alerta de segurança

A partir da página Alertas, aceda a **Definições**.

![Página de alertas com Definições em destaque](media/pim-resource-roles-configure-alerts/rbac-navigate-settings.png)

Personalize as definições nos diferentes alertas para trabalhar com o seu ambiente e objetivos de segurança.

![Definição de página para um alerta para ativar e configurar definições](media/pim-resource-roles-configure-alerts/rbac-alert-settings.png)

## <a name="next-steps"></a>Passos seguintes

- [Configurar configurações de funções de recursos Azure na Gestão de Identidade Privilegiada](pim-resource-roles-configure-role-settings.md)
