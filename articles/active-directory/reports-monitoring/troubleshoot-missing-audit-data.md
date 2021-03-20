---
title: Resolução de problemas Dados em falta nos registos de atividades | Microsoft Docs
description: Apresenta uma resolução para os dados em falta nos registos de atividades do Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 7cbe4337-bb77-4ee0-b254-3e368be06db7
ms.service: active-directory
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f48c26a65314e2f23513fba155f07db3805a516
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93123069"
---
# <a name="troubleshoot-missing-data-in-the-azure-active-directory-activity-logs"></a>Resolução de problemas: Dados em falta nos registos de atividades do Azure Active Directory 

## <a name="i-cant-find-audit-logs-for-recent-actions-in-the-azure-portal"></a>Não consigo encontrar registos de auditoria para ações recentes no portal do Azure

### <a name="symptoms"></a>Sintomas

Efetuei algumas ações no portal do Azure e esperava ver os registos de auditoria dessas ações no painel `Activity logs > Audit Logs`, mas não consegui encontrá-los.

 ![A screenshot mostra entradas de Registo de Auditoria.](./media/troubleshoot-missing-audit-data/01.png)
 
### <a name="cause"></a>Causa

As ações não aparecem de imediato nos registos de atividade. A tabela abaixo enumera os nossos números de latência para registos de atividades. 

| Relatório | Latência (P95) | Latência (P99) |
|--------|---------------|---------------|
| Auditoria de diretórios | 2 mins | 5 mins |
| Atividade de início de sessão | 2 mins | 5 mins |

### <a name="resolution"></a>Resolução

Aguarde entre 15 minutos a duas horas e veja se as ações aparecem no registo. Se não vir os registos após duas horas, [envie um pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) e nós analisamos o problema.

## <a name="i-cant-find-recent-user-sign-ins-in-the-azure-active-directory-sign-ins-activity-log"></a>Não consigo encontrar inícios de sessão de utilizador recentes no registo de atividades dos inícios de sessão do Azure Active Directory

### <a name="symptoms"></a>Sintomas

Iniciei sessão recentemente no portal do Azure e esperava ver os registos de início de sessão dessas ações no painel `Activity logs > Sign-ins`, mas não consigo encontrá-los.

 ![A screenshot mostra logins no registo de atividade.](./media/troubleshoot-missing-audit-data/02.png)
 
### <a name="cause"></a>Causa

As ações não aparecem de imediato nos registos de atividade. A tabela abaixo enumera os nossos números de latência para registos de atividades. 

| Relatório | Latência (P95) | Latência (P99) |
|--------|---------------|---------------|
| Auditoria de diretórios | 2 mins | 5 mins |
| Atividade de inscrição 2 minutos | 5 mins |

### <a name="resolution"></a>Resolução

Aguarde entre 15 minutos a duas horas e veja se as ações aparecem no registo. Se não vir os registos após duas horas, [envie um pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) e nós analisamos o problema.

## <a name="i-cant-view-more-than-30-days-of-report-data-in-the-azure-portal"></a>Não consigo ver mais de 30 dias de dados de relatório no portal do Azure

### <a name="symptoms"></a>Sintomas

Não consigo ver mais de 30 dias de dados de início de sessão e auditoria no portal do Azure. Porquê? 

 ![A screenshot mostra o menu Date.](./media/troubleshoot-missing-audit-data/03.png)

### <a name="cause"></a>Causa

Dependendo da sua licença, as Ações do Azure Active Directory armazena relatórios de atividades para as durações seguintes:

| Relatório           | Azure AD Gratuito | Azure AD Premium P1 | Azure AD Premium P2 |
| ---              | ---           | ---                 | ---                 |
| Auditoria de Diretórios  |  7 dias       | 30 dias             | 30 dias             |
| Atividade de Início de Sessão | Não disponível. Pode aceder aos seus próprios inícios de sessão por 7 dias a partir do painel de perfil de utilizador individual | 30 dias | 30 dias             |

Para obter mais informações, veja [Políticas de retenção de relatórios do Azure Active Directory](reference-reports-data-retention.md).  

### <a name="resolution"></a>Resolução

Tem duas opções para manter os dados durante mais de 30 dias. Pode utilizar as [APIs de Relatórios do Azure AD](concept-reporting-api.md) para recuperar os dados por meio programático e armazená-los numa base de dados. Em alternativa, pode integrar registos de auditoria num sistema SIEM de terceiros, como o Splunk ou o SumoLogic.

## <a name="next-steps"></a>Passos seguintes

* [Retenção de relatórios do Azure AD](reference-reports-data-retention.md).
* [Azure Ative Directory reportando latências](reference-reports-latencies.md).
* [Azure Ative Directory reportando FAQ](reports-faq.md).

