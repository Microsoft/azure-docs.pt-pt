---
title: Análise de eventos de tecido de serviço Azure com insights de aplicação
description: Saiba visualizar e analisar eventos utilizando insights de aplicação para monitorização e diagnóstico de clusters de tecido de serviço Azure.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 5c80dc9f350fec30469a9bce2ed836c276ef3a7f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75464748"
---
# <a name="event-analysis-and-visualization-with-application-insights"></a>Análise de eventos e visualização com Insights de Aplicação

Parte do Azure Monitor, Application Insights é uma plataforma extensível para monitorização e diagnóstico de aplicações. Inclui uma poderosa ferramenta de análise e consulta, dashboard e visualizações personalizáveis, e outras opções, incluindo alerta automatizado. A integração da Application Insights com o Service Fabric inclui experiências de ferramentas para o Visual Studio e o portal Azure, bem como métricas específicas do Service Fabric, proporcionando uma experiência abrangente de logagem fora da caixa. Apesar de muitos registos serem automaticamente criados e recolhidos para si com Application Insights, recomendamos que adicione mais loga personalizada às suas aplicações para criar uma experiência de diagnóstico mais rica.

Este artigo ajuda a resolver as seguintes questões comuns:

* Como sei o que se passa dentro da minha candidatura e serviços e recolher telemetria?
* Como posso resolver a minha aplicação, especialmente os serviços que comunicam uns com os outros?
* Como posso obter métricas sobre como os meus serviços estão a funcionar, por exemplo, o tempo de carregamento de página, pedidos HTTP?

O objetivo deste artigo é mostrar como obter insights e problemas a partir de dentro da Application Insights. Se quiser aprender a configurar e configurar insights de aplicação com tecido de serviço, consulte este [tutorial](service-fabric-tutorial-monitoring-aspnet.md).

## <a name="monitoring-in-application-insights"></a>Monitorização em Insights de Aplicação

Application Insights tem uma experiência rica fora da caixa ao usar o Tecido de Serviço. Na página geral, o Application Insights fornece informações-chave sobre o seu serviço, tais como o tempo de resposta e o número de pedidos processados. Ao clicar no botão 'Procurar' na parte superior, pode ver uma lista de pedidos recentes na sua aplicação. Além disso, você poderia ver pedidos falhados aqui e diagnosticar que erros podem ter ocorrido.

![Visão geral dos insights da aplicação](media/service-fabric-diagnostics-event-analysis-appinsights/ai-overview.png)

No painel certo na imagem anterior, existem dois tipos principais de entradas na lista: pedidos e eventos. Os pedidos são chamadas feitas para a API da aplicação através de pedidos HTTP neste caso, e os eventos são eventos personalizados, que funcionam como telemetria que você pode adicionar em qualquer lugar do seu código. Pode ainda explorar a instrumentação das suas aplicações na [Application Insights API para eventos e métricas personalizadas.](../azure-monitor/app/api-custom-events-metrics.md) Clicar num pedido mostraria mais detalhes, como mostrado na imagem seguinte, incluindo dados específicos do Tecido de Serviço, que é recolhido no pacote de nuget de tecido de serviço de Insights de Aplicação Insights. Esta informação é útil para resolução de problemas e saber qual é o estado da sua aplicação, e toda esta informação é pesquisável dentro de Application Insights

![Dados de pedido de insights de aplicação](media/service-fabric-diagnostics-event-analysis-appinsights/ai-request-details.png)

A Application Insights tem uma visão designada para consulta de todos os dados que entram. Clique em "Metrics Explorer" no topo da página 'Overview' para navegar para o portal Insights de Aplicação. Aqui você pode executar consultas contra eventos personalizados mencionados antes, pedidos, exceções, contadores de desempenho e outras métricas usando a linguagem de consulta Kusto. O exemplo que se segue mostra todos os pedidos na última hora.

![Dados de pedido de insights de aplicação](media/service-fabric-diagnostics-event-analysis-appinsights/ai-metrics-explorer.png)

Para explorar ainda mais as capacidades do portal Application Insights, dirija-se à [documentação](../azure-monitor/app/app-insights-dashboards.md)do portal Application Insights .

### <a name="configuring-application-insights-with-eventflow"></a>Configurar insights de aplicação com EventFlow

Se estiver a utilizar o EventFlow para `Microsoft.Diagnostics.EventFlow.Outputs.ApplicationInsights`agregar eventos, certifique-se de importar o pacote NuGet. O seguinte código é exigido na secção de *saídas* do *eventoFlowConfig.json:*

```json
"outputs": [
    {
        "type": "ApplicationInsights",
        "instrumentationKey": "***ADD INSTRUMENTATION KEY HERE***"
    }
]
```

Certifique-se de que faz as alterações necessárias nos seus filtros, bem como inclua quaisquer outras inputs (juntamente com os respetivos pacotes NuGet).

## <a name="application-insights-sdk"></a>Insights de aplicação SDK

Recomenda-se utilizar o EventFlow e o WAD como soluções de agregação, pois permitem uma abordagem mais modular aos diagnósticos e monitorização, ou seja, se pretender alterar as suas saídas do EventFlow, não necessita de alterações na sua instrumentação real, apenas uma simples modificação no seu ficheiro config. Se, no entanto, decidir investir na utilização de Insights de Aplicação e não for provável que mude para uma plataforma diferente, deverá procurar utilizar o novo SDK da Application Insights para agregar eventos e enviá-los para Application Insights. Isto significa que deixará de ter de configurar o EventFlow para enviar os seus dados para Insights de Aplicação, mas irá instalar o pacote NuGet de Tecido de Serviço da ApplicationInsight. Os detalhes sobre o pacote podem ser encontrados [aqui.](https://github.com/Microsoft/ApplicationInsights-ServiceFabric)

O suporte da [Application Insights para Microservices e Contentores](https://azure.microsoft.com/blog/app-insights-microservices/) mostra-lhe algumas das novas funcionalidades que estão a ser trabalhadas (atualmente ainda em versão beta), que lhe permitem ter opções de monitorização fora da caixa mais ricas com insights de aplicação. Estes incluem o rastreio da dependência (usado na construção de um AppMap de todos os seus serviços e aplicações num cluster e a comunicação entre eles), e uma melhor correlação de vestígios provenientes dos seus serviços (ajuda a identificar melhor um problema no fluxo de trabalho de uma aplicação ou serviço).

Se estiver a desenvolver-se em .NET e provavelmente utilizará alguns dos modelos de programação do Service Fabric, e estiver disposto a usar o Application Insights como plataforma para visualizar e analisar dados de eventos e registos, recomendamos que vá através da rota SDK de Insights de Aplicação como o seu fluxo de trabalho de monitorização e diagnóstico. Leia [isto](../azure-monitor/app/asp-net-more.md) e [este para](../azure-monitor/app/asp-net-trace-logs.md) começar a usar insights de aplicação para recolher e exibir os seus registos.

## <a name="navigating-the-application-insights-resource-in-azure-portal"></a>Navegar no recurso Application Insights no portal Azure

Depois de configurar os Insights de Aplicação como uma saída para os seus eventos e registos, as informações devem começar a aparecer no seu recurso Application Insights em poucos minutos. Navegue para o recurso Application Insights, que o levará ao painel de recursos da Application Insights. Clique em **Pesquisar** na barra de tarefas Application Insights para ver os mais recentes vestígios que recebeu e para poder filtrar através deles.

*O Metrics Explorer* é uma ferramenta útil para criar dashboards personalizados com base em métricas que as suas aplicações, serviços e cluster podem estar a reportar. Consulte [a Exploreing Metrics in Application Insights](../azure-monitor/app/metrics-explorer.md) para configurar alguns gráficos para si mesmo com base nos dados que está a recolher.

Clicar no **Analytics** irá levá-lo ao portal Application Insights Analytics, onde poderá consultar eventos e vestígios com maior alcance e opcionalidade. Leia mais sobre isso no [Analytics in Application Insights](../azure-monitor/app/analytics.md).

## <a name="next-steps"></a>Passos seguintes

* [Configurar alertas em IA](../azure-monitor/app/alerts.md) para serem notificados sobre alterações no desempenho ou utilização
* [Smart Detection in Application Insights](../azure-monitor/app/proactive-diagnostics.md) realiza uma análise proativa da telemetria enviada para Application Insights para avisá-lo de potenciais problemas de desempenho
