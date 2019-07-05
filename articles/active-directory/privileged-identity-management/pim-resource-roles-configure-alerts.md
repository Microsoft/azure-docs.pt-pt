---
title: Configurar alertas de segurança para funções de recursos do Azure no PIM - Azure Active Directory | Documentos da Microsoft
description: Saiba como configurar alertas de segurança para funções de recursos do Azure no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c7ce8b79644a9ffc9481ba825ec5623a9268983
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476338"
---
# <a name="configure-security-alerts-for-azure-resource-roles-in-pim"></a>Configurar alertas de segurança para funções de recursos do Azure no PIM
O Azure Active Directory (Azure AD) Privileged Identity Management (PIM) gera alertas quando houver atividade suspeita ou não segura no seu ambiente. Quando for acionado um alerta, este aparece na página de alertas. 

![Recursos do Azure - listagem de alerta, nível de risco e contagem de página de alertas](media/pim-resource-roles-configure-alerts/rbac-alerts-page.png)

## <a name="review-alerts"></a>Rever alertas
Selecione um alerta para ver um relatório que lista os utilizadores ou funções que acionou o alerta, juntamente com conselhos de remediação.

![Relatório de alerta mostrando última análise de tempo, descrição, passos de mitigação, tipo, gravidade, o impacto de segurança e como impedir futuras ocorrências](media/pim-resource-roles-configure-alerts/rbac-alert-info.png)

## <a name="alerts"></a>Alertas
| Alerta | Severity | Acionador | Recomendação |
| --- | --- | --- | --- |
| **Existem demasiados proprietários atribuídos a um recurso** |Médio |Muitos utilizadores têm a função de proprietário. |Reveja os utilizadores na lista e reatribuir alguns para funções com menos privilégios. |
| **Existem demasiados proprietários permanentes atribuídos a um recurso** |Médio |Demasiados utilizadores permanentemente são atribuídos a uma função. |Reveja os utilizadores na lista e atribua novamente à requer ativação para utilização de função. |
| **Função duplicada criada** |Médio |Várias funções têm os mesmos critérios. |Utilize apenas uma destas funções. |


### <a name="severity"></a>Severity
* **Alta**: Requer uma ação imediata devido a uma violação de política. 
* **Médio**: Não necessita de uma ação imediata, mas sinaliza uma potencial violação de política.
* **Baixa**: Não necessita de uma ação imediata, mas sugere uma alteração de política preferencial.

## <a name="configure-security-alert-settings"></a>Configurar definições de alerta de segurança
A partir da página de alertas, aceda a **definições**.

![Página de alertas com as configurações realçadas](media/pim-resource-roles-configure-alerts/rbac-navigate-settings.png)

Personalize as configurações nos alertas diferentes para trabalhar com o seu ambiente e os objetivos de segurança.

![Página de um alerta ativar e configurar as definições de configuração](media/pim-resource-roles-configure-alerts/rbac-alert-settings.png)

## <a name="next-steps"></a>Passos Seguintes

- [Configurar as definições de função de recursos do Azure no PIM](pim-resource-roles-configure-role-settings.md)
