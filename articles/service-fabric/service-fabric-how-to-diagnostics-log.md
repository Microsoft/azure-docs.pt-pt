---
title: Gerar eventos a partir de uma aplicação .NET do Service Fabric no Azure ou um cluster autónomo
description: Saiba mais sobre como adicionar o registo para a sua aplicação .NET do Service Fabric alojada num cluster do Azure ou um cluster autónomo.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/27/2018
ms.author: ryanwi
ms.openlocfilehash: 0e5cfa42eba5a2110ebf8879c771f2fef048ae03
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57835718"
---
# <a name="add-logging-to-your-service-fabric-application"></a>Adicionar registos à sua aplicação do Service Fabric

A aplicação tem de fornecer informações suficientes para forensicamente depurá-lo quando surgem problemas. O registo é uma das coisas mais importantes que pode adicionar a sua aplicação do Service Fabric. Quando ocorre uma falha, registro em log boa pode fornecer uma forma de investigar falhas. Ao analisar padrões de log, pode encontrar formas de melhorar o desempenho ou o design da sua aplicação. Este documento demonstra Algumas opções de registo diferente.

## <a name="eventflow"></a>EventFlow

O [o EventFlow biblioteca](https://github.com/Azure/diagnostics-eventflow) suite permite que os aplicativos definir que dados de diagnóstico para recolher e, em que devem ser produzidos para. Dados de diagnóstico podem ser qualquer coisa, desde os contadores de desempenho para rastreios de aplicações. Ele é executado no mesmo processo que a aplicação, para que a sobrecarga de comunicação é minimizada. Para obter mais informações sobre o EventFlow e o Service Fabric, veja [agregação de eventos do Azure Service Fabric com o EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md).

### <a name="using-structured-eventsource-events"></a>O uso de eventos de EventSource estruturados

Definir mensagem caso de utilização de eventos por permite-lhe dados de pacote sobre o evento, no contexto do evento. Pode procurar mais facilmente e filtro com base nos nomes ou valores das propriedades de evento especificado. Estruturar o faz de saída de instrumentação mais fácil de ler, mas requer mais tempo e idéias para definir um evento para cada caso de utilização. 

Algumas definições de eventos podem ser partilhadas em toda a aplicação. Por exemplo, um início de método ou parar eventos teriam de ser reutilizados em muitos serviços dentro de um aplicativo. Um serviço de domínio específico, como um sistema de pedidos, poderá ter uma **CreateOrder** evento, que tem seu próprio evento único. Esta abordagem poderá gerar muitos eventos e potencialmente requerem a coordenação de identificadores de entre as equipes de projeto. 

```csharp
[EventSource(Name = "MyCompany-VotingState-VotingStateService")]
internal sealed class ServiceEventSource : EventSource
{
    public static readonly ServiceEventSource Current = new ServiceEventSource();

    // The instance constructor is private to enforce singleton semantics.
    private ServiceEventSource() : base() { }

    ...

    // The ServiceTypeRegistered event contains a unique identifier, an event attribute that defined the event, and the code implementation of the event.
    private const int ServiceTypeRegisteredEventId = 3;
    [Event(ServiceTypeRegisteredEventId, Level = EventLevel.Informational, Message = "Service host process {0} registered service type {1}", Keywords = Keywords.ServiceInitialization)]
    public void ServiceTypeRegistered(int hostProcessId, string serviceType)
    {
        WriteEvent(ServiceTypeRegisteredEventId, hostProcessId, serviceType);
    }

    // The ServiceHostInitializationFailed event contains a unique identifier, an event attribute that defined the event, and the code implementation of the event.
    private const int ServiceHostInitializationFailedEventId = 4;
    [Event(ServiceHostInitializationFailedEventId, Level = EventLevel.Error, Message = "Service host initialization failed", Keywords = Keywords.ServiceInitialization)]
    public void ServiceHostInitializationFailed(string exception)
    {
        WriteEvent(ServiceHostInitializationFailedEventId, exception);
    }

    ...

```

### <a name="using-eventsource-generically"></a>Usando EventSource genericamente

Como definir eventos específicos pode ser difícil, muitas pessoas definem alguns eventos com um conjunto comum de parâmetros que em geral, as informações como uma cadeia de saída. Grande parte o aspecto estruturado é perdido e é mais difícil procurar e filtrar os resultados. Nesta abordagem, alguns eventos que, normalmente, correspondem aos níveis de registo são definidos. O fragmento seguinte define uma mensagem de depuração e o erro:

```csharp
[EventSource(Name = "MyCompany-VotingState-VotingStateService")]
internal sealed class ServiceEventSource : EventSource
{
    public static readonly ServiceEventSource Current = new ServiceEventSource();

    // The Instance constructor is private, to enforce singleton semantics.
    private ServiceEventSource() : base() { }

    ...

    private const int DebugEventId = 10;
    [Event(DebugEventId, Level = EventLevel.Verbose, Message = "{0}")]
    public void Debug(string msg)
    {
        WriteEvent(DebugEventId, msg);
    }

    private const int ErrorEventId = 11;
    [Event(ErrorEventId, Level = EventLevel.Error, Message = "Error: {0} - {1}")]
    public void Error(string error, string msg)
    {
        WriteEvent(ErrorEventId, error, msg);
    }

    ...

```

Usar uma mistura de instrumentação estruturada e genérica também pode funcionar bem. Instrumentação estruturada é utilizada para relatórios de erros e de métricas. Eventos genéricos podem ser utilizados para o registo detalhado que é consumido pelos engenheiros para resolução de problemas.

## <a name="microsoftextensionslogging"></a>Microsoft.Extensions.Logging

O registo do ASP.NET Core ([pacote Microsoft.Extensions.Logging NuGet](https://www.nuget.org/packages/Microsoft.Extensions.Logging)) é uma arquitetura de registo que fornece uma API de log padrão para a sua aplicação. Suporte para outros back-ends de Registro em log pode ser conectado ao registo do ASP.NET Core. Isto dá-lhe uma grande variedade de suporte para iniciar sessão na sua aplicação é processada, sem ter de alterar muito código.

1. Adicionar a **Microsoft.Extensions.Logging** pacote NuGet ao projeto pretende instrumento. Além disso, adicione quaisquer pacotes de fornecedor. Para obter mais informações, consulte [iniciar sessão no ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging).
2. Adicionar uma **usando** diretiva para **Microsoft.Extensions.Logging** ao seu ficheiro de serviço.
3. Defina uma variável privada na sua classe de serviço.

   ```csharp
   private ILogger _logger = null;
   ```

4. No construtor da sua classe de serviço, adicione este código:

   ```csharp
   _logger = new LoggerFactory().CreateLogger<Stateless>();
   ```

5. Inicie instrumentar o seu código em seus métodos. Aqui estão alguns exemplos:

   ```csharp
   _logger.LogDebug("Debug-level event from Microsoft.Logging");
   _logger.LogInformation("Informational-level event from Microsoft.Logging");

   // In this variant, we're adding structured properties RequestName and Duration, which have values MyRequest and the duration of the request.
   // Later in the article, we discuss why this step is useful.
   _logger.LogInformation("{RequestName} {Duration}", "MyRequest", requestDuration);
   ```

### <a name="using-other-logging-providers"></a>Utilizar outros fornecedores de serviços de registo

Alguns fornecedores terceiros de utilizar a abordagem descrita na secção anterior, incluindo [Serilog](https://serilog.net/), [NLog](https://nlog-project.org/), e [Loggr](https://github.com/imobile3/Loggr.Extensions.Logging). Pode conectar a cada uma delas em log do ASP.NET Core, ou pode usá-los separadamente. Serilog tem uma funcionalidade que otimiza a todas as mensagens enviadas a partir de um agente de log. Esta funcionalidade pode ser útil para o nome do serviço, tipo e informações da partição de saída. Para utilizar esta capacidade na infra-estrutura do ASP.NET Core, siga estes passos:

1. Adicionar a **Serilog**, **Serilog.Extensions.Logging**, **Serilog.Sinks.Literate**, e **Serilog.Sinks.Observable** pacotes NuGet para o projeto. 
2. Criar um `LoggerConfiguration` e a instância de agente de log.

   ```csharp
   Log.Logger = new LoggerConfiguration().WriteTo.LiterateConsole().CreateLogger();
   ```

3. Adicionar um `Serilog.ILogger` argumento para o construtor de serviço e vamos passar o agente de log criado recentemente.

   ```csharp
   ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
   ```

4. No construtor do serviço, cria enrichers de propriedade para **ServiceTypeName**, **ServiceName**, **PartitionId**, e **InstanceId** .

   ```csharp
   public Stateless(StatelessServiceContext context, Serilog.ILogger serilog)
       : base(context)
   {
       PropertyEnricher[] properties = new PropertyEnricher[]
       {
           new PropertyEnricher("ServiceTypeName", context.ServiceTypeName),
           new PropertyEnricher("ServiceName", context.ServiceName),
           new PropertyEnricher("PartitionId", context.PartitionId),
           new PropertyEnricher("InstanceId", context.ReplicaOrInstanceId),
       };

       serilog.ForContext(properties);

       _logger = new LoggerFactory().AddSerilog(serilog.ForContext(properties)).CreateLogger<Stateless>();
   }
   ```

5. Instrumente o código o mesmo como se estivesse usando o ASP.NET Core sem Serilog.

   >[!NOTE]
   >Recomendamos que *não* utilizar estáticas `Log.Logger` com o exemplo anterior. Service Fabric pode alojar várias instâncias do mesmo tipo de serviço dentro de um único processo. Se usar o estático `Log.Logger`, ao último escritor de enrichers a propriedade irá mostrar os valores para todas as instâncias em execução. Este é um motivo por que a variável Logger é uma variável de membro particular da classe de serviço. Além disso, deve fazer o `_logger` disponíveis para código comum, que pode ser utilizado em todos os serviços.

## <a name="next-steps"></a>Passos Seguintes

- Leia mais informações sobre [aplicação de monitorização no Service Fabric](service-fabric-diagnostics-event-generation-app.md).
- Leia sobre o registo com [o EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) e [Windows Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md).










