---
title: Diretório Ativo Azure reportando latências | Microsoft Docs
description: Saiba mais sobre o tempo que leva para reportar eventos para aparecer no seu portal Azure
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
ms.openlocfilehash: 0498ee1c57cfa661884fe3209d4e089b54996fae
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89231066"
---
# <a name="azure-active-directory-reporting-latencies"></a>Latências dos relatórios do Azure Active Directory

A latência é o tempo que o Azure Ative Directory (Azure AD) reporta dados para aparecer no [portal Azure](https://portal.azure.com). Este artigo enumera a latência esperada para os diferentes tipos de relatórios. 

## <a name="activity-reports"></a>Relatórios de atividade

Existem dois tipos de relatórios de atividade:

- [Iniciar s-ins](concept-sign-ins.md) – Fornece informações sobre o uso de aplicações geridas e atividades de inscrição do utilizador
- [Registos de auditoria](concept-audit-logs.md) - Fornece informações sobre atividade do sistema sobre utilizadores e grupos, aplicações geridas e atividades de diretório

A tabela que se segue lista as informações de latência dos relatórios de atividade. 

> [!NOTE]
> **A latência (percentil 95)** refere-se ao momento em que 95% dos registos serão reportados, e **a Latência (percentil 99)** refere-se ao momento em que 99% dos registos serão reportados. 
>

| Relatório | Latência (percentil 95) |Latência (percentil 99)|
| :-- | --- | --- |
| Registos de auditoria | 2 mins  | 5 mins  |
| Inícios de sessão | 2 mins  | 5 mins |

### <a name="how-soon-can-i-see-activities-data-after-getting-a-premium-license"></a>Quando posso ver os dados das atividades depois de obter uma licença premium?

Se já tem dados de atividades com a sua licença gratuita, então pode vê-lo imediatamente na atualização. Se não tiver quaisquer dados, levará um ou dois dias para que os dados apareçam nos relatórios depois de atualizar para uma licença premium.

## <a name="security-reports"></a>Relatórios de segurança

Existem dois tipos de relatórios de segurança:

- [Entradas arriscadas](../identity-protection/overview-identity-protection.md) - Um sinal de risco é um indicador para uma tentativa de inscrição que pode ter sido realizada por alguém que não é o legítimo proprietário de uma conta de utilizador. 
- [Utilizadores sinalizados para o risco](../identity-protection/overview-identity-protection.md) - Um utilizador de risco é um indicador para uma conta de utilizador que pode ter sido comprometida. 

A tabela que se segue lista as informações de latência para relatórios de segurança.

| Relatório | Mínimo | Média | Máximo |
| :-- | --- | --- | --- |
| Utilizadores em risco          | 5 minutos   | 15 minutos  | Duas horas  |
| Inícios de sessão de risco         | 5 minutos   | 15 minutos  | Duas horas  |

## <a name="risk-detections"></a>Deteções de riscos

O Azure AD utiliza algoritmos de aprendizagem automática adaptativa e heurística para detetar ações suspeitas relacionadas com as suas contas de utilizador. Cada ação suspeita detetada é armazenada num registo chamado deteção de **risco.**

A tabela que se segue lista as informações de latência para deteções de riscos.

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
* [Acesso programático aos relatórios AD do Azure](concept-reporting-api.md)
* [Deteções de risco do Azure Ative Directory](../identity-protection/overview-identity-protection.md)