---
title: Deteção Inteligente em Insights de Aplicações Azure / Microsoft Docs
description: O Application Insights realiza uma análise profunda automática da telemetria da sua aplicação e alerta para potenciais problemas.
ms.topic: conceptual
ms.date: 02/07/2019
ms.openlocfilehash: b7fecf886515780858ecc503a29be3bd76b73c3f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83700851"
---
# <a name="smart-detection-in-application-insights"></a>Deteção Inteligente no Application Insights
 A Smart Detection avisa-o automaticamente de potenciais problemas de desempenho e anomalias de falha na sua aplicação web. Realiza uma análise proactiva da telemetria que a sua aplicação envia para o [Application Insights](../../azure-monitor/app/app-insights-overview.md). Se houver um aumento súbito das taxas de falha, ou padrões anormais no desempenho do cliente ou servidor, obtém-se um alerta. Esta funcionalidade não necessita de configuração. Funciona se a sua aplicação enviar telemetria suficiente.

Pode aceder às deteções emitidas pela Smart Detection, tanto a partir dos e-mails que recebe, como a partir da lâmina de Deteção Inteligente.

## <a name="review-your-smart-detections"></a>Reveja as suas Deteções Inteligentes
Pode descobrir deteções de duas formas:

* **Recebe um e-mail** da Application Insights. Veja a seguir um exemplo típico:
  
    ![Alerta de e-mail](./media/proactive-diagnostics/03.png)
  
    Clique no botão grande para abrir mais detalhes no portal.
* **A lâmina de deteção inteligente** em Insights de Aplicação. Selecione **Smart Detection** no menu **Investigar** para ver uma lista de deteções recentes.

![Ver deteções recentes](./media/proactive-diagnostics/04.png)

Selecione uma deteção para ver os seus detalhes.

## <a name="what-problems-are-detected"></a>Que problemas são detetados?
A Deteção Inteligente deteta e notifica sobre uma variedade de questões, tais como:

* [Deteção inteligente - Anomalias de falha.](../../azure-monitor/app/proactive-failure-diagnostics.md) Utilizamos machine learning para definir a taxa esperada de pedidos falhados para a sua app, correlacionando-se com a carga e outros fatores. Se a taxa de avaria sair do envelope esperado, enviamos um alerta.
* [Deteção inteligente - Anomalias de desempenho.](../../azure-monitor/app/proactive-performance-diagnostics.md) Obtém notificações se o tempo de resposta de uma operação ou duração de dependência estiver a abrandar em comparação com a linha de base histórica ou se identificarmos um padrão anómalo no tempo de resposta ou tempo de carga da página.   
* Degradações gerais e problemas, como [a degradação do trace,](https://docs.microsoft.com/azure/azure-monitor/app/proactive-trace-severity) [fuga de memória,](https://docs.microsoft.com/azure/azure-monitor/app/proactive-potential-memory-leak) [aumento anormal do volume de exceções](https://docs.microsoft.com/azure/azure-monitor/app/proactive-exception-volume) e [anti-padrões de segurança.](https://docs.microsoft.com/azure/azure-monitor/app/proactive-application-security-detection-pack)

(As ligações de ajuda em cada notificação levam-no aos artigos relevantes.)

## <a name="smart-detection-email-notifications"></a>Notificações de email de Deteção Inteligente

Todas as regras de Deteção Inteligente, com exceção das regras marcadas como _pré-visualização,_ são configuradas por padrão para enviar notificações de e-mail quando forem encontradas deteções.

Configurar notificações de e-mail para uma regra específica de Deteção Inteligente pode ser feito abrindo a lâmina de **Definições** de Deteção Inteligente e selecionando a regra, que abrirá a lâmina **de regra de Edição.**

Em alternativa, pode alterar a configuração utilizando os modelos do Gestor de Recursos Azure. [Consulte as regras de deteção inteligente de Deteção de Aplicações para gerir o resultado utilizando modelos do Gestor de Recursos Azure](https://docs.microsoft.com/azure/azure-monitor/app/proactive-arm-config) para obter mais detalhes.

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Próximos passos
Estas ferramentas de diagnóstico ajudam-no a inspecionar a telemetria a partir da sua aplicação:

* [Explorador métrico](../../azure-monitor/platform/metrics-charts.md)
* [Explorador de pesquisa](../../azure-monitor/app/diagnostic-search.md)
* [Analytics - linguagem de consulta poderosa](../../azure-monitor/log-query/get-started-portal.md)

A Deteção Inteligente é completamente automática. Mas talvez queira fazer mais alguns alertas?

* [Alertas métricos configurados manualmente](../../azure-monitor/platform/alerts-log.md)
* [Testes Web de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md) 

