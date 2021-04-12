---
title: O que é o Azure Application Insights? | Microsoft Docs
description: Gestão de Desempenho de Aplicações e controlo da utilização da sua aplicação Web publicada.  Detete, faça a triagem, diagnostique problemas e veja como é que as pessoas utilizam a sua aplicação.
ms.topic: overview
ms.date: 06/03/2019
ms.custom: mvc
ms.openlocfilehash: f25a53cb227d6894aaabaf82033178f15f61c173
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076212"
---
# <a name="what-is-application-insights"></a>O que é o Application Insights?
Application Insights, uma funcionalidade do [Azure Monitor,](../overview.md)é um serviço extensível de Gestão de Desempenho de Aplicações (APM) para desenvolvedores e profissionais de DevOps. Use-o para monitorizar as suas aplicações ao vivo. Irá detetar automaticamente anomalias de desempenho e inclui poderosas ferramentas de análise para ajudá-lo a diagnosticar problemas e a entender o que os utilizadores realmente fazem com a sua aplicação.  Foi concebido para o ajudar a melhorar continuamente o desempenho e a usabilidade. Funciona para apps em uma grande variedade de plataformas, incluindo .NET, Node.js, Java e Python hospedados no local, híbridos ou qualquer nuvem pública. Integra-se com o seu processo de DevOps e tem pontos de ligação a diversas outras ferramentas de programação. Pode monitorizar e analisar a telemetria de aplicações móveis ao integrar com o Visual Studio App Center.

## <a name="how-does-application-insights-work"></a>Como funciona o Application Insights?
Instale um pequeno pacote de instrumentação (SDK) na sua aplicação ou ative os Insights de Aplicação utilizando o Agente de Insights de Aplicação quando [suportado](./platforms.md). A instrumentação monitoriza a sua aplicação e direciona os dados de telemetria para um Recurso de Insights de Aplicação Azure utilizando um GUID único a que nos referimos como chave de instrumentação.

Pode instrumentar não só a aplicação do serviço Web, mas também qualquer componente em segundo plano e o JavaScript nas próprias páginas Web. A aplicação e os seus componentes podem ser executados em qualquer lugar - não tem de ser hospedado em Azure.

![A instrumentação do Application Insights na sua aplicação envia telemetria para o seu recurso do Application Insights.](./media/app-insights-overview/diagram.png)

Além disso, pode extrair telemetria dos ambientes anfitriões, como contadores de desempenho, diagnósticos do Azure ou registos do Docker. Também pode configurar testes Web que enviem periodicamente pedidos sintéticos para o serviço Web.

Todos estes fluxos de telemetria estão integrados no Azure Monitor. No portal do Azure, pode aplicar avançadas ferramentas de análise e pesquisa aos dados não processados.

### <a name="whats-the-overhead"></a>Qual é o contra?
O impacto no desempenho da sua aplicação é pequeno. As chamadas de controlo não são limitativas, são colocadas em lotes e enviadas em threads separados.

## <a name="what-does-application-insights-monitor"></a>O que monitoriza o Application Insights?

O Application Insights destina-se à equipa de programação, para o ajudar a compreender o desempenho da aplicação e como está a ser utilizada. Monitoriza:

* **Taxas de pedidos, tempos de resposta e taxas de falhas** - saiba quais são as páginas mais populares, em que alturas do dia e onde estão localizados os seus utilizadores. Veja que páginas têm o melhor desempenho. Se os tempos de resposta e as taxas de falhas aumentarem quando há mais pedidos, é possível que tenha um problema relativo a recursos. 
* **Taxas de dependência, tempos de resposta e taxas de falhas** - veja se os serviços externos o estão a atrasar.
* **Exceções** - Analise as estatísticas agregadas, ou escolha instâncias específicas e fure o traço da pilha e os pedidos relacionados. São reportadas exceções do servidor e do browser.
* **Vistas de página e desempenho de carga** - reportados pelos browsers dos utilizadores.
* **Chamadas AJAX** das páginas Web - taxas, tempos de resposta e taxas de falhas.
* **Contagens de utilizadores e sessões**.
* **Contadores de desempenho** dos computadores de servidor Windows ou Linux, como CPU, memória e utilização de rede. 
* **Diagnóstico do anfitrião** do Docker ou do Azure. 
* **Registos de rastreio de diagnóstico** da sua aplicação - para que possa correlacionar eventos de rastreio a pedidos.
* **Eventos e métricas personalizadas** que escreve manualmente no código do cliente ou do servidor, para controlar eventos comerciais, como artigos vendidos ou jogos ganhos.

## <a name="where-do-i-see-my-telemetry"></a>Onde posso ver a minha telemetria?

Existem muitas formas para explorar os seus dados. Veja estes artigos:

| Descrição do artigo   | Imagem |
| --- | --- |
| [**Deteção inteligente e alertas manuais**](./proactive-diagnostics.md)<br/>Configurar alertas automáticos que se adaptem aos padrões normais de telemetria e gatilho da sua aplicação quando há algo fora do padrão habitual. Também pode [definir alertas](../alerts/alerts-log.md) em níveis específicos de métricas personalizadas ou padrão. |![Exemplo de alerta](./media/app-insights-overview/alerts-tn.png) |
| [**Mapa de aplicação**](./app-map.md)<br/>Explore os componentes da sua aplicação, com métricas-chave e alertas. |![Mapa da aplicação](./media/app-insights-overview/appmap-tn.png)  |
| [**Gerador de perfis**](./profiler.md)<br/>Verifique os perfis de execução de amostras de pedidos. |![A captura do ecrã mostra perfis de execução de pedidos amostrados.](./media/app-insights-overview/profiler.png) |
| [**Análise de utilização**](./usage-overview.md)<br/>Analise a retenção e a segmentação do utilizador.|![Ferramenta de retenção](./media/app-insights-overview/retention.png) |
| [**Diagnostic search for instance data**](./diagnostic-search.md) (Pesquisa de diagnósticos para dados de instâncias)<br/>Procure e filtre eventos como pedidos, exceções, chamadas de dependências, rastreios de registo e visualizações de página.  |![Pesquisar telemetria](./media/app-insights-overview/search-tn.png) |
| [**Metrics Explorer for aggregated data**](../essentials/metrics-charts.md) (Explorador de Métricas para dados agregados)<br/>Explore, filtre e segmente dados agregados, como taxas de pedidos, falhas e exceções, tempos de resposta e tempos de carregamento de páginas. |![Métricas](./media/app-insights-overview/metrics-tn.png) |
| [**Dashboards**](./overview-dashboard.md)<br/>Combine dados de vários recursos e partilhe-os com outras pessoas. Ideais para aplicações com vários componentes e para visualização contínua na sala de equipa. |![Exemplo de dashboards](./media/app-insights-overview/dashboard-tn.png) |
| [**Live Metrics Stream**](./live-stream.md)<br/>Quando implementa uma compilação nova, veja estes indicadores de desempenho em tempo quase real, para ter a certeza de que está tudo a funcionar conforme esperado. |![Exemplo de métricas em direto](./media/app-insights-overview/live-metrics-tn.png) |
| [**Análise**](../logs/log-query-overview.md)<br/>Responda a perguntas difíceis sobre o desempenho e a utilização da sua aplicação através desta poderosa linguagem de consultas. |![Exemplo de análise](./media/app-insights-overview/analytics-tn.png) |
| [**Visual Studio**](./visual-studio.md)<br/>Veja os dados de desempenho no código. Aceda ao código a partir dos rastreios de pilha.|![Screenshot mostra Detalhes de Exceção no Estúdio Visual e um exemplo de ir codificar a partir de traços de pilha.](./media/app-insights-overview/visual-studio-tn.png) |
| [**Debugger snapshot**](./snapshot-debugger.md)<br/>Depure instantâneos de amostra de operações em tempo real, com valores de parâmetro.|![Visual Studio](./media/app-insights-overview/snapshot.png) |
| [**Power BI**](./export-power-bi.md)<br/>Integre métricas de utilização com outras métricas de business intelligence.| ![Power BI](./media/app-insights-overview/power-bi.png)|
| [**API REST**](https://dev.applicationinsights.io/)<br/>Escreva código para executar consultas nas métricas e nos dados não processados.| ![API REST](./media/app-insights-overview/rest-tn.png) |
| [**Exportação contínua**](./export-telemetry.md)<br/>Exportação em massa de dados não processados para armazenamento, assim que são recebidos. |![Exportar](./media/app-insights-overview/export-tn.png) |

## <a name="how-do-i-use-application-insights"></a>Como posso utilizar o Application Insights?

### <a name="monitor"></a>Monitor
Instale o Application Insights na sua aplicação, configure [testes Web de disponibilidade](./monitor-web-app-availability.md) e:

* Confira o painel de [aplicações](./overview-dashboard.md) padrão para a sua sala de equipa para manter um olho na carga, capacidade de resposta e no desempenho das suas dependências, cargas de página e chamadas AJAX.
* Saiba quais são os pedidos mais lentos e que mais falham.
* Veja o [Live Stream](./live-stream.md) quando implementar uma versão nova, para tomar conhecimento imediatamente de eventuais degradações.

### <a name="detect-diagnose"></a>Detetar, Diagnosticar
Quando receber um alerta ou detetar um problema:

* Avalie o número de utilizadores afetados.
* Correlacionar falhas com exceções, chamadas de dependência e vestígios.
* Examine o gerador de perfis, instantâneos, rastreios de pilha e registos de rastreio.

### <a name="build-measure-learn"></a>Compilar, Medir, Aprender
[Meça a eficácia](./usage-overview.md) de cada nova funcionalidade que implementa.

* Planeie medir a forma como os clientes utilizam o UX ou as funcionalidades empresariais.
* Escreva telemetria personalizada no seu código.
* Baseie o próximo ciclo de programação em evidências sólidas a partir da sua telemetria.

## <a name="get-started"></a>Introdução
O Application Insights é um dos muitos serviços alojados no Microsoft Azure e a telemetria é enviada para aqui, para análise e apresentação. Assim, antes de fazer mais alguma coisa, vai precisar de uma subscrição do [Microsoft Azure](https://azure.com). A inscrição é gratuita e, se optar pelo [plano de preços](https://azure.microsoft.com/pricing/details/application-insights/) básico do Application Insights, não há custos até que a sua aplicação se desenvolva e tenha uma utilização significativa. Se a sua organização já tiver uma subscrição, pode adicionar a sua conta Microsoft à mesma.

Existem várias formas de começar. Comece com a que funcionar melhor para si. Pode acrescentar outras mais tarde.

* **Em tempo de execução: instrumente a sua aplicação Web no servidor.** Ideal para aplicações já implantadas. Evita atualizações ao código.
  * [**ASP.NET ou ASP.NET Aplicações Core hospedadas em Azure Web Apps**](./azure-web-apps.md)
  * [**ASP.NET aplicações hospedadas no IIS em Azure VM ou conjunto de escala de máquina virtual Azure**](./azure-vm-vmss-apps.md)
  * [**ASP.NET candidaturas hospedadas no IIS no local VM**](./monitor-performance-live-website-now.md)
* **No momento de programação: adicione o Application Insights ao seu código.** Permite-lhe personalizar a coleção de telemetria e enviar telemetria adicional.
  * [Aplicações ASP.NET](./asp-net.md)
  * [Aplicações ASP.NET Core](./asp-net-core.md)
  * [Aplicações de consola .NET](./console.md)
  * [Java](./java-get-started.md)
  * [Node.js](./nodejs.md)
  * [Python](./opencensus-python.md)
  * [Outras plataformas](./platforms.md)
* **[Instrumente as suas páginas web](./javascript.md)** para visualização de página, AJAX e outras telemetria do lado do cliente.
* **[Analise a utilização da aplicação móvel](../app/mobile-center-quickstart.md)** ao integrar com o Centro de Aplicações do Visual Studio.
* **[Testes de disponibilidade](./monitor-web-app-availability.md)** - envie um ping ao seu Web site regularmente a partir dos nossos servidores.

## <a name="next-steps"></a>Passos seguintes
Introdução ao tempo de execução com:

* [Azure VM e Azure série de máquinas virtuais definir aplicativos hospedados no IIS](./azure-vm-vmss-apps.md)
* [Servidor do IIS](./monitor-performance-live-website-now.md)
* [Aplicações Web do Azure](./azure-web-apps.md)

Introdução no momento de programação com:

* [ASP.NET](./asp-net.md)
* [ASP.NET Core](./asp-net-core.md)
* [Java](./java-get-started.md)
* [Node.js](./nodejs.md)
* [Python](./opencensus-python.md)
* [JavaScript](./javascript.md)


## <a name="support-and-feedback"></a>Suporte e comentários
* Perguntas e Problemas:
  * [Resolução de problemas][qna]
  * [Microsoft Q&Uma página de perguntas](/answers/topics/azure-monitor.html)
  * [Stackoverflow](https://stackoverflow.com/questions/tagged/ms-application-insights)
* As suas sugestões:
  * [UserVoice](https://feedback.azure.com/forums/357324-application-insights/filters/top)
* Blogue:
  * [Blogue do Application Insights](https://azure.microsoft.com/blog/tag/application-insights)

<!--Link references-->

[android]: ../app/mobile-center-quickstart.md
[azure]: ../../insights-perf-analytics.md
[client]: ./javascript.md
[desktop]: ./windows-desktop.md
[greenbrown]: ./asp-net.md
[ios]: ../app/mobile-center-quickstart.md
[java]: ./java-get-started.md
[knowUsers]: app-insights-web-track-usage.md
[platforms]: ./platforms.md
[portal]: https://portal.azure.com/
[qna]: ../faq.md
[redfield]: ./monitor-performance-live-website-now.md

