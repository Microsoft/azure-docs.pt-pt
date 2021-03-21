---
title: Telemetria para análise de tráfego de pesquisa
titleSuffix: Azure Cognitive Search
description: Ativar a análise do tráfego de pesquisa para Azure Cognitive Search, recolher telemetria e eventos iniciados pelo utilizador usando Insights de Aplicação e, em seguida, analisar as descobertas num relatório power BI.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 1/29/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 89d067162dacb7a0ca25de826630e1986f1035e1
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102485474"
---
# <a name="collect-telemetry-data-for-search-traffic-analytics"></a>Recolher dados de telemetria para análise de tráfego de pesquisa

A análise do tráfego de pesquisa é um padrão para recolher telemetria sobre interações do utilizador com a sua aplicação Azure Cognitive Search, como eventos de clique iniciados pelo utilizador e entradas de teclado. Utilizando estas informações, pode determinar a eficácia da sua solução de pesquisa, incluindo termos de pesquisa populares, taxa de clique e quais as entradas de consulta que produzem resultados nulos.

Este padrão requer uma dependência de [Application Insights](../azure-monitor/app/app-insights-overview.md) (uma característica do [Azure Monitor](../azure-monitor/index.yml)) para recolher dados do utilizador. Requer que adicione instrumentação ao código do seu cliente, conforme descrito neste artigo. Finalmente, vai precisar de um mecanismo de reporte para analisar os dados. Recomendamos o Power BI, mas pode utilizar o Painel de Aplicação ou qualquer ferramenta que se conecte ao Application Insights.

> [!NOTE]
> O padrão descrito neste artigo é para cenários avançados e dados clickstream gerados por código que adiciona ao seu cliente. Em contraste, os registos de serviço são fáceis de configurar, fornecem uma gama de métricas, e podem ser feitos no portal sem necessidade de código. Para todos os cenários é recomendado a ativação de registos. Para obter mais informações, consulte [Recolher e analisar dados de registo.](search-monitor-logs.md)

## <a name="identify-relevant-search-data"></a>Identificar dados de pesquisa relevantes

Para ter métricas úteis para análise de tráfego de pesquisa, é necessário registar alguns sinais dos utilizadores da sua aplicação de pesquisa. Estes sinais significam conteúdo que os utilizadores estão interessados e que consideram relevantes. Para análise de tráfego de pesquisa, estes incluem:

+ Eventos de pesquisa gerados pelo utilizador: Apenas as consultas de pesquisa iniciadas por um utilizador são interessantes. Outros pedidos de pesquisa, como os usados para povoar facetas ou recuperar informações internas, não são importantes. Certifique-se apenas de eventos iniciados pelo utilizador para evitar distorções ou distorções nos seus resultados.

+ Eventos de clique gerados pelo utilizador: Numa página de resultados de pesquisa, um evento de clique geralmente significa que um documento é um resultado relevante para uma consulta de pesquisa específica.

Ao ligar eventos de pesquisa e clicar em eventos com um ID de correlação, você ganhará uma compreensão mais profunda de quão bem a funcionalidade de pesquisa da sua aplicação está a executar.

## <a name="add-search-traffic-analytics"></a>Adicionar análise de tráfego de pesquisa

Na página do [portal](https://portal.azure.com) do seu serviço de Pesquisa Cognitiva Azure, abra a página Search Traffic Analytics para aceder a uma folha de batota para seguir este padrão de telemetria. A partir desta página, pode selecionar ou criar um recurso Application Insights, obter a chave de instrumentação, copiar os snippets que pode adaptar para a sua solução e fazer o download de um relatório Power BI que é construído sobre o esquema refletido no padrão.

![Pesquisa de tráfego Página analítica no portal](media/search-traffic-analytics/azuresearch-trafficanalytics.png "Pesquisa de tráfego Página analítica no portal")

## <a name="1---set-up-application-insights"></a>1 - Configurar Insights de Aplicação

Selecione um recurso de Insights de Aplicação existente ou [crie um](../azure-monitor/app/create-new-resource.md) se ainda não tiver um. Se utilizar a página Search Traffic Analytics, pode copiar a chave de instrumentação que a sua aplicação necessita para se ligar a Insights de Aplicação.

Assim que tiver um recurso Application Insights, pode seguir [instruções para que os idiomas e plataformas suportados registem](../azure-monitor/app/platforms.md) a sua aplicação. O registo é simplesmente adicionar a chave de instrumentação do Application Insights ao seu código, que configura a associação. Pode encontrar a chave no portal ou na página Search Traffic Analytics quando selecionar um recurso existente.

Um atalho que funciona para alguns tipos de projetos do Visual Studio reflete-se nos seguintes passos. Cria um recurso e regista a sua aplicação em apenas alguns cliques.

1. Para o desenvolvimento de Estúdio Visual e ASP.NET, abra a sua solução e selecione **Project**  >  **Add Application Insights Telemetria**.

1. Clique em **Get Started** (Começar).

1. Registe a sua aplicação fornecendo uma conta Microsoft, subscrição Azure e um recurso Application Insights (um novo recurso é o padrão). Clique em **Registar**.

Neste momento, a sua aplicação está configurada para monitorização da aplicação, o que significa que todas as cargas de página são rastreadas com métricas padrão. Para obter mais informações sobre os passos anteriores, consulte [a telemetria do lado do servidor do Enable Application Insights](../azure-monitor/app/asp-net-core.md#enable-application-insights-server-side-telemetry-visual-studio).

## <a name="2---add-instrumentation"></a>2 - Adicionar instrumentação

Este passo é onde você instrumento a sua própria aplicação de pesquisa, usando o recurso Application Insights que criou no degrau acima. Há quatro passos neste processo, começando pela criação de um cliente de telemetria.

### <a name="step-1-create-a-telemetry-client"></a>Passo 1: Criar um cliente de telemetria

Crie um objeto que envie eventos para Application Insights. Pode adicionar instrumentação ao código de aplicação do lado do servidor ou ao código do lado do cliente em execução num browser, expresso aqui como variantes C# e JavaScript (para outros idiomas, consulte a lista completa de [plataformas e quadros suportados).](../azure-monitor/app/platforms.md) Escolha a abordagem que lhe dá a profundidade desejada da informação.

A telemetria do lado do servidor capta métricas na camada de aplicação, por exemplo, em aplicações que são funcionando como um serviço web na nuvem, ou como uma aplicação no local numa rede corporativa. A telemetria do lado do servidor captura eventos de pesquisa e clique, a posição de um documento nos resultados e informações de consulta, mas a sua recolha de dados será avaliada em qualquer informação disponível nessa camada.

No cliente, poderá ter um código adicional que manipula entradas de consulta, adiciona navegação ou inclui contexto (por exemplo, consultas iniciadas a partir de uma página inicial versus uma página de produto). Se isto descrever a sua solução, poderá optar pela instrumentação do lado do cliente para que a sua telemetria reflita o detalhe adicional. A forma como este detalhe adicional é recolhido vai além do âmbito deste padrão, mas pode rever Insights de [Aplicação para páginas web](../azure-monitor/app/javascript.md#explore-browserclient-side-data) para obter mais direção. 

**Utilizar C#**

Para C#, o **InstrumentationKey** deve ser definido na configuração da sua aplicação, tal como appsettings.jsse o seu projeto for ASP.NET. Volte a consultar as instruções de registo se não tiver a certeza da localização da chave.

```csharp
private static TelemetryClient _telemetryClient;

// Add a constructor that accepts a telemetry client:
public HomeController(TelemetryClient telemetry)
{
    _telemetryClient = telemetry;
}
```

**Utilizar JavaScript**

O snippet atual (listado abaixo) é a versão "5", a versão está codificada no snippet como sv:"#" e a [versão atual também está disponível no GitHub](https://go.microsoft.com/fwlink/?linkid=2156318).

```html
<script type="text/javascript">
!function(T,l,y){var S=T.location,k="script",D="instrumentationKey",C="ingestionendpoint",I="disableExceptionTracking",E="ai.device.",b="toLowerCase",w="crossOrigin",N="POST",e="appInsightsSDK",t=y.name||"appInsights";(y.name||T[e])&&(T[e]=t);var n=T[t]||function(d){var g=!1,f=!1,m={initialize:!0,queue:[],sv:"5",version:2,config:d};function v(e,t){var n={},a="Browser";return n[E+"id"]=a[b](),n[E+"type"]=a,n["ai.operation.name"]=S&&S.pathname||"_unknown_",n["ai.internal.sdkVersion"]="javascript:snippet_"+(m.sv||m.version),{time:function(){var e=new Date;function t(e){var t=""+e;return 1===t.length&&(t="0"+t),t}return e.getUTCFullYear()+"-"+t(1+e.getUTCMonth())+"-"+t(e.getUTCDate())+"T"+t(e.getUTCHours())+":"+t(e.getUTCMinutes())+":"+t(e.getUTCSeconds())+"."+((e.getUTCMilliseconds()/1e3).toFixed(3)+"").slice(2,5)+"Z"}(),iKey:e,name:"Microsoft.ApplicationInsights."+e.replace(/-/g,"")+"."+t,sampleRate:100,tags:n,data:{baseData:{ver:2}}}}var h=d.url||y.src;if(h){function a(e){var t,n,a,i,r,o,s,c,u,p,l;g=!0,m.queue=[],f||(f=!0,t=h,s=function(){var e={},t=d.connectionString;if(t)for(var n=t.split(";"),a=0;a<n.length;a++){var i=n[a].split("=");2===i.length&&(e[i[0][b]()]=i[1])}if(!e[C]){var r=e.endpointsuffix,o=r?e.location:null;e[C]="https://"+(o?o+".":"")+"dc."+(r||"services.visualstudio.com")}return e}(),c=s[D]||d[D]||"",u=s[C],p=u?u+"/v2/track":d.endpointUrl,(l=[]).push((n="SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details)",a=t,i=p,(o=(r=v(c,"Exception")).data).baseType="ExceptionData",o.baseData.exceptions=[{typeName:"SDKLoadFailed",message:n.replace(/\./g,"-"),hasFullStack:!1,stack:n+"\nSnippet failed to load ["+a+"] -- Telemetry is disabled\nHelp Link: https://go.microsoft.com/fwlink/?linkid=2128109\nHost: "+(S&&S.pathname||"_unknown_")+"\nEndpoint: "+i,parsedStack:[]}],r)),l.push(function(e,t,n,a){var i=v(c,"Message"),r=i.data;r.baseType="MessageData";var o=r.baseData;return o.message='AI (Internal): 99 message:"'+("SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details) ("+n+")").replace(/\"/g,"")+'"',o.properties={endpoint:a},i}(0,0,t,p)),function(e,t){if(JSON){var n=T.fetch;if(n&&!y.useXhr)n(t,{method:N,body:JSON.stringify(e),mode:"cors"});else if(XMLHttpRequest){var a=new XMLHttpRequest;a.open(N,t),a.setRequestHeader("Content-type","application/json"),a.send(JSON.stringify(e))}}}(l,p))}function i(e,t){f||setTimeout(function(){!t&&m.core||a()},500)}var e=function(){var n=l.createElement(k);n.src=h;var e=y[w];return!e&&""!==e||"undefined"==n[w]||(n[w]=e),n.onload=i,n.onerror=a,n.onreadystatechange=function(e,t){"loaded"!==n.readyState&&"complete"!==n.readyState||i(0,t)},n}();y.ld<0?l.getElementsByTagName("head")[0].appendChild(e):setTimeout(function(){l.getElementsByTagName(k)[0].parentNode.appendChild(e)},y.ld||0)}try{m.cookie=l.cookie}catch(p){}function t(e){for(;e.length;)!function(t){m[t]=function(){var e=arguments;g||m.queue.push(function(){m[t].apply(m,e)})}}(e.pop())}var n="track",r="TrackPage",o="TrackEvent";t([n+"Event",n+"PageView",n+"Exception",n+"Trace",n+"DependencyData",n+"Metric",n+"PageViewPerformance","start"+r,"stop"+r,"start"+o,"stop"+o,"addTelemetryInitializer","setAuthenticatedUserContext","clearAuthenticatedUserContext","flush"]),m.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4};var s=(d.extensionConfig||{}).ApplicationInsightsAnalytics||{};if(!0!==d[I]&&!0!==s[I]){var c="onerror";t(["_"+c]);var u=T[c];T[c]=function(e,t,n,a,i){var r=u&&u(e,t,n,a,i);return!0!==r&&m["_"+c]({message:e,url:t,lineNumber:n,columnNumber:a,error:i}),r},d.autoExceptionInstrumented=!0}return m}(y.cfg);function a(){y.onInit&&y.onInit(n)}(T[t]=n).queue&&0===n.queue.length?(n.queue.push(a),n.trackPageView({})):a()}(window,document,{
src: "https://js.monitor.azure.com/scripts/b/ai.2.min.js", // The SDK URL Source
// name: "appInsights", // Global SDK Instance name defaults to "appInsights" when not supplied
// ld: 0, // Defines the load delay (in ms) before attempting to load the sdk. -1 = block page load and add to head. (default) = 0ms load after timeout,
// useXhr: 1, // Use XHR instead of fetch to report failures (if available),
crossOrigin: "anonymous", // When supplied this will add the provided value as the cross origin attribute on the script tag
// onInit: null, // Once the application insights instance has loaded and initialized this callback function will be called with 1 argument -- the sdk instance (DO NOT ADD anything to the sdk.queue -- As they won't get called)
cfg: { // Application Insights Configuration
    instrumentationKey: "<YOUR INSTRUMENTATION KEY>"
}});
</script>
```

> [!NOTE]
> Para a legibilidade e para reduzir possíveis erros javaScript, todas as opções de configuração possíveis estão listadas numa nova linha no código de snippet acima, se não quiser alterar o valor de uma linha comentada, pode ser removida.


### <a name="step-2-request-a-search-id-for-correlation"></a>Passo 2: Solicite um ID de pesquisa para correlação

Para correlacionar os pedidos de pesquisa com cliques, é necessário ter um ID de correlação que relaciona estes dois eventos distintos. A Azure Cognitive Search fornece-lhe um ID de pesquisa quando o solicita com um cabeçalho HTTP.

Ter o ID de pesquisa permite a correlação das métricas emitidas pela Azure Cognitive Search para o pedido em si, com as métricas personalizadas que você está registando em Insights de Aplicação.

**Use C# (mais recente v11 SDK)**

O SDK mais recente requer a utilização de um Pipeline Http para definir o cabeçalho conforme detalhado nesta [amostra](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Azure.Core/samples/Pipeline.md#implementing-a-syncronous-policy).

```csharp
// Create a custom policy to add the correct headers
public class SearchIdPipelinePolicy : HttpPipelineSynchronousPolicy
{
    public override void OnSendingRequest(HttpMessage message)
    {
        message.Request.Headers.SetValue("x-ms-azs-return-searchid", "true");
    }
}
```

```csharp
// This sample uses the .NET SDK https://www.nuget.org/packages/Azure.Search.Documents

SearchClientOptions clientOptions = new SearchClientOptions();
clientOptions.AddPolicy(new SearchIdPipelinePolicy(), HttpPipelinePosition.PerCall);

var client = new SearchClient("<SearchServiceName>", "<IndexName>", new AzureKeyCredential("<QueryKey>"), options: clientOptions);

Response<SearchResults<SearchDocument>> response = await client.SearchAsync<SearchDocument>(searchText: searchText, searchOptions: options);
string searchId = string.Empty;
if (response.GetRawResponse().Headers.TryGetValues("x-ms-azs-searchid", out IEnumerable<string> headerValues))
{
    searchId = headerValues.FirstOrDefault();
}
```

**Use C# (v10 SDK mais antigo)**

```csharp
// This sample uses the .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

var client = new SearchIndexClient(<SearchServiceName>, <IndexName>, new SearchCredentials(<QueryKey>));

// Use HTTP headers so that you can get the search ID from the response
var headers = new Dictionary<string, List<string>>() { { "x-ms-azs-return-searchid", new List<string>() { "true" } } };
var response = await client.Documents.SearchWithHttpMessagesAsync(searchText: searchText, searchParameters: parameters, customHeaders: headers);
string searchId = string.Empty;
if (response.Response.Headers.TryGetValues("x-ms-azs-searchid", out IEnumerable<string> headerValues))
{
    searchId = headerValues.FirstOrDefault();
}
```

**Use JavaScript (chamando REST APIs)**

```javascript
request.setRequestHeader("x-ms-azs-return-searchid", "true");
request.setRequestHeader("Access-Control-Expose-Headers", "x-ms-azs-searchid");
var searchId = request.getResponseHeader('x-ms-azs-searchid');
```

### <a name="step-3-log-search-events"></a>Passo 3: Registar eventos de pesquisa

Sempre que um pedido de pesquisa é emitido por um utilizador, deve registar isso como um evento de pesquisa com o seguinte esquema num evento personalizado Application Insights. Lembre-se de registar apenas consultas de pesquisa geradas pelo utilizador.

+ Nome do serviço de pesquisa **SearchService**: (string)
+ **SearchId**: (guia) identificador único da consulta de pesquisa (vem na resposta à pesquisa)
+ **ÍndiceName**: índice de serviço de pesquisa (cadeia) a ser consultado
+ **QueryTerms**: (cadeia) termos de pesquisa introduzidos pelo utilizador
+ **ResultadosCount**: (int) número de documentos que foram devolvidos (vem na resposta à pesquisa)
+ **ScoringProfile**: (cadeia) nome do perfil de pontuação utilizado, se houver

> [!NOTE]
> Solicite a contagem de consultas geradas pelo utilizador adicionando $count=fiel à sua consulta de pesquisa. Para obter mais informações, consulte [Documentos de Pesquisa (REST)](/rest/api/searchservice/search-documents#query-parameters).
>

**Utilizar C#**

```csharp
var properties = new Dictionary <string, string> 
{
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

### <a name="step-4-log-click-events"></a>Passo 4: Registar eventos

Sempre que um utilizador clica num documento, é um sinal que deve ser registado para efeitos de análise de pesquisa. Utilize eventos personalizados da Application Insights para registar estes eventos com o seguinte esquema:

+ **Nome de serviço**: (string) nome do serviço de pesquisa
+ **SearchId**: (guia) identificador único da consulta de pesquisa relacionada
+ **DocId**: (string) identificador de documentos
+ **Posição**: (int) classificação do documento na página de resultados de pesquisa

> [!NOTE]
> Posição refere-se à ordem cardeal na sua candidatura. Você é livre de definir este número, desde que seja sempre o mesmo, para permitir a comparação.
>

**Utilizar C#**

```csharp
var properties = new Dictionary <string, string> 
{
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

## <a name="3---analyze-in-power-bi"></a>3 - Analisar em Power BI

Depois de ter instrumentalizado a sua aplicação e verificado que a sua aplicação está corretamente ligada ao Application Insights, descarrega um modelo de relatório predefinido para analisar dados no ambiente de trabalho power BI. O relatório contém gráficos e tabelas predefinidos úteis para analisar os dados adicionais capturados para análise de tráfego de pesquisa.

1. No painel de navegação esquerda do painel de pesquisa cognitiva Azure, em **Definições,** clique em **Pesquisar análises de tráfego**.

1. Na página **de análise de tráfego de pesquisa,** no passo 3, clique em Get Power BI **Desktop** para instalar Power BI.

   ![Obtenha relatórios de Power BI](./media/search-traffic-analytics/get-use-power-bi.png "Obtenha relatórios de Power BI")

1. Na mesma página, clique no **relatório Download Power BI**.

1. O relatório abre no Power BI Desktop, e é solicitado que se conecte a Application Insights e forneça credenciais. Pode encontrar informações de ligação nas páginas do portal Azure para o seu recurso Application Insights. Para obter credenciais, forneça o mesmo nome de utilizador e palavra-passe que utiliza para o acesso ao portal.

   ![Ligue-se ao Application Insights](./media/search-traffic-analytics/connect-to-app-insights.png "Ligue-se ao Application Insights")

1. Clique **em Carregar**.

O relatório contém gráficos e tabelas que o ajudam a tomar decisões mais informadas para melhorar o desempenho e relevância da sua pesquisa.

As métricas incluíam os seguintes itens:

+ Volume de pesquisa e pares de termo-documento mais populares: termos que resultam no mesmo documento clicado, encomendado por cliques.
+ Pesquisas sem cliques: termos para consultas de topo que registam nenhum clique

A imagem que se segue mostra como um relatório incorporado pode parecer se tiver usado todos os elementos do esquema.

![Painel de bi de potência para pesquisa cognitiva de Azure](./media/search-traffic-analytics/azuresearch-powerbi-dashboard.png "Painel de bi de potência para pesquisa cognitiva de Azure")

## <a name="next-steps"></a>Passos seguintes

Instrumento a sua aplicação de pesquisa para obter dados poderosos e perspicazes sobre o seu serviço de pesquisa.

Pode encontrar mais informações sobre [o Application Insights](../azure-monitor/app/app-insights-overview.md) e visitar a página de [preços](https://azure.microsoft.com/pricing/details/application-insights/) para saber mais sobre os seus diferentes níveis de serviço.

Saiba mais sobre a criação de relatórios incríveis. Consulte ['Começar com o Power BI Desktop'](/power-bi/fundamentals/desktop-getting-started) para obter mais detalhes.
