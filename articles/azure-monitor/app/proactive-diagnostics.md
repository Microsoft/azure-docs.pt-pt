---
title: Detecção inteligente no insights Aplicativo Azure | Microsoft Docs
description: Application Insights executa uma análise profunda automática da telemetria de seu aplicativo e avisa sobre possíveis problemas.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 02/07/2019
ms.openlocfilehash: ae9af5721e53277f6c939840721ca50ea67ca51e
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72818812"
---
# <a name="smart-detection-in-application-insights"></a>Detecção inteligente no Application Insights
 A detecção inteligente avisa automaticamente sobre possíveis problemas de desempenho e anomalias de falha em seu aplicativo Web. Ele executa uma análise proativa da telemetria que seu aplicativo envia para [Application insights](../../azure-monitor/app/app-insights-overview.md). Se houver um aumento repentino nas taxas de falha ou padrões anormais no desempenho do cliente ou do servidor, você receberá um alerta. Este recurso não precisa de configuração. Ele funcionará se seu aplicativo enviar telemetria suficiente.

Você pode acessar as detecções emitidas pela detecção inteligente a partir dos emails recebidos e da folha detecção inteligente.

## <a name="review-your-smart-detections"></a>Examine suas detecções inteligentes
Você pode descobrir detecções de duas maneiras:

* **Você receberá um email** de Application insights. Veja um exemplo típico:
  
    ![Alerta de email](./media/proactive-diagnostics/03.png)
  
    Clique no botão grande para abrir mais detalhes no Portal.
* **A folha detecção inteligente** no Application insights. Selecione **detecção inteligente** no menu **investigar** para ver uma lista de detecções recentes.

![Exibir detecções recentes](./media/proactive-diagnostics/04.png)

Selecione uma detecção para ver seus detalhes.

## <a name="what-problems-are-detected"></a>Quais problemas são detectados?
A detecção inteligente detecta e notifica sobre uma variedade de problemas, como:

* [Detecção inteligente-anomalias de falha](../../azure-monitor/app/proactive-failure-diagnostics.md). Usamos o Machine Learning para definir a taxa esperada de solicitações com falha para seu aplicativo, correlacionando-se com a carga e outros fatores. Se a taxa de falha ficar fora do envelope esperado, enviaremos um alerta.
* [Detecção inteligente-anomalias de desempenho](../../azure-monitor/app/proactive-performance-diagnostics.md). Você receberá notificações se o tempo de resposta de uma operação ou a duração da dependência estiver diminuindo em comparação com a linha de base histórica ou se identificarmos um padrão anormal no tempo de resposta ou no tempo de carregamento da página.   
* Degradações gerais e problemas, como [rastreamento percebido](https://docs.microsoft.com/azure/azure-monitor/app/proactive-trace-severity), [vazamento de memória](https://docs.microsoft.com/azure/azure-monitor/app/proactive-potential-memory-leak), [aumento anormal no volume de exceção](https://docs.microsoft.com/azure/azure-monitor/app/proactive-exception-volume) e [antipadrões de segurança](https://docs.microsoft.com/azure/azure-monitor/app/proactive-application-security-detection-pack).

(Os links da ajuda em cada notificação levam você aos artigos relevantes.)

## <a name="smart-detection-email-notifications"></a>Notificações por email de detecção inteligente

Todas as regras de detecção inteligente, exceto as regras marcadas como _Visualização_, são configuradas por padrão para enviar notificações por email quando as detecções são encontradas.

Configurar notificações por email para uma regra de detecção inteligente específica pode ser feito abrindo a folha **configurações** de detecção inteligente e selecionando a regra, que abrirá a folha **Editar regra** .

Como alternativa, você pode alterar a configuração usando modelos de Azure Resource Manager. [Consulte gerenciar Application insights regras de detecção inteligente usando modelos de Azure Resource Manager](https://docs.microsoft.com/azure/azure-monitor/app/proactive-arm-config) para obter mais detalhes.

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Passos seguintes
Essas ferramentas de diagnóstico ajudam você a inspecionar a telemetria do seu aplicativo:

* [Gerenciador de métricas](../../azure-monitor/app/metrics-explorer.md)
* [Gerenciador de pesquisa](../../azure-monitor/app/diagnostic-search.md)
* [Análise – linguagem de consulta eficiente](../../azure-monitor/log-query/get-started-portal.md)

A detecção inteligente é completamente automática. Mas talvez você queira configurar mais alguns alertas?

* [Alertas de métrica configurados manualmente](../../azure-monitor/app/alerts.md)
* [Testes da Web de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md) 

