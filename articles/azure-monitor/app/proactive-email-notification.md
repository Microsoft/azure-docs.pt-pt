---
title: O Azure Application Insights deteção inteligente – futuras alterações aos destinatários de notificação predefinido | Documentos da Microsoft
description: Monitorizar rastreios de aplicações com o Azure Application Insights para padrões invulgares na telemetria de rastreio.
services: application-insights
author: harelbr
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 03/13/2019
ms.author: harelbr
ms.openlocfilehash: f984a34be1c5d5fdd18a00812107318df8f5d9bf
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57889289"
---
# <a name="smart-detection-e-mail-notification-change"></a>Alterar a notificação de email de deteção inteligente

Com base nos comentários dos clientes, no dia 1 de Abril de 2019, estamos a alterar as funções predefinidas que recebem notificações por e-mail a partir de deteção inteligente.

## <a name="what-is-changing"></a>O que está a mudar?

Atualmente, são enviadas notificações de e-mail de deteção inteligente por predefinição, para o _proprietário da subscrição_, _contribuinte da subscrição_, e _leitor de subscrição_ funções. Muitas vezes, estas funções incluem os utilizadores que não estão envolvidos ativamente na monitorização, o que faz com que muitos destes utilizadores recebam notificações desnecessariamente. Para melhorar esta experiência, estamos a efetuar uma alteração para que as notificações por correio eletrónico só vão para o [monitorização leitor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) e [Contribuidor de monitorização](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) funções por predefinição.

## <a name="scope-of-this-change"></a>Âmbito desta alteração

Esta alteração irá afetar todas as regras de Deteção Inteligente, com exceção das seguintes:

* Regras de Deteção Inteligente marcadas como pré-visualização. Estas regras de deteção inteligente não é suportada notificações por e-mail hoje mesmo.

* Regra de Anomalias de Falha. Esta regra irá iniciar o direcionamento as novas funções de predefinição depois ele migrou de um alerta de clássico para a plataforma de alertas unificada (estão disponíveis mais informações [aqui](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement).)

## <a name="how-to-prepare-for-this-change"></a>Como se preparar para esta alteração?

Para garantir que sejam enviadas notificações de e-mail de deteção inteligente para utilizadores relevantes, esses utilizadores tem de ser atribuídos para o [leitor de monitorização](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) ou [Contribuidor de monitorização](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) funções da subscrição.

Para atribuir utilizadores às funções de leitor de monitorização ou Contribuidor de monitorização através do portal do Azure, siga os passos descritos no [adicionar uma atribuição de função](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment) artigo. Certifique-se de selecionar o _monitorização leitor_ ou _Contribuidor de monitorização_ que a função ao qual os utilizadores são atribuídos.

> [!NOTE]
> Destinatários específicos de notificações de deteção inteligente, configuradas utilizando o _destinatários de e-mail adicionais_ opção nas definições da regra, não será afetada por esta alteração. Esses destinatários irão continuar a receber as notificações por e-mail.

Se tiver dúvidas ou preocupações sobre esta alteração, não hesite em [contacte-nos](mailto:smart-alert-feedback@microsoft.com).

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre a deteção inteligente:

- [Anomalias de falha](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [Vazamentos de memória](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [Anomalias de desempenho](../../azure-monitor/app/proactive-performance-diagnostics.md)