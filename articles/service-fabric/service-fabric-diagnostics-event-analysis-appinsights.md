---
title: Análise de eventos de Service Fabric do Azure com Application Insights
description: Saiba mais sobre como Visualizar e analisar eventos usando Application Insights para monitoramento e diagnóstico de clusters de Service Fabric do Azure.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 5c80dc9f350fec30469a9bce2ed836c276ef3a7f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75464748"
---
# <a name="event-analysis-and-visualization-with-application-insights"></a>Análise e visualização de eventos com Application Insights

Parte do Azure Monitor, Application Insights é uma plataforma extensível para monitoramento e diagnóstico de aplicativos. Ele inclui uma poderosa ferramenta de análise e consulta, painéis personalizáveis e visualizações, além de outras opções, incluindo alertas automatizados. A integração do Application Insights com o Service Fabric inclui experiências de ferramentas para o Visual Studio e o portal do Azure, bem como Service Fabric métricas específicas, fornecendo uma experiência de registro em log abrangente. Embora muitos logs sejam criados e coletados automaticamente para você com Application Insights, recomendamos que você adicione mais logs personalizados aos seus aplicativos para criar uma experiência de diagnóstico mais rica.

Este artigo ajuda a abordar as seguintes perguntas comuns:

* Como fazer saber o que está acontecendo dentro de meu aplicativo e serviços e reunir a telemetria?
* Como fazer solucionar problemas do meu aplicativo, especialmente serviços se comunicando uns com os outros?
* Como fazer obter métricas sobre como meus serviços estão sendo executados, por exemplo, tempo de carregamento de página, solicitações HTTP?

A finalidade deste artigo é mostrar como obter informações e solucionar problemas no Application Insights. Se você quiser saber como configurar e configurar Application Insights com Service Fabric, confira este [tutorial](service-fabric-tutorial-monitoring-aspnet.md).

## <a name="monitoring-in-application-insights"></a>Monitoramento no Application Insights

O Application Insights tem uma experiência pronta para uso ao usar o Service Fabric. Na página Visão geral, Application Insights fornece informações importantes sobre o serviço, como o tempo de resposta e o número de solicitações processadas. Ao clicar no botão ' Pesquisar ' na parte superior, você poderá ver uma lista de solicitações recentes em seu aplicativo. Além disso, você poderá ver as solicitações com falha aqui e diagnosticar quais erros podem ter ocorrido.

![Visão geral de Application Insights](media/service-fabric-diagnostics-event-analysis-appinsights/ai-overview.png)

No painel direito na imagem anterior, há dois tipos principais de entradas na lista: solicitações e eventos. As solicitações são chamadas feitas à API do aplicativo por meio de solicitações HTTP, nesse caso, e os eventos são eventos personalizados, que atuam como telemetria, você pode adicionar qualquer lugar em seu código. Você pode explorar ainda mais a instrumentação de seus aplicativos na [API Application insights para métricas e eventos personalizados](../azure-monitor/app/api-custom-events-metrics.md). Clicar em uma solicitação exibirá mais detalhes, conforme mostrado na imagem a seguir, incluindo dados específicos para Service Fabric, que são coletados no pacote do Application Insights Service Fabric NuGet. Essas informações são úteis para solucionar problemas e saber qual é o estado do seu aplicativo, e todas essas informações são pesquisáveis no Application Insights

![Detalhes da solicitação de Application Insights](media/service-fabric-diagnostics-event-analysis-appinsights/ai-request-details.png)

Application Insights tem uma exibição designada para consultar todos os dados que chegam. Clique em "Metrics Explorer" na parte superior da página de visão geral para navegar até o portal de Application Insights. Aqui, você pode executar consultas em eventos personalizados mencionados antes, solicitações, exceções, contadores de desempenho e outras métricas usando a linguagem de consulta Kusto. O exemplo a seguir mostra todas as solicitações na última 1 hora.

![Detalhes da solicitação de Application Insights](media/service-fabric-diagnostics-event-analysis-appinsights/ai-metrics-explorer.png)

Para explorar ainda mais os recursos do portal de Application Insights, acesse a [documentação do portal do Application insights](../azure-monitor/app/app-insights-dashboards.md).

### <a name="configuring-application-insights-with-eventflow"></a>Configurando Application Insights com EventFlow

Se você estiver usando EventFlow para agregar eventos, certifique-se de importar o pacote NuGet `Microsoft.Diagnostics.EventFlow.Outputs.ApplicationInsights`. O código a seguir é necessário na seção de *saídas* do *eventFlowConfig. JSON*:

```json
"outputs": [
    {
        "type": "ApplicationInsights",
        "instrumentationKey": "***ADD INSTRUMENTATION KEY HERE***"
    }
]
```

Certifique-se de fazer as alterações necessárias em seus filtros, bem como incluir outras entradas (juntamente com seus respectivos pacotes NuGet).

## <a name="application-insights-sdk"></a>SDK do Application Insights

É recomendável usar EventFlow e WAD como soluções de agregação, pois elas permitem uma abordagem mais modular para diagnóstico e monitoramento, ou seja, se você quiser alterar suas saídas do EventFlow, não será necessário alterar para sua instrumentação real, apenas um modificação simples no arquivo de configuração. No entanto, se você decidir investir no uso de Application Insights e não for provável que ele mude para uma plataforma diferente, deverá examinar o uso do Application Insights ' novo SDK para agregar eventos e enviá-los para Application Insights. Isso significa que você não precisará mais configurar o EventFlow para enviar seus dados para Application Insights, mas, em vez disso, instalará o pacote NuGet Service Fabric do ApplicationInsight. Os detalhes sobre o pacote podem ser encontrados [aqui](https://github.com/Microsoft/ApplicationInsights-ServiceFabric).

[Application insights suporte para microservices e contêineres](https://azure.microsoft.com/blog/app-insights-microservices/) mostra alguns dos novos recursos que estão sendo trabalhados (atualmente ainda em versão beta), que permitem que você tenha opções de monitoramento mais avançadas com Application insights. Isso inclui o acompanhamento de dependência (usado na criação de um AppMap de todos os seus serviços e aplicativos em um cluster e a comunicação entre eles) e uma correlação melhor de rastreamentos provenientes de seus serviços (ajuda a identificar melhor um problema no fluxo de trabalho de um aplicativo ou serviço).

Se você estiver desenvolvendo no .NET e provavelmente usará alguns dos modelos de programação de Service Fabric e estiver disposto a usar Application Insights como sua plataforma de visualização e análise de dados de eventos e de log, recomendamos que você passe pelo Application Insights Rota do SDK como seu fluxo de trabalho de monitoramento e diagnóstico. Leia [isso](../azure-monitor/app/asp-net-more.md) para [começar a usar](../azure-monitor/app/asp-net-trace-logs.md) o Application insights para coletar e exibir seus logs.

## <a name="navigating-the-application-insights-resource-in-azure-portal"></a>Navegando no recurso de Application Insights no portal do Azure

Depois de configurar Application Insights como uma saída para seus eventos e logs, as informações devem começar a aparecer no recurso de Application Insights em alguns minutos. Navegue até o recurso Application Insights, que levará você para o painel de recursos do Application Insights. Clique em **Pesquisar** na barra de tarefas Application insights para ver os rastreamentos mais recentes que ele recebeu e para poder filtrá-los.

*Metrics Explorer* é uma ferramenta útil para a criação de painéis personalizados com base em métricas que seus aplicativos, serviços e cluster podem reportar. Consulte [explorando métricas em Application insights](../azure-monitor/app/metrics-explorer.md) para configurar alguns gráficos para você mesmo com base nos dados que você está coletando.

Clicar em **análise** levará você para o portal de análise de Application insights, no qual você pode consultar eventos e rastreamentos com maior escopo e opcionalidade. Leia mais sobre isso em [análise em Application insights](../azure-monitor/app/analytics.md).

## <a name="next-steps"></a>Passos seguintes

* [Configurar alertas no ia](../azure-monitor/app/alerts.md) para ser notificado sobre alterações no desempenho ou no uso
* A [detecção inteligente no Application insights](../azure-monitor/app/proactive-diagnostics.md) executa uma análise proativa da telemetria que está sendo enviada para Application insights para avisá-lo sobre possíveis problemas de desempenho
