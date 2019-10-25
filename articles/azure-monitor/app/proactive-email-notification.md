---
title: Detecção inteligente do Aplicativo Azure insights – alterações futuras nos destinatários de notificação padrão | Microsoft Docs
description: Monitore rastreamentos de aplicativos com Aplicativo Azure insights para padrões incomuns na telemetria de rastreamento.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: harelbr
ms.author: harelbr
ms.date: 03/13/2019
ms.reviewer: mbullwin
ms.openlocfilehash: fa1f98b5a9ee592a4c702e87e365eff7941194d4
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72820620"
---
# <a name="smart-detection-e-mail-notification-change"></a>Alteração de notificação por email de detecção inteligente

Com base nos comentários dos clientes, em 1º de abril de 2019, estamos alterando as funções padrão que recebem notificações por email da detecção inteligente.

## <a name="what-is-changing"></a>O que está mudando?

Atualmente, as notificações de email de detecção inteligente são enviadas por padrão para as funções _proprietário da assinatura_, _colaborador_da assinatura e _leitor de assinatura_ . Muitas vezes, estas funções incluem os utilizadores que não estão envolvidos ativamente na monitorização, o que faz com que muitos destes utilizadores recebam notificações desnecessariamente. Para melhorar essa experiência, estamos fazendo uma alteração para que as notificações de email apenas vá para as funções de [colaborador](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) [de monitoramento e](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) monitoramento por padrão.

## <a name="scope-of-this-change"></a>Escopo desta alteração

Esta alteração irá afetar todas as regras de Deteção Inteligente, com exceção das seguintes:

* Regras de Deteção Inteligente marcadas como pré-visualização. Essas regras de detecção inteligente não dão suporte a notificações por email hoje.

* Regra de Anomalias de Falha. Essa regra começará a direcionar as novas funções padrão depois que ela for migrada de um alerta clássico para a plataforma de alertas unificados (mais informações estão disponíveis [aqui](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement)).

## <a name="how-to-prepare-for-this-change"></a>Como se preparar para essa alteração?

Para garantir que as notificações por email da detecção inteligente sejam enviadas aos usuários relevantes, esses usuários devem ser atribuídos ao [leitor de monitoramento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) ou às funções de colaborador de [monitoramento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) da assinatura.

Para atribuir usuários ao leitor de monitoramento ou às funções de colaborador de monitoramento por meio do portal do Azure, siga as etapas descritas no artigo [Adicionar uma atribuição de função](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment) . Certifique-se de selecionar o _leitor de monitoramento_ ou o colaborador de _monitoramento_ como a função à qual os usuários são atribuídos.

> [!NOTE]
> Os destinatários específicos de notificações de detecção inteligente, configurados usando a opção de _destinatários de email adicionais_ nas configurações de regra, não serão afetados por essa alteração. Esses destinatários continuarão recebendo as notificações por email.

Se você tiver dúvidas ou preocupações sobre essa alteração, não hesite em [entrar em contato conosco](mailto:smart-alert-feedback@microsoft.com).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a detecção inteligente:

- [Anomalias de falha](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [Vazamentos de memória](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [Anomalias de desempenho](../../azure-monitor/app/proactive-performance-diagnostics.md)