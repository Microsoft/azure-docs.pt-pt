---
title: Deteção no Azure Application Insights inteligente | Documentos da Microsoft
description: Application Insights faz uma análise detalhada automática da telemetria da aplicação e avisa sobre possíveis problemas.
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: 2eeb4a35-c7a1-49f7-9b68-4f4b860938b2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mbullwin
ms.openlocfilehash: edbd7000001ae6927078e2f1bb9e348cc78f9efa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61299673"
---
# <a name="smart-detection-in-application-insights"></a>Deteção inteligente no Application Insights
 Deteção inteligente automaticamente avisa-o de potenciais problemas de desempenho em seu aplicativo web. Ele faz uma análise de proativa de telemetria que a aplicação envia para o [Application Insights](../../azure-monitor/app/app-insights-overview.md). Se houver um aumento súbito do taxas de falhas ou padrões anormais no desempenho do cliente ou servidor, será criado um alerta. Esta funcionalidade não precisa nenhuma configuração. Ele funciona se a sua aplicação enviar telemetria suficiente.

Pode aceder aos alertas de deteção inteligente tanto dos e-mails recebidos e a partir do painel de deteção inteligente.

## <a name="review-your-smart-detections"></a>Reveja as deteções inteligentes
Pode descobrir deteções de duas formas:

* **Receber um e-mail** do Application Insights. Eis um exemplo típico:
  
    ![Alerta de e-mail](./media/proactive-diagnostics/03.png)
  
    Clique no botão grande para abrir mais detalhes no portal.
* **O mosaico de deteção inteligente** na descrição geral da sua aplicação o painel mostra uma contagem dos alertas recentes. Clique no mosaico para ver uma lista dos alertas recentes.

![Ver deteções de recentes](./media/proactive-diagnostics/04.png)

Selecione um alerta para ver os respetivos detalhes.

## <a name="what-problems-are-detected"></a>Que problemas são detetados?
Existem três tipos de Deteção:

* [Deteção – anomalias de falha inteligente](../../azure-monitor/app/proactive-failure-diagnostics.md). Podemos utilizar o machine learning para definir a taxa esperada de pedidos falhados para a sua aplicação, correlacionar com carga e outros fatores. Se a taxa de falhas ficar fora do envelope esperado, podemos enviar um alerta.
* [Deteção inteligente - desempenho anomalias](../../azure-monitor/app/proactive-performance-diagnostics.md). Obter notificações se o tempo de resposta de uma duração da operação ou dependência é lento em comparação com a linha de base histórica ou se podemos identificar um padrão de anómalo em tempo de resposta ou tempo de carregamento de página.   
* [Deteção - problemas de serviço Cloud do Azure inteligente](https://azure.microsoft.com/blog/proactive-notifications-on-cloud-service-issues-with-azure-diagnostics-and-application-insights/). Obtenha alertas se a aplicação estiver alojada no Azure Cloud Services e uma instância de função tem falhas no arranque, reciclagem frequente ou falhas de tempo de execução.

(As ligações de ajuda em cada notificação levá-lo para os artigos relevantes.)

## <a name="smart-detection-email-notifications"></a>Notificações de e-mail de deteção inteligentes

Todas as regras de deteção inteligente, exceto para regras marcadas como pré-visualização, são configuradas por predefinição para enviar notificações por e-mail quando são encontradas deteções.

Configurar notificações por e-mail para uma regra de deteção inteligente específica pode ser feito ao abrir a deteção inteligente **configurações** painel e selecionando a regra que irá abrir o **Editar regra** painel.

Em alternativa, pode alterar a configuração utilizando modelos Azure Resource Manager. [Ver as regras de deteção inteligente de gerir o Application Insights através de modelos do Azure Resource Manager](https://docs.microsoft.com/azure/azure-monitor/app/proactive-arm-config) para obter mais detalhes.

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Passos Seguintes
Estas ferramentas de diagnóstico ajudá-lo inspecionar a telemetria da sua aplicação:

* [Explorador de métricas](../../azure-monitor/app/metrics-explorer.md)
* [Explorador de pesquisa](../../azure-monitor/app/diagnostic-search.md)
* [Análise - poderosa linguagem de consultas](../../azure-monitor/log-query/get-started-portal.md)

Deteção inteligente é totalmente automática. Mas talvez deseje configurar alguns alertas mais?

* [Alertas de métricas configurados manualmente](../../azure-monitor/app/alerts.md)
* [Testes web de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md) 

