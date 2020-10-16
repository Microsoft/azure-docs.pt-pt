---
title: Azure Application Insights substitui pontos finais padrão da SDK
description: Modificar os pontos finais do Azure Monitor Insights SDK para regiões como o Governo de Azure.
ms.topic: conceptual
ms.date: 07/26/2019
ms.custom: references_regions, devx-track-js
ms.openlocfilehash: d6cea9044cd4898480fcc30532a05e6c8a407012
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91333295"
---
# <a name="application-insights-overriding-default-endpoints"></a>Insights de aplicação sobreprimido pontos finais predefinidos

Para enviar dados do Application Insights para determinadas regiões, terá de substituir os endereços de ponto final predefinidos. Cada SDK requer modificações ligeiramente diferentes, todas descritas neste artigo. Estas alterações requerem ajustar o código de amostra e substituir os valores de espaços reservados para `QuickPulse_Endpoint_Address` , e com os `TelemetryChannel_Endpoint_Address` `Profile_Query_Endpoint_address` endereços de ponto final reais para a sua região específica. O final deste artigo contém ligações aos endereços de ponto final para as regiões onde esta configuração é necessária.

> [!NOTE]
> [As cadeias de conexão](./sdk-connection-string.md?tabs=net) são o novo método preferido de definir pontos finais personalizados dentro de Insights de Aplicação.

---

## <a name="sdk-code-changes"></a>Alterações de código SDK

# <a name="net"></a>[.NET](#tab/net)

> [!NOTE]
> O ficheiro applicationinsights.config é automaticamente substituído sempre que uma atualização SDK é executada. Depois de realizar uma atualização SDK certifique-se de reentrar nos valores específicos do ponto final da região.

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

# <a name="net-core"></a>[.NET Core](#tab/netcore)

Modifique a appsettings.jsno ficheiro do seu projeto da seguinte forma para ajustar o ponto final principal:

```json
"ApplicationInsights": {
    "InstrumentationKey": "instrumentationkey",
    "TelemetryChannel": {
      "EndpointAddress": "TelemetryChannel_Endpoint_Address"
    }
  }
```

Os valores para Métricas Vivas e o Ponto Final de Consulta de Perfil só podem ser definidos por código. Para anular os valores predefinidos para todos os valores do ponto final via código, escove as seguintes alterações no `ConfigureServices` método do `Startup.cs` ficheiro:

```csharp
using Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse; //Place at top of Startup.cs file

   services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) => module.QuickPulseServiceEndpoint="QuickPulse_Endpoint_Address");

   services.AddSingleton<IApplicationIdProvider, ApplicationInsightsApplicationIdProvider>(_ => new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "Profile_Query_Endpoint_address" });

   services.AddSingleton<ITelemetryChannel>(_ => new ServerTelemetryChannel() { EndpointAddress = "TelemetryChannel_Endpoint_Address" });

    //Place in the ConfigureServices method. Place this before services.AddApplicationInsightsTelemetry("instrumentation key"); if it's present
```

# <a name="azure-functions"></a>[Funções do Azure](#tab/functions)

Para funções Azure é agora recomendado utilizar [cordas de ligação definidas](./sdk-connection-string.md?tabs=net) nas definições de Aplicação da Função. Para aceder às definições de aplicação para **Settings**a sua função a partir do painel de funções selecione  >  Definições**configurações configurações**  >  **configurações de configuração de configuração**. 

Nome: `APPLICATIONINSIGHTS_CONNECTION_STRING` Valor: `Connection String Value`

# <a name="java"></a>[Java](#tab/java)

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

Modifique o `application.properties` ficheiro e adicione:

```yaml
azure.application-insights.channel.in-process.endpoint-address= TelemetryChannel_Endpoint_Address
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

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

# <a name="javascript"></a>[JavaScript](#tab/js)

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

# <a name="python"></a>[Python](#tab/python)

Para obter orientações sobre a modificação do ponto final de ingestão para o SDK opencensus-python consulte o [repo opencensus-python.](https://github.com/census-instrumentation/opencensus-python/blob/af284a92b80bcbaf5db53e7e0813f96691b4c696/contrib/opencensus-ext-azure/opencensus/ext/azure/common/__init__.py)

---

## <a name="regions-that-require-endpoint-modification"></a>Regiões que requerem modificação do ponto final

Atualmente, as únicas regiões que necessitam de modificações no ponto final são [o Governo de Azure](../../azure-government/compare-azure-government-global-azure.md#application-insights) e [a Azure China.](/azure/china/resources-developer-guide)

|Region |  Nome do ponto final | Valor |
|-----------------|:------------|:-------------|
| Azure China | Canal da Telemetria | `https://dc.applicationinsights.azure.cn/v2/track` |
| Azure China | QuickPulse (Métricas Ao Vivo) |`https://live.applicationinsights.azure.cn/QuickPulseService.svc` |
| Azure China | Consulta de perfis |`https://dc.applicationinsights.azure.cn/api/profiles/{0}/appId`  |
| Azure Government | Canal da Telemetria |`https://dc.applicationinsights.us/v2/track` |
| Azure Government | QuickPulse (Métricas Ao Vivo) |`https://quickpulse.applicationinsights.us/QuickPulseService.svc` |
| Azure Government | Consulta de perfis |`https://dc.applicationinsights.us/api/profiles/{0}/appId` |

Se utilizar atualmente a [API de Insights de Aplicação,](https://dev.applicationinsights.io/
) que é normalmente acedida através de 'api.applicationinsights.io' terá de utilizar um ponto final que seja local para a sua região:

|Region |  Nome do ponto final | Valor |
|-----------------|:------------|:-------------|
| Azure China | API REST | `api.applicationinsights.azure.cn` |
| Azure Government | API REST | `api.applicationinsights.us`|

> [!NOTE]
> Atualmente, a monitorização baseada em agentes/extensões codeless para os Serviços de Aplicações Azure não é **suportada** nestas regiões. Assim que esta funcionalidade estiver disponível este artigo será atualizado.

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre as modificações personalizadas para o Governo Azure, consulte as orientações detalhadas para [a monitorização e gestão do Azure.](../../azure-government/compare-azure-government-global-azure.md#application-insights)
- Para saber mais sobre a Azure China, consulte o [Azure China Playbook](/azure/china/).
