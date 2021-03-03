---
title: Cadeias de ligação em Azure Application Insights | Microsoft Docs
description: Como usar cordas de ligação.
ms.topic: conceptual
author: timothymothra
ms.author: tilee
ms.date: 01/17/2020
ms.custom: devx-track-js, devx-track-csharp
ms.reviewer: mbullwin
ms.openlocfilehash: 26b551b5e3c21dcd77a5656a6f8a18c5de4feaf1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101723474"
---
# <a name="connection-strings"></a>Cadeias de ligação

## <a name="overview"></a>Descrição Geral

As cadeias de ligação fornecem aos utilizadores do Application Insight uma única definição de configuração, eliminando a necessidade de várias definições de procuração. Altamente útil para servidores web intranet, ambientes de nuvem soberana ou híbrida que procuram enviar dados para o serviço de monitorização.

Os pares de valor chave proporcionam uma maneira fácil para os utilizadores definirem uma combinação de sufixo de prefixo para cada serviço/produto Application Insights (IA).

> [!IMPORTANT]
> Não recomendamos a definição da tecla de ligação e instrumentação. No caso de um utilizador definir ambos, o que for que tenha sido definido por último terá precedência. 


## <a name="scenario-overview"></a>Scenario overview (Descrição geral do cenário) 

Cenários de clientes onde visualizamos isto tendo mais impacto:

- Exceções de firewall ou redirecionamentos de procuração 

    Nos casos em que é necessária monitorização para o servidor web intranet, a nossa solução anterior pediu aos clientes que adicionassem pontos finais de serviço individuais à sua configuração. Para mais informações, consulte [aqui.](../faq.md#can-i-monitor-an-intranet-web-server) 
    As cordas de ligação oferecem uma alternativa melhor reduzindo este esforço a uma única configuração. Um simples prefixo, alteração de sufixo permite a população automática e a reorientação de todos os pontos finais para os serviços certos. 

- Ambientes de nuvem soberana ou híbrida

    Os utilizadores podem enviar dados para uma [Região governamental do Azure](../../azure-government/compare-azure-government-global-azure.md#application-insights)definida.
    As cadeias de ligação permitem definir definições de ponto final para os seus servidores intranet ou definições de nuvem híbrida. 

## <a name="getting-started"></a>Introdução

### <a name="finding-my-connection-string"></a>Encontrar a minha ligação?

A sua cadeia de ligação é exibida na lâmina de visão geral do seu recurso Application Insights.

![fio de conexão na lâmina de visão geral](media/overview-dashboard/overview-connection-string.png)

### <a name="schema"></a>Esquema

#### <a name="max-length"></a>Comprimento máximo

A ligação tem um comprimento máximo suportado de 4096 caracteres.

#### <a name="key-value-pairs"></a>Pares chave-valor

A cadeia de ligação consiste numa lista de configurações representadas como pares de valor-chave separados por pontos e vírgulas: `key1=value1;key2=value2;key3=value3`

#### <a name="syntax"></a>Syntax

- `InstrumentationKey`(ex: 00000000-0000-0000-0000-0000000000000000000000000000)  A cadeia de ligação é um campo **necessário.**
- `Authorization` (ex: ikey) (Esta configuração é opcional porque hoje só apoiamos a autorização ikey.)
- `EndpointSuffix` (ex: applicationinsights.azure.cn) A definição do sufixo do ponto final instrui a nuvem Azure a que se conecta. O SDK reunirá o resto do ponto final para serviços individuais.
- Pontos finais explícitos.
  Qualquer serviço pode ser explicitamente ultrapassado na cadeia de ligação.
   - `IngestionEndpoint``https://dc.applicationinsights.azure.com`(ex)
   - `LiveEndpoint``https://live.applicationinsights.azure.com`(ex)
   - `ProfilerEndpoint``https://profiler.applicationinsights.azure.com`(ex)
   - `SnapshotEndpoint``https://snapshot.applicationinsights.azure.com`(ex)

#### <a name="endpoint-schema"></a>Esquema de ponto final

`<prefix>.<suffix>`
- Prefixo: Define um serviço. 
- Sufixo: Define o nome de domínio comum.

##### <a name="valid-suffixes"></a>Sufixos válidos

Aqui está uma lista de sufixos válidos 
- applicationinsights.azure.cn
- applicationinsights.us


Consulte também: https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification


##### <a name="valid-prefixes"></a>Prefixos válidos

- [Ingestão de telemetria:](./app-insights-overview.md)`dc`
- [Métricas ao Vivo:](./live-stream.md)`live`
- [Perfilador:](./profiler-overview.md)`profiler`
- [Instantâneo:](./snapshot-debugger.md)`snapshot`



## <a name="connection-string-examples"></a>Exemplos de cadeia de ligação


### <a name="minimal-valid-connection-string"></a>Cadeia de ligação mínima válida

`InstrumentationKey=00000000-0000-0000-0000-000000000000;`

Neste exemplo, apenas foi definida a Chave de Instrumentação.

- Regime de autorização falha no "ikey" 
- Chave de instrumentação: 0000000-0000-0000-0000-0000-0000000000000000000
- Os URIs de serviço regional baseiam-se nos incumprimentos da [SDK](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/Microsoft.ApplicationInsights/Extensibility/Implementation/Endpoints/Constants.cs) e ligar-se-ão ao Azure global público:
   - Ingestão: `https://dc.services.visualstudio.com/`
   - Métricas ao vivo: `https://rt.services.visualstudio.com/`
   - Perfilador: `https://profiler.monitor.azure.com/`
   - Debugger: `https://snapshot.monitor.azure.com/`



### <a name="connection-string-with-endpoint-suffix"></a>Cadeia de ligação com sufixo de ponto final

`InstrumentationKey=00000000-0000-0000-0000-000000000000;EndpointSuffix=ai.contoso.com;`

Neste exemplo, esta cadeia de ligação especifica o sufixo do ponto final e o SDK construirá pontos finais de serviço.

- Regime de autorização falha no "ikey" 
- Chave de instrumentação: 0000000-0000-0000-0000-0000-0000000000000000000
- As URI de serviço regional baseiam-se no sufixo de ponto final fornecido: 
   - Ingestão: `https://dc.ai.contoso.com`
   - Métricas ao vivo: `https://live.ai.contoso.com`
   - Perfilador: `https://profiler.ai.contoso.com`
   - Debugger: `https://snapshot.ai.contoso.com`  



### <a name="connection-string-with-explicit-endpoint-overrides"></a>Cadeia de ligação com sobreposições explícitas de ponto final 

`InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://custom.com:111/;LiveEndpoint=https://custom.com:222/;ProfilerEndpoint=https://custom.com:333/;SnapshotEndpoint=https://custom.com:444/;`

Neste exemplo, esta cadeia de ligação especifica sobreposições explícitas para cada serviço. O SDK utilizará os pontos finais exatos fornecidos sem modificação.

- Regime de autorização falha no "ikey" 
- Chave de instrumentação: 0000000-0000-0000-0000-0000-0000000000000000000
- Os URIs de serviço regional baseiam-se nos valores explícitos de sobreposição: 
   - Ingestão: `https://custom.com:111/`
   - Métricas ao vivo: `https://custom.com:222/`
   - Perfilador: `https://custom.com:333/`
   - Debugger: `https://custom.com:444/`  


## <a name="how-to-set-a-connection-string"></a>Como definir uma cadeia de ligação

As cordas de ligação são suportadas nas seguintes versões SDK:
- .NET e .NET Core v2.12.0
- Java v2.5.1 e Java 3.0
- JavaScript v2.3.0
- NodeJS v1.5.0
- Python v1.0.0

Uma cadeia de ligação pode ser definida por código, variável ambiental ou ficheiro de configuração.



### <a name="environment-variable"></a>Variável de ambiente

- Cadeia de ligação: `APPLICATIONINSIGHTS_CONNECTION_STRING`

### <a name="code-samples"></a>Exemplos de código

# <a name="netnetcore"></a>[.NET/.NetCore](#tab/net)

Definir a propriedade [TelemetriaConfiguration.ConnectionString](https://github.com/microsoft/ApplicationInsights-dotnet/blob/add45ceed35a817dc7202ec07d3df1672d1f610d/BASE/src/Microsoft.ApplicationInsights/Extensibility/TelemetryConfiguration.cs#L271-L274) ou [ApplicationInsightsServiceOptions.ConnectionString](https://github.com/microsoft/ApplicationInsights-dotnet/blob/81288f26921df1e8e713d31e7e9c2187ac9e6590/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs#L66-L69)

.NET Explicitamente Definido:
```csharp
var configuration = new TelemetryConfiguration
{
    ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
};
```

.NET Config File:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
    <ConnectionString>InstrumentationKey=00000000-0000-0000-0000-000000000000</ConnectionString>
</ApplicationInsights>
```

Conjunto NetCore explicitamente:
```csharp
public void ConfigureServices(IServiceCollection services)
{
    var options = new ApplicationInsightsServiceOptions { ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;" };
    services.AddApplicationInsightsTelemetry(options: options);
}
```

NetCore config.jsem: 

```json
{
  "ApplicationInsights": {
    "ConnectionString" : "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
    }
  }
```


# <a name="java"></a>[Java](#tab/java)


Java (v2.5.x) Explicitamente Definido:
```java
TelemetryConfiguration.getActive().setConnectionString("InstrumentationKey=00000000-0000-0000-0000-000000000000");
```

ApplicationInsights.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
    <ConnectionString>InstrumentationKey=00000000-0000-0000-0000-000000000000;</ConnectionString>
</ApplicationInsights>
```

# <a name="javascript"></a>[JavaScript](#tab/js)

Importante: O JavaScript não suporta a utilização de Variáveis ambientais.

Utilizando o corte:

O snippet atual (listado abaixo) é a versão "5", a versão está codificada no snippet como sv:"#" e a [versão atual também está disponível no GitHub](https://go.microsoft.com/fwlink/?linkid=2156318).

```html
<script type="text/javascript">
!function(T,l,y){var S=T.location,k="script",D="instrumentationKey",C="ingestionendpoint",I="disableExceptionTracking",E="ai.device.",b="toLowerCase",w="crossOrigin",N="POST",e="appInsightsSDK",t=y.name||"appInsights";(y.name||T[e])&&(T[e]=t);var n=T[t]||function(d){var g=!1,f=!1,m={initialize:!0,queue:[],sv:"5",version:2,config:d};function v(e,t){var n={},a="Browser";return n[E+"id"]=a[b](),n[E+"type"]=a,n["ai.operation.name"]=S&&S.pathname||"_unknown_",n["ai.internal.sdkVersion"]="javascript:snippet_"+(m.sv||m.version),{time:function(){var e=new Date;function t(e){var t=""+e;return 1===t.length&&(t="0"+t),t}return e.getUTCFullYear()+"-"+t(1+e.getUTCMonth())+"-"+t(e.getUTCDate())+"T"+t(e.getUTCHours())+":"+t(e.getUTCMinutes())+":"+t(e.getUTCSeconds())+"."+((e.getUTCMilliseconds()/1e3).toFixed(3)+"").slice(2,5)+"Z"}(),iKey:e,name:"Microsoft.ApplicationInsights."+e.replace(/-/g,"")+"."+t,sampleRate:100,tags:n,data:{baseData:{ver:2}}}}var h=d.url||y.src;if(h){function a(e){var t,n,a,i,r,o,s,c,u,p,l;g=!0,m.queue=[],f||(f=!0,t=h,s=function(){var e={},t=d.connectionString;if(t)for(var n=t.split(";"),a=0;a<n.length;a++){var i=n[a].split("=");2===i.length&&(e[i[0][b]()]=i[1])}if(!e[C]){var r=e.endpointsuffix,o=r?e.location:null;e[C]="https://"+(o?o+".":"")+"dc."+(r||"services.visualstudio.com")}return e}(),c=s[D]||d[D]||"",u=s[C],p=u?u+"/v2/track":d.endpointUrl,(l=[]).push((n="SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details)",a=t,i=p,(o=(r=v(c,"Exception")).data).baseType="ExceptionData",o.baseData.exceptions=[{typeName:"SDKLoadFailed",message:n.replace(/\./g,"-"),hasFullStack:!1,stack:n+"\nSnippet failed to load ["+a+"] -- Telemetry is disabled\nHelp Link: https://go.microsoft.com/fwlink/?linkid=2128109\nHost: "+(S&&S.pathname||"_unknown_")+"\nEndpoint: "+i,parsedStack:[]}],r)),l.push(function(e,t,n,a){var i=v(c,"Message"),r=i.data;r.baseType="MessageData";var o=r.baseData;return o.message='AI (Internal): 99 message:"'+("SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details) ("+n+")").replace(/\"/g,"")+'"',o.properties={endpoint:a},i}(0,0,t,p)),function(e,t){if(JSON){var n=T.fetch;if(n&&!y.useXhr)n(t,{method:N,body:JSON.stringify(e),mode:"cors"});else if(XMLHttpRequest){var a=new XMLHttpRequest;a.open(N,t),a.setRequestHeader("Content-type","application/json"),a.send(JSON.stringify(e))}}}(l,p))}function i(e,t){f||setTimeout(function(){!t&&m.core||a()},500)}var e=function(){var n=l.createElement(k);n.src=h;var e=y[w];return!e&&""!==e||"undefined"==n[w]||(n[w]=e),n.onload=i,n.onerror=a,n.onreadystatechange=function(e,t){"loaded"!==n.readyState&&"complete"!==n.readyState||i(0,t)},n}();y.ld<0?l.getElementsByTagName("head")[0].appendChild(e):setTimeout(function(){l.getElementsByTagName(k)[0].parentNode.appendChild(e)},y.ld||0)}try{m.cookie=l.cookie}catch(p){}function t(e){for(;e.length;)!function(t){m[t]=function(){var e=arguments;g||m.queue.push(function(){m[t].apply(m,e)})}}(e.pop())}var n="track",r="TrackPage",o="TrackEvent";t([n+"Event",n+"PageView",n+"Exception",n+"Trace",n+"DependencyData",n+"Metric",n+"PageViewPerformance","start"+r,"stop"+r,"start"+o,"stop"+o,"addTelemetryInitializer","setAuthenticatedUserContext","clearAuthenticatedUserContext","flush"]),m.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4};var s=(d.extensionConfig||{}).ApplicationInsightsAnalytics||{};if(!0!==d[I]&&!0!==s[I]){var c="onerror";t(["_"+c]);var u=T[c];T[c]=function(e,t,n,a,i){var r=u&&u(e,t,n,a,i);return!0!==r&&m["_"+c]({message:e,url:t,lineNumber:n,columnNumber:a,error:i}),r},d.autoExceptionInstrumented=!0}return m}(y.cfg);function a(){y.onInit&&y.onInit(n)}(T[t]=n).queue&&0===n.queue.length?(n.queue.push(a),n.trackPageView({})):a()}(window,document,{nConfig||{}).ApplicationInsightsAnalytics||{};if(!0!==d[C]&&!0!==s[C]){method="onerror",t(["_"+method]);var c=T[method];T[method]=function(e,t,n,a,i){var r=c&&c(e,t,n,a,i);return!0!==r&&m["_"+method]({message:e,url:t,lineNumber:n,columnNumber:a,error:i}),r},d.autoExceptionInstrumented=!0}return m}(y.cfg);(T[t]=n).queue&&0===n.queue.length&&n.trackPageView({})}(window,document,{
src: "https://js.monitor.azure.com/scripts/b/ai.2.min.js", // The SDK URL Source
// name: "appInsights", // Global SDK Instance name defaults to "appInsights" when not supplied
// ld: 0, // Defines the load delay (in ms) before attempting to load the sdk. -1 = block page load and add to head. (default) = 0ms load after timeout,
// useXhr: 1, // Use XHR instead of fetch to report failures (if available),
crossOrigin: "anonymous", // When supplied this will add the provided value as the cross origin attribute on the script tag
// onInit: null, // Once the application insights instance has loaded and initialized this callback function will be called with 1 argument -- the sdk instance (DO NOT ADD anything to the sdk.queue -- As they won't get called)
cfg: { // Application Insights Configuration
  connectionString:"InstrumentationKey=00000000-0000-0000-0000-000000000000;"
}});
</script>
```

> [!NOTE]
> Para a legibilidade e para reduzir possíveis erros javaScript, todas as opções de configuração possíveis estão listadas numa nova linha no código de snippet acima, se não quiser alterar o valor de uma linha comentada, pode ser removida.

Configuração manual:
```javascript
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  connectionString: 'InstrumentationKey=00000000-0000-0000-0000-000000000000;'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView();
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("InstrumentationKey=00000000-0000-0000-0000-000000000000;");
appInsights.start();
```

# <a name="python"></a>[Python](#tab/python)

Recomendamos que os utilizadores estabeleçam a variável ambiental.

Para definir explicitamente a cadeia de ligação:

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'), sampler=ProbabilitySampler(1.0))
```

---

## <a name="next-steps"></a>Passos seguintes

Introdução ao tempo de execução com:

* [Azure VM e Azure série de máquinas virtuais definir aplicativos hospedados no IIS](./azure-vm-vmss-apps.md)
* [Servidor do IIS](./monitor-performance-live-website-now.md)
* [Aplicações Web do Azure](./azure-web-apps.md)

Introdução no momento de programação com:

* [ASP.NET](./asp-net.md)
* [ASP.NET Core](./asp-net-core.md)
* [Java](./java-get-started.md)
* [Node.js](./nodejs.md)
* [Python](./opencensus-python.md)

