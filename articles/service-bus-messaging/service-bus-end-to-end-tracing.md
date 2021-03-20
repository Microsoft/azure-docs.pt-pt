---
title: Azure Service Bus rastreio e diagnósticos | Microsoft Docs
description: Visão geral dos diagnósticos de clientes do Service Bus e rastreio de ponta a ponta (cliente através de todos os serviços envolvidos no processamento.)
ms.topic: article
ms.date: 02/03/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 19b284aceb83fbbc2bcf662b2b58941e6a5b36f9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99539218"
---
# <a name="distributed-tracing-and-correlation-through-service-bus-messaging"></a>Rastreio distribuído e correlação através de mensagens de Bus de Serviço

Um dos problemas comuns no desenvolvimento de micro serviços é a capacidade de rastrear a operação de um cliente através de todos os serviços que estão envolvidos no processamento. É útil para depurar, análise de desempenho, testes A/B e outros cenários típicos de diagnóstico.
Uma parte deste problema é rastrear peças lógicas de trabalho. Inclui o resultado do processamento de mensagens e as chamadas de latência e dependência externa. Outra parte é a correlação destes eventos de diagnóstico para além dos limites do processo.

Quando um produtor envia uma mensagem através de uma fila, normalmente acontece no âmbito de outra operação lógica, iniciada por algum outro cliente ou serviço. A mesma operação é continuada pelo consumidor assim que recebe uma mensagem. Tanto o produtor como o consumidor (e outros serviços que processam a operação), presumivelmente emitem eventos de telemetria para rastrear o fluxo de operação e o resultado. Para correlacionar tais eventos e rastrear a operação de ponta a ponta, cada serviço que reporte telemetria tem de carimbar todos os eventos com um contexto de traço.

A microsoft Azure Service Bus as mensagens definiram propriedades de carga útil que os produtores e consumidores devem usar para passar esse contexto de traço.
O protocolo baseia-se no [protocolo HTTP Correlation](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md).

# <a name="azuremessagingservicebus-sdk-latest"></a>[Azure.messaging.ServiceBus SDK (mais recente)](#tab/net-standard-sdk-2)
| Nome da Propriedade        | Description                                                 |
|----------------------|-------------------------------------------------------------|
|  Diagnostic-Id       | Identificador único de uma chamada externa do produtor para a fila. Consulte o [Pedido-Id no protocolo HTTP](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#request-id) para a lógica, considerações e formato |

## <a name="service-bus-net-client-autotracing"></a>Service Bus .NET Cliente autotracing
A `ServiceBusProcessor` classe do cliente [Azure Messaging Service Bus para .NET](/dotnet/api/azure.messaging.servicebus.servicebusprocessor) fornece pontos de instrumentação de rastreio que podem ser ligados por sistemas de rastreio ou pedaço de código do cliente. A instrumentação permite rastrear todas as chamadas para o serviço de mensagens Service Bus do lado do cliente. Se o processamento de mensagens for feito utilizando (padrão de manipulador [ `ProcessMessageAsync` de `ServiceBusProcessor` ](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processmessageasync) mensagens), o processamento da mensagem também é instrumentado.

### <a name="tracking-with-azure-application-insights"></a>Rastreio com insights de aplicação Azure

[O Microsoft Application Insights](https://azure.microsoft.com/services/application-insights/) fornece capacidades de monitorização de desempenho ricas, incluindo pedidos autómicos e rastreio de dependência.

Dependendo do seu tipo de projeto, instale a Application Insights SDK:
- [ASP.NET](../azure-monitor/app/asp-net.md) - instale a versão 2.5-beta2 ou superior
- [ASP.NET Core](../azure-monitor/app/asp-net-core.md) - instale a versão 2.2.0-beta2 ou superior.
Estas ligações fornecem detalhes sobre a instalação do SDK, a criação de recursos e a configuração da SDK (se necessário). Para non-ASP.NET aplicações, consulte o artigo [Azure Application Insights for Console Applications.](../azure-monitor/app/console.md)

Se utilizar (padrão de manipulador [ `ProcessMessageAsync` de `ServiceBusProcessor` ](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processmessageasync) mensagens) para processar mensagens, o processamento da mensagem também é instrumentado. Todas as chamadas de Service Bus efetuadas pelo seu serviço são automaticamente rastreadas e correlacionadas com outros itens de telemetria. Caso contrário, consulte o seguinte exemplo para o rastreio manual de processamento de mensagens.

#### <a name="trace-message-processing"></a>Processamento de mensagens de rastreio

```csharp
async Task ProcessAsync(ProcessMessageEventArgs args)
{
    ServiceBusReceivedMessage message = args.Message;
    if (message.ApplicationProperties.TryGetValue("Diagnostic-Id", out var objectId) && objectId is string diagnosticId)
    {
        var activity = new Activity("ServiceBusProcessor.ProcessMessage");
        activity.SetParentId(diagnosticId);
        // If you're using Microsoft.ApplicationInsights package version 2.6-beta or higher, you should call StartOperation<RequestTelemetry>(activity) instead
        using (var operation = telemetryClient.StartOperation<RequestTelemetry>("Process", activity.RootId, activity.ParentId))
        {
            telemetryClient.TrackTrace("Received message");
            try 
            {
            // process message
            }
            catch (Exception ex)
            {
                telemetryClient.TrackException(ex);
                operation.Telemetry.Success = false;
                throw;
            }

            telemetryClient.TrackTrace("Done");
        }
    }
}
```

Neste exemplo, é reportado o pedido de telemetria para cada mensagem processada, com um tempotalho, duração e resultado (sucesso). A telemetria também tem um conjunto de propriedades de correlação. Vestígios aninhados e exceções relatados durante o processamento de mensagens são também carimbados com propriedades de correlação que os representam como "crianças" do `RequestTelemetry` .

Caso escdaize chamadas para componentes externos suportados durante o processamento de mensagens, também são automaticamente rastreados e correlacionados. Consulte [as operações personalizadas de rastreio com Application Insights .NET SDK](../azure-monitor/app/custom-operations-tracking.md) para rastreio manual e correlação.

Se estiver a executar algum código externo para além do SDK Application Insights, espere ver uma **duração** mais longa ao visualizar os registos de Insights de Aplicação. 

![Duração mais longa no registo de Insights de Aplicação](./media/service-bus-end-to-end-tracing/longer-duration.png)

Não significa que houve um atraso em receber a mensagem. Neste cenário, a mensagem já foi recebida desde que a mensagem é transmitida como um parâmetro para o código SDK. E a etiqueta **de nome** nos registos app Insights **(Processo)** indica que a mensagem está agora a ser processada pelo seu código de processamento de eventos externo. Esta questão não tem a ver com o Azure. Em vez disso, estas métricas referem-se à eficiência do seu código externo, dado que a mensagem já foi recebida da Service Bus. 

### <a name="tracking-without-tracing-system"></a>Rastreio sem sistema de rastreio
Caso o seu sistema de rastreio não suporte chamadas automáticas de Service Bus, poderá estar a tentar adicionar esse suporte a um sistema de rastreio ou à sua aplicação. Esta secção descreve eventos de diagnóstico enviados pelo cliente Service Bus .NET.  

Service Bus .NET Client é instrumentado utilizando sistema primitivos de rastreio [.NET.Diagnósticos.Atividade](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) e [Sistema.Diagnósticos.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md).

`Activity` serve como um contexto de traço enquanto `DiagnosticSource` é um mecanismo de notificação. 

Se não houver ouvinte para os eventos DiagnosticSource, a instrumentação está desligada, mantendo zero custos de instrumentação. O DiagnosticSource dá todo o controlo ao ouvinte:
- controlos ouvintes que fontes e eventos para ouvir
- ouvinte controla taxa de evento e amostragem
- os eventos são enviados com uma carga útil que fornece todo o contexto para que possa aceder e modificar o objeto de mensagem durante o evento

Familiarize-se com [o Guia do Utilizador DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) antes de prosseguir com a implementação.

Vamos criar um ouvinte para eventos de Service Bus em ASP.NET aplicação Core que escreve registos com microsoft.Extension.Logger.
Utiliza a biblioteca [System.Reative.Core](https://www.nuget.org/packages/System.Reactive.Core) para subscrever o DiagnosticSource (também é fácil subscrever o DiagnosticSource sem ele)

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory factory, IApplicationLifetime applicationLifetime)
{
    // configuration...

    var serviceBusLogger = factory.CreateLogger("Azure.Messaging.ServiceBus");

    IDisposable innerSubscription = null;
    IDisposable outerSubscription = DiagnosticListener.AllListeners.Subscribe(delegate (DiagnosticListener listener)
    {
        // subscribe to the Service Bus DiagnosticSource
        if (listener.Name == "Azure.Messaging.ServiceBus")
        {
            // receive event from Service Bus DiagnosticSource
            innerSubscription = listener.Subscribe(delegate (KeyValuePair<string, object> evnt)
            {
                // Log operation details once it's done
                if (evnt.Key.EndsWith("Stop"))
                {
                    Activity currentActivity = Activity.Current;
                    serviceBusLogger.LogInformation($"Operation {currentActivity.OperationName} is finished, Duration={currentActivity.Duration}, Id={currentActivity.Id}, StartTime={currentActivity.StartTimeUtc}");
                }
            });
        }
    });

    applicationLifetime.ApplicationStopping.Register(() =>
    {
        outerSubscription?.Dispose();
        innerSubscription?.Dispose();
    });
}
```

Neste exemplo, o ouvinte regista a duração, o resultado, o identificador único e a hora de início de cada operação do Service Bus.

### <a name="events"></a>Eventos
Todos os eventos terão as seguintes propriedades que estejam em conformidade com a especificação de telemetria aberta: https://github.com/open-telemetry/opentelemetry-specification/blob/master/specification/trace/api.md .

- `message_bus.destination` – fila/tópico/caminho de subscrição
- `peer.address` – espaço de nome totalmente qualificado
- `kind` – produtor, consumidor ou cliente. O produtor é utilizado no envio de mensagens, consumidor ao receber e cliente quando se acomoda.
- `component` – `servicebus`

Todos os eventos também têm propriedades 'Entidade' e 'Endpoint', são omitidos em abaixo da tabela
  * `string Entity` - - Nome da entidade (fila, tópico, e assim por diante.)
  * `Uri Endpoint` - Service Bus endpoint URL

### <a name="instrumented-operations"></a>Operações instrumentadas
Aqui está a lista completa de operações instrumentadas:

| Nome da Operação | API rastreado |
| -------------- | ----------- | 
| ServiceBusSender.Send | ServiceBusSender.SendMessageAsync<br/>ServiceBusSender.SendMessagesAsync |
| ServiceBusSender.Schedule | ServiceBusSender.ScheduleMessageAsync<br/>ServiceBusSender.ScheduleMessagesAsync | 
| ServiceBusSender.Cancela | ServiceBusSender.CancelScheduledMessageAsync<br/>ServiceBusSender.CancelScheduledMessAsync |
| ServiceBusReceiver.Receive | ServiceBusReceiver.ReceiveMessageAsync<br/>ServiceBusReceiver.ReceiveMessagesAsync |
| ServiceBusReceiver.ReceiveDefer | ServiceBusReceiver.ReceiveDeferredMessagesAsync |
| ServiceBusReceiver.Peek | ServiceBusReceiver.PeekMessageAsync<br/>ServiceBusReceiver.PeekMessagesAsync |
| ServiceBusReceiver.Abandone | ServiceBusReceiver.AbandonMessagesAsync |
| ServiceBusReceiver.Complete | ServiceBusReceiver.CompleteMessagesAsync |
| ServiceBusReceiver.DeadLetter | ServiceBusReceiver.DeadLetterMessagesAsync |
| ServiceBusReceiver.Diferir |  ServiceBusReceiver.DeferMessagesAsync |
| ServiceBusReceiver.RenewMessageLock | ServiceBusReceiver.RenewMessageLockAsync |
| ServiceBusSessionReceiver.RenewSessionLock | ServiceBusSessionReceiver.RenewSessionLockAsync |
| ServiceBusSessionReceiver.GetSessionState | ServiceBusSessionReceiver.GetSessionStateAsync |
| ServiceBusSessionReceiver.SetSessionState | ServiceBusSessionReceiver.SetSessionStateAsync |
| ServiceBusProcessor.ProcessMessage | Conjunto de chamada de processador no ServiceBusProcessor. Propriedade ProcessMessageAsync |
| ServiceBusSessionProcessor.ProcessSessionMessage | Conjunto de chamada de processador no ServiceBusSessionProcessor. Propriedade ProcessMessageAsync |

### <a name="filtering-and-sampling"></a>Filtragem e amostragem

Em alguns casos, é desejável registar apenas uma parte dos eventos para reduzir o desempenho ou o consumo de armazenamento. Pode registar apenas eventos 'Stop' (como em exemplo anterior) ou percentagem de amostra dos eventos. 
`DiagnosticSource` fornecer maneira de alcançá-lo com `IsEnabled` predicado. Para obter mais informações, consulte [a filtragem baseada em contexto em DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#context-based-filtering).

`IsEnabled` pode ser chamado várias vezes para uma única operação para minimizar o impacto do desempenho.

`IsEnabled` é chamada na seguinte sequência:

1. `IsEnabled(<OperationName>, string entity, null)` por exemplo, `IsEnabled("ServiceBusSender.Send", "MyQueue1")` . . Note que não há "Iniciar" ou "Stop" no final. Utilize-o para filtrar determinadas operações ou filas. Se o método de retorno `false` voltar, os eventos para a operação não são enviados.

   * Para as operações de 'Processo' e 'ProcessoSession', também `IsEnabled(<OperationName>, string entity, Activity activity)` recebes chamadas de retorno. Utilize-o para filtrar eventos baseados em `activity.Id` propriedades ou Tags.
  
2. `IsEnabled(<OperationName>.Start)` por exemplo, `IsEnabled("ServiceBusSender.Send.Start")` . . Verifique se o evento "Iniciar" deve ser disparado. O resultado apenas afeta o evento 'Iniciar', mas a instrumentação adicional não depende dele.

Não há `IsEnabled` nada para o evento "Stop".

Se algum resultado de operação for exceção, `IsEnabled("ServiceBusSender.Send.Exception")` é chamado. Só pode subscrever eventos de "Exceção" e impedir o resto da instrumentação. Neste caso, ainda tens de lidar com tais exceções. Uma vez que outras instrumentações são desativadas, não deve esperar que o contexto do traço flua com as mensagens de consumidor para produtor.

Também pode utilizar `IsEnabled` estratégias de amostragem. A amostragem com base na `Activity.Id` ou `Activity.RootId` garante uma amostragem consistente em todos os pneus (desde que seja propagada pelo sistema de rastreio ou pelo seu próprio código).

Na presença de vários `DiagnosticSource` ouvintes para a mesma fonte, é suficiente para apenas um ouvinte aceitar o evento, por isso não há garantias que `IsEnabled` seja chamada.



# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

| Nome da Propriedade        | Description                                                 |
|----------------------|-------------------------------------------------------------|
|  Diagnostic-Id       | Identificador único de uma chamada externa do produtor para a fila. Consulte o [Pedido-Id no protocolo HTTP](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#request-id) para a lógica, considerações e formato |
|  Correlation-Context | Contexto de operação, que é propagado em todos os serviços envolvidos no processamento de operações. Para mais informações, consulte [Correlation-Context no protocolo HTTP](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#correlation-context) |

## <a name="service-bus-net-client-autotracing"></a>Service Bus .NET Cliente autotracing

A partir da versão 3.0.0 [o Microsoft Azure ServiceBus Client para .NET](/dotnet/api/microsoft.azure.servicebus.queueclient) fornece pontos de instrumentação de rastreamento que podem ser ligados por sistemas de rastreio ou peça de código do cliente.
A instrumentação permite rastrear todas as chamadas para o serviço de mensagens Service Bus do lado do cliente. Se o processamento de mensagens for feito com o [padrão de manipulador de mensagens,](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler)o processamento de mensagens também é instrumentado

### <a name="tracking-with-azure-application-insights"></a>Rastreio com insights de aplicação Azure

[O Microsoft Application Insights](https://azure.microsoft.com/services/application-insights/) fornece capacidades de monitorização de desempenho ricas, incluindo pedidos autómicos e rastreio de dependência.

Dependendo do seu tipo de projeto, instale a Application Insights SDK:
- [ASP.NET](../azure-monitor/app/asp-net.md) - instale a versão 2.5-beta2 ou superior
- [ASP.NET Core](../azure-monitor/app/asp-net-core.md) - instale a versão 2.2.0-beta2 ou superior.
Estas ligações fornecem detalhes sobre a instalação do SDK, a criação de recursos e a configuração da SDK (se necessário). Para non-ASP.NET aplicações, consulte o artigo [Azure Application Insights for Console Applications.](../azure-monitor/app/console.md)

Se utilizar o [padrão do manipulador de mensagens](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler) para processar mensagens, está feito: todas as chamadas de Service Bus efetuadas pelo seu serviço são automaticamente rastreadas e correlacionadas com outros itens de telemetria. Caso contrário, consulte o seguinte exemplo para o rastreio manual de processamento de mensagens.

#### <a name="trace-message-processing"></a>Processamento de mensagens de rastreio

```csharp
private readonly TelemetryClient telemetryClient;

async Task ProcessAsync(Message message)
{
    var activity = message.ExtractActivity();
    
    // If you're using Microsoft.ApplicationInsights package version 2.6-beta or higher, you should call StartOperation<RequestTelemetry>(activity) instead
    using (var operation = telemetryClient.StartOperation<RequestTelemetry>("Process", activity.RootId, activity.ParentId))
    {
        telemetryClient.TrackTrace("Received message");
        try 
        {
           // process message
        }
        catch (Exception ex)
        {
             telemetryClient.TrackException(ex);
             operation.Telemetry.Success = false;
             throw;
        }

        telemetryClient.TrackTrace("Done");
   }
}
```

Neste exemplo, `RequestTelemetry` é reportado para cada mensagem processada, tendo um tempotamp, duração e resultado (sucesso). A telemetria também tem um conjunto de propriedades de correlação.
Vestígios aninhados e exceções relatados durante o processamento de mensagens são também carimbados com propriedades de correlação que os representam como "crianças" do `RequestTelemetry` .

Caso escdaize chamadas para componentes externos suportados durante o processamento de mensagens, também são automaticamente rastreados e correlacionados. Consulte [as operações personalizadas de rastreio com Application Insights .NET SDK](../azure-monitor/app/custom-operations-tracking.md) para rastreio manual e correlação.

Se estiver a executar algum código externo para além do SDK Application Insights, espere ver uma **duração** mais longa ao visualizar os registos de Insights de Aplicação. 

![Duração mais longa no registo de Insights de Aplicação](./media/service-bus-end-to-end-tracing/longer-duration.png)

Não significa que houve um atraso em receber a mensagem. Neste cenário, a mensagem já foi recebida desde que a mensagem é transmitida como um parâmetro para o código SDK. E a etiqueta **de nome** nos registos app Insights **(Processo)** indica que a mensagem está agora a ser processada pelo seu código de processamento de eventos externo. Esta questão não tem a ver com o Azure. Em vez disso, estas métricas referem-se à eficiência do seu código externo, dado que a mensagem já foi recebida da Service Bus. Consulte [este ficheiro no GitHub](https://github.com/Azure/azure-sdk-for-net/blob/4bab05144ce647cc9e704d46d3763de5f9681ee0/sdk/servicebus/Microsoft.Azure.ServiceBus/src/ServiceBusDiagnosticsSource.cs) para ver onde a etiqueta **Processo** é gerada e atribuída uma vez que a mensagem tenha sido recebida da Service Bus. 

### <a name="tracking-without-tracing-system"></a>Rastreio sem sistema de rastreio
Caso o seu sistema de rastreio não suporte chamadas automáticas de Service Bus, poderá estar a tentar adicionar esse suporte a um sistema de rastreio ou à sua aplicação. Esta secção descreve eventos de diagnóstico enviados pelo cliente Service Bus .NET.  

Service Bus .NET Client é instrumentado utilizando sistema primitivos de rastreio [.NET.Diagnósticos.Atividade](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) e [Sistema.Diagnósticos.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md).

`Activity` serve como um contexto de traço enquanto `DiagnosticSource` é um mecanismo de notificação. 

Se não houver ouvinte para os eventos DiagnosticSource, a instrumentação está desligada, mantendo zero custos de instrumentação. O DiagnosticSource dá todo o controlo ao ouvinte:
- controlos ouvintes que fontes e eventos para ouvir
- ouvinte controla taxa de evento e amostragem
- os eventos são enviados com uma carga útil que fornece todo o contexto para que possa aceder e modificar o objeto de mensagem durante o evento

Familiarize-se com [o Guia do Utilizador DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) antes de prosseguir com a implementação.

Vamos criar um ouvinte para eventos de Service Bus em ASP.NET aplicação Core que escreve registos com microsoft.Extension.Logger.
Utiliza a biblioteca [System.Reative.Core](https://www.nuget.org/packages/System.Reactive.Core) para subscrever o DiagnosticSource (também é fácil subscrever o DiagnosticSource sem ele)

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory factory, IApplicationLifetime applicationLifetime)
{
    // configuration...

    var serviceBusLogger = factory.CreateLogger("Microsoft.Azure.ServiceBus");

    IDisposable innerSubscription = null;
    IDisposable outerSubscription = DiagnosticListener.AllListeners.Subscribe(delegate (DiagnosticListener listener)
    {
        // subscribe to the Service Bus DiagnosticSource
        if (listener.Name == "Microsoft.Azure.ServiceBus")
        {
            // receive event from Service Bus DiagnosticSource
            innerSubscription = listener.Subscribe(delegate (KeyValuePair<string, object> evnt)
            {
                // Log operation details once it's done
                if (evnt.Key.EndsWith("Stop"))
                {
                    Activity currentActivity = Activity.Current;
                    TaskStatus status = (TaskStatus)evnt.Value.GetProperty("Status");
                    serviceBusLogger.LogInformation($"Operation {currentActivity.OperationName} is finished, Duration={currentActivity.Duration}, Status={status}, Id={currentActivity.Id}, StartTime={currentActivity.StartTimeUtc}");
                }
            });
        }
    });

    applicationLifetime.ApplicationStopping.Register(() =>
    {
        outerSubscription?.Dispose();
        innerSubscription?.Dispose();
    });
}
```

Neste exemplo, o ouvinte regista a duração, o resultado, o identificador único e a hora de início de cada operação do Service Bus.

#### <a name="events"></a>Eventos

Para cada operação, são enviados dois eventos: "Iniciar" e "Parar". Provavelmente, só está interessado em eventos de "Stop". Fornecem o resultado da operação, e iniciam a hora e a duração como propriedades da Atividade.

A carga útil do evento fornece a um ouvinte o contexto da operação, replica os parâmetros de entrada da API e o valor de retorno. A carga útil do evento 'Stop' tem todas as propriedades da carga útil do evento 'Start', para que possa ignorar completamente o evento 'Iniciar'.

Todos os eventos também têm propriedades 'Entidade' e 'Endpoint', são omitidos em abaixo da tabela
  * `string Entity` - - Nome da entidade (fila, tópico, etc.)
  * `Uri Endpoint` - Service Bus endpoint URL

Cada evento 'Stop' tem `Status` propriedade com `TaskStatus` operação async foi concluído com, que também é omitido na tabela seguinte para simplicidade.

Aqui está a lista completa de operações instrumentadas:

| Nome da Operação | API rastreado | Propriedades específicas da carga útil|
|----------------|-------------|---------|
| Microsoft.Azure.ServiceBus.Send | [MensagemSender.SendAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.sendasync) | `IList<Message> Messages` - Lista de mensagens enviadas |
| Microsoft.Azure.ServiceBus.ScheduleMessage | [MensagensSender.ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.schedulemessageasync) | `Message Message` - Mensagem a ser processada<br/>`DateTimeOffset ScheduleEnqueueTimeUtc` - Compensação de mensagens agendadas<br/>`long SequenceNumber` - Número de sequência de mensagem programada (carga útil do evento 'Stop') |
| Microsoft.Azure.ServiceBus.Cancela | [MensagemSEnder.CancelScheduledMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.cancelscheduledmessageasync) | `long SequenceNumber` - Sequência número de mensagem te a ser cancelada | 
| Microsoft.Azure.ServiceBus.receive | [MensagemReceiver.ReceiveAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) | `int RequestedMessageCount` - O número máximo de mensagens que podem ser recebidas.<br/>`IList<Message> Messages` - Lista de mensagens recebidas (carga útil do evento 'Stop') |
| Microsoft.Azure.ServiceBus.Peek | [MensagemReceiver.PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync) | `int FromSequenceNumber` - O ponto de partida a partir do qual navegar num lote de mensagens.<br/>`int RequestedMessageCount` - O número de mensagens para recuperar.<br/>`IList<Message> Messages` - Lista de mensagens recebidas (carga útil do evento 'Stop') |
| Microsoft.Azure.ServiceBus.receiveDefered | [MessageReceiver.ReceiveDeferredMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receivedeferredmessageasync) | `IEnumerable<long> SequenceNumbers` - A lista que contém os números de sequência a receber.<br/>`IList<Message> Messages` - Lista de mensagens recebidas (carga útil do evento 'Stop') |
| Microsoft.Azure.ServiceBus.Complete | [MensagemReceiver.CompleteAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.completeasync) | `IList<string> LockTokens` - A lista que contém os tokens de bloqueio das mensagens correspondentes a completar.|
| Microsoft.Azure.ServiceBus.Abandone | [MensagemReceiver.AbandonAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.abandonasync) | `string LockToken` - O sinal de bloqueio da mensagem correspondente a abandonar. |
| Microsoft.Azure.ServiceBus.Defer | [MensagemReceiver.DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) | `string LockToken` - O sinal de bloqueio da mensagem correspondente para adiar. | 
| Microsoft.Azure.ServiceBus.DeadLetter | [MensagemReceiver.DeadLetterAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deadletterasync) | `string LockToken` - O sinal da mensagem correspondente à letra morta. | 
| Microsoft.Azure.ServiceBus.RenewLock | [MensagemReceiver.RenewLockAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync) | `string LockToken` - O sinal de bloqueio da mensagem correspondente para renovar o bloqueio.<br/>`DateTime LockedUntilUtc` - Nova data e hora de validade do token de bloqueio no formato UTC. ('Stop' event payload)|
| Microsoft.Azure.ServiceBus.Process | Função lambda manipulador de mensagens fornecida em [IReceiverClient.RegisterMessageHandler](/dotnet/api/microsoft.azure.servicebus.core.ireceiverclient.registermessagehandler) | `Message Message` - Mensagem a ser processada. |
| Microsoft.Azure.ServiceBus.ProcessSession | Função lambda de manipulador de sessão de mensagens fornecida em [IQueueClient.RegisterSessionHandler](/dotnet/api/microsoft.azure.servicebus.iqueueclient.registersessionhandler) | `Message Message` - Mensagem a ser processada.<br/>`IMessageSession Session` - Sessão a ser processada |
| Microsoft.Azure.ServiceBus.addrule | [SubscriçãoClient.AddRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.addruleasync) | `RuleDescription Rule` - A descrição da regra que fornece a regra a adicionar. |
| Microsoft.Azure.ServiceBus.removerule | [SubscriçãoClient.RemoveRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.removeruleasync) | `string RuleName` - Nome da regra a remover. |
| Microsoft.Azure.ServiceBus.GetRules | [SubscriçãoClient.GetRulesAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.getrulesasync) | `IEnumerable<RuleDescription> Rules` - Todas as regras associadas à subscrição. (apenas a carga útil de Stop) |
| Microsoft.Azure.ServiceBus.AcceptMessageSssion | [ISessionClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.azure.servicebus.isessionclient.acceptmessagesessionasync) | `string SessionId` - A sessãoId presente nas mensagens. |
| Microsoft.Azure.ServiceBus.GetSessionState | [IMessageSession.GetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.getstateasync) | `string SessionId` - A sessãoId presente nas mensagens.<br/>`byte [] State` - Estado de sessão (carga útil do evento 'Stop') |
| Microsoft.Azure.ServiceBus.SetSessionState | [IMessageSession.SetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.setstateasync) | `string SessionId` - A sessãoId presente nas mensagens.<br/>`byte [] State` - Estado de sessão |
| Microsoft.Azure.ServiceBus.Renovações | [IMessageSession.RenewSessionLockAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.renewsessionlockasync) | `string SessionId` - A sessãoId presente nas mensagens. |
| Microsoft.Azure.ServiceBus.Exceção | qualquer API instrumentada| `Exception Exception` - Instância de exceção |

Em todos os eventos, pode aceder `Activity.Current` ao contexto de funcionamento atual.

#### <a name="logging-more-properties"></a>Registar mais propriedades

`Activity.Current` fornece um contexto detalhado da operação atual e dos seus pais. Para mais informações, consulte [a documentação da Atividade.](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md)
A instrumentação do Service Bus fornece mais informações no `Activity.Current.Tags` - eles têm e sempre que `MessageId` `SessionId` estão disponíveis.

As atividades que acompanham o evento 'Receber', 'Peek' e 'ReceiveDeferred' também podem ter `RelatedTo` etiqueta. Detém uma lista distinta de `Diagnostic-Id` (s) mensagens que foram recebidas como resultado.
Tal operação pode resultar em várias mensagens não relacionadas a serem recebidas. Além disso, `Diagnostic-Id` não se sabe quando a operação começa, pelo que as operações de "Receber" podem ser correlacionadas com operações de 'Processo' usando apenas esta Tag. É útil ao analisar problemas de desempenho para verificar o tempo que demorou a receber a mensagem.

Uma forma eficiente de registar tags é iterar sobre elas, por isso adicionar Tags ao exemplo anterior parece 

```csharp
Activity currentActivity = Activity.Current;
TaskStatus status = (TaskStatus)evnt.Value.GetProperty("Status");

var tagsList = new StringBuilder();
foreach (var tags in currentActivity.Tags)
{
    tagsList.Append($", {tags.Key}={tags.Value}");
}

serviceBusLogger.LogInformation($"{currentActivity.OperationName} is finished, Duration={currentActivity.Duration}, Status={status}, Id={currentActivity.Id}, StartTime={currentActivity.StartTimeUtc}{tagsList}");
```

#### <a name="filtering-and-sampling"></a>Filtragem e amostragem

Em alguns casos, é desejável registar apenas uma parte dos eventos para reduzir o desempenho ou o consumo de armazenamento. Pode registar apenas eventos 'Stop' (como em exemplo anterior) ou percentagem de amostra dos eventos. 
`DiagnosticSource` fornecer maneira de alcançá-lo com `IsEnabled` predicado. Para obter mais informações, consulte [a filtragem baseada em contexto em DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#context-based-filtering).

`IsEnabled` pode ser chamado várias vezes para uma única operação para minimizar o impacto do desempenho.

`IsEnabled` é chamada na seguinte sequência:

1. `IsEnabled(<OperationName>, string entity, null)` por exemplo, `IsEnabled("Microsoft.Azure.ServiceBus.Send", "MyQueue1")` . . Note que não há "Iniciar" ou "Stop" no final. Utilize-o para filtrar determinadas operações ou filas. Se o método de retorno do `false` retorno, os eventos para a operação não são enviados

   * Para as operações de 'Processo' e 'ProcessoSession', também `IsEnabled(<OperationName>, string entity, Activity activity)` recebes chamadas de retorno. Utilize-o para filtrar eventos baseados em `activity.Id` propriedades ou Tags.
  
2. `IsEnabled(<OperationName>.Start)` por exemplo, `IsEnabled("Microsoft.Azure.ServiceBus.Send.Start")` . . Verifique se o evento "Iniciar" deve ser disparado. O resultado apenas afeta o evento 'Iniciar', mas a instrumentação adicional não depende dele.

Não há `IsEnabled` nada para o evento "Stop".

Se algum resultado de operação for exceção, `IsEnabled("Microsoft.Azure.ServiceBus.Exception")` é chamado. Só pode subscrever eventos de "Exceção" e impedir o resto da instrumentação. Neste caso, ainda tens de lidar com tais exceções. Uma vez que outras instrumentações são desativadas, não deve esperar que o contexto do traço flua com as mensagens de consumidor para produtor.

Também pode utilizar `IsEnabled` estratégias de amostragem. A amostragem com base na `Activity.Id` ou `Activity.RootId` garante uma amostragem consistente em todos os pneus (desde que seja propagada pelo sistema de rastreio ou pelo seu próprio código).

Na presença de vários `DiagnosticSource` ouvintes para a mesma fonte, é suficiente para apenas um ouvinte aceitar o evento, por isso não há garantias que `IsEnabled` seja chamada.

---

## <a name="next-steps"></a>Passos seguintes

* [Correlação de Insights de Aplicação](../azure-monitor/app/correlation.md)
* [Application Insights Monitor Dependencies](../azure-monitor/app/asp-net-dependencies.md) para ver se REST, SQL ou outros recursos externos estão a atrasá-lo.
* [Acompanhe as operações personalizadas com o Application Insights .NET SDK](../azure-monitor/app/custom-operations-tracking.md)

