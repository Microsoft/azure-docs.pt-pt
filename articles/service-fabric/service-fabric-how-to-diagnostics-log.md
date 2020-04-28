---
title: Gerar eventos de log a partir de uma aplicação .NET
description: Saiba como adicionar a sessão de registo à sua aplicação .NET Service Fabric alojada num cluster Azure ou num cluster autónomo.
author: srrengar
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: srrengar
ms.openlocfilehash: 8c4721584e74bd7f7111c516f2d16bd190392bb5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75614371"
---
# <a name="add-logging-to-your-service-fabric-application"></a>Adicionar registos à sua aplicação do Service Fabric

A sua aplicação deve fornecer informações suficientes para depura-la forense quando surgirem problemas. A exploração madeireira é uma das coisas mais importantes que pode adicionar à sua aplicação Service Fabric. Quando ocorre uma falha, uma boa exploração madeireira pode dar-lhe uma forma de investigar falhas. Ao analisar padrões de registo, poderá encontrar formas de melhorar o desempenho ou o design da sua aplicação. Este documento demonstra algumas opções de exploração madeireira diferentes.

## <a name="eventflow"></a>Fluxo de Eventos

A suíte [da biblioteca EventFlow](https://github.com/Azure/diagnostics-eventflow) permite que as aplicações definam quais os dados de diagnóstico a recolher e para onde devem ser saídas. Os dados de diagnóstico podem ser qualquer coisa, desde contadores de desempenho a vestígios de aplicações. Funciona no mesmo processo que a aplicação, pelo que a comunicação é minimizada. Para mais informações sobre o EventFlow e o Tecido de Serviço, consulte a agregação de eventos de tecido de [serviço Azure com EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md).

### <a name="using-structured-eventsource-events"></a>Usando eventos estruturados EventSource

Definir eventos de mensagens por caso de utilização permite-lhe embalar dados sobre o evento, no contexto do evento. Pode pesquisar e filtrar mais facilmente com base nos nomes ou valores das propriedades especificadas do evento. Estruturar a saída de instrumentação facilita a leitura, mas requer mais pensamento e tempo para definir um evento para cada caso de utilização. 

Algumas definições de eventopodem ser partilhadas em toda a aplicação. Por exemplo, um evento de início ou paragem de métodos seria reutilizado em muitos serviços dentro de uma aplicação. Um serviço específico de domínio, como um sistema de encomendas, pode ter um evento **CreateOrder,** que tem o seu próprio evento único. Esta abordagem pode gerar muitos eventos, e potencialmente exigir coordenação de identificadores entre equipas de projeto. 

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

### <a name="using-eventsource-generically"></a>Usando o EventSource genericamente

Como definir eventos específicos pode ser difícil, muitas pessoas definem alguns eventos com um conjunto comum de parâmetros que geralmente produzem a sua informação como uma corda. Grande parte do aspeto estruturado perde-se, e é mais difícil pesquisar e filtrar os resultados. Nesta abordagem, são definidos alguns eventos que normalmente correspondem aos níveis de exploração madeireira. O seguinte corte define uma mensagem de depuração e erro:

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

A utilização de um híbrido de instrumentação estruturada e genérica também pode funcionar bem. A instrumentação estruturada é utilizada para reportar erros e métricas. Eventos genéricos podem ser usados para a exploração madeireira detalhada que é consumida por engenheiros para resolução de problemas.

## <a name="microsoftextensionslogging"></a>Microsoft.Extensions.Logging

O ASP.NET core logging[(Microsoft.Extensions.Logging NuGet package](https://www.nuget.org/packages/Microsoft.Extensions.Logging)) é uma estrutura de registo que fornece uma API de registo padrão para a sua aplicação. O suporte para outras extremidades de exploração pode ser ligado ao ASP.NET registo do Núcleo. Isto dá-lhe uma grande variedade de suporte para o login na sua aplicação é processado, sem ter que alterar muito código.

1. Adicione o pacote **Microsoft.Extensions.Logging** NuGet ao projeto que pretende instrumentar. Além disso, adicione quaisquer pacotes de fornecedores. Para mais informações, consulte [o Loglogging no núcleo ASP.NET](https://docs.microsoft.com/aspnet/core/fundamentals/logging).
2. Adicione uma diretiva **de utilização** para **Microsoft.Extensions.Logging** no seu ficheiro de serviço.
3. Defina uma variável privada dentro da sua classe de serviço.

   ```csharp
   private ILogger _logger = null;
   ```

4. No construtor da sua classe de serviço, adicione este código:

   ```csharp
   _logger = new LoggerFactory().CreateLogger<Stateless>();
   ```

5. Comece a instrumentar o seu código nos seus métodos. Aqui estão algumas amostras:

   ```csharp
   _logger.LogDebug("Debug-level event from Microsoft.Logging");
   _logger.LogInformation("Informational-level event from Microsoft.Logging");

   // In this variant, we're adding structured properties RequestName and Duration, which have values MyRequest and the duration of the request.
   // Later in the article, we discuss why this step is useful.
   _logger.LogInformation("{RequestName} {Duration}", "MyRequest", requestDuration);
   ```

### <a name="using-other-logging-providers"></a>Utilização de outros fornecedores de exploração madeireira

Alguns fornecedores de terceiros utilizam a abordagem descrita na secção anterior, incluindo [Serilog,](https://serilog.net/) [NLog](https://nlog-project.org/)e [Loggr](https://github.com/imobile3/Loggr.Extensions.Logging). Pode ligar cada um destes a ASP.NET registo core, ou pode usá-los separadamente. O Serilog tem uma funcionalidade que enriquece todas as mensagens enviadas de um madeireiro. Esta funcionalidade pode ser útil para obter o nome, tipo e informação de partição do serviço. Para utilizar esta capacidade na infraestrutura ASP.NET Core, faça estes passos:

1. Adicione os pacotes **Serilog**, **Serilog.Extensions.Logging,** **Serilog.Sinks.Literate**e **Serilog.Sinks.Observable** NuGet pacotes ao projeto. 
2. Crie `LoggerConfiguration` uma e a instância de madeireiro.

   ```csharp
   Log.Logger = new LoggerConfiguration().WriteTo.LiterateConsole().CreateLogger();
   ```

3. Adicione `Serilog.ILogger` um argumento ao construtor de serviços, e passe o recém-criado madeiro.

   ```csharp
   ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
   ```

4. No construtor de serviços, cria enriquecedores de propriedades para **ServiceTypeName**, **ServiceName**, **PartitionId**, e **InstanceId**.

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

5. Instrumente o código como se estivesse a usar ASP.NET Core sem Serilog.

   >[!NOTE]
   >Recomendamos que *não* utilize a `Log.Logger` estática com o exemplo anterior. O Serviço Tecido pode acolher várias instâncias do mesmo tipo de serviço num único processo. Se utilizar a `Log.Logger`estática, o último escritor dos enriquecedores de propriedades mostrará valores para todos os casos que estão em execução. Esta é uma das razões pelas quais a variável _logger é uma variável membro privado da classe de serviço. Além disso, `_logger` deve disponibilizar o código comum, que pode ser utilizado em todos os serviços.

## <a name="next-steps"></a>Passos seguintes

- Leia mais informações sobre monitorização de [aplicações em Tecido de Serviço](service-fabric-diagnostics-event-generation-app.md).
- Leia sobre o registo com [o EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) e [o Windows Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md).










