---
title: implementar a análise de tráfego de pesquisa - Azure Search
description: Ative a análise de tráfego de pesquisa para o Azure Search adicionar telemetria e eventos iniciados pelo usuário em arquivos de log.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: c30c8bae3e76778a31cdd0695acde52b5b1c6b02
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60749619"
---
# <a name="implement-search-traffic-analytics-in-azure-search"></a>Implementar a análise de tráfego de pesquisa no Azure Search
Procurar análises de tráfego é um padrão para a implementação de um ciclo de comentários para o seu serviço de pesquisa. Este padrão descreve os dados necessários e como reuni-los com o Application Insights, líder da indústria para monitorizar os serviços em várias plataformas.

Procurar análises de tráfego permite-lhe ganhar visibilidade para o serviço de pesquisa e desbloquear informações sobre os seus utilizadores e seu comportamento. Fazendo com que dados sobre o que escolhem os seus utilizadores, é possível para tomar decisões que melhorar ainda mais a sua experiência de pesquisa e para voltar e quando os resultados são não o que o esperado.

O Azure Search oferece uma solução de telemetria que integra o Azure Application Insights e o Power BI para fornecer monitorização e de controlo detalhadas. Como a interação com o Azure Search é apenas através de APIs, a telemetria tem de ser implementada pelos desenvolvedores com pesquisa, seguindo as instruções nesta página.

## <a name="identify-relevant-search-data"></a>Identificar os dados de pesquisa relevantes

Para que as métricas de pesquisa útil, é necessário para alguns sinais de registo dos utilizadores de aplicativo de pesquisa. Esses sinais significar o conteúdo que os utilizadores estejam interessados em e o que considerar relevantes para as suas necessidades.

Existem dois sinais que precisa de análise de tráfego de pesquisa:

1. Eventos de pesquisa de gerados pelo utilizador: apenas consultas de pesquisa iniciadas por um utilizador são interessantes. Pedidos de pesquisa utilizados para preencher facetas, conteúdo adicional ou quaisquer informações internas, não são importantes e eles inclinar e tendência seus resultados.

2. Eventos de clique gerados pelo utilizador: Por cliques neste documento, nos Referimos a um utilizador selecionar um resultado de pesquisa específico devolvido por uma consulta de pesquisa. Um clique, geralmente, significa que um documento é um resultado relevante para uma consulta de pesquisa específica.

Vinculando a pesquisa e clique em eventos com um id de correlação, é possível analisar os comportamentos dos utilizadores na sua aplicação. Estas informações de pesquisa são impossíveis de se obter com apenas registos de tráfego de pesquisa.

## <a name="add-search-traffic-analytics"></a>Adicionar a análise de tráfego de pesquisa

Os sinais mencionados na secção anterior devem ser reunidos a partir da aplicação de pesquisa à medida que o usuário interage com ele. O Application Insights é uma solução de monitorização extensível, disponível para várias plataformas, com opções flexíveis de instrumentação. Utilização do Application Insights permite-lhe tirar partido dos relatórios de pesquisa de Power BI criado pelo Azure Search para facilitar a análise de dados.

Na [portal](https://portal.azure.com) página para o seu serviço de Azure Search, o painel de análise de tráfego de pesquisa contém uma referência rápida para seguir este padrão de telemetria. Também pode selecionar ou criar um recurso do Application Insights e ver os dados necessários, tudo num único local.

![Instruções de análise de tráfego de pesquisa][1]

## <a name="1---select-a-resource"></a>1 - Selecione um recurso

Tem de selecionar um recurso do Application Insights para utilizar ou criar um se ainda não tiver um. Pode usar um recurso que já está em utilização para registar os eventos personalizados necessários.

Ao criar um novo recurso do Application Insights, todos os tipos de aplicação são válidos para este cenário. Selecione aquele que melhor se adequa a plataforma que está a utilizar.

Terá da chave de instrumentação para criar o cliente de telemetria para a sua aplicação. Pode obtê-lo a partir do dashboard do portal do Application Insights ou pode obtê-lo da página de análise de tráfego de pesquisa, selecionar a instância que pretende utilizar.

## <a name="2---add-instrumentation"></a>2 - Adicionar instrumentação

Nesta fase é onde instrumente a sua própria aplicação de pesquisa, usando o recurso do Application Insights sua criado no passo acima. Existem quatro etapas para este processo:

**Passo 1: Criar um cliente de telemetria** esse é o objeto para enviar eventos para o recurso do Application Insights.

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

Para outros idiomas e plataformas, consulte o concluída [lista](https://docs.microsoft.com/azure/application-insights/app-insights-platforms).

**Passo 2: Um ID de pesquisa para a correlação de pedido** para correlacionar os pedidos de pesquisa com cliques, é necessário ter um id de correlação que se relacione esses dois eventos distintos. O Azure Search fornece um Id de pesquisa quando solicitá-la com um cabeçalho:

*C#*

    // This sample uses the Azure Search .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

    var client = new SearchIndexClient(<ServiceName>, <IndexName>, new SearchCredentials(<QueryKey>)
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

**Passo 3: Registar eventos de pesquisa**

Sempre que um pedido de pesquisa é emitido por um utilizador, deve iniciar que como um evento de pesquisa com o esquema abaixo num evento personalizado do Application Insights:

**ServiceName**: nome do serviço de pesquisa (cadeia) **SearchId**: identificador de exclusivo (guid) da consulta de pesquisa (acontecer na resposta da pesquisa) **IndexName**: índice de serviço de pesquisa (cadeia) a ser consultado **QueryTerms**: termos de pesquisa (cadeia) inseridos pelo usuário **parâmetro ResultCount**: (int) número de documentos que foram devolvidos (acontecer na resposta da pesquisa)  **ScoringProfile**: nome de (cadeia) do perfil de classificação utilizado, se aplicável

> [!NOTE]
> Número de pedidos em consultas de utilizador gerado ao adicionar $count = TRUE para a consulta de pesquisa. Ver mais informações [aqui](https://docs.microsoft.com/rest/api/searchservice/search-documents#request)
>

> [!NOTE]
> Lembre-se apenas registar as consultas de pesquisa que são geradas pelos utilizadores.
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

**Passo 4: Inicie sessão clique eventos**

Sempre que um usuário clica num documento, o que é um sinal de que tem de ter sessão iniciado para fins de análise de pesquisa. Utilize eventos personalizados do Application Insights para registar estes eventos com o esquema seguinte:

**ServiceName**: nome do serviço de pesquisa (cadeia) **SearchId**: identificador de exclusivo (guid) da consulta de pesquisa relacionada **DocId**: identificador de documento (cadeia) **posição**: página de resultados de classificação (int) do documento na pesquisa

> [!NOTE]
> Posição refere-se na ordem de cardinal em seu aplicativo. Tem liberdade para definir este número, desde que é sempre o mesmo, para permitir a comparação.
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

## <a name="3---analyze-in-power-bi"></a>3 - analisar no Power BI

Depois de ter instrumentado a sua aplicação e verificado que seu aplicativo está ligado corretamente para o Application Insights, pode utilizar um modelo predefinido criado pelo Azure Search para o Power BI desktop. 

O Azure search fornece uma monitorização [o pacote de conteúdos do Power BI](https://app.powerbi.com/getdata/services/azure-search) para que pode analisar dados de registo. O pacote de conteúdos de T adiciona predefinidos de gráficos e tabelas útil para analisar os dados adicionais capturados para análise de tráfego de pesquisa. Para obter mais informações, consulte a [página de ajuda do pacote de conteúdos](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-search/). 

1. O Azure pesquisar em Painel de navegação à esquerda do dashboard, **definições**, clique em **análise de tráfego de pesquisa**.

2. Sobre o **pesquisar a análise de tráfego** página, no passo 3, clique em **obter o Power BI Desktop** para instalar o Power BI.

   ![Obter relatórios do Power BI](./media/search-traffic-analytics/get-use-power-bi.png "relatórios do Power BI obter")

2. Na mesma página, clique em **relatório do PowerBI transferir**.

3. O relatório é aberto no Power BI Desktop e lhe for pedido para ligar ao Application Insights. Pode encontrar estas informações nas páginas de portais do Azure para, recurso do Application Insights.

   ![Ligar para o Application Insights](./media/search-traffic-analytics/connect-to-app-insights.png "ligar para o Application Insights")

4. Clique em **carga**.

O relatório contém gráficos e tabelas que o ajudam a tomar decisões mais informadas para melhorar o desempenho de pesquisa e relevância.

Métricas inclui os seguintes itens:

* Clique em por meio de taxa (CTR): o rácio de utilizadores que clicar num documento específico para o número de pesquisas total.
* Pesquisas sem cliques: termos de principais consultas que registe-se sem cliques
* Mais clicado documentos: mais clicou em documentos por ID de nas últimas 24 horas, 7 dias e 30 dias.
* Pares de documento de termo popular: termos que fazer com o mesmo documento clicado, ordenado pelo cliques.
* Tempo de clicar em: cliques registados pelo tempo uma vez que a consulta de pesquisa

Captura de ecrã seguinte mostra os relatórios incorporados e análise de tráfego de pesquisa de gráficos para análise.

![Dashboard do Power BI para o Azure Search](./media/search-traffic-analytics/AzureSearch-PowerBI-Dashboard.png "dashboard do Power BI para o Azure Search")

## <a name="next-steps"></a>Passos Seguintes
Instrumente a sua aplicação de pesquisa para obter dados poderosos e esclarecedores sobre o serviço de pesquisa.

Pode encontrar mais informações sobre [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) e visite o [página de preços](https://azure.microsoft.com/pricing/details/application-insights/) para saber mais sobre os escalões de serviço diferentes.

Saiba mais sobre a criação de relatórios incríveis. Ver [introdução ao Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/) para obter detalhes

<!--Image references-->
[1]: ./media/search-traffic-analytics/AzureSearch-TrafficAnalytics.png
[2]: ./media/search-traffic-analytics/AzureSearch-AppInsightsData.png
[3]: ./media/search-traffic-analytics/AzureSearch-PBITemplate.png
