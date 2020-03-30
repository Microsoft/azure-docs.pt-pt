---
title: Diretório Azure Ative reportando tardios Microsoft Docs
description: Saiba o tempo que leva para reportar eventos para aparecer no seu portal Azure
services: active-directory
documentationcenter: ''
author: MarkusVi
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
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d031546bb4f1f05e9ea2abb5b74fe911b0b507f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74007709"
---
# <a name="azure-active-directory-reporting-latencies"></a>Latências dos relatórios do Azure Active Directory

A latência é o tempo que leva para o Azure Ative Directory (Azure AD) reportar dados para aparecer no [portal Azure](https://portal.azure.com). Este artigo enumera a latência esperada para os diferentes tipos de relatórios. 

## <a name="activity-reports"></a>Relatórios de atividade

Existem dois tipos de relatórios de atividade:

- [Iniciars inscrições](concept-sign-ins.md) – Fornece informações sobre a utilização de aplicações geridas e atividades de inscrição no utilizador
- [Registos](concept-audit-logs.md) de auditoria - Fornece informações sobre utilizadores e grupos, aplicações geridas e atividades de diretório

A tabela seguinte enumera as informações de latência para relatórios de atividade. 

> [!NOTE]
> **A latência (percentil 95)** refere-se ao tempo em que 95% dos registos serão reportados, e **A Latência (percentil 99)** refere-se ao tempo em que 99% dos registos serão reportados. 
>

| Relatório | Latência (percentil 95) |Latência (percentil 99)|
| :-- | --- | --- |
| Registos de auditoria | 2 mins  | 5 mins  |
| Inícios de sessão | 2 mins  | 5 mins |

### <a name="how-soon-can-i-see-activities-data-after-getting-a-premium-license"></a>Quando posso ver os dados das atividades depois de obter uma licença premium?

Se já tem dados de atividades com a sua licença gratuita, então pode vê-lo imediatamente em upgrade. Se não tiver dados, levará um ou dois dias para os dados aparecerem nos relatórios depois de atualizar para uma licença premium.

## <a name="security-reports"></a>Relatórios de segurança

Existem dois tipos de relatórios de segurança:

- [Inícios de sessão de risco](concept-risky-sign-ins.md) – Um início de sessão de risco é um indicador de uma tentativa de início de sessão que pode ter sido efetuada por alguém que não é o proprietário legítimo de uma conta de utilizador. 
- [Utilizadores sinalizados para risco](concept-user-at-risk.md) – Um utilizador de risco é um indicador de uma conta de utilizador que pode ter sido comprometida. 

A tabela que se segue lista as informações de latência para relatórios de segurança.

| Relatório | Mínimo | Média | Máximo |
| :-- | --- | --- | --- |
| Utilizadores em risco          | 5 minutos   | 15 minutos  | Duas horas  |
| Inícios de sessão de risco         | 5 minutos   | 15 minutos  | Duas horas  |

## <a name="risk-detections"></a>Deteções de riscos

O Azure AD utiliza algoritmos adaptáveis de aprendizagem automática e heurística para detetar ações suspeitas relacionadas com as suas contas de utilizador. Cada ação suspeita detetada é armazenada num registo chamado **deteção de risco.**

A tabela que se segue enumera as informações de latência para deteções de risco.

| Relatório | Mínimo | Média | Máximo |
| :-- | --- | --- | --- |
| Inícios de sessão de endereços IP anónimos |5 minutos |15 Minutos |Duas horas |
| Inícios de sessão de localizações desconhecidas |5 minutos |15 Minutos |Duas horas |
| Utilizadores com fuga de credenciais |Duas horas |4 horas |8 horas |
| Deslocação impossível para localizações atípicas |5 minutos |Uma hora |8 horas  |
| Inícios de sessão de dispositivos infetados |Duas horas |4 horas |8 horas  |
| Inícios de sessão de endereços IP com atividade suspeita |Duas horas |4 horas |8 horas  |


## <a name="next-steps"></a>Passos seguintes

* [Azure AD reporta visão geral](overview-reports.md)
* [Acesso programático aos relatórios da AD Azure](concept-reporting-api.md)
* [Deteção de risco de Diretório Ativo Azure](concept-risk-events.md)
