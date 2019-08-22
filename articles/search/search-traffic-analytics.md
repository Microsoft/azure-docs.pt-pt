---
title: implementar análise de tráfego de pesquisa-Azure Search
description: Habilite a análise de tráfego de pesquisa para Azure Search adicionar telemetria e eventos iniciados pelo usuário a arquivos de log.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: bb12ed2f18df100ab3f679e7a8a3ef1e7c1aca45
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647802"
---
# <a name="implement-search-traffic-analytics-in-azure-search"></a>Implementar análise de tráfego de pesquisa no Azure Search
A análise de tráfego de pesquisa é um padrão para implementar um loop de comentários para o serviço de pesquisa. Esse padrão descreve os dados necessários e como coletá-los usando Application Insights, um líder do setor para monitorar serviços em várias plataformas.

A análise de tráfego de pesquisa permite que você tenha visibilidade do serviço de pesquisa e descubra informações sobre os usuários e seu comportamento. Com os dados sobre o que seus usuários escolhem, é possível tomar decisões que melhorem ainda mais a sua experiência de pesquisa e que sejam desativadas quando os resultados não forem o esperado.

O Azure Search oferece uma solução de telemetria que integra Aplicativo Azure informações e Power BI para fornecer monitoramento e acompanhamento detalhados. Como a interação com Azure Search é apenas por meio de APIs, a telemetria deve ser implementada pelos desenvolvedores que usam a pesquisa, seguindo as instruções nesta página.

## <a name="identify-relevant-search-data"></a>Identificar dados de pesquisa relevantes

Para ter métricas de pesquisa úteis, é necessário registrar alguns sinais dos usuários do aplicativo de pesquisa. Esses sinais significam conteúdo em que os usuários estão interessados e que eles consideram relevantes para suas necessidades.

Há dois sinais Análise de Tráfego necessidades de pesquisa:

1. Eventos de pesquisa gerados pelo usuário: somente consultas de pesquisa iniciadas por um usuário são interessantes. As solicitações de pesquisa usadas para popular facetas, conteúdo adicional ou qualquer informação interna, não são importantes e distorcem e interpolarem seus resultados.

2. Eventos de clique gerados pelo usuário: Ao clicar neste documento, nos referimos a um usuário que seleciona um determinado resultado de pesquisa retornado por uma consulta de pesquisa. Um clique geralmente significa que um documento é um resultado relevante para uma consulta de pesquisa específica.

Ao vincular a pesquisa e clicar em eventos com uma ID de correlação, é possível analisar os comportamentos dos usuários em seu aplicativo. Essas informações de pesquisa são impossíveis de obter somente com logs de tráfego de pesquisa.

## <a name="add-search-traffic-analytics"></a>Adicionar análise de tráfego de pesquisa

Os sinais mencionados na seção anterior devem ser coletados do aplicativo de pesquisa à medida que o usuário interage com ele. Application Insights é uma solução de monitoramento extensível, disponível para várias plataformas, com opções de instrumentação flexíveis. O uso de Application Insights permite que você aproveite os Power BI relatórios de pesquisa criados por Azure Search para facilitar a análise dos dados.

Na página do [portal](https://portal.azure.com) para seu serviço de Azure Search, a folha Pesquisar análise de tráfego contém uma página de dicas para seguir esse padrão de telemetria. Você também pode selecionar ou criar um recurso de Application Insights e ver os dados necessários, tudo em um só lugar.

![Instruções de Análise de Tráfego de pesquisa][1]

## <a name="1---select-a-resource"></a>1-selecionar um recurso

Você precisa selecionar um recurso Application Insights para usar ou criar um, caso ainda não tenha um. Você pode usar um recurso que já está em uso para registrar os eventos personalizados necessários.

Ao criar um novo recurso de Application Insights, todos os tipos de aplicativo são válidos para esse cenário. Selecione aquele que melhor se adapta à plataforma que você está usando.

Você precisa da chave de instrumentação para criar o cliente de telemetria para seu aplicativo. Você pode obtê-lo no painel do Application Insights portal ou pode obtê-lo na página Pesquisar Análise de Tráfego, selecionando a instância que deseja usar.

## <a name="2---add-instrumentation"></a>2-adicionar instrumentação

Essa fase é onde você instrumenta seu próprio aplicativo de pesquisa, usando o recurso Application Insights criado na etapa acima. Há quatro etapas para esse processo:

**Passo 1: Criar um cliente** de telemetria esse é o objeto que envia eventos para o recurso de Application insights.

*C#*

    private TelemetryClient telemetryClient = new TelemetryClient();
    telemetryClient.InstrumentationKey = "<YOUR INSTRUMENTATION KEY>";

*JavaScript*

    <script type="text/javascript">var appInsights=window.appInsights||function(config){function r(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},u=document,e=window,o="script",s=u.createElement(o),i,f;s.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js";u.getElementsByTagName(o)[0].parentNode.appendChild(s);try{t.cookie=u.cookie}catch(h){}for(t.queue=[],i=["Event","Exception","Metric","PageView","Trace","Dependency"];i.length;)r("track"+i.pop());return r("setAuthenticatedUserContext"),r("clearAuthenticatedUserContext"),config.disableExceptionTracking||(i="onerror",r("_"+i),f=e[i],e[i]=function(config,r,u,e,o){var s=f&&f(config,r,u,e,o);return s!==!0&&t["_"+i](config,r,u,e,o),s}),t}
    ({
    instrumentationKey: "<YOUR INSTRUMENTATION KEY>"
    });
    window.appInsights=appInsights;
    </script>

Para outras linguagens e plataformas, consulte a [lista](https://docs.microsoft.com/azure/application-insights/app-insights-platforms)completa.

**Passo 2: Solicitar uma ID de pesquisa para** correlação para correlacionar solicitações de pesquisa com cliques, é necessário ter uma ID de correlação que relaciona esses dois eventos distintos. Azure Search fornece uma ID de pesquisa quando você a solicita com um cabeçalho:

*C#*

    // This sample uses the Azure Search .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

    var client = new SearchIndexClient(<SearchServiceName>, <IndexName>, new SearchCredentials(<QueryKey>)
    var headers = new Dictionary<string, List<string>>() { { "x-ms-azs-return-searchid", new List<string>() { "true" } } };
    var response = await client.Documents.SearchWithHttpMessagesAsync(searchText: searchText, searchParameters: parameters, customHeaders: headers);
    IEnumerable<string> headerValues;
    string searchId = string.Empty;
    if (response.Response.Headers.TryGetValues("x-ms-azs-searchid", out headerValues)){
     searchId = headerValues.FirstOrDefault();
    }

*JavaScript*

    request.setRequestHeader("x-ms-azs-return-searchid", "true");
    request.setRequestHeader("Access-Control-Expose-Headers", "x-ms-azs-searchid");
    var searchId = request.getResponseHeader('x-ms-azs-searchid');

**Passo 3: Eventos de pesquisa de log**

Toda vez que uma solicitação de pesquisa é emitida por um usuário, você deve registrar isso como um evento de pesquisa com o esquema a seguir em um evento Application Insights personalizado:

**SearchServiceName**: (String) nome do serviço de pesquisa **searchId**: (GUID) identificador exclusivo da consulta de pesquisa (vem na resposta depesquisa) IndexName: (String) índice do serviço de pesquisa a ser consultado **QueryTerms**: (cadeia de caracteres) pesquisa termos inseridos pelo usuário **ResultCount**: (int) número de documentos que foram retornados (vem na resposta de pesquisa) **ScoringProfile**: (cadeia de caracteres) nome do perfil de Pontuação usado, se houver

> [!NOTE]
> Solicite a contagem de consultas geradas pelo usuário adicionando $count = true à sua consulta de pesquisa. Veja mais informações [aqui](https://docs.microsoft.com/rest/api/searchservice/search-documents#request)
>

> [!NOTE]
> Lembre-se de registrar somente as consultas de pesquisa geradas pelos usuários.
>

*C#*

    var properties = new Dictionary <string, string> {
    {"SearchServiceName", <service name>},
    {"SearchId", <search Id>},
    {"IndexName", <index name>},
    {"QueryTerms", <search terms>},
    {"ResultCount", <results count>},
    {"ScoringProfile", <scoring profile used>}
    };
    telemetryClient.TrackEvent("Search", properties);

*JavaScript*

    appInsights.trackEvent("Search", {
    SearchServiceName: <service name>,
    SearchId: <search id>,
    IndexName: <index name>,
    QueryTerms: <search terms>,
    ResultCount: <results count>,
    ScoringProfile: <scoring profile used>
    });

**Passo 4: Eventos de clique de log**

Toda vez que um usuário clica em um documento, esse é um sinal que deve ser registrado para fins de análise de pesquisa. Use Application Insights eventos personalizados para registrar esses eventos no esquema a seguir:

ServiceName: (cadeia de caracteres) nome do serviço de pesquisa **searchId**: (GUID) identificador exclusivo da consulta de pesquisa relacionada **DocId**: (cadeia de caracteres) **posição**do identificador do documento: (int) classificação do documento na página de resultados da pesquisa

> [!NOTE]
> Posição refere-se à ordem cardeal em seu aplicativo. Você tem a liberdade de definir esse número, desde que seja sempre o mesmo, para permitir a comparação.
>

*C#*

    var properties = new Dictionary <string, string> {
    {"SearchServiceName", <service name>},
    {"SearchId", <search id>},
    {"ClickedDocId", <clicked document id>},
    {"Rank", <clicked document position>}
    };
    telemetryClient.TrackEvent("Click", properties);

*JavaScript*

    appInsights.trackEvent("Click", {
        SearchServiceName: <service name>,
        SearchId: <search id>,
        ClickedDocId: <clicked document id>,
        Rank: <clicked document position>
    });

## <a name="3---analyze-in-power-bi"></a>3-analisar no Power BI

Depois de instrumentar seu aplicativo e verificar se seu aplicativo está conectado corretamente ao Application Insights, você pode usar um modelo predefinido criado por Azure Search para Power BI desktop. 

O Azure Search fornece um [pacote de conteúdo](https://app.powerbi.com/getdata/services/azure-search) de monitoramento Power bi para que você possa analisar os dados de log. T o pacote de conteúdo adiciona gráficos predefinidos e tabelas úteis para analisar os dados adicionais capturados para análise de tráfego de pesquisa. Para obter mais informações, consulte a [página de ajuda do pacote de conteúdos](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-search/). 

1. No painel de navegação Azure Search painel esquerdo, em **configurações**, clique em **análise de tráfego de pesquisa**.

2. Na página **análise de tráfego de pesquisa** , na etapa 3, clique em **obter Power BI Desktop** para instalar Power bi.

   ![Obter Power bi relatórios](./media/search-traffic-analytics/get-use-power-bi.png "Obter Power bi relatórios")

2. Na mesma página, clique em **baixar relatório do PowerBI**.

3. O relatório é aberto no Power BI Desktop e você é solicitado a se conectar ao Application Insights. Você pode encontrar essas informações nas páginas portal do Azure para Application Insights recurso.

   ![Conectar-se ao Application insights](./media/search-traffic-analytics/connect-to-app-insights.png "Conectar-se ao Application insights")

4. Clique em **carregar**.

O relatório contém gráficos e tabelas que ajudam você a tomar decisões mais informadas para melhorar o desempenho e a relevância da pesquisa.

As métricas incluíam os seguintes itens:

* Taxa de clique por (CTR): a taxa de usuários que clicam em um documento específico para o número total de pesquisas.
* Pesquisa sem cliques: termos para as principais consultas que não registram nenhum clique
* Documentos mais clicados: a maioria dos documentos clicados por ID nas últimas 24 horas, 7 dias e 30 dias.
* Pares de documentos de termo populares: termos que resultam no mesmo documento clicado, ordenados por cliques.
* Hora para clicar: clica em buckets por tempo desde a consulta de pesquisa

A captura de tela a seguir mostra os relatórios e gráficos internos para analisar a análise de tráfego de pesquisa.

![Painel de Power bi para Azure Search](./media/search-traffic-analytics/AzureSearch-PowerBI-Dashboard.png "Painel de Power bi para Azure Search")

## <a name="next-steps"></a>Passos Seguintes
Instrumente seu aplicativo de pesquisa para obter dados poderosos e criteriosos sobre o serviço de pesquisa.

Você pode encontrar mais informações sobre [Application insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) e visitar a [página de preços](https://azure.microsoft.com/pricing/details/application-insights/) para saber mais sobre suas diferentes camadas de serviço.

Saiba mais sobre a criação de relatórios incríveis. Ver [introdução ao Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/) para obter detalhes

<!--Image references-->
[1]: ./media/search-traffic-analytics/AzureSearch-TrafficAnalytics.png
[2]: ./media/search-traffic-analytics/AzureSearch-AppInsightsData.png
[3]: ./media/search-traffic-analytics/AzureSearch-PBITemplate.png
