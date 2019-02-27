---
title: Dependência de controlo no Application Insights do Azure | Documentos da Microsoft
description: Analise a utilização, a disponibilidade e o desempenho da aplicação Web no local ou do Microsoft Azure com o Application Insights.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: d15c4ca8-4c1a-47ab-a03d-c322b4bb2a9e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: mbullwin
ms.openlocfilehash: 4aa18ae791e5fa573eae76d5bdb9c45b9311e6b5
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56888088"
---
# <a name="set-up-application-insights-dependency-tracking"></a>Configure o Application Insights: Rastreamento de dependências
R *dependência* é um componente externo que é chamado pela sua aplicação. Normalmente, é um serviço chamado através de HTTP, ou uma base de dados ou um sistema de ficheiros. [O Application Insights](../../azure-monitor/app/app-insights-overview.md) mede o tempo que o aplicativo aguardará dependências e a frequência com que uma chamada de dependência falha. Pode investigar chamadas específicas e estão relacionadas com pedidos e exceções.

O monitor de dependência de out-of-the-box relatórios atualmente chamadas para esses tipos de dependências:

* Servidor
  * Bases de dados SQL
  * ASP.NET web e serviços do WCF que usar associações com base em HTTP
  * Chamadas HTTP locais ou remotas
  * Azure Cosmos DB, tabela, armazenamento de BLOBs e filas 
* Páginas Web
  * Chamadas AJAX

A monitorização funciona utilizando [instrumentação de código de byte](https://msdn.microsoft.com/library/z9z62c29.aspx) cerca selecionar métodos ou com base em DiagnosticSource retornos de chamada de (nos SDKs do .NET mais recente) do .NET Framework. Sobrecarga de desempenho é mínima.

Também pode escrever suas próprias chamadas SDK para monitorizar outras dependências, ambos no código de cliente e servidor, utilizando o [TrackDependency API](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency).

> [!NOTE]
> O Azure Cosmos DB é controlado automaticamente apenas se [HTTP/HTTPS](../../cosmos-db/performance-tips.md#networking) é utilizado. Modo TCP não ser capturado pelo Application Insights.

## <a name="set-up-dependency-monitoring"></a>Configurar a monitorização de dependência
Informações de dependência parciais são recolhidas automaticamente pelos [SDK do Application Insights](asp-net.md). Para obter dados completos, instale o agente adequado para o servidor de anfitrião.

| Plataforma | Instalar |
| --- | --- |
| Servidor IIS |Qualquer um dos [instalar o Monitor de estado no seu servidor](../../azure-monitor/app/monitor-performance-live-website-now.md) ou [atualizar a sua aplicação para o .NET framework 4.6 ou posterior](https://go.microsoft.com/fwlink/?LinkId=528259) e instale o [SDK do Application Insights](asp-net.md) na sua aplicação. |
| Aplicação Web do Azure |No seu painel de controlo de aplicação web, [abrir o painel do Application Insights no seu painel de controlo de aplicação web](../../azure-monitor/app/azure-web-apps.md) e escolha a instalação, se lhe for pedido. |
| Serviço Cloud do Azure |[Tarefa de arranque de utilização](../../azure-monitor/app/cloudservices.md) ou [framework de instalar o .NET 4.6 +](../../cloud-services/cloud-services-dotnet-install-dotnet.md) |

## <a name="where-to-find-dependency-data"></a>Onde encontrar os dados de dependência
* [Mapa da aplicação](#application-map) visualiza as dependências entre as suas aplicações e componentes vizinhos.
* [Os painéis de desempenho, navegadores e falha](#performance-and-failure-blades) Mostrar dados de dependência de servidor.
* [Painel browsers](#ajax-calls) mostra as chamadas AJAX de browsers dos seus utilizadores.
* Clique em dos pedidos lentos ou com falhas para verificar as suas chamadas de dependência.
* [Análise](#analytics) pode ser usado para consultar dados de dependência.

## <a name="application-map"></a>Mapeamento de Aplicações
Mapa da aplicação atua como uma ajuda visual para detetar as dependências entre os componentes da sua aplicação. É gerado automaticamente da telemetria da sua aplicação. Este exemplo mostra as chamadas AJAX dos scripts de navegador e chamadas REST a partir da aplicação de servidor para dois serviços externos.

![Mapeamento de Aplicações](./media/asp-net-dependencies/08.png)

* **Navegar das caixas de** dependência relevante e outros gráficos.
* **Afixar mapa** para o [dashboard](../../azure-monitor/app/app-insights-dashboards.md), onde será totalmente funcional.

[Saiba mais](../../azure-monitor/app/app-map.md).

## <a name="performance-and-failure-blades"></a>Painéis de desempenho e falha
O painel de desempenho mostra a duração das chamadas de dependência efetuadas pela aplicação de servidor. Existe um gráfico de resumo e uma tabela segmentados por chamada.

![Gráficos de dependência do painel de desempenho](./media/asp-net-dependencies/dependencies-in-performance-blade.png)

Clique nos gráficos de resumos ou os itens da tabela para procurar ocorrências não processadas dessas chamadas.

![Instâncias de chamada de dependência](./media/asp-net-dependencies/dependency-call-instance.png)

**Contagens de falhas** são mostrados os **falhas** painel. Uma falha é qualquer código de retorno não está no intervalo 200-399, ou desconhecido.

> [!NOTE]
> **100% de falhas?** -Isso provavelmente indica que está a obter apenas dados parciais de dependência. Precisa [configurar a monitorização de dependência apropriado para sua plataforma](#set-up-dependency-monitoring).
>
>

## <a name="ajax-calls"></a>Chamadas AJAX
O painel Browsers mostra a taxa de duração e de falha de chamadas AJAX partir [JavaScript em páginas da web](../../azure-monitor/app/javascript.md). Eles são apresentados como dependências.

## <a name="diagnosis"></a> Diagnosticar pedidos lentos
Cada evento do pedido está associado com as chamadas de dependência, exceções e outros eventos que são controlados enquanto a aplicação está a processar o pedido. Portanto, se alguns pedidos estão a funcionar incorretamente, pode descobrir se ele é devido a respostas lentas do que uma dependência.

Vamos examinar um exemplo disso.

### <a name="tracing-from-requests-to-dependencies"></a>Rastreio de pedidos para dependências
Abra o painel desempenho e examinar a grade de pedidos:

![Lista de pedidos com contagens e médias](./media/asp-net-dependencies/02-reqs.png)

Principal aquele está a demorar muito tempo. Vejamos se conseguimos perceber em que o tempo é gasto.

Clique nessa linha para ver eventos de pedidos individuais:

![Lista de ocorrências de pedido](./media/asp-net-dependencies/03-instances.png)

Clique em qualquer instância de execução longa inspecioná-la ainda mais e desloque para baixo para as chamadas de dependência remoto relacionados com este pedido:

![Encontre as chamadas para dependências remotas, identifique a duração invulgar](./media/asp-net-dependencies/04-dependencies.png)

Ele se parece com a maioria do tempo de manutenção que este pedido foi gasto numa chamada para um serviço local.

Selecione essa linha para obter mais informações:

![Clique nessa dependência remota para identificar o culpado](./media/asp-net-dependencies/05-detail.png)

Parece que este é onde está o problema. Vamos ter apontada o problema, então, agora nós apenas precisa descobrir por que essa chamada está demorando tanto.

### <a name="request-timeline"></a>Linha cronológica de pedido
Num outro caso, não existe nenhuma chamada de dependência é bastante longa. Mas, ao mudar para a vista de linha do tempo, podemos ver onde o atraso ocorreu no nosso processamento interno:

![Encontre as chamadas para dependências remotas, identifique a duração invulgar](./media/asp-net-dependencies/04-1.png)

Parece haver uma grande lacuna depois de chamar a primeira dependência, portanto, o que devemos olhar nosso código para ver por que isso é.

### <a name="profile-your-live-site"></a>Perfil de seu site em direto

Não sabe onde o tempo passa? O [criador de perfil do Application Insights](../../azure-monitor/app/profiler.md) rastreios HTTP chama-se ao seu site em direto e mostra quais as funções em seu código demorou muito tempo.

## <a name="failed-requests"></a>Pedidos falhados
Também podem ser associados a chamadas falhadas para dependências de pedidos falhados. Novamente, podemos clicar por meio de rastrear o problema.

![Clique no gráfico de pedidos falhados](./media/asp-net-dependencies/06-fail.png)

Clicar para uma ocorrência de um pedido falhado e examinar seus eventos associados.

![Clique num tipo de pedido, clique na instância para obter uma exibição diferente da instância do mesmo, clique nele para obter detalhes da exceção.](./media/asp-net-dependencies/07-faildetail.png)

## <a name="analytics"></a>Análise
Pode controlar as dependências no [linguagem de consulta de Kusto](/azure/kusto/query/). Eis alguns exemplos.

* Encontre quaisquer chamadas de dependência falhadas:

```

    dependencies | where success != "True" | take 10
```

* Encontre as chamadas AJAX:

```

    dependencies | where client_Type == "Browser" | take 10
```

* Encontre associadas a pedidos de chamadas de dependência:

```

    dependencies
    | where timestamp > ago(1d) and  client_Type != "Browser"
    | join (requests | where timestamp > ago(1d))
      on operation_Id  
```


* Encontre as chamadas AJAX associadas a vistas de página:

```

    dependencies
    | where timestamp > ago(1d) and  client_Type == "Browser"
    | join (browserTimings | where timestamp > ago(1d))
      on operation_Id
```



## <a name="custom-dependency-tracking"></a>Controlo de dependência personalizadas
O módulo de controlo de dependência padrão Deteta automaticamente as dependências externas, tais como bases de dados e REST APIs. Mas talvez tenha alguns componentes adicionais para ser tratados da mesma forma.

Pode escrever código que envia informações de dependência, com o mesmo [TrackDependency API](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency) que é utilizada pelos módulos padrão.

Por exemplo, se criar seu código com um assembly que não escrever por conta própria, tempo todas as chamadas para o mesmo, para descobrir o que faz para os tempos de resposta de contribuição. Para que estes dados apresentados nos gráficos de dependência no Application Insights, enviá-la usando `TrackDependency`.

```csharp

            var startTime = DateTime.UtcNow;
            var timer = System.Diagnostics.Stopwatch.StartNew();
            try
            {
                success = dependency.Call();
            }
            finally
            {
                timer.Stop();
                telemetry.TrackDependency("myDependency", "myCall", startTime, timer.Elapsed, success);
                // The call above has been made obsolete in the latest SDK. The updated call follows this format:
                // TrackDependency (string dependencyTypeName, string dependencyName, string data, DateTimeOffset startTime, TimeSpan duration, bool success);
            }
```

Se quiser optar por desativar o módulo de controlo de dependência padrão, remova a referência DependencyTrackingTelemetryModule na [applicationinsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md).

## <a name="troubleshooting"></a>Resolução de problemas
*Sucesso de dependência sempre sinalizar mostra verdadeira ou falsa.*

*Consulta SQL não é apresentada por completo.*

Consulte a tabela abaixo e assim garantir que escolheu a configuração correta para ativar a monitorização de dependência para a sua aplicação.

| Plataforma | Instalar |
| --- | --- |
| Servidor IIS |Qualquer um dos [instalar o Monitor de estado no seu servidor](../../azure-monitor/app/monitor-performance-live-website-now.md). Ou [atualizar a sua aplicação para o .NET framework 4.6 ou posterior](https://go.microsoft.com/fwlink/?LinkId=528259) e instale o [SDK do Application Insights](asp-net.md) na sua aplicação. |
| IIS Express |Utilize em vez disso, o servidor de IIS. |
| Aplicação Web do Azure |No seu painel de controlo de aplicação web, [abrir o painel do Application Insights no seu painel de controlo de aplicação web](../../azure-monitor/app/azure-web-apps.md) e escolha a instalação, se lhe for pedido. |
| Serviço Cloud do Azure |[Tarefa de arranque de utilização](../../azure-monitor/app/cloudservices.md) ou [framework de instalar o .NET 4.6 +](../../cloud-services/cloud-services-dotnet-install-dotnet.md). |

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Passos Seguintes
* [Exceções](../../azure-monitor/app/asp-net-exceptions.md)
* [Dados de utilizador e página](../../azure-monitor/app/javascript.md)
* [Disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md)
