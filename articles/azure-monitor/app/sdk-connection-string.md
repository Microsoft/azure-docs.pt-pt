---
title: Cordas de ligação em Insights de Aplicação Azure  Microsoft Docs
description: Como utilizar cordas de ligação.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: timothymothra
ms.author: tilee
ms.date: 01/17/2020
ms.reviewer: mbullwin
ms.openlocfilehash: 2cf3c784c711977a6fe758bb9017e1f0dc404c33
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76992069"
---
# <a name="connection-strings"></a>Cadeias de ligação

## <a name="overview"></a>Visão geral

As cordas de ligação fornecem aos utilizadores do Application Insight uma única definição de configuração, eliminando a necessidade de várias definições de procuração. Altamente útil para servidores web intranet, ambientes de nuvem soberana ou híbrido que procuram enviar dados para o serviço de monitorização.

Os pares de valor-chave fornecem uma maneira fácil para os utilizadores definirem uma combinação de sufixo prefixo para cada serviço/produto Application Insights (IA).

> [!IMPORTANT]
> Não recomendamos a definição da tecla de fio de ligação e de instrumentação. No caso de um utilizador definir ambos, o que for definido por último terá precedência. 


## <a name="scenario-overview"></a>Descrição geral do cenário 

Cenários do cliente onde visualizamos isto tendo mais impacto:

- Exceções de firewall ou redirecionamentos por procuração 

    Nos casos em que é necessária monitorização para servidor web intranet, a nossa solução anterior pediu aos clientes que adicionassem pontos finais de serviço individuais à sua configuração. Para obter mais informações, consulte [aqui](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#can-i-monitor-an-intranet-web-server). 
    As cordas de ligação oferecem uma alternativa melhor reduzindo este esforço para um único ajuste. Uma simples emenda prefixo e sufixo permite a população automática e a reorientação de todos os pontos finais para os serviços certos. 

- Ambientes de nuvem soberana ou híbrido

    Os utilizadores podem enviar dados para uma [região governamental azure](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#application-insights)definida.
    As cordas de ligação permitem definir as definições de ponto final para os seus servidores intranet ou definições híbridas de nuvem. 

## <a name="getting-started"></a>Introdução

### <a name="finding-my-connection-string"></a>Encontrar a minha corda de ligação?

A sua cadeia de ligação é exibida na lâmina de visão geral do seu recurso Application Insights.

![cadeia de conexão na lâmina de visão geral](media/overview-dashboard/overview-connection-string.png)

### <a name="schema"></a>Esquema

#### <a name="max-length"></a>Comprimento máximo

A ligação tem um comprimento máximo suportado de 4096 caracteres.

#### <a name="key-value-pairs"></a>Pares chave-valor

A cadeia de ligação consiste numa lista de definições representadas como pares de valor-chave separados por ponto evígula: `key1=value1;key2=value2;key3=value3`

#### <a name="syntax"></a>Sintaxe

- `InstrumentationKey` (ex: 00000000-0000-0000-0000-000000000000000000) A corda de ligação é um campo **necessário.**
- `Authorization` (ex: ikey) (Esta definição é opcional porque hoje só apoiamos a autorização ikey.)
- `EndpointSuffix` (ex: applicationinsights.azure.cn) A definição do sufixo de ponto final instruirá o SDK a que a nuvem Azure se liga. O SDK reunirá o resto do ponto final para serviços individuais.
- Pontos Finais Explícitos.
  Qualquer serviço pode ser explicitamente ultrapassado na cadeia de ligação.
   - `IngestionEndpoint` (ex: https://dc.applicationinsights.azure.com)
   - `LiveEndpoint` (ex: https://live.applicationinsights.azure.com)
   - `ProfilerEndpoint` (ex: https://profiler.applicationinsights.azure.com)
   - `SnapshotEndpoint` (ex: https://snapshot.applicationinsights.azure.com)

#### <a name="endpoint-schema"></a>Esquema de endpoint

`<prefix>.<suffix>`
- Prefixo: Define um serviço. 
- Sufixo: Define o nome de domínio comum.

##### <a name="valid-suffixes"></a>Sufixos válidos

Aqui está uma lista de sufixos válidos 
- applicationinsights.azure.cn
- applicationinsights.us


Ver também: https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification


##### <a name="valid-prefixes"></a>Prefixos válidos

- [Ingestão de telemetria](./app-insights-overview.md): `dc`
- [Métricas ao Vivo](./live-stream.md): `live`
- [Perfil :](./profiler-overview.md)`profiler`
- [Snapshot](./snapshot-debugger.md): `snapshot`



## <a name="connection-string-examples"></a>Exemplos de cordas de ligação


### <a name="minimal-valid-connection-string"></a>Corda de ligação mínima válida

`InstrumentationKey=00000000-0000-0000-0000-000000000000;`

Neste exemplo, apenas foi definida a chave de instrumentação.

- Regime de autorização não passa por "ikey" 
- Chave de Instrumentação: 00000000-0000-0000-0000-0000-000000000000000000
- Os URIs de serviço regional baseiam-se nos incumprimentos do [SDK](https://github.com/microsoft/ApplicationInsights-dotnet/blob/e50d569cebf485e72e98f4a08a0bc0e30cdf42bc/BASE/src/Microsoft.ApplicationInsights/Extensibility/Implementation/Endpoints/Constants.cs#L6) e ligar-se-ão ao azure global público:
   - Ingestão: https://dc.services.visualstudio.com/
   - Métricas ao vivo: https://rt.services.visualstudio.com/
   - Profiler: https://agent.azureserviceprofiler.net/
   - https://agent.azureserviceprofiler.net/  



### <a name="connection-string-with-endpoint-suffix"></a>Cadeia de ligação com sufixo de ponto final

`InstrumentationKey=00000000-0000-0000-0000-000000000000;EndpointSuffix=ai.contoso.com;`

Neste exemplo, esta cadeia de ligação especifica o sufixo de ponto final e o SDK construirá pontos finais de serviço.

- Regime de autorização não passa por "ikey" 
- Chave de Instrumentação: 00000000-0000-0000-0000-0000-000000000000000000
- As URIs de serviço regional baseiam-se no sufixo final fornecido: 
   - Ingestão: https://dc.ai.contoso.com
   - Métricas ao vivo: https://live.ai.contoso.com
   - Profiler: https://profiler.ai.contoso.com 
   - https://snapshot.ai.contoso.com   



### <a name="connection-string-with-explicit-endpoint-overrides"></a>Cadeia de ligação com sobreposições explícitas de ponto final 

`InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://custom.com:111/;LiveEndpoint=https://custom.com:222/;ProfilerEndpoint=https://custom.com:333/;SnapshotEndpoint=https://custom.com:444/;`

Neste exemplo, esta cadeia de ligação especifica sobreposições explícitas para cada serviço. O SDK utilizará os pontos finais exatos fornecidos sem modificação.

- Regime de autorização não passa por "ikey" 
- Chave de Instrumentação: 00000000-0000-0000-0000-0000-000000000000000000
- Os URIs de serviço regional baseiam-se nos valores explícitos de sobreposição: 
   - Ingestão: https://custom.com:111/
   - Métricas ao vivo: https://custom.com:222/
   - Profiler: https://custom.com:333/ 
   - https://custom.com:444/   


## <a name="how-to-set-a-connection-string"></a>Como definir uma cadeia de ligação

As cordas de ligação são suportadas nas seguintes versões SDK:
- .NET e .NET Core v2.12.0
- Java v2.5.1
- Javascript v2.3.0
- NodeJS v1.5.0
- Python v1.0.0

Uma cadeia de ligação pode ser definida em código, variável ambiental ou ficheiro de configuração.



### <a name="environment-variable"></a>Variável de ambiente

- Fio de ligação: `APPLICATIONINSIGHTS_CONNECTION_STRING`

### <a name="net-sdk-example"></a>exemplo sDK .Net

TelemettryConfiguration.ConnectionString: https://github.com/microsoft/ApplicationInsights-dotnet/blob/add45ceed35a817dc7202ec07d3df1672d1f610d/BASE/src/Microsoft.ApplicationInsights/Extensibility/TelemetryConfiguration.cs#L271-L274

.Net Explicitamente Definido:
```csharp
var configuration = new TelemetryConfiguration
{
    ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
};
```

Ficheiro Config .Net:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
    <ConnectionString>InstrumentationKey=00000000-0000-0000-0000-000000000000</ConnectionString>
</ApplicationInsights>
```


NetCore config.json: 

```json
{
  "ApplicationInsights": {
    "ConnectionString" : "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
    }
  }
```


### <a name="java-sdk-example"></a>Exemplo java SDK


Java explicitamente definido:
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

### <a name="javascript-sdk-example"></a>Exemplo Javascript SDK

Importante: O Javascript não suporta o uso de Variáveis Ambientais.

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

### <a name="node-sdk-example"></a>Exemplo sdk do nó

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("InstrumentationKey=00000000-0000-0000-0000-000000000000;");
appInsights.start();
```

### <a name="python-sdk-example"></a>Exemplo Python SDK

Recomendamos que os utilizadores desloque a variável ambiental.

Para definir explicitamente a cadeia de ligação:

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'), sampler=ProbabilitySampler(1.0))
```


## <a name="next-steps"></a>Passos seguintes

Introdução ao tempo de execução com:

* [VM do Azure e conjunto de dimensionamento de máquinas virtuais do Azure aplicativos hospedados pelo IIS](../../azure-monitor/app/azure-vm-vmss-apps.md)
* [Servidor do IIS](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Aplicações Web do Azure](../../azure-monitor/app/azure-web-apps.md)

Introdução no momento de programação com:

* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [Node.js](../../azure-monitor/app/nodejs.md)
* [Python (versão prévia)](../../azure-monitor/app/opencensus-python.md)
