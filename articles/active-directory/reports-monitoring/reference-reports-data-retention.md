---
title: Políticas de retenção de relatórios do Azure Active Directory | Documentos da Microsoft
description: Políticas de retenção de dados de relatórios no Azure Active Directory
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
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 126eee1664d810850f63e702abe78b241649d665
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58438823"
---
# <a name="azure-active-directory-report-retention-policies"></a>Políticas de retenção de relatórios do Azure Active Directory

Neste artigo, ficará a conhecer as políticas de retenção de dados para os relatórios de atividade diferente no Azure Active Directory. 

### <a name="when-does-azure-ad-start-collecting-data"></a>Quando é que o Azure AD iniciar a recolha de dados?

| Azure AD Edition | Início da coleção |
| :--              | :--   |
| Azure AD Premium P1 <br /> Azure AD Premium P2 | Quando se inscreve para uma subscrição |
| Azure AD Gratuito <br /> Azure AD Básico | A primeira vez que abre o [painel Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) ou utilizar o [APIs de relatórios](https://aka.ms/aadreports)  |

---

### <a name="when-is-the-activity-data-available-in-the-azure-portal"></a>Quando os dados de atividade está disponível no portal do Azure?

- **Imediatamente** – se já estiver trabalhando com relatórios no portal do Azure.
- **Dentro de 2 horas** - se de que ainda não ativou a criação de relatórios no portal do Azure.

---

### <a name="how-soon-can-i-see-activities-data-after-getting-a-premium-license"></a>Assim como pode ver dados de atividades depois de obter uma licença premium?

Se já tiver dados de atividades com sua licença gratuita, em seguida, pode vê-lo imediatamente a actualização. Se não tiver quaisquer dados, em seguida, irá demorar um ou dois dias para os dados sejam apresentados nos relatórios depois de atualizar para uma licença premium.

---

### <a name="can-i-see-last-months-data-after-getting-an-azure-ad-premium-license"></a>Pode ver dados do mês anterior depois de obter uma licença do Azure AD premium?

Se recentemente a mudar para uma versão premium (incluindo uma versão de avaliação), pode ver dados de cópia de segurança a 7 dias inicialmente. Quando os dados acumulam-se, pode ver os dados nos últimos 30 dias.

---

### <a name="when-does-azure-ad-start-collecting-security-signal-data"></a>Quando é que o Azure AD comece a recolher dados de sinal de segurança?  

Para sinais de segurança, o processo de coleta é iniciado quando participar para utilizar o **Centro de proteção de identidade**. 

---

### <a name="how-long-does-azure-ad-store-the-data"></a>O período de tempo é que o Azure AD armazena os dados?

**Relatórios de atividade**    

| Relatório                 | Azure AD Gratuito | Azure AD Básico | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--            | :--                 | :--                 |
| Auditoria de Diretórios        | 7 dias        |  7 dias        | 30 dias             | 30 dias             |
| Atividade de Início de Sessão       | N/A           |  N/A           | 30 dias             | 30 dias             |
| Utilização MFA do Azure        | 30 dias       |  30 dias       | 30 dias             | 30 dias             |

Pode manter os dados de atividade de auditoria e início de sessão por mais tempo do que o período de retenção predefinido descrito acima, o encaminhamento para uma conta de armazenamento do Azure com o Azure Monitor. Para obter mais informações, consulte [registos de arquivo do Azure AD para uma conta de armazenamento do Azure](quickstart-azure-monitor-route-logs-to-storage-account.md).

**Sinais de segurança**

| Relatório         | Azure AD Gratuito | Azure AD Básico | Azure AD Premium P1 | Azure AD Premium P2 |
| :--            | :--           | :--            | :--                 | :--                 |
| Utilizadores em risco  | 7 dias        | 7 dias         | 30 dias             | 90 dias             |
| Inícios de sessão de risco | 7 dias        | 7 dias         |  30 dias            | 90 dias             |

---
