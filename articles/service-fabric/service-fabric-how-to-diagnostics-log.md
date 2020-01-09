---
title: Gerar eventos de log de um aplicativo .NET
description: Saiba mais sobre como adicionar o log ao seu aplicativo .NET Service Fabric hospedado em um cluster do Azure ou em um cluster autônomo.
author: srrengar
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: srrengar
ms.openlocfilehash: 8c4721584e74bd7f7111c516f2d16bd190392bb5
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75614371"
---
# <a name="add-logging-to-your-service-fabric-application"></a>Adicionar registro em log ao seu aplicativo Service Fabric

Seu aplicativo deve fornecer informações suficientes para depurá-lo de forma forense quando surgirem problemas. O registro em log é uma das coisas mais importantes que você pode adicionar ao seu aplicativo Service Fabric. Quando ocorre uma falha, um bom registro em log pode fornecer uma maneira de investigar falhas. Analisando os padrões de log, você pode encontrar maneiras de melhorar o desempenho ou o design do seu aplicativo. Este documento demonstra algumas opções de log diferentes.

## <a name="eventflow"></a>EventFlow

O pacote de [biblioteca EventFlow](https://github.com/Azure/diagnostics-eventflow) permite que os aplicativos definam quais dados de diagnóstico coletar e para onde eles devem ser enviados. Os dados de diagnóstico podem ser qualquer coisa, desde contadores de desempenho até rastreamentos de aplicativo. Ele é executado no mesmo processo que o aplicativo, portanto, a sobrecarga de comunicação é minimizada. Para obter mais informações sobre EventFlow e Service Fabric, consulte [agregação de eventos de Service Fabric do Azure com EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md).

### <a name="using-structured-eventsource-events"></a>Usando eventos de EventSource estruturados

Definir eventos de mensagem por caso de uso permite empacotar dados sobre o evento no contexto do evento. Você pode pesquisar e filtrar com mais facilidade com base nos nomes ou valores das propriedades do evento especificado. Estruturar a saída de instrumentação facilita a leitura, mas exige mais idéias e mais tempo para definir um evento para cada caso de uso. 

Algumas definições de evento podem ser compartilhadas em todo o aplicativo. Por exemplo, um evento de início ou de parada do método seria reutilizado em vários serviços dentro de um aplicativo. Um serviço específico de domínio, como um sistema de pedidos, pode ter um evento **CreateOrder** , que tem seu próprio evento exclusivo. Essa abordagem pode gerar muitos eventos e potencialmente exigir coordenação de identificadores em equipes de projeto. 

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

Como a definição de eventos específicos pode ser difícil, muitas pessoas definem alguns eventos com um conjunto comum de parâmetros que geralmente geram suas informações como uma cadeia de caracteres. Grande parte do aspecto estruturado é perdida e é mais difícil Pesquisar e filtrar os resultados. Nessa abordagem, alguns eventos que geralmente correspondem aos níveis de log são definidos. O trecho a seguir define uma mensagem de erro e de depuração:

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

O uso de uma instrumentação híbrida estruturada e genérica também pode funcionar bem. A instrumentação estruturada é usada para relatar erros e métricas. Eventos genéricos podem ser usados para o log detalhado que é consumido por engenheiros para solução de problemas.

## <a name="microsoftextensionslogging"></a>Microsoft.Extensions.Logging

O log de ASP.NET Core ([Microsoft. Extensions. log NuGet Package](https://www.nuget.org/packages/Microsoft.Extensions.Logging)) é uma estrutura de log que fornece uma API de log padrão para seu aplicativo. O suporte para outros back-ends de log pode ser conectado ao log de ASP.NET Core. Isso lhe dá uma ampla variedade de suporte para registro em log em seu aplicativo, sem precisar alterar muito código.

1. Adicione o pacote NuGet **Microsoft. Extensions. Logging** ao projeto que você deseja instrumentar. Além disso, adicione qualquer pacote de provedor. Para obter mais informações, consulte [Logging in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging).
2. Adicione uma diretiva **using** para **Microsoft. Extensions. Logging** ao seu arquivo de serviço.
3. Defina uma variável particular dentro de sua classe de serviço.

   ```csharp
   private ILogger _logger = null;
   ```

4. No construtor de sua classe de serviço, adicione este código:

   ```csharp
   _logger = new LoggerFactory().CreateLogger<Stateless>();
   ```

5. Comece a instrumentar seu código em seus métodos. Aqui estão alguns exemplos:

   ```csharp
   _logger.LogDebug("Debug-level event from Microsoft.Logging");
   _logger.LogInformation("Informational-level event from Microsoft.Logging");

   // In this variant, we're adding structured properties RequestName and Duration, which have values MyRequest and the duration of the request.
   // Later in the article, we discuss why this step is useful.
   _logger.LogInformation("{RequestName} {Duration}", "MyRequest", requestDuration);
   ```

### <a name="using-other-logging-providers"></a>Usando outros provedores de log

Alguns provedores de terceiros usam a abordagem descrita na seção anterior, incluindo [Serilog](https://serilog.net/), [NLog](https://nlog-project.org/)e [Loggr](https://github.com/imobile3/Loggr.Extensions.Logging). Você pode conectar cada um deles em ASP.NET Core log ou pode usá-los separadamente. O Serilog tem um recurso que enriquece todas as mensagens enviadas de um agente. Esse recurso pode ser útil para gerar o nome do serviço, o tipo e as informações de partição. Para usar essa funcionalidade na infraestrutura de ASP.NET Core, siga estas etapas:

1. Adicione os pacotes NuGet **Serilog**, **Serilog. Extensions. log**, **Serilog. Sinks.** e **Serilog. Sinks. observáveis** ao projeto. 
2. Crie um `LoggerConfiguration` e a instância do agente.

   ```csharp
   Log.Logger = new LoggerConfiguration().WriteTo.LiterateConsole().CreateLogger();
   ```

3. Adicione um argumento `Serilog.ILogger` ao construtor de serviço e passe o agente recém-criado.

   ```csharp
   ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
   ```

4. No construtor de serviço, cria os aprimoradores de propriedade para o Service **TypeName**, o **ServiceName**, o **PartitionID**e a **InstanceId**.

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

5. Instrumente o código como se estivesse usando ASP.NET Core sem Serilog.

   >[!NOTE]
   >Recomendamos que você *não* use o `Log.Logger` estático com o exemplo anterior. Service Fabric pode hospedar várias instâncias do mesmo tipo de serviço em um único processo. Se você usar o `Log.Logger`estático, o último gravador dos aprimoradores de propriedade mostrará valores para todas as instâncias em execução. Esse é um dos motivos pelos quais a variável _logger é uma variável de membro particular da classe de serviço. Além disso, você deve tornar o `_logger` disponível para o código comum, que pode ser usado em todos os serviços.

## <a name="next-steps"></a>Passos seguintes

- Leia mais informações sobre [monitoramento de aplicativos no Service Fabric](service-fabric-diagnostics-event-generation-app.md).
- Leia sobre registro em log com [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) e [Windows diagnóstico do Azure](service-fabric-diagnostics-event-aggregation-wad.md).










