---
title: Cadeias de conexão em Azure Application Insights / Microsoft Docs
description: Como usar cordas de ligação.
ms.topic: conceptual
author: timothymothra
ms.author: tilee
ms.date: 01/17/2020
ms.custom: devx-track-js, devx-track-csharp
ms.reviewer: mbullwin
ms.openlocfilehash: f4227c28329233c7f414c6c45e4a3c1420bf47be
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91335165"
---
# <a name="connection-strings"></a>Cadeias de ligação

## <a name="overview"></a>Descrição geral

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
- Os URIs de serviço regional baseiam-se nos incumprimentos da [SDK](https://github.com/microsoft/ApplicationInsights-dotnet/blob/e50d569cebf485e72e98f4a08a0bc0e30cdf42bc/BASE/src/Microsoft.ApplicationInsights/Extensibility/Implementation/Endpoints/Constants.cs#L6) e ligar-se-ão ao Azure global público:
   - Ingestão: `https://dc.services.visualstudio.com/`
   - Métricas ao vivo: `https://rt.services.visualstudio.com/`
   - Perfilador: `https://agent.azureserviceprofiler.net/`
   - Debugger: `https://agent.azureserviceprofiler.net/`



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

# <a name="netnetcore"></a>[.NET/.NetCore](#tab/net)

TelemetriaConfiguration.ConnectionString: https://github.com/microsoft/ApplicationInsights-dotnet/blob/add45ceed35a817dc7202ec07d3df1672d1f610d/BASE/src/Microsoft.ApplicationInsights/Extensibility/TelemetryConfiguration.cs#L271-L274

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

```javascript
<script type="text/javascript">
var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("addTelemetryInitializer"),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),t.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4},!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
{
  connectionString:"InstrumentationKey=00000000-0000-0000-0000-000000000000;"
}
);window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```


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

