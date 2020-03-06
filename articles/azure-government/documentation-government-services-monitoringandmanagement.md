---
title: Monitorização do Governo Azure + Gestão  Microsoft Docs
description: Isto fornece uma comparação de características e orientações sobre o desenvolvimento de candidaturas para o Governo Azure.
services: azure-government
cloud: gov
author: gsacavdm
ms.assetid: 4b7720c1-699e-432b-9246-6e49fb77f497
ms.service: azure-government
ms.topic: article
ms.workload: azure-government
ms.date: 12/11/2019
ms.author: gsacavdm
ms.openlocfilehash: b6f395964c286aca1bc8a1c190edeea00ba6e15c
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78362262"
---
# <a name="azure-government-monitoring--management"></a>Monitorização do Governo Azure + Gestão
Este artigo descreve as variações e considerações dos serviços de monitorização e gestão para o ambiente do Governo Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="advisor"></a>Assistente
O conselheiro está geralmente disponível no Governo de Azure.

Para mais informações, consulte [a Documentação Pública do Advisor.](../advisor/advisor-overview.md)

### <a name="variations"></a>Variações
As seguintes recomendações do Conselho Consultivo não estão atualmente disponíveis no Governo do Azure:

* Elevada Disponibilidade
  * Configure a sua porta de entrada VPN para ativo para a resiliência de ligação
  * Create Azure Service Health alerta para ser notificado quando problemas do Azure o afetam
  * Configure Pontos finais do Gestor de Tráfego para resiliência
  * Utilize a eliminação suave para a sua Conta de Armazenamento Azure
* Desempenho
  * Melhorar o desempenho e a fiabilidade do Serviço de Aplicações
  * Reduza o tempo de DNS para viver no seu perfil de Traffic Manager para falhar em pontos finais saudáveis mais rapidamente
  * Melhorar o desempenho do Armazém de Dados SQL
  * Utilizar o Armazenamento Premium
  * Migrar a sua Conta de Armazenamento para O Gestor de Recursos Azure
* Custo
  * Compre casos de máquinas virtuais reservadas para economizar dinheiro sobre os custos de pagamento
  * Eliminar circuitos expressroute não provisionados
  * Eliminar ou reconfigurar os gateways da rede virtual inativos

O cálculo utilizado para recomendar que se desliga si ou desligue as máquinas virtuais subutilizadas é o seguinte no Governo azure:

O Advisor monitoriza o uso da sua máquina virtual durante 7 dias e identifica máquinas virtuais de baixa utilização. As máquinas virtuais são consideradas de baixa utilização se a sua utilização de CPU for de 5% ou menos e a sua utilização da rede for inferior a 2% ou se a carga de trabalho atual puder ser acomodada por um tamanho de máquina virtual menor. Se pretender ser mais agressivo na identificação de máquinas virtuais subutilizadas, pode ajustar a regra de utilização do CPU numa base de subscrição.

## <a name="automation"></a>Automatização
A automação está geralmente disponível no Governo Azure.

Para mais informações, consulte [a Automatização documentação pública.](../automation/automation-intro.md)

## <a name="azure-migrate"></a>Azure Migrate

A Azure Migrate está geralmente disponível no Governo de Azure.

Para mais informações, consulte a [documentação do Azure Migrate.](../migrate/migrate-overview.md)

### <a name="variations"></a>Variações
As seguintes funcionalidades do Azure Migrate não estão atualmente disponíveis no Governo Azure:

* A funcionalidade de visualização da dependência não está disponível no Governo do Azure, uma vez que o Azure Migrate depende do Mapa de Serviços para visualização da dependência, que atualmente não está disponível no Governo do Azure.
* Só é possível criar avaliações para o Governo azure como regiões-alvo e utilizar ofertas do Governo Azure.

## <a name="backup"></a>Cópia de segurança
O backup está geralmente disponível no Governo de Azure.

Para mais informações, consulte [o Azure Government Backup](documentation-government-services-backup.md).

## <a name="policy"></a>Política
A política está geralmente disponível no Governo de Azure.

Para obter mais informações, veja [Azure Policy](../governance/policy/overview.md).

## <a name="site-recovery"></a>Site Recovery
A Recuperação do Sítio Azure está geralmente disponível no Governo de Azure.

Para mais informações, consulte [a documentação comercial de Recuperação do Site.](../site-recovery/site-recovery-overview.md)

### <a name="variations"></a>Variações
As seguintes funcionalidades de Recuperação do Site não estão atualmente disponíveis no Governo do Azure:
* Notificação por e-mail

| Site Recovery | Clássica | Resource Manager |
| --- | --- | --- |
| VMware/Físico  | GA | GA |
| Hyper-V | GA | GA |
| Site para Site | GA | GA |

Os seguintes URLs para Recuperação do Local são diferentes no Governo azure:

| Azure Public | Azure Government | Notas |
| --- | --- | --- |
| \*.hypervrecoverymanager.windowsazure.com | \*.hypervrecoverymanager.windowsazure.us | Acesso ao Serviço de Recuperação do Site |
| \*.backup.windowsazure.com  | \*.backup.windowsazure.us | Serviço de Acesso à Proteção |
| \*.blob.core.windows.net | \*.blob.core.usgovcloudapi.net | Para armazenar os Instantâneos VM |
| https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi | https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi | Para baixar MySQL |

## <a name="monitor"></a>Monitorizar
O Azure Monitor está geralmente disponível no Governo de Azure.

Para mais informações, consulte [o Monitor documentação comercial.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview)

### <a name="variations"></a>Variações
As seguintes secções detalham diferenças e suposições para as características do Azure Monitor no Governo de Azure:

#### <a name="action-groups"></a>Grupos de Ação
Os Grupos de Ação estão geralmente disponíveis no Governo de Azure, sem diferenças em relação ao Azure comercial.   

#### <a name="activity-log-alerts"></a>Alertas de Registo de Atividades
Os Alertas de Registo de Atividades estão geralmente disponíveis no Governo Azure sem diferenças em relação ao Azure comercial.

#### <a name="alerts-experience"></a>Experiência de Alertas
Os alertas unificados da experiência ui estão disponíveis para alertas métricos e de registo no Governo de Azure.

#### <a name="autoscale"></a>Dimensionamento Automático
A escala automática está geralmente disponível no Governo Azure.

Se estiver a utilizar chamadas PowerShell/ARM/REST para especificar as definições, defina a "Localização" da escala automática para "USGov Virginia" ou "USGov Iowa". O recurso visado pela Escala Automática pode existir em qualquer região. Um exemplo da definição está abaixo:

```powershell
$rule1 = New-AzAutoscaleRule -MetricName "Requests" -MetricResourceId "/subscriptions/S1/resourceGroups/RG1/providers/Microsoft.Web/sites/WebSite1" -Operator GreaterThan -MetricStatistic Average -Threshold 10 -TimeGrain 00:01:00 -ScaleActionCooldown 00:05:00 -ScaleActionDirection Increase -ScaleActionScaleType ChangeCount -ScaleActionValue "1"
$rule2 = New-AzAutoscaleRule -MetricName "Requests" -MetricResourceId "/subscriptions/S1/resourceGroups/RG1/providers/Microsoft.Web/sites/WebSite1" -Operator GreaterThan -MetricStatistic Average -Threshold 10 -TimeGrain 00:01:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionScaleType ChangeCount -ScaleActionValue "2"
$profile1 = New-AzAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1, $rule2 -Name "MyProfile"
$webhook_scale = New-AzAutoscaleWebhook -ServiceUri https://example.com?mytoken=mytokenvalue
$notification1= New-AzAutoscaleNotification -CustomEmails myname@company.com -SendEmailToSubscriptionAdministrator -SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
Add-AzAutoscaleSetting -Location "USGov Virginia" -Name "MyScaleVMSSSetting" -ResourceGroup sdubeys-usgv -TargetResourceId /subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Web/serverFarms/ServerFarm1 -AutoscaleProfiles $profile1 -Notifications $notification1
```

Se estiver interessado em implementar automaticamente os seus recursos, utilize chamadas PowerShell/ARM/Rest para especificar as definições.

Para obter mais informações sobre a utilização do PowerShell, consulte [documentação pública](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-powershell-samples#create-and-manage-autoscale-settings).

#### <a name="metrics"></a>Métricas
As métricas estão geralmente disponíveis no Governo de Azure. No entanto, as métricas multidimensionais são suportadas apenas através da API REST. A capacidade [de mostrar métricas multidimensionais](../azure-monitor/platform/metrics-charts.md) está em pré-visualização no portal do Governo Azure.

#### <a name="metric-alerts"></a>Alertas de Métricas
A primeira geração de alertas de métricas está geralmente disponível tanto no Governo Azure como no azure comercial. A primeira geração chama-se *Alertas (Clássico)* . A segunda geração de alertas métricos (também chamada de experiência de [alertas unificados)](../azure-monitor/platform/alerts-overview.md)também está disponível, mas com um conjunto reduzido de fornecedores de recursos [em comparação com a nuvem pública.](../azure-monitor/platform/alerts-metric-near-real-time.md) Mais serão adicionados ao longo do tempo. 

Os recursos atualmente suportados na experiência de alertas de segunda geração são:
- Microsoft.ApiManagement/service
- Microsoft.Cache/redis
- Microsoft.Compute/virtualMachines
- Microsoft.DBforMySQL/servers
- Microsoft.DBforPostgreSQL/servers
- Microsoft.DBforMariaDB/servers
- Microsoft.Devices/IotHubs
- Microsoft.EventGrid/domains
- Microsoft.EventGrid/topics
- Microsoft.EventHub/clusters
- Microsoft.EventHub/namespaces
- Microsoft.Insights/components
- Microsoft.Network/dnsZones
- Microsoft.Network/loadBalancers
- Microsoft.Network/natGateways
- Microsoft.Network/privateEndpoints
- Microsoft.Network/privateLinkServices
- Microsoft.Network/trafficManagerProfiles
- Microsoft.OperationalInsights/workspaces
- Microsoft.PowerBIDedicated/capacities
- Microsoft.Relay/namespaces
- Microsoft.ServiceBus/namespaces
- Microsoft.Sql/managedInstances
- Microsoft.Sql/servers/databases
- Microsoft.Sql/servers/elasticPools
- Microsoft.Storage/storageAccounts
- Microsoft.Storage/storageAccounts/blobServices
- Microsoft.Storage/storageAccounts/fileServices
- Microsoft.Storage/storageAccounts/queueServices
- Microsoft.Storage/storageAccounts/tableServices
- Microsoft.Web/hostingEnvironments/multiRolePools
- Microsoft.Web/hostingEnvironments/workerPools
- Microsoft.Web/serverfarms
- Microsoft.Web/sites
- Microsoft.Web/sites/slots

Ainda pode utilizar [alertas clássicos](../azure-monitor/platform/alerts-classic.overview.md) para recursos ainda não disponíveis na segunda geração de alertas. 

Ao utilizar chamadas PowerShell/ARM/Rest para criar alertas métricos, terá de definir a "Localização" do alerta métrico para "USGov Virginia" ou "USGov Iowa". Um exemplo da definição está abaixo:

```powershell
$actionEmail = New-AzAlertRuleEmail -CustomEmail myname@company.com
$actionWebhook = New-AzAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
Add-AzMetricAlertRule -Name vmcpu_gt_1 -Location "USGov Virginia" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Actions $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

Para obter mais informações sobre a utilização do PowerShell, consulte [documentação pública](../azure-monitor/platform/powershell-quickstart-samples.md).

## <a name="application-insights"></a>Application Insights

> [!NOTE]
> Atualmente, não é **suportado**um controlo baseado em agentes/extensões sem código para os Serviços de Aplicações Azure. Assim que esta funcionalidade estiver disponível, este artigo será atualizado.

Esta secção descreve a configuração suplementar que é necessária para usar insights de aplicação no Governo Azure. Para saber mais sobre o Azure Monitor e os Insights de Aplicação, confira toda a [documentação.](https://docs.microsoft.com/azure/azure-monitor/overview)

### <a name="enable-application-insights-for-aspnet--aspnet-core-with-visual-studio"></a>Ativar insights de aplicação para ASP.NET e ASP.NET Core com estúdio visual

Atualmente para os clientes do Governo Azure, a única forma de ativar os Insights de Aplicação através do tradicional botão **De adição de aplicações Insights Telemettry** no Estúdio Visual requer uma pequena salieste manual. Os clientes que experimentam o problema associado podem ver mensagens de erro como _"Não existe nenhuma subscrição azure associada a esta conta_ ou _"A subscrição selecionada não suporta Insights de Aplicação,_ mesmo que o fornecedor de recursos `microsoft.insights` tenha um estatuto de registo para a subscrição. Para mitigar esta questão, deve executar os seguintes passos:

1. Switch Visual Studio para [direcionar a nuvem do Governo Azure](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-vs).

2. Criar (ou se já existente conjunto) a variável ambiente do utilizador para AzureGraphApiVersion da seguinte forma: (Para criar uma variável do Ambiente do Utilizador vá para **o Painel** de Controlo > **Sistema** > **Definições avançadas** do sistema > **Variáveis** **avançadas** do ambiente > .)

    `Variable name: AzureGraphApiVersion` `Variable value: 2014-04-01`

3. Faça as modificações finais de Ponto final de Aplicação adequadas para [ASP.NET](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#net-with-applicationinsightsconfig) ou [ASP.NET Core](#aspnet-core) dependendo do seu tipo de projeto.

### <a name="snapshot-debugger"></a>Depurador de Instantâneos

Snapshot Debugger está agora disponível para clientes do Governo Azure. Para utilizar o Snapshot Debugger, o único pré-requisito adicional é garantir que está a utilizar a [versão 1.3.5](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.5-pre-1906.403) do Snapshot Collector ou posterior. Em seguida, basta seguir a documentação padrão [de Snapshot Debugger](https://docs.microsoft.com/azure/azure-monitor/app/snapshot-debugger).

### <a name="sdk-endpoint-modifications"></a>Modificações de ponto final do SDK

Para enviar dados de Informações de Aplicação para a região do Governo de Azure, terá de modificar os endereços de ponto final predefinidos que são utilizados pelos SDKs de Insights de Aplicação. Cada SDK requer modificações ligeiramente diferentes.

### <a name="net-with-applicationinsightsconfig"></a>.NET com applicationinsights.config

> [!NOTE]
> O ficheiro applicationinsights.config é automaticamente substituído sempre que for realizada uma atualização SDK. Depois de realizar uma atualização SDK, certifique-se de que reintroduza os valores finais específicos da região.

```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>https://quickpulse.applicationinsights.us/QuickPulseService.svc</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>https://dc.applicationinsights.us/v2/track</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>https://dc.applicationinsights.us/api/profiles/{0}/appId</ProfileQueryEndpoint>
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
      "EndpointAddress": "https://dc.applicationinsights.us/v2/track"
    }
  }
```

Os valores das Métricas Ao Vivo e do Ponto final da Consulta de Perfil só podem ser definidos por código. Para anular os valores predefinidos para todos os valores de ponto final através de código, epreenda as seguintes alterações no método `ConfigureServices` do ficheiro `Startup.cs`:

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel; //place at top of Startup.cs file

   services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) => module.QuickPulseServiceEndpoint="https://quickpulse.applicationinsights.us/QuickPulseService.svc");

   services.AddSingleton<IApplicationIdProvider, ApplicationInsightsApplicationIdProvider>(_ => new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "https://dc.applicationinsights.us/api/profiles/{0}/appId" });

   services.AddSingleton<ITelemetryChannel>(_ => new ServerTelemetryChannel() { EndpointAddress = "https://dc.applicationinsights.us/v2/track" });

    //place in ConfigureServices method. If present, place this prior to   services.AddApplicationInsightsTelemetry("instrumentation key");
```

### <a name="azure-functions"></a>Funções do Azure

Por favor, instale os seguintes pacotes no seu projeto Função:

- Microsoft.ApplicationInsights versão 2.10.0
- Microsoft.ApplicationInsights.PerfCounterCollector versão 2.10.0
- Microsoft.ApplicationInsights.WindowsServer.TelemettryChannel versão 2.10.0

E adicione (ou modifique) o código de arranque para a sua aplicação Função:

```csharp
[assembly: FunctionsStartup(typeof(Example.Startup))]
namespace Example
{
  class Startup : FunctionsStartup
  {
      public override void Configure(IFunctionsHostBuilder builder)
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
                  QuickPulseServiceEndpoint = "https://quickpulse.applicationinsights.us/QuickPulseService.svc"
              });

          builder.Services.AddSingleton<IApplicationIdProvider, ApplicationInsightsApplicationIdProvider>(_ => new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "https://dc.applicationinsights.us/api/profiles/{0}/appId" });

          builder.Services.AddSingleton<ITelemetryChannel>(_ => new ServerTelemetryChannel() { EndpointAddress = "https://dc.applicationinsights.us/v2/track" });
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
  <EndpointAddress>https://dc.applicationinsights.us/v2/track</EndpointAddress>
  </Channel>
</ApplicationInsights>
```

### <a name="spring-boot"></a>Spring Boot

Modificar o ficheiro `application.properties` e adicionar:

```yaml
azure.application-insights.channel.in-process.endpoint-address= https://dc.applicationinsights.us/v2/track
```

### <a name="nodejs"></a>Node.js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY');
appInsights.defaultClient.config.endpointUrl = "https://dc.applicationinsights.us/v2/track"; // ingestion
appInsights.defaultClient.config.profileQueryEndpoint = "https://dc.applicationinsights.us/api/profiles/{0}/appId"; // appid/profile lookup
appInsights.defaultClient.config.quickPulseHost = "https://quickpulse.applicationinsights.us/QuickPulseService.svc"; //live metrics
appInsights.Configuration.start();
```

Os pontos finais também podem ser configurados através de variáveis ambientais:

```
Instrumentation Key: "APPINSIGHTS_INSTRUMENTATIONKEY"
Profile Endpoint: "https://dc.applicationinsights.us/api/profiles/{0}/appId"
Live Metrics Endpoint: "https://quickpulse.applicationinsights.us/QuickPulseService.svc"
```

### <a name="javascript"></a>JavaScript

```javascript
<script type="text/javascript">
   var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){
      function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/next/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t
   }({
      instrumentationKey:"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx"
      endpointUrl: "https://dc.applicationinsights.us/v2/track"
   });

   window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

### <a name="firewall-exceptions"></a>Exceções à firewall

O serviço Azure Application Insights utiliza vários endereços IP. Poderá precisar de saber estes endereços se a aplicação que está a monitorizar estiver hospedada atrás de uma firewall.

> [!NOTE]
> Embora estes endereços sejam estáticos, é possível que tenhamos que mudá-los de vez em quando. Todo o tráfego de Application Insights representa tráfego de saída, com exceção da monitorização da disponibilidade e dos webhooks, que requerem regras de firewall de entrada.

### <a name="outgoing-ports"></a>Portos de saída
É necessário abrir algumas portas de saída na firewall do seu servidor para permitir que o SDK de Insights de Aplicação e/ou monitor de estado envie dados para o portal:

| Objetivo | do IdP | IP | Portas |
| --- | --- | --- | --- |
| Telemetria | dc.applicationinsights.us | 23.97.4.113 | 443 |

## <a name="azure-monitor-logs"></a>Registos do Azure Monitor
Os registos do Monitor Azure estão geralmente disponíveis no Governo Azure.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

### <a name="variations"></a>Variações

* As soluções disponíveis no Governo azure incluem:
  * [Network Performance Monitor (NPM)](https://blogs.msdn.microsoft.com/azuregov/2017/09/05/network-performance-monitor-general-availability/) - NPM é uma solução de monitorização de rede baseada na nuvem para ambientes de nuvem pública e híbrida. As organizações usam o NPM para monitorizar a disponibilidade da rede em ambientes no local e na nuvem.  Endpoint Monitor - uma subcapacidade de NPM, monitoriza a conectividade da rede com aplicações.

As seguintes funcionalidades e soluções de registo slogs do Azure Monitor não estão atualmente disponíveis no Governo Azure.

* Soluções que estão em pré-visualização no Microsoft Azure, incluindo:
  * Mapa de Serviços
  * Solução de Upgrade do Windows 10 Analytics
  * Solução insights de aplicação
  * Solução de Análise do Grupo de Segurança em Rede Azure
  * Solução Azure Automation Analytics
  * Solução de Ana-chave Analítica Analytics
* Soluções e funcionalidades que requerem atualizações para o software no local, incluindo:
  * Solução Surface Hub
* Características que estão em pré-visualização no Global Azure, incluindo:
  * Exportação de dados para Power BI
* Métricas azure e diagnósticos Azure

Os URLs para registos Do Monitor Azure são diferentes no Governo Azure:

| Azure Public | Azure Government | Notas |
| --- | --- | --- |
| mms.microsoft.com |oms.microsoft.us |Portal de espaços de trabalho Log Analytics |
| *workspaceId*.ods.opinsights.azure.com |*workspaceId*.ods.opinsights.azure.us |[API do recoletor de dados](../azure-monitor/platform/data-collector-api.md) |
| \*.ods.opinsights.azure.com |\*.ods.opinsights.azure.us |Comunicação do agente - [configurar definições de firewall](../log-analytics/log-analytics-proxy-firewall.md) |
| \*.oms.opinsights.azure.com |\*.oms.opinsights.azure.us |Comunicação do agente - [configurar definições de firewall](../log-analytics/log-analytics-proxy-firewall.md) |
| \*.blob.core.windows.net |\*.blob.core.usgovcloudapi.net |Comunicação do agente - [configurar definições de firewall](../log-analytics/log-analytics-proxy-firewall.md) |
| portal.loganalytics.io |portal.loganalytics.us |Portal de Análise Avançada - [configurar definições](../azure-monitor/log-query/portals.md) de firewall |
| api.loganalytics.io |api.loganalytics.us |Portal de Análise Avançada - [configurar definições](../azure-monitor/log-query/portals.md) de firewall |
| docs.loganalytics.io |docs.loganalytics.us |Portal de Análise Avançada - [configurar definições](../azure-monitor/log-query/portals.md) de firewall |
| \*.azure-automation.net |\*.azure-automation.us |Azure Automation - [configurar definições](../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements) de firewall |
| N/D | *.usgovtrafficmanager.net | Azure Traffic Manager - [configurar definições](../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements) de firewall |

As seguintes funcionalidades de registos do Monitor Azure comportam-se de forma diferente no Governo Azure:

* Para ligar o seu grupo de gestão de gestão de operações do System Center aos registos do Monitor de Sistema, precisa de descarregar e importar pacotes de gestão atualizados.
  + Gestor de Operações do Centro de Sistema2016
    1. Instalar [o Rollup update 2 para System Center Operations Manager 2016](https://support.microsoft.com/help/3209591).
    2. Importar os pacotes de gestão incluídos como parte do Update Rollup 2 para O Gestor de Operações. Para obter informações sobre como importar um pacote de gestão a partir de um disco, consulte Como Importar um Pacote de Gestão de Gestão de [Operações](https://technet.microsoft.com/library/hh212691.aspx).
    3. Para ligar o Gestor de Operações aos registos do Monitor Azure, siga os passos do [Connect Operations Manager aos registos do Monitor Azure](../azure-monitor/platform/om-agents.md).
  + Gestor de Operações do Centro de Sistema 2012 R2 UR3 (ou posterior) / Gestor de Operações 2012 SP1 UR7 (ou posteriormente)
    1. Descarregue e guarde os pacotes de [gestão atualizados.](https://go.microsoft.com/fwlink/?LinkId=828749)
    2. Descarregue o ficheiro que descarregou.
    3. Importar os pacotes de gestão para O Gestor de Operações. Para obter informações sobre como importar um pacote de gestão a partir de um disco, consulte Como Importar um Pacote de Gestão de Gestão de [Operações](https://technet.microsoft.com/library/hh212691.aspx).
    4. Para ligar o Gestor de Operações aos registos do Monitor Azure, siga os passos do [Connect Operations Manager aos registos do Monitor Azure](../azure-monitor/platform/om-agents.md).

* Para obter mais informações sobre a utilização de grupos de computador do Gestor de Configuração, consulte O Gestor de [Configuração de Ligação ao Monitor Azure](../azure-monitor/platform/collect-sccm.md).

### <a name="frequently-asked-questions"></a>Perguntas mais frequentes
* Posso migrar dados dos registos do Azure Monitor no Microsoft Azure para o Governo do Azure?
  * Não. Não é possível mover dados ou o seu espaço de trabalho do Microsoft Azure para o Governo Azure.
* Posso alternar entre os espaços de trabalho do Microsoft Azure e do Governo Azure do portal Da Suite de Gestão de Operações?
  * Não. Os portais da Microsoft Azure e do Governo Azure são separados e não partilham informações.

Para mais informações, consulte [o Azure Monitor regista documentação pública.](../log-analytics/log-analytics-overview.md)

## <a name="scheduler"></a>Scheduler
Para obter informações sobre este serviço e como utilizá-lo, consulte a [Documentação do Agendador Azure.](../scheduler/index.md)

## <a name="azure-portal"></a>Portal do Azure
O portal do Governo Azure pode ser acedido [aqui.](https://portal.azure.us)

## <a name="azure-resource-manager"></a>Azure Resource Manager
Para obter informações sobre este serviço e como utilizá-lo, consulte a Documentação do [Gestor de Recursos do Azure.](../azure-resource-manager/management/overview.md)

## <a name="next-steps"></a>Passos seguintes
* Subscreva o [blog do Governo Azure](https://blogs.msdn.microsoft.com/azuregov/)
* Obtenha ajuda no Stack Overflow usando o [azure-gov](https://stackoverflow.com/questions/tagged/azure-gov)
* Dar feedback ou solicitar novas funcionalidades através do fórum de [feedback do Governo Azure](https://feedback.azure.com/forums/558487-azure-government)
