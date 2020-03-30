---
title: ApplicationInsights.config reference - Azure [ Microsoft Docs
description: Ative ou desative os módulos de recolha de dados e adicione contadores de desempenho e outros parâmetros.
ms.topic: conceptual
ms.date: 05/22/2019
ms.reviewer: olegan
ms.openlocfilehash: b2c407036277b17c0f8c08f3261c932a6dc66624
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276182"
---
# <a name="configuring-the-application-insights-sdk-with-applicationinsightsconfig-or-xml"></a>Configurar o SDK do Application Insights com ApplicationInsights.config ou .xml
O Application Insights .NET SDK consiste numa série de pacotes NuGet. O [pacote principal](https://www.nuget.org/packages/Microsoft.ApplicationInsights) fornece a API para o envio de telemetria para os Insights de Aplicação. [Pacotes adicionais](https://www.nuget.org/packages?q=Microsoft.ApplicationInsights) fornecem *módulos* de telemetria e *inicializadores* para rastrear automaticamente a telemetria a partir da sua aplicação e do seu contexto. Ao ajustar o ficheiro de configuração, pode ativar ou desativar módulos de telemetria e inicializadores e definir parâmetros para alguns deles.

O ficheiro de `ApplicationInsights.config` `ApplicationInsights.xml`configuração está nomeado ou, dependendo do tipo da sua aplicação. É adicionado automaticamente ao seu projeto quando instala a [maioria das versões do SDK][start]. Por padrão, ao utilizar a experiência automatizada dos projetos de modelo do Estúdio Visual que suportam **adicionar > Application Insights Telemettry,** o ficheiro ApplicationInsights.config é criado na pasta raiz do projeto e quando cumprido é copiado para a pasta do lixo. Também é adicionado a uma aplicação web pelo [Status Monitor num servidor IIS][redfield]. O ficheiro de configuração é ignorado se for utilizada [extensão para o website do Azure](azure-web-apps.md) ou extensão para o Conjunto de Escala de [Máquinas Azure VM.](azure-vm-vmss-apps.md)

Não há um ficheiro equivalente para controlar o [SDK numa página web.][client]

Este documento descreve as secções que vê no ficheiro de configuração, como controlam os componentes do SDK e quais as embalagens NuGet que carregam esses componentes.

> [!NOTE]
> ApplicationInsights.config e .xml instruções não se aplicam ao .NET Core SDK. Para configurar as aplicações .NET Core, siga [este](../../azure-monitor/app/asp-net-core.md) guia.

## <a name="telemetry-modules-aspnet"></a>Módulos de Telemetria (ASP.NET)
Cada Módulo de Telemetria recolhe um tipo específico de dados e utiliza a API central para enviar os dados. Os módulos são instalados por diferentes pacotes NuGet, que também adicionam as linhas necessárias ao ficheiro .config.

Há um nó no ficheiro de configuração de cada módulo. Para desativar um módulo, elimine o nó ou comente-o.

### <a name="dependency-tracking"></a>Controlo de Dependências
[O rastreio de dependência](../../azure-monitor/app/asp-net-dependencies.md) recolhe telemetria sobre chamadas que a sua aplicação faz para bases de dados e serviços externos e bases de dados. Para permitir que este módulo funcione num servidor IIS, é necessário instalar o [Status Monitor][redfield].

Também pode escrever o seu próprio código de rastreio de dependência utilizando a [API TrackDependency](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency).

* `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) NuGet.

As dependências podem ser recolhidas automaticamente sem modificar o seu código utilizando a anexação baseada no agente (sem código). Para usá-lo em aplicações web Azure, ative a [extensão De Insights](azure-web-apps.md)de Aplicação . Para usá-lo em conjunto de escala de máquina virtual Azure VM ou Azure, activaa a [extensão de monitorização da aplicação para vm e conjunto](azure-vm-vmss-apps.md)de escala de máquina virtual .

### <a name="performance-collector"></a>Colecionador de desempenho
Recolhe contadores de desempenho do [sistema,](../../azure-monitor/app/performance-counters.md) tais como CPU, memória e carga de rede a partir de instalações IIS. Pode especificar quais os contadores a recolher, incluindo contadores de desempenho que criou.

* `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule`
* [Microsoft.ApplicationInsights.PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector) NuGet.

### <a name="application-insights-diagnostics-telemetry"></a>Informações de aplicação Diagnostica Telemetria
Os `DiagnosticsTelemetryModule` relatórios falham no próprio código de instrumentação de Insights de Aplicação. Por exemplo, se o código não conseguir `ITelemetryInitializer` aceder aos contadores de desempenho ou se um for lançado uma exceção. A telemetria de vestígios rastreada por este módulo aparece na Pesquisa de [Diagnóstico][diagnostic].

```
* `Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule`
* [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet package. If you only install this package, the ApplicationInsights.config file is not automatically created.
```

### <a name="developer-mode"></a>Modo de desenvolvimento
`DeveloperModeWithDebuggerAttachedTelemetryModule`força os `TelemetryChannel` Insights de Aplicação a enviar dados imediatamente, um item de telemetria de cada vez, quando um desbugger está ligado ao processo de aplicação. Isto reduz a quantidade de tempo entre o momento em que a sua aplicação rastreia a telemetria e quando aparece no portal Application Insights. Causa sobrecargasignificativa na CPU e na largura de banda da rede.

* `Microsoft.ApplicationInsights.WindowsServer.DeveloperModeWithDebuggerAttachedTelemetryModule`
* [Insights de aplicação Windows Server](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) Pacote NuGet

### <a name="web-request-tracking"></a>Rastreio de pedidos web
Informa o tempo de resposta e o código de [resultados](../../azure-monitor/app/asp-net.md) dos pedidos http.

* `Microsoft.ApplicationInsights.Web.RequestTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet

### <a name="exception-tracking"></a>Rastreio de exceções
`ExceptionTrackingTelemetryModule`rastreia exceções não manipuladas na sua aplicação web. Ver [Falhas e exceções.][exceptions]

* `Microsoft.ApplicationInsights.Web.ExceptionTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet
* `Microsoft.ApplicationInsights.WindowsServer.UnobservedExceptionTelemetryModule`- rastreia exceções de [tarefas não observadas.](https://blogs.msdn.com/b/pfxteam/archive/2011/09/28/task-exception-handling-in-net-4-5.aspx)
* `Microsoft.ApplicationInsights.WindowsServer.UnhandledExceptionTelemetryModule`- rastreia exceções não tratadas para funções de trabalhadores, serviços windows e aplicações de consola.
* [Insights de aplicação Windows Server](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) Pacote NuGet.

### <a name="eventsource-tracking"></a>Rastreio de Origem de Eventos
`EventSourceTelemetryModule`permite configurar eventos do EventSource a serem enviados para Application Insights como vestígios. Para obter informações sobre o rastreio de eventos Source, consulte [A Utilização de EventosSource](../../azure-monitor/app/asp-net-trace-logs.md#use-eventsource-events).

* `Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule`
* [Microsoft.ApplicationInsights.EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener) 

### <a name="etw-event-tracking"></a>Rastreio de eventos da ETW
`EtwCollectorTelemetryModule`permite configurar eventos de fornecedores de ETW para serem enviados para Application Insights como vestígios. Para obter informações sobre o rastreio de eventos DaTW, consulte [a utilização de eventos ETW](../../azure-monitor/app/asp-net-trace-logs.md#use-etw-events).

* `Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule`
* [Microsoft.ApplicationInsights.EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector) 

### <a name="microsoftapplicationinsights"></a>Microsoft.ApplicationInsights
O pacote Microsoft.ApplicationInsights fornece o [Núcleo API](https://msdn.microsoft.com/library/mt420197.aspx) do SDK. Os outros Módulos de Telemetria usam isto, e também pode [usá-lo para definir a sua própria telemetria.](../../azure-monitor/app/api-custom-events-metrics.md)

* Nenhuma entrada em ApplicationInsights.config.
* [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet pacote. Se instalar este NuGet, não é gerado um ficheiro .config.

## <a name="telemetry-channel"></a>Canal de Telemetria
O canal de [telemetria](telemetry-channels.md) gere tampão e transmissão de telemetria para o serviço Application Insights.

* `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel`é o canal padrão para aplicações web. Amortece os dados na memória e emprega mecanismos de retry e armazenamento de discos locais para uma entrega de telemetria mais fiável.
* `Microsoft.ApplicationInsights.InMemoryChannel`é um canal de telemetria leve, que é usado se nenhum outro canal estiver configurado. 

## <a name="telemetry-initializers-aspnet"></a>Iniciais de telemetria (ASP.NET)
Os Iniciais de Telemetria estabelecem propriedades de contexto que são enviadas juntamente com cada item de telemetria.

Pode [escrever os seus próprios iniciais](../../azure-monitor/app/api-filtering-sampling.md#add-properties) para definir propriedades de contexto.

Os iniciais padrão são todos definidos pelos pacotes Web ou WindowsServer NuGet:

* `AccountIdTelemetryInitializer`define a propriedade AccountId.
* `AuthenticatedUserIdTelemetryInitializer`define a propriedade AuthenticatedUserId conforme definido pelo JavaScript SDK.
* `AzureRoleEnvironmentTelemetryInitializer`atualiza as `RoleName` `RoleInstance` propriedades do `Device` contexto para todos os itens de telemetria com informações extraídas do ambiente de execução azure.
* `BuildInfoConfigComponentVersionTelemetryInitializer`atualiza a `Version` propriedade `Component` do contexto para todos os itens de telemetria com o valor extraído do `BuildInfo.config` ficheiro produzido pela MS Build.
* `ClientIpHeaderTelemetryInitializer`atualiza `Ip` a `Location` propriedade do contexto de todos os itens `X-Forwarded-For` de telemetria com base no cabeçalho HTTP do pedido.
* `DeviceTelemetryInitializer`atualiza as seguintes `Device` propriedades do contexto para todos os itens de telemetria.
  * `Type`é definido para "PC"
  * `Id`é definido para o nome de domínio do computador onde a aplicação web está em execução.
  * `OemName`é definido para o valor `Win32_ComputerSystem.Manufacturer` extraído do campo utilizando OWM.
  * `Model`é definido para o valor `Win32_ComputerSystem.Model` extraído do campo utilizando OWM.
  * `NetworkType`é definido para o valor `NetworkInterface`extraído do .
  * `Language`está definido para o `CurrentCulture`nome do .
* `DomainNameRoleInstanceTelemetryInitializer`atualiza a `RoleInstance` propriedade `Device` do contexto para todos os itens de telemetria com o nome de domínio do computador onde a aplicação web está em execução.
* `OperationNameTelemetryInitializer`atualiza a `Name` propriedade `RequestTelemetry` do `Name` e da `Operation` propriedade do contexto de todos os itens de telemetria com base no método HTTP, bem como nomes de ASP.NET controlador MVC e ação invocada para processar o pedido.
* `OperationIdTelemetryInitializer`ou `OperationCorrelationTelemetryInitializer` atualiza `Operation.Id` a propriedade de contexto de todos os itens de telemetria `RequestTelemetry.Id`rastreados ao manusear um pedido com o gerado automaticamente .
* `SessionTelemetryInitializer`atualiza a `Id` propriedade `Session` do contexto para todos os itens de telemetria com valor extraído do `ai_session` cookie gerado pelo código de instrumentação ApplicationInsights JavaScript em execução no navegador do utilizador.
* `SyntheticTelemetryInitializer`ou `SyntheticUserAgentTelemetryInitializer` atualiza `User`as `Session` `Operation` propriedades de todos os itens de telemetria rastreados ao manusear um pedido de uma fonte sintética, como um teste de disponibilidade ou um bot de motor de busca. Por padrão, o [Metrics Explorer](../../azure-monitor/app/metrics-explorer.md) não exibe telemetria sintética.

    O `<Filters>` conjunto identificando propriedades dos pedidos.
* `UserTelemetryInitializer`atualiza as `Id` `AcquisitionDate` propriedades `User` do contexto para todos os itens de telemetria com valores extraídos do `ai_user` cookie gerado pelo código de instrumentação JavaScript De Aplicação Insights JavaScript em execução no navegador do utilizador.
* `WebTestTelemetryInitializer`define o ID do utilizador, o ID da sessão e as propriedades de origem sintética para pedidos HTTP que provêm de testes de [disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md).
  O `<Filters>` conjunto identificando propriedades dos pedidos.

Para aplicações .NET em execução em `Microsoft.ApplicationInsights.ServiceFabric` Tecido de Serviço, pode incluir o pacote NuGet. Este pacote `FabricTelemetryInitializer`inclui um , que adiciona propriedades de Tecido de Serviço a artigos de telemetria. Para mais informações, consulte a [página gitHub](https://github.com/Microsoft/ApplicationInsights-ServiceFabric/blob/master/README.md) sobre as propriedades adicionadas por este pacote NuGet.

## <a name="telemetry-processors-aspnet"></a>Processadores de Telemetria (ASP.NET)
Os Processadores de Telemetria podem filtrar e modificar cada item de telemetria pouco antes de ser enviado do SDK para o portal.

Pode [escrever os seus próprios Processadores de Telemetria.](../../azure-monitor/app/api-filtering-sampling.md#filtering)

#### <a name="adaptive-sampling-telemetry-processor-from-200-beta3"></a>Processador de telemetria de amostragem adaptativo (a partir de 2.0.0-beta3)
Opção ativada por predefinição. Se a sua aplicação enviar muita telemetria, este processador remove parte dela.

```xml

    <TelemetryProcessors>
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    </TelemetryProcessors>

```

O parâmetro fornece o alvo que o algoritmo tenta alcançar. Cada instância do SDK funciona de forma independente, por isso, se o seu servidor for um conjunto de várias máquinas, o volume real de telemetria será multiplicado em conformidade.

[Saiba mais sobre a amostragem.](../../azure-monitor/app/sampling.md)

#### <a name="fixed-rate-sampling-telemetry-processor-from-200-beta1"></a>Processador de telemetria de amostragem a taxa fixa (de 2.0.0-beta1)
Existe também um processador de [telemetria de amostragem](../../azure-monitor/app/api-filtering-sampling.md) padrão (a partir de 2.0.1):

```XML

    <TelemetryProcessors>
     <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">

     <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
     <SamplingPercentage>10</SamplingPercentage>
     </Add>
   </TelemetryProcessors>

```



## <a name="channel-parameters-java"></a>Parâmetros do canal (Java)
Estes parâmetros afetam a forma como o Java SDK deve armazenar e descarregar os dados de telemetria que recolhe.

#### <a name="maxtelemetrybuffercapacity"></a>MaxTelemettryBufferCapacity
O número de artigos de telemetria que podem ser armazenados no armazenamento na memória do SDK. Quando este número é atingido, o tampão de telemetria é lavado - isto é, os itens de telemetria são enviados para o servidor Application Insights.

* Min: 1
* Máximo: 1000
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

#### <a name="flushintervalinseconds"></a>FlushIntervalinSeconds
Determina com que frequência os dados armazenados no armazenamento em memória devem ser lavados (enviados para Informações de Aplicação).

* Min: 1
* Máximo: 300
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
Determina o tamanho máximo em MB que é atribuído ao armazenamento persistente no disco local. Este armazenamento é utilizado para itens de telemetria persistentes que não foram transmitidos ao ponto final do Application Insights. Quando o tamanho do armazenamento tiver sido atingido, novos artigos de telemetria serão descartados.

* Min: 1
* Máximo: 100
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

## <a name="instrumentationkey"></a>Chave de Instrumentação
Isto determina o recurso Application Insights no qual os seus dados aparecem. Normalmente cria-se um recurso separado, com uma chave separada, para cada uma das suas aplicações.

Se quiser definir a chave de forma dinâmica - por exemplo, se quiser enviar resultados da sua aplicação para diferentes recursos - pode omitir a chave do ficheiro de configuração e defini-la em código.

Para definir a chave para todos os casos de TelemettryClient, incluindo módulos de telemetria padrão. Faça-o num método de inicialização, como global.aspx.cs num serviço ASP.NET:

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights;

    protected void Application_Start()
    {
        TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
        configuration.InstrumentationKey = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
        var telemetryClient = new TelemetryClient(configuration);
   
```

Se apenas quiser enviar um conjunto específico de eventos para um recurso diferente, pode definir a chave para um TelemettryClient específico:

```csharp

    var tc = new TelemetryClient();
    tc.Context.InstrumentationKey = "----- my key ----";
    tc.TrackEvent("myEvent");
    // ...

```

Para obter uma nova chave, [crie um novo recurso no portal Application Insights][new].



## <a name="applicationid-provider"></a>Fornecedor applicationid

_Disponível a partir de v2.6.0_

O objetivo deste fornecedor é procurar um ID de aplicação baseado numa Chave de Instrumentação. O ID de aplicação está incluído na RequestTelemettry e DependênciaTelemetria e utilizado para determinar a Correlação no Portal.

Isto está disponível por definição `TelemetryConfiguration.ApplicationIdProvider` em código ou em config.

### <a name="interface-iapplicationidprovider"></a>Interface: IApplicationIdProvider

```csharp
public interface IApplicationIdProvider
{
    bool TryGetApplicationId(string instrumentationKey, out string applicationId);
}
```


Fornecemos duas implementações no [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) `ApplicationInsightsApplicationIdProvider` sdk: e `DictionaryApplicationIdProvider`.

### <a name="applicationinsightsapplicationidprovider"></a>ApplicationInsightsApplicationIdProvider

Isto é um invólucro à volta da nossa API de perfil. Acelerará os pedidos e os resultados da cache.

Este fornecedor é adicionado ao seu ficheiro config quando instala o [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) ou [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/)

Esta classe tem `ProfileQueryEndpoint`uma propriedade opcional.
Por defeito, `https://dc.services.visualstudio.com/api/profiles/{0}/appId`isto está definido para .
Se precisar de configurar um proxy para esta configuração, recomendamos a procuração{0}do endereço base e incluindo "/api/perfis/ /appId". Note que{0}' ' é substituído no prazo de execução por pedido com a Chave de Instrumentação.

#### <a name="example-configuration-via-applicationinsightsconfig"></a>Configuração de exemplo via ApplicationInsights.config:
```xml
<ApplicationInsights>
    ...
    <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
        <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
    </ApplicationIdProvider>
    ...
</ApplicationInsights>
```

#### <a name="example-configuration-via-code"></a>Configuração do exemplo via código:
```csharp
TelemetryConfiguration.Active.ApplicationIdProvider = new ApplicationInsightsApplicationIdProvider();
```

### <a name="dictionaryapplicationidprovider"></a>DicionárioApplicationIdProvider

Trata-se de um fornecedor estático, que contará com os seus pares de identificação de chave de instrumentação configurado/ aplicação.

Esta classe tem `Defined`uma propriedade , que é um Dicionário<cordas, string> de instrumentação chave para pares de id de aplicação.

Esta classe tem `Next` uma propriedade opcional que pode ser usada para configurar outro fornecedor para usar quando é solicitada uma Chave de Instrumentação que não existe na sua configuração.

#### <a name="example-configuration-via-applicationinsightsconfig"></a>Configuração de exemplo via ApplicationInsights.config:
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

#### <a name="example-configuration-via-code"></a>Configuração do exemplo via código:
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
[Saiba mais sobre a API.][api]

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[client]: ../../azure-monitor/app/javascript.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[exceptions]: ../../azure-monitor/app/asp-net-exceptions.md
[netlogs]: ../../azure-monitor/app/asp-net-trace-logs.md
[new]: ../../azure-monitor/app/create-new-resource.md 
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
[start]: ../../azure-monitor/app/app-insights-overview.md
