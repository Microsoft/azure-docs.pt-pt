---
title: Referência de ApplicationInsights. config-Azure | Microsoft Docs
description: Habilite ou desabilite módulos de coleta de dados e adicione contadores de desempenho e outros parâmetros.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/22/2019
ms.reviewer: olegan
ms.openlocfilehash: 94ae9035c1657c1ce20c40234ddca95ae30d9edd
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677543"
---
# <a name="configuring-the-application-insights-sdk-with-applicationinsightsconfig-or-xml"></a>Configurar o SDK do Application Insights com ApplicationInsights.config ou .xml
O SDK do .NET Application Insights consiste em vários pacotes NuGet. O [pacote principal](https://www.nuget.org/packages/Microsoft.ApplicationInsights) fornece a API para enviar telemetria para o Application insights. [Pacotes adicionais](https://www.nuget.org/packages?q=Microsoft.ApplicationInsights) fornecem *módulos* de telemetria e *inicializadores* para controlar automaticamente a telemetria do seu aplicativo e seu contexto. Ao ajustar o arquivo de configuração, você pode habilitar ou desabilitar módulos e inicializadores de telemetria e definir parâmetros para alguns deles.

O arquivo de configuração é denominado `ApplicationInsights.config` ou `ApplicationInsights.xml`, dependendo do tipo de seu aplicativo. Ele é adicionado automaticamente ao seu projeto quando você [instala a maioria das versões do SDK][start]. Por padrão, ao usar a experiência automatizada dos projetos de modelo do Visual Studio que dão suporte à **adição de > Application insights Telemetry**, o arquivo ApplicationInsights. config é criado na pasta raiz do projeto e, quando compatível, é copiado para o pasta bin. Ele também é adicionado a um aplicativo Web por [status monitor em um servidor IIS][redfield]. O arquivo de configuração será ignorado se a [extensão para o site](azure-web-apps.md) ou [a extensão do Azure para a VM do Azure e o conjunto de dimensionamento de máquinas virtuais](azure-vm-vmss-apps.md) for usado.

Não há um arquivo equivalente para controlar o [SDK em uma página da Web][client].

Este documento descreve as seções que você vê no arquivo de configuração, como eles controlam os componentes do SDK e quais pacotes NuGet carregam esses componentes.

> [!NOTE]
> As instruções ApplicationInsights. config e. xml não se aplicam ao SDK do .NET Core. Para configurar aplicativos .NET Core, siga [este](../../azure-monitor/app/asp-net-core.md) guia.

## <a name="telemetry-modules-aspnet"></a>Módulos de telemetria (ASP.NET)
Cada módulo de telemetria coleta um tipo específico de dados e usa a API principal para enviar os dados. Os módulos são instalados por diferentes pacotes do NuGet, que também adicionam as linhas necessárias ao arquivo. config.

Há um nó no arquivo de configuração para cada módulo. Para desabilitar um módulo, exclua o nó ou comente-o.

### <a name="dependency-tracking"></a>Acompanhamento de dependência
O [controle de dependência](../../azure-monitor/app/asp-net-dependencies.md) coleta a telemetria sobre chamadas que seu aplicativo faz para bancos de dados e serviços externos e bancos de dados. Para permitir que esse módulo funcione em um servidor IIS, você precisa [instalar o status monitor][redfield].

Você também pode escrever seu próprio código de rastreamento de dependência usando a [API TrackDependency](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency).

* `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule`
* Pacote NuGet [Microsoft. ApplicationInsights. DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) .

As dependências podem ser coletadas automaticamente sem modificar seu código usando a anexação baseada em agente (sem código). Para usá-lo em aplicativos Web do Azure, habilite a [extensão Application insights](azure-web-apps.md). Para usá-lo na VM do Azure ou no conjunto de dimensionamento de máquinas virtuais do Azure, habilite a [extensão de monitoramento de aplicativo para VM e conjunto de dimensionamento de máquinas](azure-vm-vmss-apps.md)

### <a name="performance-collector"></a>Coletor de desempenho
[Coleta contadores de desempenho do sistema](../../azure-monitor/app/performance-counters.md) , como CPU, memória e carga de rede de instalações do IIS. Você pode especificar quais contadores coletar, incluindo contadores de desempenho que você configurou por conta própria.

* `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule`
* Pacote NuGet [Microsoft. ApplicationInsights. PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector) .

### <a name="application-insights-diagnostics-telemetry"></a>Telemetria de diagnóstico de Application Insights
O `DiagnosticsTelemetryModule` relata erros no código de instrumentação Application Insights em si. Por exemplo, se o código não puder acessar contadores de desempenho ou se um `ITelemetryInitializer` lançar uma exceção. A telemetria de rastreamento rastreada por esse módulo aparece na [pesquisa de diagnóstico][diagnostic].

```
* `Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule`
* [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet package. If you only install this package, the ApplicationInsights.config file is not automatically created.
```

### <a name="developer-mode"></a>Modo de desenvolvedor
`DeveloperModeWithDebuggerAttachedTelemetryModule` força o Application Insights `TelemetryChannel` a enviar dados imediatamente, um item de telemetria por vez, quando um depurador é anexado ao processo do aplicativo. Isso reduz a quantidade de tempo entre o momento em que o aplicativo rastreia a telemetria e quando ela aparece no portal de Application Insights. Isso causa uma sobrecarga significativa na largura de banda da CPU e da rede.

* `Microsoft.ApplicationInsights.WindowsServer.DeveloperModeWithDebuggerAttachedTelemetryModule`
* [Application insights o Windows Server](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) Pacote NuGet

### <a name="web-request-tracking"></a>Acompanhamento de solicitação da Web
Relata o [tempo de resposta e o código de resultado](../../azure-monitor/app/asp-net.md) de solicitações HTTP.

* `Microsoft.ApplicationInsights.Web.RequestTrackingTelemetryModule`
* Pacote NuGet [Microsoft. ApplicationInsights. Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)

### <a name="exception-tracking"></a>Acompanhamento de exceção
`ExceptionTrackingTelemetryModule` rastreia exceções sem tratamento em seu aplicativo Web. Consulte [falhas e exceções][exceptions].

* `Microsoft.ApplicationInsights.Web.ExceptionTrackingTelemetryModule`
* Pacote NuGet [Microsoft. ApplicationInsights. Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)
* `Microsoft.ApplicationInsights.WindowsServer.UnobservedExceptionTelemetryModule`-rastreia [exceções de tarefas não observadas](https://blogs.msdn.com/b/pfxteam/archive/2011/09/28/task-exception-handling-in-net-4-5.aspx).
* `Microsoft.ApplicationInsights.WindowsServer.UnhandledExceptionTelemetryModule`-rastreia exceções sem tratamento para funções de trabalho, serviços do Windows e aplicativos de console.
* [Application insights o Windows Server](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) Pacote NuGet.

### <a name="eventsource-tracking"></a>Acompanhamento de EventSource
`EventSourceTelemetryModule` permite que você configure eventos EventSource a serem enviados a Application Insights como rastreamentos. Para obter informações sobre como controlar eventos EventSource, consulte [usando eventos EventSource](../../azure-monitor/app/asp-net-trace-logs.md#use-eventsource-events).

* `Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule`
* [Microsoft. ApplicationInsights. EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener) 

### <a name="etw-event-tracking"></a>Rastreamento de eventos ETW
`EtwCollectorTelemetryModule` permite que você configure eventos de provedores ETW a serem enviados a Application Insights como rastreamentos. Para obter informações sobre como controlar eventos ETW, consulte [usando eventos ETW](../../azure-monitor/app/asp-net-trace-logs.md#use-etw-events).

* `Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule`
* [Microsoft. ApplicationInsights. EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector) 

### <a name="microsoftapplicationinsights"></a>Microsoft. ApplicationInsights
O pacote Microsoft. ApplicationInsights fornece a [API principal](https://msdn.microsoft.com/library/mt420197.aspx) do SDK. Os outros módulos de telemetria usam isso, e você também pode [usá-lo para definir sua própria telemetria](../../azure-monitor/app/api-custom-events-metrics.md).

* Nenhuma entrada em ApplicationInsights. config.
* Pacote NuGet [Microsoft. ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) . Se você apenas instalar esse NuGet, nenhum arquivo. config será gerado.

## <a name="telemetry-channel"></a>Canal de telemetria
O [canal de telemetria](telemetry-channels.md) gerencia o buffer e a transmissão de telemetria para o serviço de Application insights.

* `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel` é o canal padrão para aplicativos Web. Ele armazena em buffer os dados na memória e emprega mecanismos de repetição e armazenamento em disco local para entrega de telemetria mais confiável.
* `Microsoft.ApplicationInsights.InMemoryChannel` é um canal de telemetria leve, que será usado se nenhum outro canal estiver configurado. 

## <a name="telemetry-initializers-aspnet"></a>Inicializadores de telemetria (ASP.NET)
Inicializadores de telemetria definem propriedades de contexto que são enviadas junto com cada item de telemetria.

Você pode [escrever seus próprios inicializadores](../../azure-monitor/app/api-filtering-sampling.md#add-properties) para definir propriedades de contexto.

Os inicializadores padrão são todos definidos pelos pacotes NuGet Web ou WindowsServer:

* `AccountIdTelemetryInitializer` define a propriedade AccountId.
* `AuthenticatedUserIdTelemetryInitializer` define a propriedade AuthenticatedUserId como definida pelo SDK do JavaScript.
* `AzureRoleEnvironmentTelemetryInitializer` atualiza as propriedades `RoleName` e `RoleInstance` do contexto `Device` para todos os itens de telemetria com informações extraídas do ambiente de tempo de execução do Azure.
* `BuildInfoConfigComponentVersionTelemetryInitializer` atualiza a propriedade `Version` do contexto de `Component` para todos os itens de telemetria com o valor extraído do arquivo de `BuildInfo.config` produzido pelo MS Build.
* `ClientIpHeaderTelemetryInitializer` atualiza `Ip` Propriedade do contexto de `Location` de todos os itens de telemetria com base no cabeçalho HTTP `X-Forwarded-For` da solicitação.
* `DeviceTelemetryInitializer` atualiza as propriedades a seguir do contexto de `Device` para todos os itens de telemetria.
  * `Type` está definido como "PC"
  * `Id` é definido como o nome de domínio do computador em que o aplicativo Web está em execução.
  * `OemName` é definido como o valor extraído do campo `Win32_ComputerSystem.Manufacturer` usando o WMI.
  * `Model` é definido como o valor extraído do campo `Win32_ComputerSystem.Model` usando o WMI.
  * `NetworkType` é definido como o valor extraído da `NetworkInterface`.
  * `Language` é definido como o nome do `CurrentCulture`.
* `DomainNameRoleInstanceTelemetryInitializer` atualiza a propriedade `RoleInstance` do contexto de `Device` para todos os itens de telemetria com o nome de domínio do computador em que o aplicativo Web está sendo executado.
* `OperationNameTelemetryInitializer` atualiza a propriedade `Name` do `RequestTelemetry` e a propriedade `Name` do contexto de `Operation` de todos os itens de telemetria com base no método HTTP, bem como nomes do controlador MVC ASP.NET e da ação invocada para processar a solicitação.
* `OperationIdTelemetryInitializer` ou `OperationCorrelationTelemetryInitializer` atualiza a propriedade de contexto `Operation.Id` de todos os itens de telemetria rastreados ao manipular uma solicitação com o `RequestTelemetry.Id` gerado automaticamente.
* `SessionTelemetryInitializer` atualiza a propriedade `Id` do contexto de `Session` para todos os itens de telemetria com o valor extraído do cookie `ai_session` gerado pelo código de instrumentação de JavaScript ApplicationInsights em execução no navegador do usuário.
* `SyntheticTelemetryInitializer` ou `SyntheticUserAgentTelemetryInitializer` atualiza as propriedades de contextos `User`, `Session` e `Operation` de todos os itens de telemetria rastreados ao lidar com uma solicitação de uma fonte sintética, como um teste de disponibilidade ou bot de mecanismo de pesquisa. Por padrão, [Metrics Explorer](../../azure-monitor/app/metrics-explorer.md) não exibe telemetria sintética.

    O `<Filters>` definir as propriedades de identificação das solicitações.
* `UserTelemetryInitializer` atualiza as propriedades `Id` e `AcquisitionDate` do contexto `User` para todos os itens de telemetria com valores extraídos do cookie `ai_user` gerado pelo código de instrumentação JavaScript Application Insights executado no navegador do usuário.
* `WebTestTelemetryInitializer` define a ID de usuário, a ID de sessão e as propriedades de origem sintética para solicitações HTTP provenientes de [testes de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md).
  O `<Filters>` definir as propriedades de identificação das solicitações.

Para aplicativos .NET em execução no Service Fabric, você pode incluir o pacote NuGet `Microsoft.ApplicationInsights.ServiceFabric`. Este pacote inclui um `FabricTelemetryInitializer`, que adiciona Service Fabric Propriedades a itens de telemetria. Para obter mais informações, consulte a [página do GitHub](https://github.com/Microsoft/ApplicationInsights-ServiceFabric/blob/master/README.md) sobre as propriedades adicionadas por este pacote NuGet.

## <a name="telemetry-processors-aspnet"></a>Processadores de telemetria (ASP.NET)
Os processadores de telemetria podem filtrar e modificar cada item de telemetria logo antes de ele ser enviado do SDK para o Portal.

Você pode [escrever seus próprios processadores de telemetria](../../azure-monitor/app/api-filtering-sampling.md#filtering).

#### <a name="adaptive-sampling-telemetry-processor-from-200-beta3"></a>Processador de telemetria de amostragem adaptável (de 2.0.0-Beta3)
Opção ativada por predefinição. Se seu aplicativo envia muita telemetria, esse processador remove parte dele.

```xml

    <TelemetryProcessors>
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    </TelemetryProcessors>

```

O parâmetro fornece o destino que o algoritmo tenta atingir. Cada instância do SDK funciona de forma independente, portanto, se o servidor for um cluster de vários computadores, o volume real de telemetria será multiplicado de acordo.

[Saiba mais sobre amostragem](../../azure-monitor/app/sampling.md).

#### <a name="fixed-rate-sampling-telemetry-processor-from-200-beta1"></a>Processador de telemetria de amostragem de taxa fixa (de 2.0.0-beta1)
Também há um processador de [telemetria de amostragem](../../azure-monitor/app/api-filtering-sampling.md) padrão (da 2.0.1):

```XML

    <TelemetryProcessors>
     <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">

     <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
     <SamplingPercentage>10</SamplingPercentage>
     </Add>
   </TelemetryProcessors>

```



## <a name="channel-parameters-java"></a>Parâmetros de canal (Java)
Esses parâmetros afetam como o SDK do Java deve armazenar e liberar os dados de telemetria coletados.

#### <a name="maxtelemetrybuffercapacity"></a>MaxTelemetryBufferCapacity
O número de itens de telemetria que podem ser armazenados no armazenamento na memória do SDK. Quando esse número é atingido, o buffer de telemetria é liberado, ou seja, os itens de telemetria são enviados para o servidor de Application Insights.

* Mín.: 1
* Máx.: 1000
* Padrão: 500

```

  <ApplicationInsights>
      ...
      <Channel>
       <MaxTelemetryBufferCapacity>100</MaxTelemetryBufferCapacity>
      </Channel>
      ...
  </ApplicationInsights>
```

#### <a name="flushintervalinseconds"></a>FlushIntervalInSeconds
Determina com que frequência os dados armazenados no armazenamento na memória devem ser liberados (enviados para Application Insights).

* Mín.: 1
* Máx.: 300
* Padrão: 5

```

    <ApplicationInsights>
      ...
      <Channel>
        <FlushIntervalInSeconds>100</FlushIntervalInSeconds>
      </Channel>
      ...
    </ApplicationInsights>
```

#### <a name="maxtransmissionstoragecapacityinmb"></a>MaxTransmissionStorageCapacityInMB
Determina o tamanho máximo em MB que é alocado para o armazenamento persistente no disco local. Esse armazenamento é usado para persistir itens de telemetria que falharam ao serem transmitidos para o ponto de extremidade Application Insights. Quando o tamanho do armazenamento tiver sido atendido, novos itens de telemetria serão descartados.

* Mín.: 1
* Máx.: 100
* Padrão: 10

```

   <ApplicationInsights>
      ...
      <Channel>
        <MaxTransmissionStorageCapacityInMB>50</MaxTransmissionStorageCapacityInMB>
      </Channel>
      ...
   </ApplicationInsights>
```

#### <a name="local-forwarder"></a>Reencaminhador local

O [encaminhador local](opencensus-local-forwarder.md) é um agente que coleta Application insights ou telemetria [OpenCensus](https://opencensus.io/) de uma variedade de SDKs e estruturas e as encaminha para Application insights. Ele é capaz de ser executado no Windows e no Linux. Quando associado ao SDK do Java Application Insights, o encaminhador local fornece suporte completo para [métricas ao vivo](../../azure-monitor/app/live-stream.md) e amostragem adaptável.

```xml
<Channel type="com.microsoft.applicationinsights.channel.concrete.localforwarder.LocalForwarderTelemetryChannel">
<EndpointAddress><!-- put the hostname:port of your LocalForwarder instance here --></EndpointAddress>

<!-- The properties below are optional. The values shown are the defaults for each property -->

<FlushIntervalInSeconds>5</FlushIntervalInSeconds><!-- must be between [1, 500]. values outside the bound will be rounded to nearest bound -->
<MaxTelemetryBufferCapacity>500</MaxTelemetryBufferCapacity><!-- units=number of telemetry items; must be between [1, 1000] -->
</Channel>
```

Se você estiver usando o SpringBoot Starter, adicione o seguinte ao seu arquivo de configuração (Application. Properties):

```yml
azure.application-insights.channel.local-forwarder.endpoint-address=<!--put the hostname:port of your LocalForwarder instance here-->
azure.application-insights.channel.local-forwarder.flush-interval-in-seconds=<!--optional-->
azure.application-insights.channel.local-forwarder.max-telemetry-buffer-capacity=<!--optional-->
```

Os valores padrão são os mesmos para a configuração SpringBoot Application. Properties e applicationinsights. xml.

## <a name="instrumentationkey"></a>instrumentationKey
Isso determina o Application Insights recurso no qual os dados são exibidos. Normalmente, você cria um recurso separado, com uma chave separada, para cada um de seus aplicativos.

Se você quiser definir a chave dinamicamente, por exemplo, se quiser enviar resultados de seu aplicativo para recursos diferentes, você poderá omitir a chave do arquivo de configuração e defini-la no código.

Para definir a chave para todas as instâncias de TelemetryClient, incluindo módulos de telemetria padrão, defina a chave em TelemetryConfiguration. Active. Faça isso em um método de inicialização, como global.aspx.cs em um serviço ASP.NET:

```csharp

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey =
          // - for example -
          WebConfigurationManager.AppSettings["ikey"];
      //...
```

Se você quiser apenas enviar um conjunto específico de eventos para um recurso diferente, poderá definir a chave para um TelemetryClient específico:

```csharp

    var tc = new TelemetryClient();
    tc.Context.InstrumentationKey = "----- my key ----";
    tc.TrackEvent("myEvent");
    // ...

```

Para obter uma nova chave, [crie um novo recurso no portal de Application insights][new].



## <a name="applicationid-provider"></a>Provedor ApplicationId

_Disponível a partir de v 2.6.0_

A finalidade desse provedor é Pesquisar uma ID de aplicativo com base em uma chave de instrumentação. A ID do aplicativo está incluída em RequestTelemetry e DependencyTelemetry e usada para determinar a correlação no Portal.

Isso está disponível por meio da configuração de `TelemetryConfiguration.ApplicationIdProvider` no código ou na configuração.

### <a name="interface-iapplicationidprovider"></a>Interface: IApplicationIdProvider

```csharp
public interface IApplicationIdProvider
{
    bool TryGetApplicationId(string instrumentationKey, out string applicationId);
}
```


Fornecemos duas implementações no SDK [Microsoft. ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) : `ApplicationInsightsApplicationIdProvider` e `DictionaryApplicationIdProvider`.

### <a name="applicationinsightsapplicationidprovider"></a>ApplicationInsightsApplicationIdProvider

Trata-se de um wrapper em nossa API de perfil. Ele limitará as solicitações e os resultados do cache.

Esse provedor é adicionado ao seu arquivo de configuração quando você instala o [Microsoft. ApplicationInsights. DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) ou [o Microsoft. ApplicationInsights. Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/)

Essa classe tem uma propriedade opcional `ProfileQueryEndpoint`.
Por padrão, isso é definido como `https://dc.services.visualstudio.com/api/profiles/{0}/appId`.
Se você precisar configurar um proxy para essa configuração, é recomendável fazer o proxy do endereço base e incluir "/API/Profiles/{0}/appId". Observe que ' {0} ' é substituído em tempo de execução por solicitação com a chave de instrumentação.

#### <a name="example-configuration-via-applicationinsightsconfig"></a>Exemplo de configuração por meio de ApplicationInsights. config:
```xml
<ApplicationInsights>
    ...
    <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
        <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
    </ApplicationIdProvider>
    ...
</ApplicationInsights>
```

#### <a name="example-configuration-via-code"></a>Exemplo de configuração por meio de código:
```csharp
TelemetryConfiguration.Active.ApplicationIdProvider = new ApplicationInsightsApplicationIdProvider();
```

### <a name="dictionaryapplicationidprovider"></a>DictionaryApplicationIdProvider

Este é um provedor estático, que dependerá de seus pares de ID de aplicativo/chave de instrumentação configurados.

Essa classe tem uma propriedade `Defined`, que é um dicionário < cadeia de caracteres > de chave de instrumentação para pares de ID de aplicativo.

Essa classe tem uma propriedade opcional `Next` que pode ser usada para configurar outro provedor a ser usado quando uma chave de instrumentação é solicitada que não existe em sua configuração.

#### <a name="example-configuration-via-applicationinsightsconfig"></a>Exemplo de configuração por meio de ApplicationInsights. config:
```xml
<ApplicationInsights>
    ...
    <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.DictionaryApplicationIdProvider, Microsoft.ApplicationInsights">
        <Defined>
            <Type key="InstrumentationKey_1" value="ApplicationId_1"/>
            <Type key="InstrumentationKey_2" value="ApplicationId_2"/>
        </Defined>
        <Next Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights" />
    </ApplicationIdProvider>
    ...
</ApplicationInsights>
```

#### <a name="example-configuration-via-code"></a>Exemplo de configuração por meio de código:
```csharp
TelemetryConfiguration.Active.ApplicationIdProvider = new DictionaryApplicationIdProvider{
 Defined = new Dictionary<string, string>
    {
        {"InstrumentationKey_1", "ApplicationId_1"},
        {"InstrumentationKey_2", "ApplicationId_2"}
    }
};
```




## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre a API][api].

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[client]: ../../azure-monitor/app/javascript.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[exceptions]: ../../azure-monitor/app/asp-net-exceptions.md
[netlogs]: ../../azure-monitor/app/asp-net-trace-logs.md
[new]: ../../azure-monitor/app/create-new-resource.md 
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
[start]: ../../azure-monitor/app/app-insights-overview.md
