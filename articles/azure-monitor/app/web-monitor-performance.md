---
title: Monitore a integridade e o uso do seu aplicativo com o Application Insights
description: Introdução ao Application Insights. Analise o uso, a disponibilidade e o desempenho de seus aplicativos locais ou Microsoft Azure.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/10/2018
ms.reviewer: sdash
ms.openlocfilehash: ebf6fa6d3dac6c63dfaa349a77a08bc81d402ef8
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899304"
---
# <a name="monitor-performance-in-web-applications"></a>Monitorizar o desempenho nas aplicações Web


Verifique se seu aplicativo está com um bom desempenho e descubra rapidamente as falhas. [Application insights][start] informará sobre quaisquer problemas de desempenho e exceções, além de ajudá-lo a encontrar e diagnosticar as causas raiz.

Application Insights pode monitorar aplicativos e serviços Web Java e ASP.NET, serviços WCF. Eles podem ser hospedados localmente, em máquinas virtuais ou como Microsoft Azure sites. 

No lado do cliente, Application Insights pode tirar telemetria de páginas da Web e uma ampla variedade de dispositivos, incluindo aplicativos iOS, Android e Windows Store.

## <a name="setup"></a>Configurar o monitoramento de desempenho
Se você ainda não adicionou Application Insights ao seu projeto (ou seja, se ele não tiver o ApplicationInsights. config), escolha uma dessas maneiras para começar:

* [Aplicativos Web ASP.NET](../../azure-monitor/app/asp-net.md)
  * [Adicionar monitoramento de exceção](../../azure-monitor/app/asp-net-exceptions.md)
  * [Adicionar monitoramento de dependência](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Aplicativos Web Java EE](../../azure-monitor/app/java-get-started.md)
  * [Adicionar monitoramento de dependência](../../azure-monitor/app/java-agent.md)

## <a name="view"></a>Explorando métricas de desempenho
Na [portal do Azure](https://portal.azure.com), navegue até o recurso de Application insights que você configurou para seu aplicativo. A folha visão geral mostra os dados de desempenho básicos:

Clique em qualquer gráfico para ver mais detalhes e para ver os resultados de um período mais longo. Por exemplo, clique no bloco solicitações e selecione um intervalo de tempo:

![Clique para obter mais dados e selecione um intervalo de tempo](./media/web-monitor-performance/appinsights-48metrics.png)

Clique em um gráfico para escolher quais métricas são exibidas ou adicione um novo gráfico e selecione suas métricas:

![Clique em um grafo para escolher métricas](./media/web-monitor-performance/appinsights-61perfchoices.png)

> [!NOTE]
> **Desmarque todas as métricas** para ver a seleção completa que está disponível. As métricas se enquadram em grupos; Quando qualquer membro de um grupo é selecionado, somente os outros membros desse grupo são exibidos.

## <a name="metrics"></a>O que tudo isso significa? Blocos de desempenho e relatórios
Há várias métricas de desempenho que você pode obter. Vamos começar com os que aparecem por padrão na folha do aplicativo.

### <a name="requests"></a>Pedidos
O número de solicitações HTTP recebidas em um período especificado. Compare isso com os resultados em outros relatórios para ver como seu aplicativo se comporta conforme a carga varia.

As solicitações HTTP incluem todas as solicitações GET ou POST para páginas, dados e imagens.

Clique no bloco para obter as contagens de URLs específicas.

### <a name="average-response-time"></a>Tempo médio de resposta
Mede o tempo entre uma solicitação da Web inserindo seu aplicativo e a resposta que está sendo retornada.

Os pontos mostram uma média móvel. Se houver muitas solicitações, pode haver algumas que se desvie da média sem um pico ou DIP óbvio no grafo.

Procure picos incomuns. Em geral, espere o tempo de resposta para aumentar com um aumento nas solicitações. Se a elevação for desproporcional, seu aplicativo poderá estar atingindo um limite de recursos, como CPU ou a capacidade de um serviço que ele usa.

Clique no bloco para obter horários para URLs específicas.

![](./media/web-monitor-performance/appinsights-42reqs.png)

### <a name="slowest-requests"></a>Solicitações mais lentas
![](./media/web-monitor-performance/appinsights-44slowest.png)

Mostra quais solicitações podem precisar de ajuste de desempenho.

### <a name="failed-requests"></a>Pedidos falhados
![](./media/web-monitor-performance/appinsights-46failed.png)

Uma contagem de solicitações que geraram exceções não capturadas.

Clique no bloco para ver os detalhes de falhas específicas e selecione uma solicitação individual para ver seus detalhes. 

Apenas uma amostra representativa de falhas é mantida para inspeção individual.

### <a name="other-metrics"></a>Outras métricas
Para ver quais outras métricas você pode exibir, clique em um grafo e, em seguida, desmarque todas as métricas para ver o conjunto completo disponível. Clique em (i) para ver a definição de cada métrica.

![Desmarque todas as métricas para ver o conjunto inteiro](./media/web-monitor-performance/appinsights-62allchoices.png)

Selecionar qualquer métrica desabilita os outros que não podem aparecer no mesmo gráfico.

## <a name="set-alerts"></a>Definir alertas
Para ser notificado por email de valores incomuns de qualquer métrica, adicione um alerta. Você pode optar por enviar o email para os administradores da conta ou para endereços de email específicos.

![](./media/web-monitor-performance/appinsights-413setMetricAlert.png)

Defina o recurso antes das outras propriedades. Não escolha os recursos do WebTest se desejar definir alertas sobre métricas de desempenho ou de uso.

Tenha cuidado para observar as unidades nas quais você será solicitado a inserir o valor do limite.

*Não vejo o botão adicionar alerta.* -Esta é uma conta de grupo à qual você tem acesso somente leitura? Verifique com o administrador da conta.

## <a name="diagnosis"></a>Diagnosticando problemas
Aqui estão algumas dicas para localizar e diagnosticar problemas de desempenho:

* Configure [testes da Web][availability] a serem alertados se o seu site ficar inativo ou responder de forma incorreta ou lenta. 
* Compare a contagem de solicitações com outras métricas para ver se falhas ou respostas lentas estão relacionadas ao carregamento.
* [Inserir e Pesquisar instruções de rastreamento][diagnostic] em seu código para ajudar a identificar problemas.
* Monitore seu aplicativo Web em operação com o [Live Metrics Stream][livestream].
* Capture o estado do seu aplicativo .NET com [depurador de instantâneos][snapshot].

## <a name="find-and-fix-performance-bottlenecks-with-performance-investigation-experience"></a>Encontre e corrija gargalos de desempenho com experiência de investigação de desempenho

Você pode usar a experiência de investigação de desempenho para examinar as operações de execução lenta em seu aplicativo Web. Você pode selecionar rapidamente uma operação lenta específica e usar o [Profiler](../../azure-monitor/app/profiler.md) para que a raiz cause o código das operações lentas. Usando a nova distribuição de duração mostrada para a operação selecionada, você pode rapidamente avaliar como a experiência é boa para seus clientes. Você pode ver quantas interações de usuário foram afetadas para cada operação lenta. No exemplo a seguir, decidimos examinar mais de perto a experiência para a operação obter clientes/detalhes. Na distribuição de duração, podemos ver que há três picos. O pico mais à esquerda está em cerca de 400 MS e representa uma ótima experiência responsiva. O pico médio está em cerca de 1,2 s e representa uma experiência mediana. Por fim, no 3,6 s, temos outro pequeno pico que representa a experiência 99 º percentil, que provavelmente fará com que nossos clientes deixem de ser dessatisfeitos. Essa experiência é dez vezes mais lenta do que a ótima experiência para a mesma operação. 

![OBTER clientes/detalhes três picos de duração](./media/web-monitor-performance/PerformanceTriageViewZoomedDistribution.png)

Para ter uma noção melhor das experiências do usuário para essa operação, podemos selecionar um intervalo de tempo maior. Podemos também restringir o tempo em uma janela de tempo específica em que a operação estava lenta. No exemplo a seguir, mudamos do intervalo de tempo padrão de 24 horas para o intervalo de tempo de 7 dias e, em seguida, aplicamos o zoom na janela de tempo de 9:47 a 12:47 entre as 12 e a quarta-feiras do 13. A distribuição de duração e o número de rastreamentos de exemplo e criador de perfil foram atualizados à direita.

![OBTER clientes/detalhes três picos de duração no intervalo de 7 dias com uma janela de tempo](./media/web-monitor-performance/PerformanceTriageView7DaysZoomedTrend.png)

Para restringir as experiências lentas, vamos ampliar em seguida as durações entre 95 º e 99 º percentil. Elas representam 4% das interações do usuário que estavam lentas.

![OBTER clientes/detalhes três picos de duração no intervalo de 7 dias com uma janela de tempo](./media/web-monitor-performance/PerformanceTriageView7DaysZoomedTrendZoomed95th99th.png)

Agora podemos examinar os exemplos representativos, clicando no botão amostras ou nos rastreamentos do criador de perfil representativos clicando no botão rastreamentos do criador de perfil. Neste exemplo, há quatro rastreamentos que foram coletados para obter clientes/detalhes na janela de tempo e duração do intervalo de interesse.

Às vezes, o problema não estará no seu código, mas em uma dependência que seu código chama. Você pode alternar para a guia dependências na exibição de triagem de desempenho para investigar essas dependências lentas. Por padrão, a exibição de desempenho é a média de tendências, mas o que você realmente deseja examinar é o 95 º percentil (ou o 99 º, caso você esteja monitorando um serviço maduro). No exemplo a seguir, nos concentramos na dependência de BLOBs do Azure lenta, onde chamamos PUT fabrikamaccount. O bom cluster de experiências em cerca de 40 MS, enquanto as chamadas lentas para a mesma dependência são três vezes mais lentas, clusterizando em cerca de 120 ms. Não é necessário que muitas dessas chamadas sejam adicionadas para fazer com que a respectiva operação seja visivelmente lenta. Você pode analisar os exemplos representativos e os rastreamentos do criador de perfil, assim como é possível com a guia operações.

![OBTER clientes/detalhes três picos de duração no intervalo de 7 dias com uma janela de tempo](./media/web-monitor-performance/SlowDependencies95thTrend.png)

A experiência de investigação de desempenho mostra informações relevantes ao longo do conjunto de exemplo em que você decidiu se concentrar. A melhor maneira de examinar todas as informações disponíveis é mudar para um intervalo de tempo de 30 dias e, em seguida, selecionar geral para ver informações em todas as operações do mês passado.

![OBTER clientes/detalhes três picos de duração no intervalo de 7 dias com uma janela de tempo](./media/web-monitor-performance/Performance30DayOveralllnsights.png)


## <a name="next"></a>Passos seguintes
[Testes da Web][availability] -têm solicitações da Web enviadas ao seu aplicativo em intervalos regulares em todo o mundo.

[Capturar e Pesquisar rastreamentos de diagnóstico][diagnostic] – inserir chamadas de rastreamento e examinar os resultados para identificar os problemas.

[Acompanhamento de uso][usage] -Descubra como as pessoas usam seu aplicativo.

[Solução de problemas][qna] -e Q & A



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



