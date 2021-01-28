---
title: referência ApplicationInsights.config - Azure | Microsoft Docs
description: Ativar ou desativar os módulos de recolha de dados e adicionar contadores de desempenho e outros parâmetros.
ms.topic: conceptual
ms.date: 05/22/2019
ms.custom: devx-track-csharp
ms.reviewer: olegan
ms.openlocfilehash: d05503c2a22c476d9ab08e8aeb058ca1b9826778
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98928676"
---
# <a name="configuring-the-application-insights-sdk-with-applicationinsightsconfig-or-xml"></a>Configurar o SDK do Application Insights com ApplicationInsights.config ou .xml
O Application Insights .NET SDK consiste numa série de pacotes NuGet. O [pacote principal](https://www.nuget.org/packages/Microsoft.ApplicationInsights) fornece a API para o envio de telemetria para os Insights de Aplicação. [Pacotes adicionais](https://www.nuget.org/packages?q=Microsoft.ApplicationInsights) fornecem *módulos* de telemetria e *inicializadores* para rastrear automaticamente a telemetria a partir da sua aplicação e do seu contexto. Ao ajustar o ficheiro de configuração, pode ativar ou desativar módulos e inicializadores de Telemetria e definir parâmetros para alguns deles.

O ficheiro de configuração é nomeado `ApplicationInsights.config` `ApplicationInsights.xml` ou, dependendo do tipo da sua aplicação. É automaticamente adicionado ao seu projeto quando [instala a maioria das versões do SDK.][start] Por predefinição, ao utilizar a experiência automatizada dos projetos do modelo Visual Studio que **suportam a Add > Application Insights Telemetria,** o ficheiro ApplicationInsights.config é criado na pasta raiz do projeto e quando cumprido é copiado para a pasta do caixote do lixo. Também é adicionado a uma aplicação web pelo [Status Monitor num servidor IIS.][redfield] O ficheiro de configuração é ignorado se for utilizada [extensão para o website Azure](azure-web-apps.md) ou [extensão para Azure VM e conjunto de escala de máquina virtual.](azure-vm-vmss-apps.md)

Não existe um ficheiro equivalente para controlar o [SDK numa página web.][client]

Este documento descreve as secções que vê no ficheiro de configuração, como controlam os componentes do SDK e que os pacotes NuGet carregam esses componentes.

> [!NOTE]
> ApplicationInsights.config e .xml instruções não se aplicam ao Núcleo SDK .NET. Para configurar aplicações .NET Core, siga [este](./asp-net-core.md) guia.

## <a name="telemetry-modules-aspnet"></a>Módulos de telemetria (ASP.NET)
Cada Módulo de Telemetria recolhe um tipo específico de dados e utiliza a API central para enviar os dados. Os módulos são instalados por diferentes pacotes NuGet, que também adicionam as linhas necessárias ao ficheiro .config.

Há um nó no ficheiro de configuração de cada módulo. Para desativar um módulo, elimine o nó ou comente-o.

### <a name="dependency-tracking"></a>Controlo de Dependências
[O rastreio de dependência](./asp-net-dependencies.md) recolhe telemetria sobre chamadas que a sua aplicação faz para bases de dados e serviços externos e bases de dados. Para permitir que este módulo funcione num servidor IIS, é necessário instalar o [Status Monitor][redfield].

Também pode escrever o seu próprio código de rastreio de dependência utilizando a [API trackDependency](./api-custom-events-metrics.md#trackdependency).

* `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) NuGet.

As dependências podem ser recolhidas automaticamente sem modificar o seu código utilizando o anexo baseado em agente (sem código). Para usá-lo em aplicações web Azure, ative a [extensão Application Insights](azure-web-apps.md). Para usá-lo em Azure VM ou Azure configuração de escala de máquina virtual, permita a [extensão de monitorização da aplicação para vm e conjunto de balança de máquina virtual](azure-vm-vmss-apps.md).

### <a name="performance-collector"></a>Colecionador de desempenho
[Recolhe contadores de desempenho do sistema](./performance-counters.md) como CPU, memória e carga de rede a partir de instalações do IIS. Pode especificar quais os contadores a cobrar, incluindo os contadores de desempenho que você próprio montou.

* `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule`
* [Microsoft.ApplicationInsights.PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector) NuGet.

### <a name="application-insights-diagnostics-telemetry"></a>Telemetria de Diagnóstico de Insights de Aplicação
Os `DiagnosticsTelemetryModule` relatórios falham no próprio código de instrumentação application insights. Por exemplo, se o código não puder aceder aos contadores de desempenho ou se um `ITelemetryInitializer` lançar uma exceção. A telemetria rastreada por este módulo aparece na [Pesquisa de Diagnóstico][diagnostic].

```
* `Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule`
* [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet package. If you only install this package, the ApplicationInsights.config file is not automatically created.
```

### <a name="developer-mode"></a>Modo de Programação
`DeveloperModeWithDebuggerAttachedTelemetryModule` força o Application Insights `TelemetryChannel` a enviar dados imediatamente, um item de telemetria de cada vez, quando um depurgger é anexado ao processo de aplicação. Isto reduz a quantidade de tempo entre o momento em que a sua aplicação rastreia a telemetria e quando aparece no portal Application Insights. Causa sobrecarga significativa na CPU e largura de banda de rede.

* `Microsoft.ApplicationInsights.WindowsServer.DeveloperModeWithDebuggerAttachedTelemetryModule`
* [Aplicações Insights Servidor Windows](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) Pacote NuGet

### <a name="web-request-tracking"></a>Rastreio de pedidos na Web
Reporta o tempo de resposta e o código de [resultados](../../azure-monitor/app/asp-net.md) dos pedidos HTTP.

* `Microsoft.ApplicationInsights.Web.RequestTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet

### <a name="exception-tracking"></a>Rastreio de exceções
`ExceptionTrackingTelemetryModule` rastreia exceções não manipuladas na sua aplicação web. Ver [Falhas e exceções.][exceptions]

* `Microsoft.ApplicationInsights.Web.ExceptionTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet
* `Microsoft.ApplicationInsights.WindowsServer.UnobservedExceptionTelemetryModule` - rastreia exceções de tarefas não observadas
* `Microsoft.ApplicationInsights.WindowsServer.UnhandledExceptionTelemetryModule` - rastreia exceções não tratadas para funções de trabalhadores, serviços windows e aplicações de consola.
* [Aplicações Insights Servidor Windows](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) Pacote NuGet.

### <a name="eventsource-tracking"></a>Rastreio de EventSource
`EventSourceTelemetryModule` permite-lhe configurar eventos EventSource a serem enviados para o Application Insights como vestígios. Para obter informações sobre o rastreio de eventos EventSource, consulte [usando eventos EventSource](./asp-net-trace-logs.md#use-eventsource-events).

* `Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule`
* [Microsoft.ApplicationInsights.EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener) 

### <a name="etw-event-tracking"></a>Rastreio de eventos da ETW
`EtwCollectorTelemetryModule` permite-lhe configurar eventos de fornecedores da ETW para serem enviados para a Application Insights como vestígios. Para obter informações sobre o rastreio de eventos da ETW, consulte [a Utilização de Eventos ETW](../../azure-monitor/app/asp-net-trace-logs.md#use-etw-events).

* `Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule`
* [Microsoft.ApplicationInsights.EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector) 

### <a name="microsoftapplicationinsights"></a>Microsoft.ApplicationInsights
O pacote Microsoft.ApplicationInsights fornece a [API central](/dotnet/api/microsoft.applicationinsights) do SDK. Os outros Módulos de Telemetria usam isto, e também pode [usá-lo para definir a sua própria telemetria.](./api-custom-events-metrics.md)

* Sem entrada na ApplicationInsights.config.
* [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet pacote. Se instalar este NuGet, não é gerado nenhum ficheiro .config.

## <a name="telemetry-channel"></a>Canal da Telemetria
O [canal de telemetria](telemetry-channels.md) gere a tamponagem e transmissão de telemetria para o serviço Application Insights.

* `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel` é o canal predefinido para aplicações web. Tampona os dados na memória, e emprega mecanismos de retíria e armazenamento de discos locais para uma entrega de telemetria mais fiável.
* `Microsoft.ApplicationInsights.InMemoryChannel` é um canal de telemetria leve, que é usado se nenhum outro canal estiver configurado. 

## <a name="telemetry-initializers-aspnet"></a>Inicializadores de telemetria (ASP.NET)
Os inicializadores de telemetria definem propriedades de contexto que são enviadas juntamente com cada item de telemetria.

Pode [escrever os seus próprios inicializadores](./api-filtering-sampling.md#add-properties) para definir propriedades de contexto.

Os inicializadores standard são todos definidos quer pelos pacotes Web ou WindowsServer NuGet:

* `AccountIdTelemetryInitializer` define a propriedade AccountId.
* `AuthenticatedUserIdTelemetryInitializer` define a propriedade AuthenticatedUserId como definida pelo JavaScript SDK.
* `AzureRoleEnvironmentTelemetryInitializer` atualiza as `RoleName` propriedades e propriedades do contexto para todos os `RoleInstance` `Device` itens de telemetria com informações extraídas do ambiente de tempo de execução Azure.
* `BuildInfoConfigComponentVersionTelemetryInitializer` atualiza a `Version` propriedade do contexto para todos os `Component` itens de telemetria com o valor extraído do `BuildInfo.config` ficheiro produzido pela MS Build.
* `ClientIpHeaderTelemetryInitializer` atualiza `Ip` a propriedade do `Location` contexto de todos os itens de telemetria com base no `X-Forwarded-For` cabeçalho HTTP do pedido.
* `DeviceTelemetryInitializer` atualiza as seguintes propriedades do `Device` contexto para todos os itens de telemetria.
  * `Type` está definido para "PC"
  * `Id` é definido para o nome de domínio do computador onde a aplicação web está em execução.
  * `OemName` é definido para o valor extraído do `Win32_ComputerSystem.Manufacturer` campo usando OMI.
  * `Model` é definido para o valor extraído do `Win32_ComputerSystem.Model` campo usando OMI.
  * `NetworkType` é definido para o valor extraído do `NetworkInterface` .
  * `Language` está definido para o nome do `CurrentCulture` .
* `DomainNameRoleInstanceTelemetryInitializer` atualiza a `RoleInstance` propriedade do contexto para todos os `Device` itens de telemetria com o nome de domínio do computador onde a aplicação web está em execução.
* `OperationNameTelemetryInitializer` atualiza a `Name` propriedade `RequestTelemetry` do e da propriedade do contexto `Name` de todos os `Operation` itens de telemetria com base no método HTTP, bem como nomes de ASP.NET controlador MVC e ação invocada para processar o pedido.
* `OperationIdTelemetryInitializer` ou `OperationCorrelationTelemetryInitializer` atualiza a propriedade de contexto de todos os `Operation.Id` itens de telemetria rastreados enquanto manuseiam um pedido com o gerado automaticamente `RequestTelemetry.Id` .
* `SessionTelemetryInitializer` atualiza a `Id` propriedade do contexto para todos os `Session` itens de telemetria com valor extraído do `ai_session` cookie gerado pelo código de instrumentação JavaScript aplicação aplicação em execução no navegador do utilizador.
* `SyntheticTelemetryInitializer` ou `SyntheticUserAgentTelemetryInitializer` atualiza as propriedades , e `User` `Session` `Operation` contexts de todos os itens de telemetria rastreados ao lidar com um pedido de uma fonte sintética, como um teste de disponibilidade ou bot de motor de busca. Por predefinição, [o Metrics Explorer](../platform/metrics-charts.md) não apresenta telemetria sintética.

    O `<Filters>` conjunto identificando propriedades dos pedidos.
* `UserTelemetryInitializer` atualiza as `Id` propriedades e propriedades de contexto para todos os `AcquisitionDate` `User` itens de telemetria com valores extraídos do `ai_user` cookie gerado pelo código de instrumentação JavaScript da Aplicação Insights em execução no navegador do utilizador.
* `WebTestTelemetryInitializer` define o ID do utilizador, o ID da sessão e as propriedades de origem sintética para pedidos HTTP que vêm de [testes de disponibilidade](./monitor-web-app-availability.md).
  O `<Filters>` conjunto identificando propriedades dos pedidos.

Para aplicações .NET em execução em Service Fabric, pode incluir o `Microsoft.ApplicationInsights.ServiceFabric` pacote NuGet. Este pacote inclui um `FabricTelemetryInitializer` , que adiciona propriedades de Tecido de Serviço a itens de telemetria. Para mais informações, consulte a [página do GitHub](https://github.com/Microsoft/ApplicationInsights-ServiceFabric/blob/master/README.md) sobre as propriedades adicionadas por este pacote NuGet.

## <a name="telemetry-processors-aspnet"></a>Processadores de telemetria (ASP.NET)
Os processadores de telemetria podem filtrar e modificar cada item de telemetria pouco antes de serem enviados do SDK para o portal.

Pode [escrever os seus próprios processadores de telemetria.](./api-filtering-sampling.md#filtering)

#### <a name="adaptive-sampling-telemetry-processor-from-200-beta3"></a>Processador de telemetria de amostragem adaptativa (a partir de 2.0.0-beta3)
Opção ativada por predefinição. Se a sua aplicação enviar muita telemetria, este processador remove parte dela.

```xml

    <TelemetryProcessors>
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    </TelemetryProcessors>

```

O parâmetro fornece o alvo que o algoritmo tenta alcançar. Cada instância do SDK funciona de forma independente, por isso, se o seu servidor for um conjunto de várias máquinas, o volume real de telemetria será multiplicado em conformidade.

[Saiba mais sobre a amostragem.](./sampling.md)

#### <a name="fixed-rate-sampling-telemetry-processor-from-200-beta1"></a>Processador de telemetria de amostragem de taxa fixa (a partir de 2.0.0-beta1)
Existe também um processador de [telemetria](./api-filtering-sampling.md) de amostragem padrão (a partir de 2.0.1):

```XML

    <TelemetryProcessors>
     <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">

     <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
     <SamplingPercentage>10</SamplingPercentage>
     </Add>
   </TelemetryProcessors>

```

## <a name="instrumentationkey"></a>InstrumentaçãoKey
Isto determina o recurso Application Insights no qual os seus dados aparecem. Normalmente cria-se um recurso separado, com uma chave separada, para cada uma das suas aplicações.

Se pretender definir a chave de forma dinâmica - por exemplo, se pretender enviar resultados da sua aplicação para diferentes recursos - pode omitir a chave do ficheiro de configuração e defini-la em código.

Para definir a chave para todas as instâncias de Telemetria, incluindo módulos de telemetria padrão. Faça-o num método de inicialização, como global.aspx.cs num serviço ASP.NET:

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights;

    protected void Application_Start()
    {
        TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
        configuration.InstrumentationKey = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
        var telemetryClient = new TelemetryClient(configuration);
   
```

Se apenas quiser enviar um conjunto específico de eventos para um recurso diferente, pode definir a chave para um TelemetriaClient específico:

```csharp

    var tc = new TelemetryClient();
    tc.Context.InstrumentationKey = "----- my key ----";
    tc.TrackEvent("myEvent");
    // ...

```

Para obter uma nova chave, [crie um novo recurso no portal Application Insights][new].



## <a name="applicationid-provider"></a>Fornecedor ApplicationId

_Disponível a partir de v2.6.0_

O objetivo deste fornecedor é procurar um ID de aplicação baseado numa Chave de Instrumentação. O ID da aplicação está incluído no RequestTelemetry and DependencyTelemetry e é utilizado para determinar a Correlação no Portal.

Isto está disponível definindo `TelemetryConfiguration.ApplicationIdProvider` em código ou em config.

### <a name="interface-iapplicationidprovider"></a>Interface: IApplicationIdProvider

```csharp
public interface IApplicationIdProvider
{
    bool TryGetApplicationId(string instrumentationKey, out string applicationId);
}
```


Fornecemos duas implementações no [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) sdk: `ApplicationInsightsApplicationIdProvider` e `DictionaryApplicationIdProvider` .

### <a name="applicationinsightsapplicationidprovider"></a>ApplicationInsightsApplicationIdProvider

Este é um invólucro em torno da nossa API de perfil. Vai acelerar os pedidos e os resultados da cache.

Este fornecedor é adicionado ao seu ficheiro config quando instala [o Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) ou [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/)

Esta classe tem uma propriedade `ProfileQueryEndpoint` opcional.
Por predefinição, isto está definido para `https://dc.services.visualstudio.com/api/profiles/{0}/appId` .
Se precisar de configurar um representante para esta configuração, recomendamos a procuração do endereço base e incluindo "/api/profiles/ {0} /appId". Note que {0} ' ' ' é substituído em tempo de execução por pedido pela chave de instrumentação.

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

#### <a name="example-configuration-via-code"></a>Configuração de exemplo via código:
```csharp
TelemetryConfiguration.Active.ApplicationIdProvider = new ApplicationInsightsApplicationIdProvider();
```

### <a name="dictionaryapplicationidprovider"></a>DicionárioApplicationIdProvider

Este é um fornecedor estático, que contará com os seus pares configurados de Instrumentação/ ID de aplicação.

Esta classe tem uma `Defined` propriedade, que é um Dicionário<cadeia,> de cordas de chave de instrumentação para pares de identificação de aplicação.

Esta classe tem uma propriedade opcional `Next` que pode ser usada para configurar outro fornecedor para usar quando é solicitada uma Chave de Instrumentação que não existe na sua configuração.

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

#### <a name="example-configuration-via-code"></a>Configuração de exemplo via código:
```csharp
TelemetryConfiguration.Active.ApplicationIdProvider = new DictionaryApplicationIdProvider{
 Defined = new Dictionary<string, string>
    {
        {"InstrumentationKey_1", "ApplicationId_1"},
        {"InstrumentationKey_2", "ApplicationId_2"}
    }
};
```




## <a name="next-steps"></a>Próximos passos
[Saiba mais sobre a API.][api]

<!--Link references-->

[api]: ./api-custom-events-metrics.md
[client]: ./javascript.md
[diagnostic]: ./diagnostic-search.md
[exceptions]: ./asp-net-exceptions.md
[netlogs]: ./asp-net-trace-logs.md
[new]: ./create-new-resource.md
[redfield]: ./monitor-performance-live-website-now.md
[start]: ./app-insights-overview.md

