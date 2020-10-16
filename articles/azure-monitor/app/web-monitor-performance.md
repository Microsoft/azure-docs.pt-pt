---
title: Monitorize a saúde e utilização da sua aplicação com o Application Insights
description: Começa com a Application Insights. Analise a utilização, disponibilidade e desempenho das suas aplicações no local ou microsoft Azure.
ms.topic: conceptual
ms.date: 05/10/2018
ms.reviewer: sdash
ms.openlocfilehash: f84e1065f93fc2cca950f12d5df537b931faac8d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90987161"
---
# <a name="monitor-performance-in-web-applications"></a>Monitorizar o desempenho nas aplicações Web


Certifique-se de que a sua aplicação está a funcionar bem e descubra rapidamente quaisquer falhas. [A Application Insights][start] irá informá-lo sobre quaisquer problemas de desempenho e exceções, e ajudá-lo-á a encontrar e diagnosticar as causas da raiz.

O Application Insights pode monitorizar aplicações e serviços da Java e ASP.NET web, serviços WCF. Podem ser hospedados no local, em máquinas virtuais ou como websites da Microsoft Azure. 

Do lado do cliente, o Application Insights pode tirar telemetria a partir de páginas web e uma grande variedade de dispositivos, incluindo aplicações iOS, Android e Windows Store.

## <a name="set-up-performance-monitoring"></a><a name="setup"></a>Configurar a monitorização do desempenho
Se ainda não adicionou Application Insights ao seu projeto (isto é, se não tiver ApplicationInsights.config), escolha uma destas formas de começar:

* [Aplicações Web do ASP.NET](./asp-net.md)
  * [Adicionar monitorização de exceções](./asp-net-exceptions.md)
  * [Adicionar monitorização de dependência](./monitor-performance-live-website-now.md)
* [Aplicativos web Java EE](./java-in-process-agent.md)

## <a name="exploring-performance-metrics"></a><a name="view"></a>Explorando métricas de desempenho
No [portal Azure,](https://portal.azure.com)consulte o recurso Application Insights que configura para a sua aplicação. A lâmina de visão geral mostra dados básicos de desempenho:

Clique em qualquer gráfico para ver mais detalhes e para ver resultados por um período mais longo. Por exemplo, clique no azulejo Solicitação e, em seguida, selecione um intervalo de tempo:

![Clique em mais dados e selecione um intervalo de tempo](./media/web-monitor-performance/appinsights-48metrics.png)

Clique num gráfico para escolher quais as métricas que exibe, ou adicione um novo gráfico e selecione as suas métricas:

![Clique num gráfico para escolher métricas](./media/web-monitor-performance/appinsights-61perfchoices.png)

> [!NOTE]
> **Desmarque todas as métricas** para ver a seleção completa que está disponível. As métricas caem em grupos; quando qualquer membro de um grupo é selecionado, apenas os outros membros desse grupo aparecem.

## <a name="what-does-it-all-mean-performance-tiles-and-reports"></a><a name="metrics"></a>O que significa tudo isto? Telhas de desempenho e relatórios
Há várias métricas de desempenho que pode obter. Comecemos por aqueles que aparecem por defeito na lâmina de aplicação.

### <a name="requests"></a>Pedidos
O número de pedidos HTTP recebidos num determinado período. Compare isto com os resultados de outros relatórios para ver como a sua aplicação se comporta à medida que a carga varia.

Os pedidos HTTP incluem todos os pedidos GET ou POST para páginas, dados e imagens.

Clique no azulejo para obter contagens para URLs específicos.

### <a name="average-response-time"></a>Tempo médio de resposta
Mede o tempo entre um pedido web que insira a sua aplicação e a resposta seja devolvida.

Os pontos mostram uma média móvel. Se houver muitos pedidos, pode haver alguns que se desviam da média sem um pico óbvio ou mergulhar no gráfico.

Procure picos incomuns. Em geral, espere que o tempo de resposta suba com um aumento dos pedidos. Se o aumento for desproporcionado, a sua aplicação pode estar a atingir um limite de recursos como o CPU ou a capacidade de um serviço que utiliza.

Clique no azulejo para obter tempos para URLs específicos.

![Screenshot do painel de saúde da Aplicação, que exibe gráficos de linha ao longo do tempo de médias móveis de pedidos e tempo de resposta.](./media/web-monitor-performance/appinsights-42reqs.png)

### <a name="slowest-requests"></a>Pedidos mais lentos
![Screenshot de uma lista dos pedidos mais lentos e dos seus tempos de resposta.](./media/web-monitor-performance/appinsights-44slowest.png)

Mostra quais os pedidos que podem necessitar de afinação de desempenho.

### <a name="failed-requests"></a>Pedidos com falhas
![Screenshot de um gráfico de linha ao longo do tempo do número de pedidos falhados; isto é, pedidos que lançaram exceções sem falhas.](./media/web-monitor-performance/appinsights-46failed.png)

Uma contagem de pedidos que atirou exceções sem falhas.

Clique no azulejo para ver os detalhes de falhas específicas e selecione um pedido individual para ver o seu detalhe. 

Apenas uma amostra representativa das falhas é mantida para inspeção individual.

### <a name="other-metrics"></a>Outras métricas
Para ver que outras métricas pode apresentar, clique num gráfico e, em seguida, desmarca todas as métricas para ver o conjunto completo disponível. Clique (i) para ver a definição de cada métrica.

![Desmarcar todas as métricas para ver todo o conjunto](./media/web-monitor-performance/appinsights-62allchoices.png)

A seleção de qualquer métrica desativa as outras que não podem aparecer no mesmo gráfico.

## <a name="set-alerts"></a>Definir alertas
Para ser notificado por e-mail de valores incomuns de qualquer métrica, adicione um alerta. Pode escolher o envio do e-mail para os administradores da conta ou para endereços de e-mail específicos.

![Screenshot da caixa de diálogo 'Adicionar uma Regra de Alerta', juntamente com imagens ligadas por setas que mostram como chegar à caixa de diálogo do Metrics Explorer.](./media/web-monitor-performance/appinsights-413setMetricAlert.png)

Desaprote o recurso antes das outras propriedades. Não escolha os recursos do webtest se quiser definir alertas sobre métricas de desempenho ou utilização.

Tenha cuidado para observar as unidades em que lhe é pedido que introduza o valor limiar.

*Não vejo o botão "Adicionar Alerta".* - Esta é uma conta de grupo à qual tem acesso apenas de leitura? Verifique com o administrador da conta.

## <a name="diagnosing-issues"></a><a name="diagnosis"></a>Questões de diagnóstico
Aqui ficam algumas dicas para encontrar e diagnosticar problemas de desempenho:

* Configurar [testes web][availability] para ser alertado se o seu web site cair ou responder incorretamente ou lentamente. 
* Compare a contagem de Pedido com outras métricas para ver se falhas ou resposta lenta estão relacionadas com a carga.
* [Insira e procure vestígios][diagnostic] no seu código para ajudar a identificar problemas.
* Monitorize a sua aplicação Web em funcionamento com [live metrics stream][livestream].
* Capture o estado da sua aplicação .NET com [Snapshot Debugger][snapshot].

## <a name="find-and-fix-performance-bottlenecks-with-performance-investigation-experience"></a>Encontrar e corrigir estrangulamentos de desempenho com experiência de investigação de desempenho

Você pode usar a experiência de investigação de desempenho para rever operações de desempenho lento na sua aplicação Web. Pode selecionar rapidamente uma operação lenta específica e utilizar [o Profiler](./profiler.md) para fazer radiar as operações lentas até ao código. Utilizando a nova distribuição de duração mostrada para a operação selecionada, pode rapidamente avaliar o quão má é a experiência para os seus clientes. Pode ver quantas das suas interações foram impactadas para cada operação lenta. No exemplo seguinte, decidimos dar uma olhada mais atenta à experiência da operação GET Clientes/Detalhes. Na distribuição da duração, podemos ver que há três picos. O pico mais à esquerda é de cerca de 400 ms e representa uma grande experiência de resposta. O pico médio é de cerca de 1,2 s e representa uma experiência medíocre. Finalmente, no 3.6 s temos outro pequeno pico que representa a experiência percentil 99, o que provavelmente fará com que os nossos clientes saiam insatisfeitos. Essa experiência é dez vezes mais lenta do que a grande experiência para a mesma operação. 

![GET Clientes/Detalhes três picos de duração](./media/web-monitor-performance/PerformanceTriageViewZoomedDistribution.png)

Para obter uma melhor noção das experiências do utilizador para esta operação, podemos selecionar um intervalo de tempo maior. Podemos então também reduzir o tempo numa janela de tempo específica onde a operação foi lenta. No exemplo seguinte, mudamos do intervalo de tempo padrão de 24 horas para o intervalo de 7 dias e depois zoomed para o intervalo de tempo 9:47 para 12:47 entre Tue o 12º e Wed o 13º. Tanto a distribuição da duração como o número de amostras e de perfis foram atualizados à direita.

![Screenshot mostra Performance (pré-visualização) com três picos de duração em 7 dias de intervalo com uma janela de tempo](./media/web-monitor-performance/PerformanceTriageView7DaysZoomedTrend.png)

Para nos estreitarmos nas experiências lentas, vamos aproximar-nos das durações que caem entre o percentil 95 e o percentil 99. Estes representam os 4% das interações dos utilizadores que foram lentas.

![Screenshot mostra CLIENTES GET/Detalhes três picos de duração em 7 dias de intervalo com uma janela de tempo.](./media/web-monitor-performance/PerformanceTriageView7DaysZoomedTrendZoomed95th99th.png)

Podemos agora olhar para as amostras representativas, clicando no botão Amostras, ou nos vestígios do perfil representativo, clicando no botão de traços profiler. Neste exemplo, existem quatro vestígios que foram recolhidos para clientes GET/Detalhes na janela de tempo e duração de alcance de interesse.

Às vezes, o problema não está no seu código, mas sim numa dependência que o seu código chama. Pode mudar para o separador Dependencies na visão de triagem de desempenho para investigar dependências tão lentas. Por padrão, a visão de desempenho é de médias de tendência, mas o que realmente quer olhar é o percentil 95 (ou o 99º, caso esteja a monitorizar um serviço maduro). No exemplo seguinte, concentrámo-nos na lenta dependência do Azure BLOB, onde chamamos put fabrikamaccount. As boas experiências aglomeram-se em torno de 40 ms, enquanto as chamadas lentas para a mesma dependência são três vezes mais lentas, agrupando cerca de 120 ms. Não são precisos muitos destes pedidos para somar para que a operação da respetiva operação abrande visivelmente. Pode perfurar as amostras representativas e os vestígios do perfil, tal como pode com o separador Operações.

![GET Clientes/Detalhes três picos de duração em 7 dias de intervalo com uma janela de tempo](./media/web-monitor-performance/SlowDependencies95thTrend.png)

A experiência de investigação de desempenho mostra insights relevantes juntamente com o conjunto de amostras em que decidiu focar-se. A melhor maneira de olhar para todas as informações disponíveis é mudar para um intervalo de tempo de 30 dias e, em seguida, selecionar Overall para ver insights em todas as operações do mês passado.

![A screenshot mostra Performance (pré-visualização) com a janela geral aberta e Insights selecionadas.](./media/web-monitor-performance/Performance30DayOveralllnsights.png)


## <a name="next-steps"></a><a name="next"></a>Passos seguintes
[Testes web][availability] - Tenha pedidos web enviados para a sua aplicação em intervalos regulares de todo o mundo.

[Capturar e pesquisar vestígios de diagnóstico][diagnostic] - Insira as chamadas de traços e penetre nos resultados para identificar problemas.

[Rastreio de utilização][usage] - Descubra como as pessoas usam a sua aplicação.

[Resolução de problemas][qna] - e Q & A



<!--Link references-->

[availability]: ./monitor-web-app-availability.md
[diagnostic]: ./diagnostic-search.md
[greenbrown]: ./asp-net.md
[qna]: ../faq.md
[redfield]: ./monitor-performance-live-website-now.md
[start]: ./app-insights-overview.md
[usage]: usage-overview.md
[livestream]: ./live-stream.md
[snapshot]: ./snapshot-debugger.md

