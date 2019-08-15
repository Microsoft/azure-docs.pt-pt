---
title: Por quanto tempo o Azure AD armazena dados de relatório? | Microsoft Docs
description: Saiba por quanto tempo o Azure armazena os vários tipos de dados de relatório.
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: ''
ms.topic: reference
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: c52f8873527d92e621ef032f5bc3e82d3364a691
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989586"
---
# <a name="how-long-does-azure-ad-store-reporting-data"></a>Por quanto tempo o Azure AD armazena dados de relatório?

Neste artigo, você aprende sobre as políticas de retenção de dados para os diferentes relatórios de atividade no Azure Active Directory. 

### <a name="when-does-azure-ad-start-collecting-data"></a>Quando o Azure AD começa a coletar dados?

| Edição do Azure AD | Início da coleta |
| :--              | :--   |
| Azure AD Premium P1 <br /> Azure AD Premium P2 | Quando você se inscreve para uma assinatura |
| Azure AD Gratuito <br /> Azure AD Básico | Na primeira vez que você abrir a [folha Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) ou usar as [APIs de relatório](https://aka.ms/aadreports)  |

---

### <a name="when-is-the-activity-data-available-in-the-azure-portal"></a>Quando os dados de atividade estão disponíveis no portal do Azure?

- **Imediatamente** -se você já tiver trabalhado com relatórios no portal do Azure.
- **Dentro de 2 horas** -se você ainda não ativou o relatório no portal do Azure.

---

### <a name="how-soon-can-i-see-activities-data-after-getting-a-premium-license"></a>Quanto tempo é possível ver os dados das atividades depois de obter uma licença Premium?

Se você já tiver dados de atividades com sua licença gratuita, poderá vê-los imediatamente na atualização. Se você não tiver dados, levará um ou dois dias para que os dados sejam exibidos nos relatórios após a atualização para uma licença Premium.

---

### <a name="can-i-see-last-months-data-after-getting-an-azure-ad-premium-license"></a>Posso ver os dados do mês passado depois de obter uma licença do Azure AD Premium?

Se você alternou recentemente para uma versão Premium (incluindo uma versão de avaliação), você pode ver dados de até 7 dias inicialmente. Quando os dados são acumulados, você pode ver os dados dos últimos 30 dias.

---

### <a name="when-does-azure-ad-start-collecting-security-signal-data"></a>Quando o Azure AD começa a coletar dados de sinal de segurança?  

Para sinais de segurança, o processo de coleta é iniciado quando você opta por usar o **centro de proteção de identidade**. 

---

### <a name="how-long-does-azure-ad-store-the-data"></a>Por quanto tempo o Azure AD armazena os dados?

**Relatórios de atividade**    

| Relatório                 | Azure AD Gratuito | Azure AD Básico | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--            | :--                 | :--                 |
| Registos de auditoria             | 7 dias        |  7 dias        | 30 dias             | 30 dias             |
| Inícios de sessão               | N/A           |  N/A           | 30 dias             | 30 dias             |
| Uso do Azure MFA        | 30 dias       |  30 dias       | 30 dias             | 30 dias             |

Você pode manter os dados de atividade de entrada e de auditoria por mais tempo do que o período de retenção padrão descrito acima ao roteá-lo para uma conta de armazenamento do Azure usando Azure Monitor. Para obter mais informações, consulte [arquivar logs do Azure AD em uma conta de armazenamento do Azure](quickstart-azure-monitor-route-logs-to-storage-account.md).

**Sinais de segurança**

| Relatório         | Azure AD Gratuito | Azure AD Básico | Azure AD Premium P1 | Azure AD Premium P2 |
| :--            | :--           | :--            | :--                 | :--                 |
| Utilizadores em risco  | 7 dias        | 7 dias         | 30 dias             | 90 dias             |
| Inícios de sessão de risco | 7 dias        | 7 dias         |  30 dias            | 90 dias             |

---
