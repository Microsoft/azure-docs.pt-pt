---
title: Monitorize a saúde e o uso da sua aplicação com Insights de Aplicação
description: Começar com a Aplicação Insights. Analise a utilização, disponibilidade e desempenho das suas aplicações no local ou microsoft Azure.
ms.topic: conceptual
ms.date: 05/10/2018
ms.reviewer: sdash
ms.openlocfilehash: e398f23559729580ae4ad8b6507c2f09328052b5
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79275623"
---
# <a name="monitor-performance-in-web-applications"></a>Monitorizar o desempenho nas aplicações Web


Certifique-se de que a sua aplicação está a ter um bom desempenho e descubra rapidamente sobre eventuais falhas. Os Insights de [Aplicação][start] irão informá-lo sobre quaisquer problemas de desempenho e exceções, e ajudá-lo-á a encontrar e diagnosticar as causas principais.

Os Insights de Aplicação podem monitorizar tanto a Java como ASP.NET aplicações e serviços web, serviços WCF. Podem ser hospedados no local, em máquinas virtuais ou como websites do Microsoft Azure. 

Do lado do cliente, o Application Insights pode tirar telemetria a partir de páginas web e uma grande variedade de dispositivos, incluindo aplicações iOS, Android e Windows Store.

## <a name="setup"></a>Configurar a monitorização do desempenho
Se ainda não adicionou Informações de Aplicação ao seu projeto (isto é, se não tiver ApplicationInsights.config), escolha uma destas formas de começar:

* [ASP.NET aplicações web](../../azure-monitor/app/asp-net.md)
  * [Adicionar monitorização de exceção](../../azure-monitor/app/asp-net-exceptions.md)
  * [Adicionar monitorização da dependência](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Aplicativos web Java EE](../../azure-monitor/app/java-get-started.md)
  * [Adicionar monitorização da dependência](../../azure-monitor/app/java-agent.md)

## <a name="view"></a>Explorar métricas de desempenho
No [portal Azure,](https://portal.azure.com)navegue para o recurso Application Insights que configura para a sua aplicação. A lâmina de visão geral mostra dados básicos de desempenho:

Clique em qualquer gráfico para ver mais detalhes e para ver resultados por um período mais longo. Por exemplo, clique no azulejo De pedidos e, em seguida, selecione um intervalo de tempo:

![Clique para mais dados e selecione um intervalo de tempo](./media/web-monitor-performance/appinsights-48metrics.png)

Clique num gráfico para escolher quais as métricas que exibe, ou adicione um novo gráfico e selecione as suas métricas:

![Clique num gráfico para escolher métricas](./media/web-monitor-performance/appinsights-61perfchoices.png)

> [!NOTE]
> **Desfaça todas as métricas** para ver a seleção completa que está disponível. As métricas caem em grupos; quando qualquer membro de um grupo é selecionado, apenas aparecem os outros membros desse grupo.

## <a name="metrics"></a>O que significa tudo isto? Telhas e relatórios de desempenho
Há várias métricas de desempenho que pode obter. Vamos começar com aqueles que aparecem por defeito na lâmina de aplicação.

### <a name="requests"></a>Pedidos
O número de pedidos http recebidos num período determinado. Compare-o com os resultados de outros relatórios para ver como a sua aplicação se comporta à medida que a carga varia.

Os pedidos http incluem todos os pedidos GET ou POST para páginas, dados e imagens.

Clique no azulejo para obter contagens para URLs específicos.

### <a name="average-response-time"></a>Tempo médio de resposta
Mede o tempo entre um pedido web que entra na sua aplicação e a resposta que está a ser devolvida.

Os pontos mostram uma média em movimento. Se houver muitos pedidos, pode haver alguns que se desviam da média sem um pico ou mergulho óbvio no gráfico.

Procure picos incomuns. Em geral, espera-se que o tempo de resposta aumente com o aumento dos pedidos. Se o aumento for desproporcionado, a sua aplicação pode estar a atingir um limite de recursos como cpU ou a capacidade de um serviço que utiliza.

Clique no azulejo para obter tempos para URLs específicos.

![](./media/web-monitor-performance/appinsights-42reqs.png)

### <a name="slowest-requests"></a>Pedidos mais lentos
![](./media/web-monitor-performance/appinsights-44slowest.png)

Mostra quais os pedidos que podem precisar de afinação de desempenho.

### <a name="failed-requests"></a>Pedidos falhados
![](./media/web-monitor-performance/appinsights-46failed.png)

Uma contagem de pedidos que atirou exceções não apanhadas.

Clique no azulejo para ver os detalhes de falhas específicas e selecione um pedido individual para ver os seus detalhes. 

Apenas uma amostra representativa de falhas é retida para inspeção individual.

### <a name="other-metrics"></a>Outras métricas
Para ver que outras métricas pode visualizar, clique num gráfico e, em seguida, desmarque todas as métricas para ver o conjunto completo disponível. Clique em (i) para ver a definição de cada métrica.

![Desmarque todas as métricas para ver todo o conjunto](./media/web-monitor-performance/appinsights-62allchoices.png)

Selecionar qualquer métrica desativa os outros que não podem aparecer no mesmo gráfico.

## <a name="set-alerts"></a>Definir alertas
Para ser notificado por e-mail de valores incomuns de qualquer métrica, adicione um alerta. Pode optar por enviar o e-mail aos administradores da conta ou a endereços de e-mail específicos.

![](./media/web-monitor-performance/appinsights-413setMetricAlert.png)

Desloque o recurso antes das outras propriedades. Não escolha os recursos webtest se pretender definir alertas sobre métricas de desempenho ou de utilização.

Tenha cuidado para observar as unidades em que lhe é pedido que entre no valor limiar.

*Não vejo o botão Add Alert.* - É uma conta de grupo à qual tem acesso apenas a leitura? Verifique com o administrador da conta.

## <a name="diagnosis"></a>Questões de diagnóstico
Aqui ficam algumas dicas para encontrar e diagnosticar problemas de desempenho:

* Instale [testes web][availability] para ser alertado se o seu web site descer ou responder incorretamente ou lentamente. 
* Compare a contagem de Pedidos com outras métricas para ver se falhas ou resposta lenta estão relacionadas com a carga.
* [Insira e procure as declarações][diagnostic] de rastreio no seu código para ajudar a identificar problemas.
* Monitorize a sua aplicação Web em funcionamento com live [Metrics Stream][livestream].
* Capture o estado da sua aplicação .NET com [Snapshot Debugger][snapshot].

## <a name="find-and-fix-performance-bottlenecks-with-performance-investigation-experience"></a>Encontrar e corrigir estrangulamentos de desempenho com experiência de investigação de desempenho

Você pode usar a experiência de investigação de desempenho para rever operações de desempenho lento na sua aplicação Web. Pode selecionar rapidamente uma operação lenta específica e utilizar o [Profiler](../../azure-monitor/app/profiler.md) para criar as operações lentas para o código. Utilizando a nova distribuição de duração mostrada para a operação selecionada, pode rapidamente avaliar o quão má é a experiência para os seus clientes. Pode ver quantas interações do seu utilizador foram impactadas para cada operação lenta. No exemplo seguinte, decidimos olhar mais de perto a experiência para a operação GET Clientes/Detalhes. Na distribuição de duração, podemos ver que há três picos. O pico mais à esquerda é de cerca de 400 ms e representa uma grande experiência responsiva. O pico médio é de cerca de 1,2 s e representa uma experiência medíocre. Finalmente, nos 3,6 s temos outro pequeno pico que representa a 99ª experiência percentil, o que é provável que faça com que os nossos clientes saiam insatisfeitos. Essa experiência é dez vezes mais lenta do que a grande experiência para a mesma operação. 

![OBTER Clientes/Detalhes três picos de duração](./media/web-monitor-performance/PerformanceTriageViewZoomedDistribution.png)

Para ter uma melhor noção das experiências do utilizador para esta operação, podemos selecionar uma maior gama de tempo. Em seguida, podemos também reduzir a tempo numa janela de tempo específica onde a operação foi lenta. No exemplo seguinte, passamos do intervalo de tempo padrão de 24 horas para o intervalo de 7 dias e, em seguida, ampliamos para a janela de tempo 9:47 para 12:47 entre Tue 12 e Wed 13. Tanto a distribuição da duração como o número de amostras e de perfis foram atualizados à direita.

![CLIENTES/Detalhes GET Três picos de duração em 7 dias com uma janela de tempo](./media/web-monitor-performance/PerformanceTriageView7DaysZoomedTrend.png)

Para reduzir as experiências lentas, nós aproximamos as durações que caem entre o 95º e o percentil 99. Estes representam os 4% das interações dos utilizadores que foram lentas.

![CLIENTES/Detalhes GET Três picos de duração em 7 dias com uma janela de tempo](./media/web-monitor-performance/PerformanceTriageView7DaysZoomedTrendZoomed95th99th.png)

Podemos agora olhar para as amostras representativas, clicando no botão Amostras, ou nos traços de perfil representativo, clicando no botão de rastreios do Profiler. Neste exemplo, existem quatro vestígios que foram recolhidos para clientes/detalhes get no período de tempo e duração de tempo de juros.

Às vezes, o problema não estará no seu código, mas sim numa dependência das suas chamadas de código. Pode mudar para o separador Dependencies na visão de triagem de desempenho para investigar tais dependências lentas. Por padrão, a visão de desempenho é a tendência média, mas o que realmente quer ver é o percentil 95 (ou o 99º, no caso de estar a monitorizar um serviço maduro). No exemplo seguinte, focámo-nos na lenta dependência do Azure BLOB, onde chamamos PUT fabrikamaccount. As boas experiências agrupam-se em torno de 40 ms, enquanto as chamadas lentas para a mesma dependência são três vezes mais lentas, agrucontando cerca de 120 ms. Não são precisos muitas destas chamadas para somar para fazer com que a respetiva operação abrande visivelmente. Pode perfurar as amostras representativas e os vestígios do perfil, tal como pode com o separador Operações.

![CLIENTES/Detalhes GET Três picos de duração em 7 dias com uma janela de tempo](./media/web-monitor-performance/SlowDependencies95thTrend.png)

A experiência de investigação de desempenho mostra insights relevantes juntamente com o conjunto de amostras em que decidiu focar-se. A melhor maneira de ver todas as informações disponíveis é mudar para um intervalo de 30 dias e, em seguida, selecionar Global para ver insights em todas as operações durante o mês passado.

![CLIENTES/Detalhes GET Três picos de duração em 7 dias com uma janela de tempo](./media/web-monitor-performance/Performance30DayOveralllnsights.png)


## <a name="next"></a>Passos seguintes
[Testes][availability] web - Tenha pedidos web enviados para a sua aplicação em intervalos regulares de todo o mundo.

[Capturar e pesquisar vestígios][diagnostic] de diagnóstico - Insira chamadas de rastreio e vasculhe os resultados para identificar problemas.

[Rastreio de utilização][usage] - Descubra como as pessoas usam a sua aplicação.

[Resolução de problemas][qna] - e Q & A



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



