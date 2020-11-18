---
title: Quanto tempo a Azure AD armazena dados de reporte? | Microsoft Docs
description: Saiba quanto tempo a Azure armazena os vários tipos de dados de reporte.
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
ms.date: 11/05/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc5f902d75084d649f211d589e53041f1eb37f0e
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94834715"
---
# <a name="how-long-does-azure-ad-store-reporting-data"></a>Quanto tempo a Azure AD armazena dados de reporte?


Neste artigo, você aprende sobre as políticas de retenção de dados para os diferentes relatórios de atividade no Azure Ative Direy. 

### <a name="when-does-azure-ad-start-collecting-data"></a>Quando é que o Azure AD começa a recolher dados?

| Edição AD Azure | Início da Coleção |
| :--              | :--   |
| Azure AD Premium P1 <br /> Azure AD Premium P2 | Quando se inscreve para uma subscrição |
| Azure AD Gratuito| A primeira vez que abre a lâmina do [Diretório Ativo Azure](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) ou utiliza as [APIs de reporte](./overview-reports.md)  |

---

### <a name="when-is-the-activity-data-available-in-the-azure-portal"></a>Quando é que os dados de atividade estão disponíveis no portal Azure?

- **Imediatamente** - Se já esteve a trabalhar com relatórios no portal Azure.
- **Dentro de 2 horas** - Se ainda não ligou a reportagem no portal Azure.

---

### <a name="how-soon-can-i-see-activities-data-after-getting-a-premium-license"></a>Quando posso ver os dados das atividades depois de obter uma licença premium?

Se já tem dados de atividades com a sua licença gratuita, então pode vê-lo imediatamente na atualização. Se não tiver quaisquer dados, levará um ou dois dias para que os dados apareçam nos relatórios depois de atualizar para uma licença premium.

---

### <a name="when-does-azure-ad-start-collecting-security-signal-data"></a>Quando é que o Azure AD começa a recolher dados de sinais de segurança?  

Para sinais de segurança, o processo de recolha começa quando opta por utilizar o **Centro de Proteção de Identidade**. 

---

### <a name="how-long-does-azure-ad-store-the-data"></a>Quanto tempo a Azure AD armazena os dados?

**Relatórios de atividade**    

| Relatório                 | Azure AD Gratuito | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| Registos de auditoria             | 7 dias        | 30 dias             | 30 dias             |
| Inícios de sessão               | 7 dias        | 30 dias             | 30 dias             |
| Utilização do Azure AD MFA        | 30 dias       | 30 dias             | 30 dias             |

Pode reter os dados de atividade de auditoria e de inscrição por mais tempo do que o período de retenção predefinido acima descrito, encaminhando-os para uma conta de armazenamento Azure utilizando o Azure Monitor. Para obter mais informações, consulte [os registos da Archive Azure AD numa conta de armazenamento Azure](quickstart-azure-monitor-route-logs-to-storage-account.md).

**Sinais de segurança**

| Relatório         | Azure AD Gratuito | Azure AD Premium P1 | Azure AD Premium P2 |
| :--            | :--           | :--                 | :--                 |
| Utilizadores em risco  | 7 dias        | 30 dias             | 90 dias             |
| Inícios de sessão de risco | 7 dias        | 30 dias             | 90 dias             |

---

### <a name="can-i-see-last-months-data-after-getting-an-azure-ad-premium-license"></a>Posso ver os dados do mês passado depois de obter uma licença premium AZure AD?

**Não,** não pode. A Azure armazena até sete dias de dados de atividade para uma versão gratuita. Isto significa que, quando se muda de uma versão gratuita para uma versão premium, só se pode ver até 7 dias de dados.

---