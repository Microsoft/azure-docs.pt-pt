---
title: Alteração da notificação de Deteção Inteligente - Insights de Aplicação Azure
description: Alterar para os destinatários de notificação padrão da Deteção Inteligente. A Deteção Inteligente permite-lhe monitorizar os rastreios de aplicações com insights de aplicação Azure para padrões incomuns na telemetria de vestígios.
ms.topic: conceptual
author: harelbr
ms.author: harelbr
ms.date: 03/13/2019
ms.reviewer: mbullwin
ms.openlocfilehash: f73e5bbdd8585b3367e529a8fa00630042e56cac
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671788"
---
# <a name="smart-detection-e-mail-notification-change"></a>Alteração da notificação de e-mail de Deteção Inteligente

Com base no feedback do cliente, no dia 1 de abril de 2019, estamos a alterar as funções padrão que recebem notificações de e-mail da Smart Detection.

## <a name="what-is-changing"></a>O que está a mudar?

Atualmente, as notificações de e-mail de Deteção Inteligente são enviadas por padrão para o Proprietário de _Subscrição,_ _Colaborador de Subscrição_e Funções de _Leitor de Subscrição._ Estas funções incluem frequentemente utilizadores que não estão ativamente envolvidos na monitorização, o que faz com que muitos destes utilizadores recebam notificações desnecessariamente. Para melhorar esta experiência, estamos a fazer uma alteração para que as notificações por e-mail só vão para as funções de [Monitoring Reader](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) e [Monitorcontributor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) por padrão.

## <a name="scope-of-this-change"></a>Âmbito desta alteração

Esta alteração afetará todas as regras de Deteção Inteligente, excluindo as seguintes:

* Regras de Deteção Inteligente marcadas como pré-visualização. Estas regras de Deteção Inteligente não suportam notificações de e-mail hoje.

* Anomalias falhadas regra. Esta regra começará a direcionar as novas funções padrão uma vez que seja migrada de um alerta clássico para a plataforma de alertaunificada (mais informações estão disponíveis [aqui](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement).)

## <a name="how-to-prepare-for-this-change"></a>Como se preparar para esta mudança?

Para garantir que as notificações de e-mail da Smart Detection são enviadas para utilizadores relevantes, esses utilizadores devem ser atribuídos às funções de [Monitoring Reader](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) ou [Monitoring Contributor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) da subscrição.

Para atribuir aos utilizadores as funções de Monitoring Reader ou Monitoring Contributor através do portal Azure, siga os passos descritos no artigo de [atribuição de funções Adicionar.](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment) Certifique-se de selecionar o _Leitor de Monitorização_ ou _Colaborador de Monitorização_ como a função a que os utilizadores são atribuídos.

> [!NOTE]
> Os destinatários específicos das notificações de Deteção Inteligente, configurados utilizando a opção adicional de _destinatários_ de email nas definições da regra, não serão afetados por esta alteração. Estes destinatários continuarão a receber as notificações por e-mail.

Se tiver alguma dúvida ou preocupação sobre esta mudança, não hesite em [contactar-nos.](mailto:smart-alert-feedback@microsoft.com)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre deteção inteligente:

- [Anomalias de falha](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [Fugas de memória](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [Anomalias de desempenho](../../azure-monitor/app/proactive-performance-diagnostics.md)
