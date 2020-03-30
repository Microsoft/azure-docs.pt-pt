---
title: Insights de aplicação Azure sobrepõem pontos finais sdk padrão
description: Modificar os pontos finais de aplicação do Monitor de Azure padrão SDK para regiões como o Governo Azure.
ms.topic: conceptual
ms.date: 07/26/2019
ms.openlocfilehash: b4ab05c7ee815b385ffb2d1ff9e621063d744dd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298324"
---
# <a name="application-insights-overriding-default-endpoints"></a>Insights de aplicação sobrepondo pontos finais de padrão

Para enviar dados de Informações de Aplicação para determinadas regiões, terá de anular os endereços de ponto final predefinidos. Cada SDK requer modificações ligeiramente diferentes, todas descritas neste artigo. Estas alterações requerem ajustar o código da `QuickPulse_Endpoint_Address`amostra `TelemetryChannel_Endpoint_Address`e `Profile_Query_Endpoint_address` substituir os valores do espaço reservado para, e com os endereços finais reais para a sua região específica. O final deste artigo contém links para os endereços de ponto final para regiões onde esta configuração é necessária.

## <a name="sdk-code-changes"></a>Alterações de código SDK

### <a name="net-with-applicationinsightsconfig"></a>.NET com applicationinsights.config

> [!NOTE]
> O ficheiro applicationinsights.config é automaticamente substituído sempre que for realizada uma atualização SDK. Depois de realizar uma atualização SDK, certifique-se de que reintroduza os valores finais específicos da região.

```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>Custom_QuickPulse_Endpoint_Address</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>TelemetryChannel_Endpoint_Address</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>Profile_Query_Endpoint_address</ProfileQueryEndpoint>
  </ApplicationIdProvider>
  ...
</ApplicationInsights>
```

### <a name="aspnet-core"></a>Núcleo de ASP.NET

Modifique o ficheiro appsettings.json no seu projeto da seguinte forma de ajustar o ponto final principal:

```json
"ApplicationInsights": {
    "InstrumentationKey": "instrumentationkey",
    "TelemetryChannel": {
      "EndpointAddress": "TelemetryChannel_Endpoint_Address"
    }
  }
```

Os valores das Métricas Ao Vivo e do Ponto final da Consulta de Perfil só podem ser definidos por código. Para anular os valores predefinidos para todos os valores de ponto final através de código, epreenda as seguintes alterações no `ConfigureServices` método do `Startup.cs` ficheiro:

```csharp
using Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse; //Place at top of Startup.cs file

   services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) => module.QuickPulseServiceEndpoint="QuickPulse_Endpoint_Address");

   services.AddSingleton<IApplicationIdProvider, ApplicationInsightsApplicationIdProvider>(_ => new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "Profile_Query_Endpoint_address" });

   services.AddSingleton<ITelemetryChannel>(_ => new ServerTelemetryChannel() { EndpointAddress = "TelemetryChannel_Endpoint_Address" });

    //Place in the ConfigureServices method. Place this before services.AddApplicationInsightsTelemetry("instrumentation key"); if it's present
```

### <a name="azure-functions-v2x"></a>Funções Azure v2.x

Instale os seguintes pacotes no seu projeto de função:

- Microsoft.ApplicationInsights versão 2.10.0
- Microsoft.ApplicationInsights.PerfCounterCollector versão 2.10.0
- Microsoft.ApplicationInsights.WindowsServer.TelemettryChannel versão 2.10.0

Em seguida, adicione (ou modifique) o código de arranque para a sua aplicação de função:

```csharp
[assembly: WebJobsStartup(typeof(Example.Startup))]
namespace Example
{
  class Startup : FunctionsStartup
  {
      public override void Configure(IWebJobsBuilder builder)
      {
          var quickPulseFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(ITelemetryModule) && 
                                               sd.ImplementationType == typeof(QuickPulseTelemetryModule));
          if (quickPulseFactory != null)
          {
              builder.Services.Remove(quickPulseFactory);
          }

          var appIdFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(IApplicationIdProvider));
          if (appIdFactory != null)
          {
              builder.Services.Remove(appIdFactory);
          }

          var channelFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(ITelemetryChannel));
          if (channelFactory != null)
          {
              builder.Services.Remove(channelFactory);
          }

          builder.Services.AddSingleton<ITelemetryModule, QuickPulseTelemetryModule>(_ =>
              new QuickPulseTelemetryModule
              {
                  QuickPulseServiceEndpoint = "QuickPulse_Endpoint_Address"
              });

          builder.Services.AddSingleton<IApplicationIdProvider, ApplicationInsightsApplicationIdProvider>(_ => new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "Profile_Query_Endpoint_address" });

          builder.Services.AddSingleton<ITelemetryChannel>(_ => new ServerTelemetryChannel() { EndpointAddress = "TelemetryChannel_Endpoint_Address" });
      }
  }
}
```

### <a name="java"></a>Java

Modificar o ficheiro applicationinsights.xml para alterar o endereço de ponto final predefinido.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <InstrumentationKey>ffffeeee-dddd-cccc-bbbb-aaaa99998888</InstrumentationKey>
  <TelemetryModules>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
  </TelemetryModules>
  <TelemetryInitializers>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>
  </TelemetryInitializers>
  <!--Add the following Channel value to modify the Endpoint address-->
  <Channel type="com.microsoft.applicationinsights.channel.concrete.inprocess.InProcessTelemetryChannel">
  <EndpointAddress>TelemetryChannel_Endpoint_Address</EndpointAddress>
  </Channel>
</ApplicationInsights>
```

### <a name="spring-boot"></a>Spring Boot

Modificar `application.properties` o ficheiro e adicionar:

```yaml
azure.application-insights.channel.in-process.endpoint-address= TelemetryChannel_Endpoint_Address
```

### <a name="nodejs"></a>Node.js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY');
appInsights.defaultClient.config.endpointUrl = "TelemetryChannel_Endpoint_Address"; // ingestion
appInsights.defaultClient.config.profileQueryEndpoint = "Profile_Query_Endpoint_address"; // appid/profile lookup
appInsights.defaultClient.config.quickPulseHost = "QuickPulse_Endpoint_Address"; //live metrics
appInsights.Configuration.start();
```

Os pontos finais também podem ser configurados através de variáveis ambientais:

```
Instrumentation Key: "APPINSIGHTS_INSTRUMENTATIONKEY"
Profile Endpoint: "Profile_Query_Endpoint_address"
Live Metrics Endpoint: "QuickPulse_Endpoint_Address"
```

### <a name="javascript"></a>Javascript

```javascript
<script type="text/javascript">
    var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
    {
      instrumentationKey:"INSTRUMENTATION_KEY",
      endpointUrl: "TelemetryChannel_Endpoint_Address"
    }
    );window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

### <a name="python"></a>Python

Para obter orientações sobre a modificação do ponto final de ingestão para o SDK de sonton opencensus-python consulte o [repo opencensus-python.](https://github.com/census-instrumentation/opencensus-python/blob/af284a92b80bcbaf5db53e7e0813f96691b4c696/contrib/opencensus-ext-azure/opencensus/ext/azure/common/__init__.py)

## <a name="regions-that-require-endpoint-modification"></a>Regiões que exigem modificação do ponto final

Atualmente, as únicas regiões que exigem modificações de pontos finais são o [Governo Azure](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#application-insights) e a [China Azure.](https://docs.microsoft.com/azure/china/resources-developer-guide)

|Região |  Nome do ponto final | Valor |
|-----------------|:------------|:-------------|
| Azure China | Canal de Telemetria | `https://dc.applicationinsights.azure.cn/v2/track` |
| Azure China | QuickPulse (Métricas ao Vivo) |`https://live.applicationinsights.azure.cn/QuickPulseService.svc` |
| Azure China | Consulta de perfil |`https://dc.applicationinsights.azure.cn/api/profiles/{0}/appId`  |
| Azure Government | Canal de Telemetria |`https://dc.applicationinsights.us/v2/track` |
| Azure Government | QuickPulse (Métricas ao Vivo) |`https://quickpulse.applicationinsights.us/QuickPulseService.svc` |
| Azure Government | Consulta de perfil |`https://dc.applicationinsights.us/api/profiles/{0}/appId` |

Se utilizar atualmente a [API de Repouso DE Informações de Aplicação,](https://dev.applicationinsights.io/
) normalmente acessada através de 'api.applicationinsights.io', terá de utilizar um ponto final que seja local para a sua região:

|Região |  Nome do ponto final | Valor |
|-----------------|:------------|:-------------|
| Azure China | API REST | `api.applicationinsights.azure.cn` |
| Azure Government | API REST | `api.applicationinsights.us`|

> [!NOTE]
> Atualmente, não é **suportada** uma monitorização baseada em agentes/extensões sem código para os Serviços de Aplicações Azure. Assim que esta funcionalidade estiver disponível, este artigo será atualizado.

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre as modificações personalizadas para o Governo azure, consulte as orientações detalhadas para [a monitorização e gestão do Azure.](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#application-insights)
- Para saber mais sobre a Azure China, consulte o Livro de [Jogadas da China Azure.](https://docs.microsoft.com/azure/china/)
