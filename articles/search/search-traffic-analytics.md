---
title: Telemetria para análise de tráfego de pesquisa
titleSuffix: Azure Cognitive Search
description: Ative a análise de tráfego de pesquisa para pesquisa cognitiva azure, colete telemetria e eventos iniciados pelo utilizador usando Insights de Aplicação, e, em seguida, analisar os resultados num relatório power BI.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 7c843b45b5a398aaaa1aab66f80961560477cf18
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82128086"
---
# <a name="collect-telemetry-data-for-search-traffic-analytics"></a>Recolher dados de telemetria para análise de tráfego de pesquisa

A análise do tráfego de pesquisa é um padrão para recolher telemetria sobre as interações do utilizador com a sua aplicação De Pesquisa Cognitiva Azure, tais como eventos de clique iniciados pelo utilizador e entradas de teclado. Utilizando estas informações, pode determinar a eficácia da sua solução de pesquisa, incluindo termos de pesquisa populares, taxa de cliques e quais as inputs de consulta que produzem resultados nulos.

Este padrão requer uma dependência de Insights de [Aplicação](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) (uma funcionalidade do [Monitor Azure)](https://docs.microsoft.com/azure/azure-monitor/)para recolher dados dos utilizadores. Requer que adicione instrumentação ao seu código de cliente, conforme descrito neste artigo. Finalmente, precisará de um mecanismo de reporte para analisar os dados. Recomendamos o Power BI, mas pode utilizar o Painel de Aplicação ou qualquer ferramenta que se ligue aos Insights de Aplicação.

> [!NOTE]
> O padrão descrito neste artigo é para cenários avançados e dados de clickstream gerados pelo código que adiciona ao seu cliente. Em contraste, os registos de serviço são fáceis de configurar, fornecem uma gama de métricas, e podem ser feitos no portal sem o código necessário. É recomendada a exploração madeireira para todos os cenários. Para mais informações, consulte [Recolher e analisar dados de registo](search-monitor-logs.md).

## <a name="identify-relevant-search-data"></a>Identificar dados de pesquisa relevantes

Para ter métricas úteis para análise de tráfego de pesquisa, é necessário registar alguns sinais dos utilizadores da sua aplicação de pesquisa. Estes sinais significam conteúdo que os utilizadores estão interessados e que consideram relevantes. Para análise de tráfego de pesquisa, estes incluem:

+ Eventos de pesquisa gerados pelo utilizador: Apenas as consultas de pesquisa iniciadas por um utilizador são interessantes. Os pedidos de pesquisa utilizados para povoar facetas, conteúdos adicionais ou qualquer informação interna, não são importantes e distorcem e distorcem os seus resultados.

+ Eventos de clique gerados pelo utilizador: Numa página de resultados de pesquisa, um evento de clique geralmente significa que um documento é um resultado relevante para uma consulta de pesquisa específica.

Ao ligar eventos de pesquisa e clique com um ID de correlação, você vai obter uma compreensão mais profunda de quão bem a funcionalidade de pesquisa da sua aplicação está a funcionar.

## <a name="add-search-traffic-analytics"></a>Adicionar análise de tráfego de pesquisa

Na página do [portal](https://portal.azure.com) do seu serviço de Pesquisa Cognitiva Azure, a página Search Traffic Analytics contém uma folha de batota para seguir este padrão de telemetria. A partir desta página, pode selecionar ou criar um recurso Application Insights, obter a chave de instrumentação, copiar snippets que pode adaptar-se para a sua solução, e descarregar um relatório power BI que é construído sobre o esquema refletido no padrão.

![Pesquisar página de Análise de Tráfego no portal](media/search-traffic-analytics/azuresearch-trafficanalytics.png "Pesquisar página de Análise de Tráfego no portal")

## <a name="1---set-up-application-insights"></a>1 - Configurar insights de aplicação

Selecione um recurso de Insights de Aplicação existente ou [crie um](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) se ainda não tiver um. Se utilizar a página Search Traffic Analytics, pode copiar a chave de instrumentação que a sua aplicação necessita para se ligar aos Insights de Aplicação.

Uma vez que tenha um recurso Application Insights, pode seguir [instruções para idiomas e plataformas suportadas](https://docs.microsoft.com/azure/azure-monitor/app/platforms) para registar a sua aplicação. O registo é simplesmente adicionar a chave de instrumentação de Application Insights ao seu código, que configura a associação. Pode encontrar a chave no portal ou na página Search Traffic Analytics quando selecionar um recurso existente.

Um atalho que funciona para alguns tipos de projetos do Estúdio Visual reflete-se nos seguintes passos. Cria um recurso e regista a sua aplicação em apenas alguns cliques.

1. Para o desenvolvimento do Visual Studio e ASP.NET, abra a sua solução e selecione **Project** > **Add Application Insights Telemetria**.

1. Clique em **Começar**.

1. Registe a sua aplicação fornecendo uma conta Microsoft, subscrição Azure e um recurso Application Insights (um novo recurso é o padrão). Clique no **Registo**.

Neste ponto, a sua aplicação é configurada para monitorização de aplicações, o que significa que todas as cargas de página são rastreadas com métricas padrão. Para obter mais informações sobre os passos anteriores, consulte [a telemetria do lado do servidor Do servidor Enable Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core#enable-application-insights-server-side-telemetry-visual-studio).

## <a name="2---add-instrumentation"></a>2 - Adicionar instrumentação

Este passo é onde você instrumenta a sua própria aplicação de pesquisa, usando o recurso Application Insights que criou no passo acima. São quatro passos para este processo, começando pela criação de um cliente de telemetria.

### <a name="step-1-create-a-telemetry-client"></a>Passo 1: Criar um cliente de telemetria

Crie um objeto que envie eventos para Application Insights. Pode adicionar instrumentação ao código de aplicação do lado do servidor ou código do lado do cliente que funciona num browser, expresso aqui como variantes C# e JavaScript (para outros idiomas, consulte a lista completa de [plataformas e estruturas suportadas](https://docs.microsoft.com/azure/application-insights/app-insights-platforms). Escolha a abordagem que lhe dá a profundidade de informação desejada.

A telemetria do lado do servidor captura métricas na camada de aplicação, por exemplo em aplicações que executam como um serviço web na nuvem, ou como uma aplicação no local numa rede corporativa. A telemetria do lado do servidor captura eventos de pesquisa e clique, a posição de um documento em resultados e informações de consulta, mas a sua recolha de dados será consultada para qualquer informação disponível nessa camada.

No cliente, pode ter um código adicional que manipula as inputs de consulta, adiciona navegação ou inclui contexto (por exemplo, consultas iniciadas a partir de uma página inicial versus uma página de produto). Se isto descrever a sua solução, poderá optar pela instrumentação do lado do cliente para que a sua telemetria reflita os detalhes adicionais. A forma como este detalhe adicional é recolhido vai além do âmbito deste padrão, mas pode rever os Insights de [Aplicação para páginas web](https://docs.microsoft.com/azure/azure-monitor/app/javascript#explore-browserclient-side-data) para obter mais orientação. 

**Utilizar C#**

Para C#, a **Chave de Instrumentação** encontra-se na configuração da sua aplicação, como as definições.json se o seu projeto for ASP.NET. Consulte as instruções de registo se não tiver a certeza da localização da chave.

```csharp
private static TelemetryClient _telemetryClient;

// Add a constructor that accepts a telemetry client:
public HomeController(TelemetryClient telemetry)
    {
        _telemetryClient = telemetry;
    }
```

**Utilizar JavaScript**

```javascript
<script type="text/javascript">var appInsights=window.appInsights||function(config){function r(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},u=document,e=window,o="script",s=u.createElement(o),i,f;s.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js";u.getElementsByTagName(o)[0].parentNode.appendChild(s);try{t.cookie=u.cookie}catch(h){}for(t.queue=[],i=["Event","Exception","Metric","PageView","Trace","Dependency"];i.length;)r("track"+i.pop());return r("setAuthenticatedUserContext"),r("clearAuthenticatedUserContext"),config.disableExceptionTracking||(i="onerror",r("_"+i),f=e[i],e[i]=function(config,r,u,e,o){var s=f&&f(config,r,u,e,o);return s!==!0&&t["_"+i](config,r,u,e,o),s}),t}
({
instrumentationKey: "<YOUR INSTRUMENTATION KEY>"
});
window.appInsights=appInsights;
</script>
```

### <a name="step-2-request-a-search-id-for-correlation"></a>Passo 2: Solicitar um ID de pesquisa para correlação

Para correlacionar pedidos de pesquisa com cliques, é necessário ter uma identificação de correlação que relacione estes dois eventos distintos. A Azure Cognitive Search fornece-lhe um ID de pesquisa quando o solicita com um cabeçalho HTTP.

Ter o ID de pesquisa permite a correlação das métricas emitidas pela Azure Cognitive Search para o pedido em si, com as métricas personalizadas que está a registar em Insights de Aplicação.  

**Utilizar C#**

```csharp
// This sample uses the .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

var client = new SearchIndexClient(<SearchServiceName>, <IndexName>, new SearchCredentials(<QueryKey>)

// Use HTTP headers so that you can get the search ID from the response
var headers = new Dictionary<string, List<string>>() { { "x-ms-azs-return-searchid", new List<string>() { "true" } } };
var response = await client.Documents.SearchWithHttpMessagesAsync(searchText: searchText, searchParameters: parameters, customHeaders: headers);
string searchId = string.Empty;
if (response.Response.Headers.TryGetValues("x-ms-azs-searchid", out IEnumerable<string> headerValues)){
    searchId = headerValues.FirstOrDefault();
}
```

**Use javaScript (chamando APIs REST)**

```javascript
request.setRequestHeader("x-ms-azs-return-searchid", "true");
request.setRequestHeader("Access-Control-Expose-Headers", "x-ms-azs-searchid");
var searchId = request.getResponseHeader('x-ms-azs-searchid');
```

### <a name="step-3-log-search-events"></a>Passo 3: Eventos de pesquisa de log

Sempre que um pedido de pesquisa for emitido por um utilizador, deve registar isso como um evento de pesquisa com o seguinte esquema num evento personalizado application Insights. Lembre-se de registar apenas consultas de pesquisa geradas pelo utilizador.

+ Nome do serviço de pesquisa **SearchServiceName**: (string)
+ **SearchId**: (guia) identificador único da consulta de pesquisa (vem na resposta de pesquisa)
+ **IndexName**: (string) índice de serviço de pesquisa a ser consultado
+ **ConsultasTermos**: (string) termos de pesquisa introduzidos pelo utilizador
+ **ResultadoSContagem**: (int) número de documentos que foram devolvidos (vem na resposta de pesquisa)
+ **PontuaçãoPerfil:**(cadeia) nome do perfil de pontuação utilizado, se houver

> [!NOTE]
> Solicite a contagem de consultas geradas pelo utilizador adicionando $count=fiel à sua consulta de pesquisa. Para mais informações, consulte Documentos de [Pesquisa (REST)](/rest/api/searchservice/search-documents#counttrue--false).
>

**Utilizar C#**

```csharp
var properties = new Dictionary <string, string> {
    {"SearchServiceName", <service name>},
    {"SearchId", <search Id>},
    {"IndexName", <index name>},
    {"QueryTerms", <search terms>},
    {"ResultCount", <results count>},
    {"ScoringProfile", <scoring profile used>}
    };
_telemetryClient.TrackEvent("Search", properties);
```

**Utilizar JavaScript**

```javascript
appInsights.trackEvent("Search", {
SearchServiceName: <service name>,
SearchId: <search id>,
IndexName: <index name>,
QueryTerms: <search terms>,
ResultCount: <results count>,
ScoringProfile: <scoring profile used>
});
```

### <a name="step-4-log-click-events"></a>Passo 4: Registar eventos de clique

Sempre que um utilizador clica num documento, é um sinal que deve ser registado para fins de análise de pesquisa. Utilize eventos personalizados de Informação de Aplicação para registar estes eventos com o seguinte esquema:

+ **Nome do serviço**: (string) nome do serviço de pesquisa
+ **SearchId**: (guia) identificador único da consulta de pesquisa relacionada
+ **DocId**: (identificador de documentos de cadeia)
+ **Posição**: (int) classificação do documento na página de resultados da pesquisa

> [!NOTE]
> A posição refere-se à ordem cardeal na sua candidatura. É livre de definir este número, desde que seja sempre o mesmo, para permitir a comparação.
>

**Utilizar C#**

```csharp
var properties = new Dictionary <string, string> {
    {"SearchServiceName", <service name>},
    {"SearchId", <search id>},
    {"ClickedDocId", <clicked document id>},
    {"Rank", <clicked document position>}
    };
_telemetryClient.TrackEvent("Click", properties);
```

**Utilizar JavaScript**

```javascript
appInsights.trackEvent("Click", {
    SearchServiceName: <service name>,
    SearchId: <search id>,
    ClickedDocId: <clicked document id>,
    Rank: <clicked document position>
});
```

## <a name="3---analyze-in-power-bi"></a>3 - Analisar no Power BI

Depois de ter instrumentado a sua aplicação e verificado que a sua aplicação está corretamente ligada aos Insights de Aplicação, descarrega um modelo de relatório predefinido para analisar dados no ambiente de trabalho do Power BI. O relatório contém gráficos e tabelas pré-definidas úteis para analisar os dados adicionais capturados para análise de tráfego de pesquisa.

1. No painel de navegação à esquerda do painel de pesquisa cognitiva Azure, em **Definições,** clique em análise de **tráfego de pesquisa**.

1. Na página de análise de **tráfego de pesquisa,** no passo 3, clique em **Get Power BI Desktop** para instalar o Power BI.

   ![Obtenha relatórios de Power BI](./media/search-traffic-analytics/get-use-power-bi.png "Obtenha relatórios de Power BI")

1. Na mesma página, clique no **relatório Download Power BI**.

1. O relatório abre no Power BI Desktop, e é-lhe solicitado que se conectem aos Insights de Aplicação e forneçam credenciais. Pode encontrar informações de ligação nas páginas do portal Azure para o seu recurso Application Insights. Para obter credenciais, forneça o mesmo nome de utilizador e senha que utiliza para iniciar sessão no portal.

   ![Ligue-se ao Application Insights](./media/search-traffic-analytics/connect-to-app-insights.png "Ligue-se ao Application Insights")

1. Clique em **Carregar**.

O relatório contém gráficos e tabelas que o ajudam a tomar decisões mais informadas para melhorar o seu desempenho de pesquisa e relevância.

As métricas incluíam os seguintes itens:

+ Volume de pesquisa e pares de documentos de termo mais populares: termos que resultam no mesmo documento clicado, encomendado por cliques.
+ Pesquisas sem cliques: termos para consultas de topo que não registam cliques

A imagem que se segue mostra como pode ser um relatório incorporado se tiver usado todos os elementos do esquema.

![Painel de instrumentos power BI para pesquisa cognitiva azure](./media/search-traffic-analytics/azuresearch-powerbi-dashboard.png "Painel de instrumentos power BI para pesquisa cognitiva azure")

## <a name="next-steps"></a>Passos seguintes

Instrumente a sua aplicação de pesquisa para obter dados poderosos e perspicazes sobre o seu serviço de pesquisa.

Pode encontrar mais informações sobre os Insights de [Aplicação](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) e visitar a [página de preços](https://azure.microsoft.com/pricing/details/application-insights/) para saber mais sobre os seus diferentes níveis de serviço.

Saiba mais sobre a criação de relatórios incríveis. Ver [Começar com power bi desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/) para obter detalhes.