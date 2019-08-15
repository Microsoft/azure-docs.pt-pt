---
title: Azure Active Directoryndo latências de relatório | Microsoft Docs
description: Saiba mais sobre a quantidade de tempo que leva para relatar eventos a serem exibidos em seu portal do Azure
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: 9b88958d-94a2-4f4b-a18c-616f0617a24e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 05/13/2019
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f57f09f146e542768c83fa034f0b4e65bc6b2ae
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68987916"
---
# <a name="azure-active-directory-reporting-latencies"></a>Latências de Azure Active Directory relatórios

Latência é a quantidade de tempo que leva para os dados de relatório do Azure Active Directory (AD do Azure) aparecerem no [portal do Azure](https://portal.azure.com). Este artigo lista a latência esperada para os diferentes tipos de relatórios. 

## <a name="activity-reports"></a>Relatórios de atividade

Há dois tipos de relatórios de atividade:

- [Entradas](concept-sign-ins.md) – fornece informações sobre o uso de aplicativos gerenciados e atividades de entrada do usuário
- [Logs de auditoria](concept-audit-logs.md) – fornece informações de atividade do sistema sobre usuários e grupos, aplicativos gerenciados e atividades de diretório

A tabela a seguir lista as informações de latência para relatórios de atividade. 

> [!NOTE]
> **Latência (95 º percentil)** refere-se ao tempo pelo qual 95% dos logs serão relatados e **latência (99 º percentil)** refere-se ao tempo pelo qual 99% dos logs serão relatados. 
>

| Relatório | Latência (95 º percentil) |Latência (99 º percentil)|
| :-- | --- | --- |
| Registos de auditoria | 2 mins  | 5 mins  |
| Inícios de sessão | 2 mins  | 5 mins |

### <a name="how-soon-can-i-see-activities-data-after-getting-a-premium-license"></a>Quanto tempo é possível ver os dados das atividades depois de obter uma licença Premium?

Se você já tiver dados de atividades com sua licença gratuita, poderá vê-los imediatamente na atualização. Se você não tiver dados, levará um ou dois dias para que os dados sejam exibidos nos relatórios após a atualização para uma licença Premium.

## <a name="security-reports"></a>Relatórios de segurança

Há dois tipos de relatórios de segurança:

- [Inícios de sessão de risco](concept-risky-sign-ins.md) – Um início de sessão de risco é um indicador de uma tentativa de início de sessão que pode ter sido efetuada por alguém que não é o proprietário legítimo de uma conta de utilizador. 
- [Utilizadores sinalizados para risco](concept-user-at-risk.md) – Um utilizador de risco é um indicador de uma conta de utilizador que pode ter sido comprometida. 

A tabela a seguir lista as informações de latência para relatórios de segurança.

| Relatório | Mínimo | Average | Máximo |
| :-- | --- | --- | --- |
| Utilizadores em risco          | 5 minutos   | 15 minutos  | Duas horas  |
| Inícios de sessão de risco         | 5 minutos   | 15 minutos  | Duas horas  |

## <a name="risk-events"></a>Eventos de risco

O Azure AD usa algoritmos de aprendizado de máquina adaptáveis e heurística para detectar ações suspeitas relacionadas às suas contas de usuário. Cada ação suspeita detectada é armazenada em um registro chamado **evento de risco**.

A tabela a seguir lista as informações de latência para eventos de risco.

| Relatório | Mínimo | Average | Máximo |
| :-- | --- | --- | --- |
| Inícios de sessão de endereços IP anónimos |5 minutos |15 Minutos |Duas horas |
| Inícios de sessão de localizações desconhecidas |5 minutos |15 Minutos |Duas horas |
| Utilizadores com fuga de credenciais |Duas horas |4 horas |8 horas |
| Deslocação impossível para localizações atípicas |5 minutos |1 hora |8 horas  |
| Inícios de sessão de dispositivos infetados |Duas horas |4 horas |8 horas  |
| Inícios de sessão de endereços IP com atividade suspeita |Duas horas |4 horas |8 horas  |


## <a name="next-steps"></a>Passos seguintes

* [Visão geral dos relatórios do Azure AD](overview-reports.md)
* [Acesso programático aos relatórios do Azure AD](concept-reporting-api.md)
* [Eventos de risco do Azure Active Directory](concept-risk-events.md)
