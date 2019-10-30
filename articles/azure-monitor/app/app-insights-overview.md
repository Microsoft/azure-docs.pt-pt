---
title: O que é o Azure Application Insights? | Microsoft Docs
description: Gestão de Desempenho de Aplicações e controlo da utilização da sua aplicação Web publicada.  Detete, faça a triagem, diagnostique problemas e veja como é que as pessoas utilizam a sua aplicação.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: overview
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/03/2019
ms.custom: mvc
ms.openlocfilehash: 089b37761c11c02e34c35eb378c64862bc4fbc98
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73064144"
---
# <a name="what-is-application-insights"></a>O que são Informações da Aplicação?
Application Insights, um recurso do [Azure monitor](../overview.md), é um serviço de gerenciamento de desempenho de aplicativos (APM) extensível para desenvolvedores da Web em várias plataformas. Utilize-o para monitorizar a sua aplicação Web online. Além de detetar automaticamente anomalias de desempenho, inclui ferramentas de análise poderosas para o ajudar a diagnosticar problemas e a compreender o que os utilizadores efetivamente fazem com a sua aplicação.  Foi concebido para o ajudar a melhorar continuamente o desempenho e a usabilidade. Ele funciona para aplicativos em uma ampla variedade de plataformas, incluindo .NET, Node. js e Java EE, hospedado local, híbrido ou qualquer nuvem pública. Integra-se com o seu processo de DevOps e tem pontos de ligação a diversas outras ferramentas de programação. Pode monitorizar e analisar a telemetria de aplicações móveis ao integrar com o Visual Studio App Center.

## <a name="how-does-application-insights-work"></a>Como funciona o Application Insights?
É instalado um pequeno pacote de instrumentação na sua aplicação e configurado um recurso do Application Insights no portal do Microsoft Azure. A instrumentação monitora seu aplicativo e envia dados de telemetria para Azure Monitor. (A aplicação pode ser executada em qualquer local; não tem de estar alojada no Azure.)

Pode instrumentar não só a aplicação do serviço Web, mas também qualquer componente em segundo plano e o JavaScript nas próprias páginas Web. 

![A instrumentação do Application Insights na sua aplicação envia telemetria para o seu recurso do Application Insights.](./media/app-insights-overview/diagram.png)

Além disso, pode extrair telemetria dos ambientes anfitriões, como contadores de desempenho, diagnósticos do Azure ou registos do Docker. Também pode configurar testes Web que enviam periodicamente pedidos sintéticos para o serviço Web.

Todos esses fluxos de telemetria são integrados ao Azure Monitor. No portal do Azure, você pode aplicar ferramentas de análise e de pesquisa poderosas aos dados brutos.

### <a name="whats-the-overhead"></a>Qual é o contra?
O impacto no desempenho da sua aplicação é muito pequeno. As chamadas de controlo não são limitativas, são colocadas em lotes e enviadas em threads separados.

## <a name="what-does-application-insights-monitor"></a>O que monitoriza o Application Insights?

O Application Insights destina-se à equipa de programação, para o ajudar a compreender o desempenho da aplicação e como está a ser utilizada. Monitoriza:

* **Taxas de pedidos, tempos de resposta e taxas de falhas** - saiba quais são as páginas mais populares, em que alturas do dia e onde estão localizados os seus utilizadores. Veja que páginas têm o melhor desempenho. Se os tempos de resposta e as taxas de falhas aumentarem quando há mais pedidos, é possível que tenha um problema relativo a recursos. 
* **Taxas de dependência, tempos de resposta e taxas de falhas** - veja se os serviços externos o estão a atrasar.
* **Exceções** – analise as estatísticas agregadas ou selecione instâncias específicas e aprofunde-se no rastreamento de pilha e nas solicitações relacionadas. São reportadas exceções do servidor e do browser.
* **Vistas de página e desempenho de carga** - reportados pelos browsers dos utilizadores.
* **Chamadas AJAX** das páginas Web - taxas, tempos de resposta e taxas de falhas.
* **Contagens de utilizadores e sessões**.
* **Contadores de desempenho** dos computadores de servidor Windows ou Linux, como CPU, memória e utilização de rede. 
* **Diagnóstico do anfitrião** do Docker ou do Azure. 
* **Registos de rastreio de diagnóstico** da sua aplicação - para que possa correlacionar eventos de rastreio a pedidos.
* **Eventos e métricas personalizadas** que escreve manualmente no código do cliente ou do servidor, para controlar eventos comerciais, como artigos vendidos ou jogos ganhos.

## <a name="where-do-i-see-my-telemetry"></a>Onde posso ver a minha telemetria?

Existem muitas formas para explorar os seus dados. Veja estes artigos:

|  |  |
| --- | --- |
| [**Deteção inteligente e alertas manuais**](../../azure-monitor/app/proactive-diagnostics.md)<br/>Os alertas automáticos adaptam-se aos padrões normais da sua aplicação relativamente a telemetria e são acionados se ocorrer algo fora do padrão habitual. Também pode [definir alertas](../../azure-monitor/app/alerts.md) em níveis específicos de métricas personalizadas ou standard. |![Exemplo de alerta](./media/app-insights-overview/alerts-tn.png) |
| [**Application map**](../../azure-monitor/app/app-map.md) (Mapeamento de aplicações)<br/>Os componentes da sua aplicação, com as principais métricas e alertas. |![Mapeamento de aplicações](./media/app-insights-overview/appmap-tn.png)  |
| [**Gerador de perfis**](../../azure-monitor/app/profiler.md)<br/>Verifique os perfis de execução de amostras de pedidos. |![Gerador de perfis](./media/app-insights-overview/profiler.png) |
| [**Análise de utilização**](../../azure-monitor/app/usage-overview.md)<br/>Analise a retenção e a segmentação do utilizador.|![Ferramenta de retenção](./media/app-insights-overview/retention.png) |
| [**Diagnostic search for instance data**](../../azure-monitor/app/diagnostic-search.md) (Pesquisa de diagnósticos para dados de instâncias)<br/>Procure e filtre eventos como pedidos, exceções, chamadas de dependências, rastreios de registo e visualizações de página.  |![Pesquisar telemetria](./media/app-insights-overview/search-tn.png) |
| [**Metrics Explorer for aggregated data**](../../azure-monitor/app/metrics-explorer.md) (Explorador de Métricas para dados agregados)<br/>Explore, filtre e segmente dados agregados, como taxas de pedidos, falhas e exceções, tempos de resposta e tempos de carregamento de páginas. |![Métricas](./media/app-insights-overview/metrics-tn.png) |
| [**Dashboards**](../../azure-monitor/app/overview-dashboard.md)<br/>Combine dados de vários recursos e partilhe-os com outras pessoas. Ideais para aplicações com vários componentes e para visualização contínua na sala de equipa. |![Exemplo de dashboards](./media/app-insights-overview/dashboard-tn.png) |
| [**Live Metrics Stream**](../../azure-monitor/app/live-stream.md) (Fluxo de Métricas em Direto)<br/>Quando implementa uma compilação nova, veja estes indicadores de desempenho em tempo quase real, para ter a certeza de que está tudo a funcionar conforme esperado. |![Exemplo de métricas em direto](./media/app-insights-overview/live-metrics-tn.png) |
| [**Analytics**](../../azure-monitor/app/analytics.md) (Análise)<br/>Responda a perguntas difíceis sobre o desempenho e a utilização da sua aplicação através desta poderosa linguagem de consultas. |![Exemplo de análise](./media/app-insights-overview/analytics-tn.png) |
| [**Visual Studio**](../../azure-monitor/app/visual-studio.md)<br/>Veja os dados de desempenho no código. Aceda ao código a partir dos rastreios de pilha.|![Visual Studio](./media/app-insights-overview/visual-studio-tn.png) |
| [**Depurador de instantâneos**](../../azure-monitor/app/snapshot-debugger.md)<br/>Depure instantâneos de amostra de operações em tempo real, com valores de parâmetro.|![Visual Studio](./media/app-insights-overview/snapshot.png) |
| [**Power BI**](../../azure-monitor/app/export-power-bi.md )<br/>Integre métricas de utilização com outras métricas de business intelligence.| ![Power BI](./media/app-insights-overview/power-bi.png)|
| [**REST API**](https://dev.applicationinsights.io/) (API REST)<br/>Escreva código para executar consultas nas métricas e nos dados não processados.| ![API REST](./media/app-insights-overview/rest-tn.png) |
| [**Continuous export**](../../azure-monitor/app/export-telemetry.md) (Exportação contínua)<br/>Exportação em massa de dados não processados para armazenamento, assim que são recebidos. |![Exportar](./media/app-insights-overview/export-tn.png) |

## <a name="how-do-i-use-application-insights"></a>Como posso utilizar o Application Insights?

### <a name="monitor"></a>Monitorizar
Instale o Application Insights na sua aplicação, configure [testes Web de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md) e:

* Confira o painel do [aplicativo](../../azure-monitor/app/overview-dashboard.md) padrão para o seu espaço de equipe para ficar atento à carga, à capacidade de resposta e ao desempenho de suas dependências, cargas de página e chamadas AJAX.
* Saiba quais são os pedidos mais lentos e que mais falham.
* Veja o [Live Stream](../../azure-monitor/app/live-stream.md) quando implementar uma versão nova, para tomar conhecimento imediatamente de eventuais degradações.

### <a name="detect-diagnose"></a>Detetar, Diagnosticar
Quando receber um alerta ou detetar um problema:

* Avalie o número de utilizadores afetados.
* Correlacione falhas com exceções, chamadas de dependência e rastreamentos.
* Examine o gerador de perfis, instantâneos, rastreios de pilha e registos de rastreio.

### <a name="build-measure-learn"></a>Compilar, Medir, Aprender
[Meça a eficácia](../../azure-monitor/app/usage-overview.md) de cada funcionalidade nova que implementar.

* Planeie medir a forma como os clientes utilizam o UX ou as funcionalidades empresariais.
* Escreva telemetria personalizada no seu código.
* Baseie o próximo ciclo de programação em evidências sólidas a partir da sua telemetria.

## <a name="get-started"></a>Introdução
O Application Insights é um dos muitos serviços alojados no Microsoft Azure e a telemetria é enviada para aqui, para análise e apresentação. Assim, antes de fazer mais alguma coisa, vai precisar de uma subscrição do [Microsoft Azure](https://azure.com). A inscrição é gratuita e, se optar pelo [plano de preços](https://azure.microsoft.com/pricing/details/application-insights/) básico do Application Insights, não há custos até que a sua aplicação se desenvolva e tenha uma utilização significativa. Se a sua organização já tiver uma subscrição, pode adicionar a sua conta Microsoft à mesma.

Existem várias formas de começar. Comece com a que funcionar melhor para si. Pode acrescentar outras mais tarde.

* **Em tempo de execução: instrumente a sua aplicação Web no servidor.** Ideal para aplicativos já implantados. Evita atualizações ao código.
  * [**Aplicativos ASP.NET ou ASP.NET Core hospedados em aplicativos Web do Azure**](../../azure-monitor/app/azure-web-apps.md)
  * [**Aplicativos ASP.NET hospedados no IIS na VM do Azure ou no conjunto de dimensionamento de máquinas virtuais do Azure**](../../azure-monitor/app/azure-vm-vmss-apps.md)
  * [**Aplicativos ASP.NET hospedados na VM local do IIS**](../../azure-monitor/app/monitor-performance-live-website-now.md)
* **No momento de programação: adicione o Application Insights ao seu código.** Permite que você personalize a coleta de telemetria e envie telemetria adicional.
  * [Aplicativos ASP.NET](../../azure-monitor/app/asp-net.md)
  * [ASP.NET Core aplicativos](../../azure-monitor/app/asp-net-core.md)
  * [Aplicativos de console .NET](../../azure-monitor/app/console.md)
  * [Java](../../azure-monitor/app/java-get-started.md)
  * [Node.js](../../azure-monitor/app/nodejs.md)
  * [Python (versão prévia)](../../azure-monitor/app/opencensus-python.md)
  * [Outras plataformas](../../azure-monitor/app/platforms.md)
* **[Instrumente suas páginas da Web](../../azure-monitor/app/javascript.md)** para exibição de página, Ajax e outra telemetria do lado do cliente.
* **[Analise a utilização da aplicação móvel](../../azure-monitor/learn/mobile-center-quickstart.md)** ao integrar com o Centro de Aplicações do Visual Studio.
* **[Testes de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md)** - envie um ping ao seu Web site regularmente a partir dos nossos servidores.

## <a name="next-steps"></a>Passos seguintes
Introdução ao tempo de execução com:

* [VM do Azure e conjunto de dimensionamento de máquinas virtuais do Azure aplicativos hospedados pelo IIS](../../azure-monitor/app/azure-vm-vmss-apps.md)
* [Servidor do IIS](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Aplicações Web do Azure](../../azure-monitor/app/azure-web-apps.md)

Introdução no momento de programação com:

* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [Núcleo do ASP.NET](../../azure-monitor/app/asp-net-core.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [Node.js](../../azure-monitor/app/nodejs.md)
* [Python (versão prévia)](../../azure-monitor/app/opencensus-python.md)


## <a name="support-and-feedback"></a>Suporte e comentários
* Perguntas e Problemas:
  * [Resolução de problemas][qna]
  * [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/vstudio/home?forum=ApplicationInsights)
  * [StackOverflow](https://stackoverflow.com/questions/tagged/ms-application-insights)
* As suas sugestões:
  * [UserVoice](https://feedback.azure.com/forums/357324-application-insights/filters/top)
* Blogue:
  * [Blogue do Application Insights](https://azure.microsoft.com/blog/tag/application-insights)

<!--Link references-->

[android]: ../../azure-monitor/learn/mobile-center-quickstart.md
[azure]: ../../insights-perf-analytics.md
[client]: ../../azure-monitor/app/javascript.md
[desktop]: ../../azure-monitor/app/windows-desktop.md
[greenbrown]: ../../azure-monitor/app/asp-net.md
[ios]: ../../azure-monitor/learn/mobile-center-quickstart.md
[java]: ../../azure-monitor/app/java-get-started.md
[knowUsers]: app-insights-web-track-usage.md
[platforms]: ../../azure-monitor/app/platforms.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
