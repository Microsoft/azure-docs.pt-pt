---
title: Relatório sobre dados de análise de tráfego de pesquisa
titleSuffix: Azure Cognitive Search
description: Ative a análise de tráfego de pesquisa para pesquisa cognitiva azure, colete telemetria e eventos iniciados pelo utilizador usando Insights de Aplicação, e, em seguida, analisar os resultados num relatório power BI.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 84e60b0a942bad94d8e36eb20b5be8e3f55af80a
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190950"
---
# <a name="implement-search-traffic-analytics-in-azure-cognitive-search"></a>Implementar análises de tráfego de pesquisa em Pesquisa Cognitiva Azure

A análise do tráfego de pesquisa é um padrão para implementar um ciclo de feedback para o seu serviço de pesquisa. O objetivo é recolher telemetria em eventos de clique iniciados pelo utilizador e inputs de teclado. Utilizando estas informações, pode determinar a eficácia da sua solução de pesquisa, incluindo termos de pesquisa populares, taxa de cliques e quais as inputs de consulta que produzem resultados nulos.

Este padrão requer uma dependência de Insights de [Aplicação](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) (uma funcionalidade do [Monitor Azure)](https://docs.microsoft.com/azure/azure-monitor/)para recolher dados dos utilizadores. Também terá de adicionar instrumentação ao seu código de cliente, conforme descrito neste artigo. Finalmente, precisará de um mecanismo de reporte para analisar os dados. Recomendamos o Power BI, mas pode utilizar qualquer ferramenta que se ligue aos Insights de Aplicação.

> [!NOTE]
> O padrão descrito neste artigo é para cenários avançados e dados de clickstream gerados pelo seu cliente. Em alternativa, pode reportar informações de log geradas pelo seu serviço de pesquisa. Para obter mais informações sobre relatórios de registo de serviços, consulte [monitor consumo de recursos e atividade de consulta.](search-monitor-usage.md)

## <a name="identify-relevant-search-data"></a>Identificar dados de pesquisa relevantes

Para ter métricas de pesquisa úteis, é necessário registar alguns sinais dos utilizadores da aplicação de pesquisa. Estes sinais significam conteúdo que os utilizadores estão interessados e que consideram relevantes para as suas necessidades.

Existem dois sinais De pesquisa De tráfego analytics:

+ Eventos de pesquisa gerados pelo utilizador: apenas as consultas de pesquisa iniciadas por um utilizador são interessantes. Os pedidos de pesquisa utilizados para povoar facetas, conteúdos adicionais ou qualquer informação interna, não são importantes e distorcem e distorcem os seus resultados.

+ Eventos de clique gerados pelo utilizador: Por cliques neste documento, referimo-nos a um utilizador que seleciona um resultado de pesquisa específico devolvido a partir de uma consulta de pesquisa. Um clique geralmente significa que um documento é um resultado relevante para uma consulta de pesquisa específica.

Ao ligar eventos de pesquisa e clique com um ID de correlação, é possível analisar os comportamentos dos utilizadores na sua aplicação. Estas informações de pesquisa são impossíveis de obter apenas com registos de tráfego de pesquisa.

## <a name="add-search-traffic-analytics"></a>Adicionar análise de tráfego de pesquisa

Os sinais mencionados na secção anterior devem ser recolhidos a partir da aplicação de pesquisa, uma vez que o utilizador interage com o mesmo. Application Insights é uma solução de monitorização extensível, disponível para várias plataformas, com opções de instrumentação flexíveis. O uso de Insights de Aplicação permite-lhe tirar partido dos relatórios de pesquisa do Power BI criados pela Azure Cognitive Search para facilitar a análise de dados.

Na página do [portal](https://portal.azure.com) do seu serviço de Pesquisa Cognitiva Azure, a página Search Traffic Analytics contém uma folha de batota para seguir este padrão de telemetria. Também pode selecionar ou criar um recurso Application Insights, e ver os dados necessários, tudo num só local.

![Pesquisar instruções de Análise de Tráfego][1]

## <a name="1---select-a-resource"></a>1 - Selecione um recurso

É necessário selecionar um recurso De insights de aplicação para utilizar ou criar um se ainda não tiver um. Pode utilizar um recurso que já está em uso para registar os eventos personalizados necessários.

Ao criar um novo recurso Application Insights, todos os tipos de aplicação são válidos para este cenário. Selecione aquele que melhor se adapte à plataforma que está a utilizar.

Precisa da chave de instrumentação para criar o cliente de telemetria para a sua aplicação. Pode obtê-lo no painel do portal Application Insights, ou pode obtê-lo na página De análise de tráfego de pesquisa, selecionando a instância que pretende utilizar.

## <a name="2---add-instrumentation"></a>2 - Adicionar instrumentação

Este passo é onde você instrumenta a sua própria aplicação de pesquisa, usando o recurso Application Insights que criou no passo acima. Há quatro passos para este processo:

**Passo 1: Criar um cliente de telemetria**

Este é o objeto que envia eventos para o Recurso de Insights de Aplicação.

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

Para outras línguas e plataformas, consulte a [lista](https://docs.microsoft.com/azure/application-insights/app-insights-platforms)completa .

**Passo 2: Solicitar um ID de pesquisa para correlação**

Para correlacionar pedidos de pesquisa com cliques, é necessário ter uma identificação de correlação que relacione estes dois eventos distintos. A Azure Cognitive Search fornece-lhe um ID de pesquisa quando o solicita com um cabeçalho:

*C#*

    // This sample uses the .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

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

**Passo 3: Eventos de pesquisa de log**

Sempre que um pedido de pesquisa for emitido por um utilizador, deve registar isso como um evento de pesquisa com o seguinte esquema num evento personalizado application Insights:

**SearchServiceName**: (string) nome de serviço de pesquisa **SearchId**: (guia) identificador único da consulta de pesquisa (vem na resposta de pesquisa) **IndexName**: (string) índice de serviço de pesquisa a ser consultado **QueryTerms**: (string) termos de pesquisa introduzidos pelo **número**de documentos resultantes do utilizador: (int) número de documentos que foram devolvidos (vem na resposta de pesquisa) **ScoringPerfil**: (string) nome do perfil de pontuação utilizado, se houver

> [!NOTE]
> Pedido conta com consultas geradas pelo utilizador adicionando $count=fiel à sua consulta de pesquisa. Veja mais informações [aqui.](/rest/api/searchservice/search-documents#counttrue--false)
>

> [!NOTE]
> Lembre-se apenas de registar consultas de pesquisa que são geradas pelos utilizadores.
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

**Passo 4: Registar eventos de clique**

Sempre que um utilizador clica num documento, é um sinal que deve ser registado para fins de análise de pesquisa. Utilize eventos personalizados de Informação de Aplicação para registar estes eventos com o seguinte esquema:

**Nome de serviço**: (string) nome de serviço de pesquisa **SearchId**: (guia) identificador único da consulta de pesquisa relacionada **DocId**: (cadeia) identificador de **documentoposição Posição**: (int) classificação do documento na página de resultados de pesquisa

> [!NOTE]
> A posição refere-se à ordem cardeal na sua candidatura. É livre de definir este número, desde que seja sempre o mesmo, para permitir a comparação.
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

## <a name="3---analyze-in-power-bi"></a>3 - Analisar no Power BI

Depois de ter instrumentado a sua aplicação e verificado que a sua aplicação está corretamente ligada aos Insights de Aplicação, descarrega um modelo de relatório predefinido para analisar dados no ambiente de trabalho do Power BI. O relatório contém gráficos e tabelas pré-definidas úteis para analisar os dados adicionais capturados para análise de tráfego de pesquisa. 

1. No painel de navegação à esquerda do painel de pesquisa cognitiva Azure, em **Definições,** clique em análise de **tráfego de pesquisa**.

2. Na página de análise de **tráfego de pesquisa,** no passo 3, clique em **Get Power BI Desktop** para instalar o Power BI.

   ![Obtenha relatórios de Power BI](./media/search-traffic-analytics/get-use-power-bi.png "Obtenha relatórios de Power BI")

2. Na mesma página, clique no **relatório Download Power BI**.

3. O relatório abre no Power BI Desktop, e é-lhe solicitado que se conectem aos Insights de Aplicação e forneçam credenciais. Pode encontrar informações de ligação nas páginas do portal Azure para o seu recurso Application Insights. Para obter credenciais, forneça o mesmo nome de utilizador e senha que utiliza para iniciar sessão no portal.

   ![Ligar-se a Insights de Aplicação](./media/search-traffic-analytics/connect-to-app-insights.png "Ligar-se a Insights de Aplicação")

4. Clique em **Carregar**.

O relatório contém gráficos e tabelas que o ajudam a tomar decisões mais informadas para melhorar o seu desempenho de pesquisa e relevância.

As métricas incluíam os seguintes itens:

* Volume de pesquisa e pares de documentos de termo mais populares: termos que resultam no mesmo documento clicado, encomendado por cliques.
* Pesquisas sem cliques: termos para consultas de topo que não registam cliques

A imagem seguinte mostra os relatórios e gráficos incorporados para analisar a análise do tráfego de pesquisa.

![Painel de instrumentos power BI para pesquisa cognitiva azure](./media/search-traffic-analytics/azuresearch-powerbi-dashboard.png "Painel de instrumentos power BI para pesquisa cognitiva azure")

## <a name="next-steps"></a>Passos seguintes
Instrumente a sua aplicação de pesquisa para obter dados poderosos e perspicazes sobre o seu serviço de pesquisa.

Pode encontrar mais informações sobre os Insights de [Aplicação](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) e visitar a [página de preços](https://azure.microsoft.com/pricing/details/application-insights/) para saber mais sobre os seus diferentes níveis de serviço.

Saiba mais sobre a criação de relatórios incríveis. Ver [Começar com power bi desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/) para obter detalhes.

<!--Image references-->
[1]: ./media/search-traffic-analytics/azuresearch-trafficanalytics.png
[2]: ./media/search-traffic-analytics/azuresearch-appinsightsdata.png
[3]: ./media/search-traffic-analytics/azuresearch-pbitemplate.png
