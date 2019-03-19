---
title: Monitorizar o estado de funcionamento da sua aplicação e a utilização com o Application Insights
description: Introdução ao Application Insights. Analise a utilização, disponibilidade e desempenho das suas aplicações de Microsoft Azure ou no local.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 40650472-e860-4c1b-a589-9956245df307
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/10/2018
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: d7b8037f50fc4877fe233925f3e922648169f73b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57901173"
---
# <a name="monitor-performance-in-web-applications"></a>Monitorizar o desempenho nas aplicações Web


Certifique-se de que seu aplicativo é ter um bom desempenho e obter informações rapidamente sobre quaisquer falhas. [O Application Insights] [ start] falar sobre quaisquer problemas de desempenho e exceções e ajudar a encontrar e diagnosticar as causas de raiz.

O Application Insights pode monitorizar aplicações web de Java e ASP.NET e serviços, serviços WCF. Eles podem ser alojado no local, em máquinas virtuais, ou como Web sites do Microsoft Azure. 

No lado do cliente, o Application Insights pode demorar telemetria a partir de páginas da web e uma grande variedade de dispositivos, incluindo iOS, Android e Windows Store apps.

## <a name="setup"></a>Configurar a monitorização de desempenho
Se ainda não adicionou ainda Application Insights ao seu projeto (ou seja, se ele não tem o applicationinsights. config), escolha uma das seguintes formas para começar a utilizar:

* [Aplicações web do ASP.NET](../../azure-monitor/app/asp-net.md)
  * [Adicionar a monitorização de exceções](../../azure-monitor/app/asp-net-exceptions.md)
  * [Adicionar a monitorização de dependência](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Aplicações web de Java EE](../../azure-monitor/app/java-get-started.md)
  * [Adicionar a monitorização de dependência](../../azure-monitor/app/java-agent.md)

## <a name="view"></a>Explorar métricas de desempenho
Na [do portal do Azure](https://portal.azure.com), navegue para o recurso do Application Insights que configurou para a sua aplicação. O painel de descrição geral mostra os dados de desempenho básico:

Clique em qualquer gráfico para ver mais detalhes e para ver os resultados para um período mais longo. Por exemplo, clique no mosaico de pedidos e, em seguida, selecione um intervalo de tempo:

![Clicar para obter mais dados e selecione um intervalo de tempo](./media/web-monitor-performance/appinsights-48metrics.png)

Clique num gráfico para escolher as métricas apresenta, ou adicione um novo gráfico e selecione as suas métricas:

![Clique num gráfico para escolher métricas](./media/web-monitor-performance/appinsights-61perfchoices.png)

> [!NOTE]
> **Desmarque todas as métricas** para ver a seleção de completa está disponível. As métricas enquadram-se grupos; Quando qualquer membro de um grupo for selecionado, são apresentados apenas os outros membros desse grupo.

## <a name="metrics"></a>O que isso significa que todos os? Mosaicos de desempenho e relatórios
Existem várias métricas de desempenho, que pode obter. Vamos começar com aqueles que são apresentados por predefinição no painel da aplicação.

### <a name="requests"></a>Pedidos
O número de pedidos HTTP recebido num período de tempo especificado. Compare isso com os resultados de outros relatórios para ver como a sua aplicação se comporta como a carga varia.

Pedidos HTTP incluir todos os pedidos GET ou POST para páginas, dados e imagens.

Clique no mosaico para obter contagens de URLs específicos.

### <a name="average-response-time"></a>Tempo médio de resposta
Mede o tempo entre uma solicitação da web introduzindo seu aplicativo e a resposta que está a ser devolvida.

Os pontos de mostram uma mudança médio. Se existirem muitos pedidos, deve haver algum que evitar a média sem um pico óbvia ou dip no gráfico.

Procure picos invulgares. Em geral, esperar que o tempo de resposta a aumentar com um aumento nos pedidos. Se o aumento desproporcional, a aplicação poderá atingir um limite de recursos, tais como CPU ou a capacidade de um serviço que utiliza.

Clique no mosaico para obter os tempos de URLs específicos.

![](./media/web-monitor-performance/appinsights-42reqs.png)

### <a name="slowest-requests"></a>Pedidos mais lentos
![](./media/web-monitor-performance/appinsights-44slowest.png)

Mostra quais os pedidos poderão ter de ajuste de desempenho.

### <a name="failed-requests"></a>Pedidos falhados
![](./media/web-monitor-performance/appinsights-46failed.png)

Uma contagem de pedidos que emitiu exceções não identificadas.

Clique no mosaico para ver os detalhes de falhas específicas e selecione um pedido individual para ver os seus detalhes. 

Apenas uma amostra representativa de falhas é retida durante inspeção individual.

### <a name="other-metrics"></a>Outras métricas
Para ver o que outras métricas pode apresentar, clique num gráfico e, em seguida, desmarque todas as métricas para ver todo o conjunto. Clique (i) para ver a definição de cada uma.

![Desmarcar todas as métricas para ver o conjunto inteiro](./media/web-monitor-performance/appinsights-62allchoices.png)

Selecionar qualquer métrica desativa os outros que não pode aparecer no mesmo gráfico.

## <a name="set-alerts"></a>Definir alertas
Para ser notificado por correio eletrónico dos valores invulgares de qualquer métrica, adicione um alerta. Pode escolher para enviar o e-mail para os administradores de conta, ou para endereços de e-mail específico.

![](./media/web-monitor-performance/appinsights-413setMetricAlert.png)

Defina o recurso antes das outras propriedades. Não escolha os recursos de teste Web, se pretender definir alertas em métricas de desempenho ou na utilização.

Cuidado-se de que tenha em atenção as unidades em que lhe for pedido para introduzir o valor de limiar.

*Não vejo no botão Adicionar alerta.* -Este é um grupo de conta para o qual tem acesso só de leitura? Contacte o administrador de conta.

## <a name="diagnosis"></a>Diagnóstico de problemas
Aqui estão algumas dicas para localizar e diagnosticar problemas de desempenho:

* Configurar [testes web] [ availability] para ser alertado se o seu web site ficar inativo ou responde incorreta ou lentamente. 
* Compare a contagem de pedidos com outras métricas para ver se falhas ou lento de resposta está relacionado ao carregar.
* [Inserir e procurar declarações de rastreio] [ diagnostic] em seu código para o ajudar a identificar problemas.
* Monitorizar a sua aplicação Web na operação com [Stream de métricas em direto][livestream].
* Capturar o estado da sua aplicação .NET com [Snapshot Debugger][snapshot].

## <a name="find-and-fix-performance-bottlenecks-with-performance-investigation-experience"></a>Encontre e corrija estrangulamentos de desempenho com experiência de investigação de desempenho

Pode utilizar a experiência de investigação de desempenho para rever o desempenho de operações lentas na sua aplicação Web. Pode selecionar uma operação lenta específica e utilizar rapidamente [Profiler](../../azure-monitor/app/profiler.md) raiz com que as operações lentas em código. Utilizando a distribuição de duração novo apresentada para a operação selecionada, que pode rapidamente avaliar rapidamente a experiência ruim como é para os seus clientes. Pode ver a quantidade de suas interações do utilizador foram afetados para cada operação lenta. No exemplo a seguir, decidimos de dar uma olhada mais de perto a experiência para a operação GET Customers/Details. Da distribuição de duração, podemos ver que existem três picos. Pico mais à esquerda é cerca de 400 ms e representa a excelente experiência responsiva. Pico intermediária é em torno de 1,2 s e representa uma experiência mediocre. Por fim, o 3.6 s temos outro pequeno pico, que representa a experiência de percentil 99, que pode fazer com que os nossos clientes deixar insatisfeito. Essa experiência é dez vezes mais lenta do que o excelente experiência para a mesma operação. 

![Picos de duração de três do GET Customers/Details](./media/web-monitor-performance/PerformanceTriageViewZoomedDistribution.png)

Para obter uma melhor noção do que as experiências de utilizador para esta operação, podemos selecionar um intervalo de tempo maior. Podemos, em seguida, também restringir no tempo numa janela de tempo específico em que a operação foi lenta. No exemplo a seguir, vamos mudar da predefinição 24 horas do intervalo de tempo de intervalo de tempo para nos 7 dias e, em seguida, ampliado para a janela de tempo de 9:47 para 12:47 entre terça-feira a 12 e quarta-feira a 13. A distribuição de duração e o número de rastreios de exemplo e criador de perfil foram atualizados no lado direito.

![Intervalo de GET Customers/Details três picos de duração dentro de sete dias com uma janela de tempo](./media/web-monitor-performance/PerformanceTriageView7DaysZoomedTrend.png)

Para restringir as experiências lentas, podemos junto ampliar as durações que se enquadrem entre 95 e o percentil de 99. Representam a % de 4 interações de utilizador que foram lenta.

![Intervalo de GET Customers/Details três picos de duração dentro de sete dias com uma janela de tempo](./media/web-monitor-performance/PerformanceTriageView7DaysZoomedTrendZoomed95th99th.png)

Podemos agora examinar qualquer um dos exemplos representativos, ao clicar no botão de exemplos, ou os rastreios do profiler representativo, ao clicar no botão de rastreios do Profiler. Neste exemplo, existem quatro rastreios que tenham sido recolhidos para GET Customers/Details na duração de janela e o intervalo de tempo de interesse.

Às vezes, o problema não se será em seu código, mas em vez disso, numa dependência chama seu código. Pode mudar para o separador de dependências na vista de triagem de desempenho para investigar essas dependências lentas. Por predefinição, a vista de desempenho é médias mais populares, mas o que realmente deseja examinar é o percentil 95 (ou 99th, no caso de que está a monitorizar um serviço maduro). No exemplo a seguir Concentramo-na dependência lenta de BLOBS do Azure, onde chamamos PUT fabrikamaccount. Vale a ocorrer no cluster cerca de 40 ms, as chamadas lentas para a mesma dependência são três vezes mais lentas, cerca de 120 ms de clustering. Não use muitas destas chamadas traduzir-se para fazer com que a operação respectiva visivelmente abrandamento. Pode explorar os exemplos representativos e rastreios do profiler, como pode fazer com a guia de operações.

![Intervalo de GET Customers/Details três picos de duração dentro de sete dias com uma janela de tempo](./media/web-monitor-performance/SlowDependencies95thTrend.png)

A experiência de investigação de desempenho mostra as informações relevantes no lado do conjunto de exemplo que decidimos nos concentrar na. É a melhor forma de ver todas as informações disponíveis mudar para um intervalo de tempo de 30 dias e, em seguida, selecione geral para ver informações em todas as operações para o último mês.

![Intervalo de GET Customers/Details três picos de duração dentro de sete dias com uma janela de tempo](./media/web-monitor-performance/Performance30DayOveralllnsights.png)


## <a name="next"></a>Passos seguintes
[Testes Web] [ availability] -tem pedidos web enviados ao seu aplicativo em intervalos regulares de todo o mundo.

[Capturar e pesquisar rastreios de diagnóstico] [ diagnostic] - inserção de chamadas de rastreio e filtrar os resultados para identificar problemas.

[Controlo de utilização] [ usage] -Descubra como as pessoas utilizam a sua aplicação.

[Resolução de problemas] [ qna] - e as perguntas e respostas



<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[greenbrown]: ../../azure-monitor/app/asp-net.md
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
[start]: ../../azure-monitor/app/app-insights-overview.md
[usage]: usage-overview.md
[livestream]: ../../azure-monitor/app/live-stream.md
[snapshot]: ../../azure-monitor/app/snapshot-debugger.md



