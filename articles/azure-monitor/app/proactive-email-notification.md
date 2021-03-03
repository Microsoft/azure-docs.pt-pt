---
title: Alteração da notificação de deteção inteligente - Azure Application Insights
description: Altere para os destinatários de notificação predefinidos da Smart Detection. A Smart Detection permite monitorizar os vestígios de aplicações com Azure Application Insights para padrões incomuns em telemetria de traços.
ms.topic: conceptual
author: harelbr
ms.author: harelbr
ms.date: 02/14/2021
ms.reviewer: mbullwin
ms.openlocfilehash: fa14f0dd40a30a4750d9bb102d8e67608f958135
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101734507"
---
# <a name="smart-detection-e-mail-notification-change"></a>Alteração da notificação de e-mail de deteção inteligente

Com base no feedback do cliente, no dia 1 de abril de 2019, estamos a alterar as funções padrão que recebem notificações de e-mail da Smart Detection.

## <a name="what-is-changing"></a>O que está a mudar?

Atualmente, as notificações de email de Deteção Inteligente são enviadas por defeito para as funções _de Proprietário de Assinatura,_ _Colaborador de Assinatura_ e Leitor _de Assinaturas._ Estas funções incluem frequentemente utilizadores que não estão ativamente envolvidos na monitorização, o que faz com que muitos destes utilizadores recebam notificações desnecessariamente. Para melhorar esta experiência, estamos a fazer uma alteração para que as notificações por e-mail apenas vão para as funções [de Monitore](../../role-based-access-control/built-in-roles.md#monitoring-reader) e [Monitoring Contributor](../../role-based-access-control/built-in-roles.md#monitoring-contributor) por padrão.

## <a name="scope-of-this-change"></a>Âmbito desta mudança

Esta alteração afetará todas as regras de Deteção Inteligente, excluindo as seguintes:

* Regras de Deteção Inteligente marcadas como pré-visualização. Estas regras de Deteção Inteligente não suportam notificações por e-mail hoje em dia.

* As anomalias de falha reinam.

## <a name="how-to-prepare-for-this-change"></a>Como se preparar para esta mudança?

Para garantir que as notificações por e-mail de Smart Detection são enviadas para utilizadores relevantes, esses utilizadores devem ser atribuídos às funções de [Monitor ou](../../role-based-access-control/built-in-roles.md#monitoring-reader) [Monitoring Contributor](../../role-based-access-control/built-in-roles.md#monitoring-contributor) da subscrição.

Para atribuir os utilizadores às funções de Monitoring Reader ou Monitoring Contributor através do portal Azure, siga os passos descritos no artigo [de funções De Atribuição Azure.](../../role-based-access-control/role-assignments-portal.md) Certifique-se de selecionar o _Leitor de Monitorização_ ou _o Colaborador de Monitorização_ como a função a que os utilizadores estão atribuídos.

> [!NOTE]
> Os destinatários específicos das notificações de Deteção Inteligente, configurados utilizando a opção adicional de destinatários de _e-mail_ nas definições de regras, não serão afetados por esta alteração. Estes destinatários continuarão a receber as notificações por e-mail.

Se tiver alguma dúvida ou preocupação com esta mudança, não hesite em [contactar-nos](mailto:smart-alert-feedback@microsoft.com).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre Deteção Inteligente:

- [Anomalias de falhas](./proactive-failure-diagnostics.md)
- [Fugas de memória](./proactive-potential-memory-leak.md)
- [Anomalias de desempenho](./proactive-performance-diagnostics.md)

