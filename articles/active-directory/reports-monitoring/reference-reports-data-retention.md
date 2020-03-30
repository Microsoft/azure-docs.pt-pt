---
title: Quanto tempo a Azure AD armazena dados de reporte? | Microsoft Docs
description: Saiba quanto tempo o Azure armazena os vários tipos de dados de reporte.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: ''
ms.topic: reference
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/24/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54636600c208f8f5df9fa2e25460c63dd9f46e85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239552"
---
# <a name="how-long-does-azure-ad-store-reporting-data"></a>Quanto tempo a Azure AD armazena dados de reporte?


Neste artigo, você aprende sobre as políticas de retenção de dados para os diferentes relatórios de atividade no Azure Ative Diretório. 

### <a name="when-does-azure-ad-start-collecting-data"></a>Quando é que o Azure AD começa a recolher dados?

| Edição AD Azure | Início da Coleção |
| :--              | :--   |
| Azure AD Premium P1 <br /> Azure AD Premium P2 | Quando se inscreve para uma subscrição |
| Azure AD Gratuito| A primeira vez que abrir a lâmina de [Diretório Ativo Azure](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) ou utilizar as [APIs de reporte](https://aka.ms/aadreports)  |

---

### <a name="when-is-the-activity-data-available-in-the-azure-portal"></a>Quando estão disponíveis os dados de atividade no portal Azure?

- **Imediatamente** - Se já trabalhou com relatórios no portal Azure.
- **Dentro** de 2 horas - Se não ligou a reportagem no portal Azure.

---

### <a name="how-soon-can-i-see-activities-data-after-getting-a-premium-license"></a>Quando posso ver os dados das atividades depois de obter uma licença premium?

Se já tem dados de atividades com a sua licença gratuita, então pode vê-lo imediatamente em upgrade. Se não tiver dados, levará um ou dois dias para os dados aparecerem nos relatórios depois de atualizar para uma licença premium.

---

### <a name="can-i-see-last-months-data-after-getting-an-azure-ad-premium-license"></a>Posso ver os dados do mês passado depois de obter uma licença de prémio Azure AD?

Se mudou recentemente para uma versão premium (incluindo uma versão experimental), pode ver dados até 7 dias inicialmente. Quando os dados se acumulam, pode ver dados nos últimos 30 dias.

---

### <a name="when-does-azure-ad-start-collecting-security-signal-data"></a>Quando é que o Azure AD começa a recolher dados de sinal de segurança?  

Para sinais de segurança, o processo de recolha começa quando opta por utilizar o Centro de **Proteção de Identidade**. 

---

### <a name="how-long-does-azure-ad-store-the-data"></a>Quanto tempo a Azure AD armazena os dados?

**Relatórios de atividade**    

| Relatório                 | Azure AD Gratuito | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| Registos de auditoria             | 7 dias        | 30 dias             | 30 dias             |
| Inícios de sessão               | 7 dias        | 30 dias             | 30 dias             |
| Utilização de MFA Azure        | 30 dias       | 30 dias             | 30 dias             |

Pode reter os dados de auditoria e de atividade de entrada por mais tempo do que o período de retenção predefinido acima descrito, encaminhando-os para uma conta de armazenamento Azure utilizando o Monitor Azure. Para mais informações, consulte os [registos da AD Archive Azure numa conta](quickstart-azure-monitor-route-logs-to-storage-account.md)de armazenamento Azure .

**Sinais de segurança**

| Relatório         | Azure AD Gratuito | Azure AD Premium P1 | Azure AD Premium P2 |
| :--            | :--           | :--                 | :--                 |
| Utilizadores em risco  | 7 dias        | 30 dias             | 90 dias             |
| Inícios de sessão de risco | 7 dias        | 30 dias             | 90 dias             |

---
