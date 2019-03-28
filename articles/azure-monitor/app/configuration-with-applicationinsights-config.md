---
title: Referência de applicationinsights. config - Azure | Documentos da Microsoft
description: Ativar ou desativar os módulos de recolha de dados e adicionar contadores de desempenho e outros parâmetros.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 6e397752-c086-46e9-8648-a1196e8078c2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/19/2018
ms.reviewer: olegan
ms.author: mbullwin
ms.openlocfilehash: 1a5b6d435dcc82b59c30302f9cd711975864594c
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58522252"
---
# <a name="configuring-the-application-insights-sdk-with-applicationinsightsconfig-or-xml"></a>Configurar o SDK do Application Insights com ApplicationInsights.config ou .xml
O SDK de .NET do Application Insights é composta por um número de pacotes de NuGet. O [pacote core](https://www.nuget.org/packages/Microsoft.ApplicationInsights) fornece a API para enviar telemetria para o Application Insights. [Pacotes adicionais](https://www.nuget.org/packages?q=Microsoft.ApplicationInsights) fornecer telemetria *módulos* e *inicializadores* para rastrear automaticamente telemetria da sua aplicação e de seu contexto. Ao ajustar o ficheiro de configuração, pode ativar ou desativar os módulos de telemetria e inicializadores e definir parâmetros para alguns deles.

O ficheiro de configuração é denominado `ApplicationInsights.config` ou `ApplicationInsights.xml`, dependendo do tipo da sua aplicação. Este é adicionado automaticamente ao seu projeto quando [instalar a maioria das versões do SDK][start]. Ele também é adicionado a uma aplicação web por [Monitor de estado num servidor IIS][redfield], ou ao selecionar o Application Insights [extensão para um Web site do Azure ou a VM](azure-web-apps.md).

Aqui não é um ficheiro equivalente para controlar a [SDK numa página web][client].

Este documento descreve as secções que vir na configuração do arquivo, como elas controlam os componentes do SDK, e os pacotes de NuGet carregar esses componentes.

> [!NOTE]
> Instruções de applicationinsights. config e. xml não se aplicam para o SDK .NET Core. Para que as alterações para uma aplicação .NET Core, normalmente, podemos usar o ficheiro appSettings JSON. Um exemplo disso pode ser encontrado no [documentação de depurador de instantâneos.](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger)

## <a name="telemetry-modules-aspnet"></a>Módulos de telemetria (ASP.NET)
Cada módulo telemetria recolhe um tipo específico de dados e utiliza a API principal para enviar os dados. Os módulos são instalados por diferentes pacotes de NuGet, que também adicionar as linhas necessárias para o arquivo. config.

Há um nó no ficheiro de configuração para cada módulo. Para desativar um módulo, elimine o nó ou comentá-lo.

### <a name="dependency-tracking"></a>Rastreamento de dependências
[Rastreamento de dependências](../../azure-monitor/app/asp-net-dependencies.md) recolhe telemetria sobre chamadas torna a sua aplicação para bases de dados e serviços externos e bases de dados. Para permitir que este módulo funcionar num servidor IIS, precisa [instalar o Monitor de estado][redfield]. Para usá-lo em VMs, ou aplicações web do Azure [selecione a extensão do Application Insights](azure-web-apps.md).

Também pode escrever sua própria dependência controlo de código com o [TrackDependency API](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency).

* `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) pacote NuGet.

### <a name="performance-collector"></a>Recoletor de desempenho
[Recolhe os contadores de desempenho do sistema](../../azure-monitor/app/performance-counters.md) , tais como CPU, memória e rede de carga das instalações do IIS. Pode especificar quais contadores para recolher, incluindo os contadores de desempenho que tiver definido por.

* `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule`
* [Microsoft.ApplicationInsights.PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector) pacote NuGet.

### <a name="application-insights-diagnostics-telemetry"></a>Telemetria de diagnóstico do Application Insights
O `DiagnosticsTelemetryModule` reporta erros no código de instrumentação do Application Insights em si. Por exemplo, se o código não é possível acessar contadores de desempenho ou se um `ITelemetryInitializer` lança uma exceção. Telemetria de rastreio controlada por este módulo é apresentada no [pesquisa de diagnóstico][diagnostic]. Envia dados de diagnóstico para dc.services.vsallin.net.

* `Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule`
* [Applicationinsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) pacote NuGet. Se apenas instalar este pacote, o ficheiro applicationinsights. config não é criado automaticamente.

### <a name="developer-mode"></a>Modo de programador
`DeveloperModeWithDebuggerAttachedTelemetryModule` força o Application Insights `TelemetryChannel` para enviar dados imediatamente, item de telemetria de uma de cada vez, quando um depurador for anexado ao processo de aplicação. Isso reduz a quantidade de tempo entre o momento em que, quando seu aplicativo controla a telemetria e quando aparecer no portal do Application Insights. Ele faz com que uma sobrecarga significativa na largura de banda de CPU e da rede.

* `Microsoft.ApplicationInsights.WindowsServer.DeveloperModeWithDebuggerAttachedTelemetryModule`
* [Windows Server do Application Insights](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) pacote NuGet

### <a name="web-request-tracking"></a>Rastreamento de solicitações da Web
Relatórios do [código de tempo e o resultado da resposta](../../azure-monitor/app/asp-net.md) de pedidos HTTP.

* `Microsoft.ApplicationInsights.Web.RequestTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet package

### <a name="exception-tracking"></a>Controlo de exceções
`ExceptionTrackingTelemetryModule` roteiros exceções não processadas na sua aplicação web. Ver [falhas e exceções][exceptions].

* `Microsoft.ApplicationInsights.Web.ExceptionTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet package
* `Microsoft.ApplicationInsights.WindowsServer.UnobservedExceptionTelemetryModule` -roteiros [unobserved exceções de tarefa](https://blogs.msdn.com/b/pfxteam/archive/2011/09/28/task-exception-handling-in-net-4-5.aspx).
* `Microsoft.ApplicationInsights.WindowsServer.UnhandledExceptionTelemetryModule` -controla as exceções sem tratamento para funções de trabalho, serviços do windows e aplicativos de console.
* [Windows Server do Application Insights](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) pacote NuGet.

### <a name="eventsource-tracking"></a>Controlo de EventSource
`EventSourceTelemetryModule` permite-lhe configurar EventSource eventos a serem enviados para o Application Insights como rastreios. Para informações sobre como acompanhar eventos de EventSource, consulte [usando o EventSource Events](../../azure-monitor/app/asp-net-trace-logs.md#using-eventsource-events).

* `Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule`
* [Microsoft.ApplicationInsights.EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener) 

### <a name="etw-event-tracking"></a>Registo de eventos do ETW
`EtwCollectorTelemetryModule` permite-lhe configurar eventos a partir de fornecedores ETW a serem enviados para o Application Insights como rastreios. Para informações sobre como acompanhar eventos do ETW, consulte [usando o ETW Events](../../azure-monitor/app/asp-net-trace-logs.md#using-etw-events).

* `Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule`
* [Microsoft.ApplicationInsights.EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector) 

### <a name="microsoftapplicationinsights"></a>Microsoft.ApplicationInsights
O pacote de applicationinsights fornece a [core API](https://msdn.microsoft.com/library/mt420197.aspx) do SDK. Utilizam esta opção os outros módulos de telemetria e também pode [utilizá-lo para definir sua própria telemetria](../../azure-monitor/app/api-custom-events-metrics.md).

* Nenhuma entrada no applicationinsights. config.
* [Applicationinsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) pacote NuGet. Se instalou apenas este NuGet, não é gerado nenhum ficheiro. config.

## <a name="telemetry-channel"></a>Canal de telemetria
O canal de telemetria gere o armazenamento em buffer e transmissão de telemetria para o serviço do Application Insights.

* `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel` é o canal predefinido para os serviços. Ele coloca na memória intermédia dados na memória.
* `Microsoft.ApplicationInsights.PersistenceChannel` é uma alternativa para aplicativos de console. Ele pode guardar quaisquer dados unflushed para armazenamento persistente quando seu aplicativo seja fechado e enviará-lo quando a aplicação for iniciada novamente.

## <a name="telemetry-initializers-aspnet"></a>Inicializadores de telemetria (ASP.NET)
Inicializadores de telemetria definir as propriedades de contexto que são enviadas juntamente com todos os itens de telemetria.

Pode [escrever suas própria inicializadores](../../azure-monitor/app/api-filtering-sampling.md#add-properties) para definir as propriedades de contexto.

Os inicializadores de padrão foram todos configurados com os pacotes de Web ou WindowsServer NuGet:

* `AccountIdTelemetryInitializer` Define a propriedade AccountId.
* `AuthenticatedUserIdTelemetryInitializer` Define a propriedade de AuthenticatedUserId conforme definido pelo SDK do JavaScript.
* `AzureRoleEnvironmentTelemetryInitializer` atualizações a `RoleName` e `RoleInstance` propriedades do `Device` contexto para todos os itens de telemetria com informações extraídas do ambiente de runtime do Azure.
* `BuildInfoConfigComponentVersionTelemetryInitializer` atualizações a `Version` propriedade do `Component` contexto para todos os itens de telemetria com o valor extraído do `BuildInfo.config` ficheiro produzido pelo MS Build.
* `ClientIpHeaderTelemetryInitializer` as atualizações `Ip` propriedade do `Location` contexto de todos os itens de telemetria com base no `X-Forwarded-For` cabeçalho HTTP do pedido.
* `DeviceTelemetryInitializer` Atualiza as seguintes propriedades do `Device` contexto para todos os itens de telemetria.
  * `Type` está definido como "PC"
  * `Id` está definido para o nome de domínio do computador onde está a executar o aplicativo web.
  * `OemName` está definido como o valor extraído do `Win32_ComputerSystem.Manufacturer` campo a utilizar a WMI.
  * `Model` está definido como o valor extraído do `Win32_ComputerSystem.Model` campo a utilizar a WMI.
  * `NetworkType` está definido como o valor extraído do `NetworkInterface`.
  * `Language` está definido como o nome do `CurrentCulture`.
* `DomainNameRoleInstanceTelemetryInitializer` atualizações a `RoleInstance` propriedade o `Device` contexto para todos os itens de telemetria com o nome de domínio do computador onde está a executar a aplicação web.
* `OperationNameTelemetryInitializer` atualizações a `Name` propriedade do `RequestTelemetry` e o `Name` propriedade do `Operation` contexto de todos os itens de telemetria com base no método HTTP, bem como nomes de controlador MVC do ASP.NET e a ação invocada para processar o pedido.
* `OperationIdTelemetryInitializer` ou `OperationCorrelationTelemetryInitializer` atualizações a `Operation.Id` monitorizados de propriedade de contexto de todos os itens de telemetria e processamento de um pedido com o gerado automaticamente `RequestTelemetry.Id`.
* `SessionTelemetryInitializer` atualizações a `Id` propriedade do `Session` contexto para todos os itens de telemetria com o valor extraído do `ai_session` cookie gerado pelo código de instrumentação do Application Insights JavaScript em execução no navegador do usuário.
* `SyntheticTelemetryInitializer` ou `SyntheticUserAgentTelemetryInitializer` atualizações a `User`, `Session`, e `Operation` propriedades de contextos de todos os itens de telemetria controladas quando o processamento de um pedido de uma origem sintética, como um disponibilidade de teste ou bot do mecanismo de pesquisa. Por predefinição, [Explorador de métricas](../../azure-monitor/app/metrics-explorer.md) não apresenta a telemetria sintética.

    O `<Filters>` definir as propriedades dos pedidos de identificação.
* `UserTelemetryInitializer` atualizações a `Id` e `AcquisitionDate` propriedades de `User` contexto para todos os itens de telemetria com valores extraídos do `ai_user` cookie gerado pelo código de instrumentação do Application Insights JavaScript em execução do usuário browser.
* `WebTestTelemetryInitializer` Define o id de utilizador, id de sessão e propriedades da origem sintético para provenientes de solicitações de HTTP [testes de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md).
  O `<Filters>` definir as propriedades dos pedidos de identificação.

Para aplicações de .NET em execução no Service Fabric, pode incluir o `Microsoft.ApplicationInsights.ServiceFabric` pacote NuGet. Este pacote inclui um `FabricTelemetryInitializer`, que adiciona propriedades do Service Fabric para itens de telemetria. Para obter mais informações, consulte a [página do GitHub](https://github.com/Microsoft/ApplicationInsights-ServiceFabric/blob/master/README.md) sobre as propriedades adicionadas por este pacote de NuGet.

## <a name="telemetry-processors-aspnet"></a>Processadores de telemetria (ASP.NET)
Processadores de telemetria podem filtrar e modificar cada item de telemetria apenas antes do envio do SDK para o portal.

Pode [escrever seus próprios processadores de telemetria](../../azure-monitor/app/api-filtering-sampling.md#filtering).

#### <a name="adaptive-sampling-telemetry-processor-from-200-beta3"></a>Processador de telemetria de amostragem adaptável (a partir de 2.0.0-Beta3)
Opção ativada por predefinição. Se a sua aplicação enviar uma grande quantidade de telemetria, este processador remove algumas delas.

```xml

    <TelemetryProcessors>
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    </TelemetryProcessors>

```

O parâmetro fornece o destino que tenta obter o algoritmo. Cada instância do SDK funciona de forma independente, portanto, se o servidor for um cluster de várias máquinas, o volume real da telemetria será multiplicado em conformidade.

[Saiba mais sobre a amostragem](../../azure-monitor/app/sampling.md).

#### <a name="fixed-rate-sampling-telemetry-processor-from-200-beta1"></a>Processador de telemetria de amostragem de taxa fixa (a partir de 2.0.0-beta1)
Também é uma norma [processador de telemetria de amostragem](../../azure-monitor/app/api-filtering-sampling.md) (a partir de 2.0.1):

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
Esses parâmetros afetam como o SDK de Java deve armazenar e liberar os dados de telemetria que recolhe.

#### <a name="maxtelemetrybuffercapacity"></a>MaxTelemetryBufferCapacity
O número de itens de telemetria que podem ser armazenados no armazenamento de dentro da memória do SDK. Quando for atingido este número, o buffer de telemetria é descarregado - ou seja, os itens de telemetria são enviados para o servidor do Application Insights.

* Mínimo: 1
* Máx.: 1000
* Predefinição: 500

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
Determina a frequência com que os dados armazenados no armazenamento na memória devem ser libertados (enviada para o Application Insights).

* Mínimo: 1
* Máx.: 300
* Predefinição: 5

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
Determina o tamanho máximo em MB é alocado para o armazenamento persistente no disco local. Este armazenamento é utilizado para persistência itens de telemetria falhou a serem transmitidos para o ponto de final do Application Insights. Quando o tamanho de armazenamento tiver sido cumprido, novos itens de telemetria serão rejeitados.

* Mínimo: 1
* Máx.: 100
* Predefinição: 10

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

[Local reencaminhador](opencensus-local-forwarder.md) é um agente que recolhe Application Insights ou [OpenCensus](https://opencensus.io/) telemetria a partir de uma variedade de SDKs e estruturas e a encaminha para o Application Insights. Ele é capaz de em execução no Windows e Linux. Quando combinadas com o SDK de Java do Application Insights o reencaminhador local fornece suporte completo para [métricas em direto](../../azure-monitor/app/live-stream.md) e amostragem adaptável.

```xml
<Channel type="com.microsoft.applicationinsights.channel.concrete.localforwarder.LocalForwarderTelemetryChannel">
<EndpointAddress><!-- put the hostname:port of your LocalForwarder instance here --></EndpointAddress>

<!-- The properties below are optional. The values shown are the defaults for each property -->

<FlushIntervalInSeconds>5</FlushIntervalInSeconds><!-- must be between [1, 500]. values outside the bound will be rounded to nearest bound -->
<MaxTelemetryBufferCapacity>500</MaxTelemetryBufferCapacity><!-- units=number of telemetry items; must be between [1, 1000] -->
</Channel>
```

Se estiver a utilizar SpringBoot starter, adicione o seguinte ao ficheiro de configuração (Application):

```yml
azure.application-insights.channel.local-forwarder.endpoint-address=<!--put the hostname:port of your LocalForwarder instance here-->
azure.application-insights.channel.local-forwarder.flush-interval-in-seconds=<!--optional-->
azure.application-insights.channel.local-forwarder.max-telemetry-buffer-capacity=<!--optional-->
```

Valores predefinidos são os mesmos para a configuração de SpringBoot Application e o applicationinsights. XML.

## <a name="instrumentationkey"></a>InstrumentationKey
Determina o recurso do Application Insights em que os seus dados aparecem. Normalmente, vai criar um recurso separado, com uma chave separada, para cada uma das suas aplicações.

Se pretender definir a chave dinamicamente – por exemplo, se pretender enviar os resultados da sua aplicação para diferentes recursos - pode omitir a chave do arquivo de configuração e configurá-lo no código.

Para definir a chave para todas as instâncias de TelemetryClient, incluindo módulos de telemetria standard, defina a chave no TelemetryConfiguration.Active. Fazer isso num método de inicialização, como global.aspx.cs num serviço ASP.NET:

```csharp

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey =
          // - for example -
          WebConfigurationManager.AppSettings["ikey"];
      //...
```

Se quiser apenas enviar um conjunto específico de eventos para um recurso diferente, pode definir a chave para um TelemetryClient específico:

```csharp

    var tc = new TelemetryClient();
    tc.Context.InstrumentationKey = "----- my key ----";
    tc.TrackEvent("myEvent");
    // ...

```

Para obter uma nova chave [criar um novo recurso no portal do Application Insights][new].



## <a name="applicationid-provider"></a>Fornecedor de ApplicationId

_Disponível a partir de v2.6.0_

O objetivo deste fornecedor é pesquisar um ID de aplicação com base numa chave de instrumentação. O ID da aplicação é incluído no RequestTelemetry e DependencyTelemetry e utilizado para determinar a correlação no Portal.

Isto está disponível através da definição `TelemetryConfiguration.ApplicationIdProvider` no código ou na configuração.

### <a name="interface-iapplicationidprovider"></a>Interface: IApplicationIdProvider

```csharp
public interface IApplicationIdProvider
{
    bool TryGetApplicationId(string instrumentationKey, out string applicationId);
}
```


Fornecemos duas implementações na [applicationinsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) sdk: `ApplicationInsightsApplicationIdProvider` e `DictionaryApplicationIdProvider`.

### <a name="applicationinsightsapplicationidprovider"></a>ApplicationInsightsApplicationIdProvider

Este é um wrapper em torno da nossa API de perfil. Ele irá limitar pedidos e resultados de cache.

Esse provedor é adicionado ao seu ficheiro de configuração ao instalar um [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) ou [applicationinsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/)

Essa classe tem uma propriedade opcional `ProfileQueryEndpoint`.
Por predefinição está definida `https://dc.services.visualstudio.com/api/profiles/{0}/appId`.
Se precisar de configurar um proxy para esta configuração, recomendamos a utilização do proxy da base de endereço e incluindo "/api/perfis/{0}/appId". Tenha em atenção que "{0}" é substituída no tempo de execução por solicitação com a chave de instrumentação.

#### <a name="example-configuration-via-applicationinsightsconfig"></a>Exemplo de configuração por meio de applicationinsights. config:
```xml
<ApplicationInsights>
    ...
    <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
        <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
    </ApplicationIdProvider>
    ...
</ApplicationInsights>
```

#### <a name="example-configuration-via-code"></a>Exemplo de configuração por meio do código:
```csharp
TelemetryConfiguration.Active.ApplicationIdProvider = new ApplicationInsightsApplicationIdProvider();
```

### <a name="dictionaryapplicationidprovider"></a>DictionaryApplicationIdProvider

Este é um fornecedor estático, que dependem de sua chave de instrumentação configurado / pares de ID da aplicação.

Essa classe tem uma propriedade `Defined`, que é um Dictionary < string, string > da chave de instrumentação para pares de ID da aplicação.

Essa classe tem uma propriedade opcional `Next` que podem ser utilizadas para configurar outro fornecedor a utilizar quando for solicitada uma chave de instrumentação que não existe na sua configuração.

#### <a name="example-configuration-via-applicationinsightsconfig"></a>Exemplo de configuração por meio de applicationinsights. config:
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

#### <a name="example-configuration-via-code"></a>Exemplo de configuração por meio do código:
```csharp
TelemetryConfiguration.Active.ApplicationIdProvider = new DictionaryApplicationIdProvider{
 Defined = new Dictionary<string, string>
    {
        {"InstrumentationKey_1", "ApplicationId_1"},
        {"InstrumentationKey_2", "ApplicationId_2"}
    }
};
```




## <a name="next-steps"></a>Passos Seguintes
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
