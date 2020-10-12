---
title: Análise de eventos de tecido de serviço Azure com insights de aplicações
description: Saiba como visualizar e analisar eventos utilizando o Application Insights para monitorização e diagnóstico de clusters de tecidos de serviço Azure.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: a912b7a6d918a40aaae54c9b177250dc3c30c84d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86256565"
---
# <a name="event-analysis-and-visualization-with-application-insights"></a>Análise e visualização de eventos com Insights de Aplicação

Parte do Azure Monitor, Application Insights é uma plataforma extensível para monitorização e diagnóstico de aplicações. Inclui uma poderosa ferramenta de análise e consulta, painel de instrumentos personalizável e visualizações, e outras opções, incluindo alerta automatizado. A integração da Application Insights com o Service Fabric inclui experiências de ferramentas para o Visual Studio e o portal Azure, bem como métricas específicas do Service Fabric, proporcionando uma experiência abrangente de registo fora da caixa. Apesar de muitos registos serem automaticamente criados e recolhidos para si com o Application Insights, recomendamos que adicione mais registos personalizados às suas aplicações para criar uma experiência de diagnóstico mais rica.

Este artigo ajuda a abordar as seguintes questões comuns:

* Como sei o que se passa dentro da minha candidatura e serviços e recolha de telemetria?
* Como é que resolvo os problemas da minha aplicação, especialmente os serviços que comunicam entre si?
* Como recebo métricas sobre como os meus serviços estão a funcionar, por exemplo, tempo de carregamento de página, pedidos HTTP?

O objetivo deste artigo é mostrar como obter insights e resolução de problemas a partir de Insights de Aplicação. Se você gostaria de aprender a configurar e configurar Insights de aplicação com tecido de serviço, confira este [tutorial](service-fabric-tutorial-monitoring-aspnet.md).

## <a name="monitoring-in-application-insights"></a>Monitorização em Insights de Aplicações

A Application Insights tem uma experiência rica fora da caixa ao utilizar o Tecido de Serviço. Na página geral, o Application Insights fornece informações fundamentais sobre o seu serviço, como o tempo de resposta e o número de pedidos processados. Ao clicar no botão 'Procurar' no topo, pode ver uma lista de pedidos recentes na sua aplicação. Além disso, você seria capaz de ver pedidos falhados aqui e diagnosticar que erros podem ter ocorrido.

![Visão geral de insights de aplicação](media/service-fabric-diagnostics-event-analysis-appinsights/ai-overview.png)

No painel direito na imagem anterior, existem dois tipos principais de entradas na lista: pedidos e eventos. Os pedidos são feitos para a API da app através de pedidos HTTP neste caso, e os eventos são eventos personalizados, que atuam como telemetria que pode adicionar em qualquer lugar do seu código. Pode explorar ainda mais as suas aplicações na [API application Insights para eventos e métricas personalizados.](../azure-monitor/app/api-custom-events-metrics.md) Clicar num pedido mostraria mais detalhes, tal como mostrado na imagem seguinte, incluindo dados específicos do Service Fabric, que são recolhidos no pacote nuget do Serviço de Insights de Aplicação. Esta informação é útil para resolver problemas e saber qual é o estado da sua aplicação, e toda esta informação é pes pes pesjável dentro do Application Insights

![Detalhes do pedido de insights de aplicação](media/service-fabric-diagnostics-event-analysis-appinsights/ai-request-details.png)

A Application Insights tem uma visão designada para consulta contra todos os dados que entram. Clique em "Metrics Explorer" no topo da página 'Visão Geral' para navegar no portal 'Insights de Aplicações'. Aqui pode executar consultas contra eventos personalizados mencionados antes, pedidos, exceções, contadores de desempenho e outras métricas usando a linguagem de consulta Kusto. O exemplo a seguir mostra todos os pedidos nas últimas 1 hora.

![Detalhes do pedido de insights de aplicação](media/service-fabric-diagnostics-event-analysis-appinsights/ai-metrics-explorer.png)

Para explorar ainda mais as capacidades do portal Application Insights, dirija-se à [documentação](../azure-monitor/app/overview-dashboard.md)do portal Application Insights .

### <a name="configuring-application-insights-with-eventflow"></a>Configurar insights de aplicações com EventFlow

Se estiver a utilizar o EventFlow para agregar eventos, certifique-se de importar o `Microsoft.Diagnostics.EventFlow.Outputs.ApplicationInsights` pacote NuGet. É necessário o seguinte código na secção de *saídas* do *eventFlowConfig.jsem:*

```json
"outputs": [
    {
        "type": "ApplicationInsights",
        "instrumentationKey": "***ADD INSTRUMENTATION KEY HERE***"
    }
]
```

Certifique-se de que escoda as alterações necessárias nos seus filtros, bem como inclua quaisquer outras entradas (juntamente com as respetivas embalagens NuGet).

## <a name="application-insights-sdk"></a>Insights de Aplicação SDK

Recomenda-se a utilização do EventFlow e do WAD como soluções de agregação, pois permitem uma abordagem mais modular aos diagnósticos e monitorização, ou seja, se pretender alterar as suas saídas do EventFlow, não requer qualquer alteração à sua instrumentação real, apenas uma simples modificação no seu ficheiro config. Se, no entanto, decidir investir na utilização de Application Insights e não for suscetível de mudar para uma plataforma diferente, deverá procurar utilizar o novo SDK da Application Insights para agregar eventos e enviá-los para o Application Insights. Isto significa que deixará de ter de configurar o EventFlow para enviar os seus dados para o Application Insights, mas irá instalar o pacote DeRume de Tecido de Serviço da AplicaçãoInsight. Detalhes sobre o pacote podem ser encontrados [aqui.](https://github.com/Microsoft/ApplicationInsights-ServiceFabric)

[O suporte de Aplicações Insights para Microservices e Contentores](https://azure.microsoft.com/blog/app-insights-microservices/) mostra-lhe algumas das novas funcionalidades que estão a ser trabalhadas (atualmente ainda em versão beta), que lhe permitem ter opções de monitorização fora da caixa mais ricas com o Application Insights. Estes incluem o rastreio de dependência (usado na construção de um AppMap de todos os seus serviços e aplicações num cluster e a comunicação entre eles), e uma melhor correlação de vestígios provenientes dos seus serviços (ajuda a identificar melhor um problema no fluxo de trabalho de uma aplicação ou serviço).

Se estiver a desenvolver em .NET e provavelmente utilizar alguns dos modelos de programação da Service Fabric, e estiver disposto a usar o Application Insights como plataforma para visualizar e analisar dados de eventos e registos, então recomendamos que vá pela rota SDK application Insights como o seu fluxo de trabalho de monitorização e diagnóstico. Leia [isto](../azure-monitor/app/asp-net-more.md) e [isto](../azure-monitor/app/asp-net-trace-logs.md) para começar a usar o Application Insights para recolher e exibir os seus registos.

## <a name="navigating-the-application-insights-resource-in-azure-portal"></a>Navegar no recurso Application Insights no portal Azure

Uma vez configurado o Application Insights como uma saída para os seus eventos e registos, as informações devem começar a aparecer no seu recurso Application Insights em poucos minutos. Navegue para o recurso Application Insights, que o levará ao painel de recursos de Insights de Aplicação. Clique em **Procurar** na barra de tarefas 'Insights' de Aplicação para ver os mais recentes vestígios que recebeu e para poder filtrar através deles.

*O Metrics Explorer* é uma ferramenta útil para criar dashboards personalizados com base em métricas que as suas aplicações, serviços e cluster podem estar a reportar. Consulte [métricas de exploração em Insights](../azure-monitor/platform/metrics-charts.md) de aplicações para configurar alguns gráficos para si mesmo com base nos dados que está a recolher.

Clicar em **Analytics** irá levá-lo ao portal Application Insights Analytics, onde pode consultar eventos e vestígios com maior alcance e opcionalidade. Leia mais sobre isto na [Analytics in Application Insights](../azure-monitor/log-query/log-query-overview.md).

## <a name="next-steps"></a>Passos seguintes

* [Configurar alertas em IA](../azure-monitor/platform/alerts-log.md) para ser notificado sobre alterações no desempenho ou utilização
* [Smart Detection in Application Insights](../azure-monitor/app/proactive-diagnostics.md) realiza uma análise proativa da telemetria enviada para a Application Insights para alertá-lo sobre potenciais problemas de desempenho
