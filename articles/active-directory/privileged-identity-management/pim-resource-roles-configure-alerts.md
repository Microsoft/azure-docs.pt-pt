---
title: Configure alertas de segurança para funções de recurso Azure na Gestão de Identidade Privilegiada - Diretório Ativo Azure [ Diretório Ativo] Microsoft Docs
description: Saiba configurar alertas de segurança para funções de recursos Azure na Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0b938dc808d9b02ad4105d85a5b3125135c51d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74023074"
---
# <a name="configure-security-alerts-for-azure-resource-roles-in-privileged-identity-management"></a>Configure alertas de segurança para funções de recurso Azure na Gestão de Identidade Privilegiada

A Privileged Identity Management (PIM) gera alertas quando existe atividade suspeita ou insegura na sua organização Azure Ative Directory (Azure AD). Quando um alerta é acionado, aparece na página alertas.

![Recursos Azure - Alertas página listando alerta, nível de risco e contagem](media/pim-resource-roles-configure-alerts/rbac-alerts-page.png)

## <a name="review-alerts"></a>Rever alertas

Selecione um alerta para ver um relatório que lista os utilizadores ou funções que desencadearam o alerta, juntamente com a orientação de reparação.

![Relatório de alerta mostrando a última hora de digitalização, descrição, passos de mitigação, tipo, gravidade, impacto de segurança, e como prevenir a próxima vez](media/pim-resource-roles-configure-alerts/rbac-alert-info.png)

## <a name="alerts"></a>Alertas

| Alerta | Gravidade | Acionador | Recomendação |
| --- | --- | --- | --- |
| **Demasiados proprietários atribuídos a um recurso** |Médio |Muitos utilizadores têm o papel de proprietário. |Reveja os utilizadores da lista e reatribua alguns para papéis menos privilegiados. |
| **Demasiados proprietários permanentes atribuídos a um recurso** |Médio |Demasiados utilizadores são permanentemente atribuídos a um papel. |Reveja os utilizadores da lista e redesigne alguns para exigir a ativação para uso do papel. |
| **Papel duplicado criado** |Médio |Múltiplas funções têm os mesmos critérios. |Use apenas um destes papéis. |

### <a name="severity"></a>Gravidade

- **Alto**: Requer uma ação imediata por causa de uma violação de política. 
- **Médio**: Não requer medidas imediatas, mas sinaliza uma potencial violação da política.
- **Baixo**: Não requer medidas imediatas, mas sugere uma mudança de política preferencial.

## <a name="configure-security-alert-settings"></a>Configurar as definições de alerta de segurança

A partir da página Alertas, vá a **Definições**.

![Página de alertas com Definições destacadas](media/pim-resource-roles-configure-alerts/rbac-navigate-settings.png)

Personalize as definições nos diferentes alertas para trabalhar com o seu ambiente e objetivos de segurança.

![Página de definição para um alerta para ativar e configurar definições](media/pim-resource-roles-configure-alerts/rbac-alert-settings.png)

## <a name="next-steps"></a>Passos seguintes

- [Configure definições de funções de recursos Azure na Gestão de Identidade Privilegiada](pim-resource-roles-configure-role-settings.md)
