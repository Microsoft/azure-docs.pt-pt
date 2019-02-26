---
title: Análise de eventos de recursos de infraestrutura de serviço do Azure com o Application Insights | Documentos da Microsoft
description: Saiba mais sobre como visualizar e analisar eventos com o Application Insights para monitorização e diagnóstico de clusters do Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 7a3abd854ec5e492407d1fbdc8d170f2a27ba1bc
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56816730"
---
# <a name="event-analysis-and-visualization-with-application-insights"></a>Análise de eventos e visualização com o Application Insights

Parte do Azure Monitor, Application Insights é uma plataforma extensível para monitorização de aplicações e de diagnóstico. Ele inclui uma análise poderosas e consulta de ferramenta e dashboards personalizáveis, visualizações e ainda mais opções, incluindo alertas automáticos. Integração do Application Insights com o Service Fabric inclui ferramentas experiências para o portal do Azure e do Visual Studio, bem como métricas específicas do Service Fabric, para proporcionar uma experiência de registo de out-of-the-box abrangente. Embora muitos registos são automaticamente criados e recolhidos por si com o Application Insights, recomendamos que adicionar mais registo personalizado para as aplicações para criar uma experiência mais rica de diagnóstico.

Este artigo ajuda a resolver as seguintes perguntas comuns:

* Como posso saber o que está acontecendo dentro de minha telemetria de aplicações e serviços e coleta?
* Como posso resolver problemas de meu aplicativo, especialmente os serviços comunicam entre si?
* Como posso obter métricas sobre o meus serviços desempenho, por exemplo, o tempo de carregamento de página, pedidos de HTTP?

O objetivo deste artigo é mostrar como obter informações e resolver problemas de dentro do Application Insights. Se gostaria de saber como definir e configurar o Application Insights com o Service Fabric, veja esta [tutorial](service-fabric-tutorial-monitoring-aspnet.md).

## <a name="monitoring-in-application-insights"></a>Monitorização no Application Insights

O Application Insights tem uma avançada dessa experiência caixa ao utilizar o Service Fabric. Na página Descrição geral do Application Insights fornece informações chave sobre o seu serviço, como o tempo de resposta e o número de pedidos processados. Ao clicar no botão 'Search' na parte superior, pode ver uma lista de pedidos de recentes na sua aplicação. Além disso, seria capaz de ver aqui pedidos com falhas e diagnosticar os erros que possam ter ocorrido.

![Descrição geral do Application Insights](media/service-fabric-diagnostics-event-analysis-appinsights/ai-overview.png)

No painel à direita na imagem anterior, existem dois tipos principais de entradas na lista: pedidos e eventos. Pedidos são as chamadas feitas para API a aplicação através de pedidos de HTTP em vez disso, e os eventos são eventos personalizados, que funcionam como telemetria que pode adicionar em qualquer lugar no seu código. Pode explorar ainda mais o instrumentar seus aplicativos na [API do Application Insights para métricas e eventos personalizados](../azure-monitor/app/api-custom-events-metrics.md). Clicar num pedido seria exibida ainda mais detalhes conforme mostrado na imagem seguinte, incluindo dados específicos para o Service Fabric, que são recolhidos no pacote nuget do Service Fabric do Application Insights. Esta informação é útil para resolução de problemas e saber o que é o estado do seu aplicativo, e todas essas informações é pesquisável dentro do Application Insights

![Detalhes de pedido do Application Insights](media/service-fabric-diagnostics-event-analysis-appinsights/ai-request-details.png)

O Application Insights tem uma vista designada para a consulta em relação a todos os dados que entra em ação. Clique em "Explorador de métricas" no topo da página de descrição geral para navegar para o portal do Application Insights. Aqui pode executar consultas contra eventos personalizados, mencionados anteriormente, pedidos, exceções, contadores de desempenho e outras métricas usando a linguagem de consulta de Kusto. O exemplo seguinte mostra todos os pedidos na última 1 hora.

![Detalhes de pedido do Application Insights](media/service-fabric-diagnostics-event-analysis-appinsights/ai-metrics-explorer.png)

Para explorar ainda mais os recursos do portal do Application Insights, vá para o [documentação do Application Insights portal](../azure-monitor/app/app-insights-dashboards.md).

### <a name="configuring-application-insights-with-eventflow"></a>Configuração do Application Insights com o EventFlow

Se estiver a utilizar o EventFlow para agregar eventos, certifique-se importar o `Microsoft.Diagnostics.EventFlow.Output.ApplicationInsights`pacote NuGet. O código a seguir é necessária a *produz* secção a *eventFlowConfig.json*:

```json
"outputs": [
    {
        "type": "ApplicationInsights",
        "instrumentationKey": "***ADD INSTRUMENTATION KEY HERE***"
    }
]
```

Certifique-se efetuar as alterações necessárias nos filtros, bem como incluir todas as outras entradas (juntamente com os respetivos pacotes de NuGet).

## <a name="application-insights-sdk"></a>SDK do Application Insights

É recomendado utilizar o EventFlow e WAD como soluções de agregação, pois eles permitem que para uma abordagem mais modular para diagnóstico e não monitorização, ou seja, se quiser alterar suas saídas do EventFlow, requer nenhuma alteração à sua instrumentação real, apenas um modificação Simple para o ficheiro de configuração. Se, no entanto, decide a investir em utilizar o Application Insights e não provavelmente alterar para uma plataforma diferente, deve procurar usar o novo SDK do Application Insights para agregar eventos e enviá-los para o Application Insights. Isso significa que já não terá de configurar o EventFlow para enviar os seus dados para o Application Insights, mas em vez disso, irá instalar pacote de NuGet do Service Fabric a ApplicationInsight. Podem encontrar detalhes sobre o pacote [aqui](https://github.com/Microsoft/ApplicationInsights-ServiceFabric).

[O Application Insights suporta para Microsserviços e contentores](https://azure.microsoft.com/blog/app-insights-microservices/) mostra-lhe alguns dos novos recursos que estão a ser trabalhados (ainda atualmente em beta), que permitem que tenha mais opções de monitorização de out-of-the-box com o Application Insights. Estes incluem-se ao controlo de dependência (utilizado na criação de um AppMap de todos os seus serviços e aplicações em cluster e a comunicação entre eles) e melhor correlação de rastreios a chegar dos seus serviços (ajuda no melhor indicar um problema no fluxo de trabalho de uma aplicação ou serviço).

Se estiver a desenvolver no .NET e irá, provavelmente, utilizar alguns dos modelos de programação do Service Fabric e quiser utilizar o Application Insights como plataforma para a visualização e análise de dados de registo de eventos e, em seguida, recomendamos que vá através do Application Insights Rota SDK que o fluxo de trabalho de monitorização e diagnóstico. Leia [isso](../azure-monitor/app/asp-net-more.md) e [isso](../azure-monitor/app/asp-net-trace-logs.md) para começar a utilizar com o Application Insights para recolher e apresentar os seus registos.

## <a name="navigating-the-application-insights-resource-in-azure-portal"></a>Navegar o recurso do Application Insights no portal do Azure

Assim que tiver configurado o Application Insights como uma saída para seus eventos e registos, informações devem começar a aparecer no seu recurso do Application Insights dentro de alguns minutos. Navegue para o recurso do Application Insights, que o irá redirecionar para o dashboard de recursos do Application Insights. Clique em **pesquisa** na barra de tarefas Application Insights para ver os rastreios mais recente é recebida e ser capaz de filtrar por eles.

*Explorador de métricas* é uma ferramenta útil para criar dashboards personalizados com base nas métricas que seus aplicativos, serviços e cluster podem ser enviar relatórios. Ver [explorar métricas no Application Insights](../azure-monitor/app/metrics-explorer.md) configurar alguns gráficos para si próprio com base nos dados que está a recolher.

Clicar **Analytics** leva-o para o portal do Application Insights Analytics, onde pode consultar os eventos e rastreios com maior âmbito e optionality. Saiba mais sobre isso em [análise no Application Insights](../azure-monitor/app/analytics.md).

## <a name="next-steps"></a>Passos Seguintes

* [Configure alertas em IA](../azure-monitor/app/alerts.md) para ser notificado sobre as alterações no desempenho ou na utilização
* [Deteção no Application Insights inteligente](../azure-monitor/app/proactive-diagnostics.md) realiza uma análise proativa de telemetria a ser enviada para o Application Insights para o avisar de potenciais problemas de desempenho
