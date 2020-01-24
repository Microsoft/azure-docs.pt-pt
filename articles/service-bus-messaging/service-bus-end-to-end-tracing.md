---
title: Rastreio e diagnóstico de rastreio e diagnóstico de autocarros de serviço azure Microsoft Docs
description: Visão geral dos diagnósticos dos clientes do Serviço Bus e rastreio de ponta a ponta
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: fa71ca7ea976ab4d724a061d0d0809cdb5767f4f
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705756"
---
# <a name="distributed-tracing-and-correlation-through-service-bus-messaging"></a>Rastreio distribuído e correlação através de mensagens service bus

Um dos problemas comuns no desenvolvimento de microserviços é a capacidade de rastrear a operação de um cliente através de todos os serviços que estão envolvidos no processamento. É útil para depuração, análise de desempenho, testes de A/B e outros cenários típicos de diagnóstico.
Uma parte deste problema é rastrear pedaços lógicos de trabalho. Inclui o resultado do processamento de mensagens e as chamadas de latência e dependência externa. Outra parte é a correlação destes eventos de diagnóstico para além dos limites do processo.

Quando um produtor envia uma mensagem através de uma fila, normalmente acontece no âmbito de outra operação lógica, iniciada por algum outro cliente ou serviço. A mesma operação é prosseguida pelo consumidor assim que recebe uma mensagem. Tanto o produtor como o consumidor (e outros serviços que processam a operação), presumivelmente emitem eventos de telemetria para rastrear o fluxo de operação e o resultado. Para correlacionar tais eventos e rastrear a operação de ponta a ponta, cada serviço que reporta telemetria tem de carimbar todos os eventos com um contexto de traços.

As mensagens microsoft Azure Service Bus definiram propriedades de carga útil que os produtores e consumidores devem usar para passar esse contexto de rastreio.
O protocolo baseia-se no [protocolo HTTP Correlation](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md).

| Nome da propriedade        | Descrição                                                 |
|----------------------|-------------------------------------------------------------|
|  Diagnóstico-Id       | Identificador único de uma chamada externa do produtor para a fila. Consulte o [Request-Id no protocolo HTTP](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#request-id) para a racionalidade, considerações e formato |
|  Correlação-Contexto | Contexto de operação, que é propagado em todos os serviços envolvidos no processamento de operações. Para mais informações, consulte [correlation-context no protocolo HTTP](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#correlation-context) |

## <a name="service-bus-net-client-auto-tracing"></a>Ônibus de serviço .NET Cliente autorrastreio

A partir da versão 3.0.0 O Cliente Microsoft [Azure ServiceBus para .NET](/dotnet/api/microsoft.azure.servicebus.queueclient) fornece pontos de instrumentação de rastreio que podem ser ligados por sistemas de rastreio ou código de cliente.
A instrumentação permite rastrear todas as chamadas para o serviço de mensagens Service Bus do lado do cliente. Se o processamento de mensagens for feito com o padrão de manipulador de [mensagens,](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler)o processamento de mensagens também é instrumentado

### <a name="tracking-with-azure-application-insights"></a>Rastreio com insights de aplicação azure

[O Microsoft Application Insights](https://azure.microsoft.com/services/application-insights/) fornece capacidades de monitorização de desempenho ricas, incluindo pedidos automágicos e rastreio de dependência.

Dependendo do seu tipo de projeto, instale O SDK de Insights de Aplicação:
- [ASP.NET](../azure-monitor/app/asp-net.md) - instale a versão 2.5-beta2 ou superior
- [ASP.NET Core](../azure-monitor/app/asp-net-core.md) - instale a versão 2.2.0-beta2 ou superior.
Estes links fornecem detalhes sobre a instalação de SDK, criação de recursos e configuração de SDK (se necessário). Para non-ASP.NET aplicações, consulte o artigo Insights de [Aplicações de Aplicações azure para aplicações de consolas.](../azure-monitor/app/console.md)

Se utilizar o padrão de manipulador de [mensagens](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler) para processar mensagens, está feito: todas as chamadas de Ônibus de serviço feitas pelo seu serviço são automaticamente rastreadas e correlacionadas com outros itens de telemetria. Caso contrário, consulte o seguinte exemplo para o rastreio manual de mensagens.

#### <a name="trace-message-processing"></a>Processamento de mensagens de rastreio

```csharp
private readonly TelemetryClient telemetryClient;

async Task ProcessAsync(Message message)
{
    var activity = message.ExtractActivity();
    
    // If you are using Microsoft.ApplicationInsights package version 2.6-beta or higher, you should call StartOperation<RequestTelemetry>(activity) instead
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

Neste exemplo, `RequestTelemetry` é reportado para cada mensagem processada, tendo um carimbo de tempo, duração e resultado (sucesso). A telemetria também tem um conjunto de propriedades de correlação.
Os vestígios aninhados e as exceções relatadas durante o processamento de mensagens também são carimbados com propriedades de correlação que os representam como "crianças" da `RequestTelemetry`.

Caso efaça chamadas para componentes externos suportados durante o processamento de mensagens, também são automaticamente rastreados e correlacionados. Consulte [as operações personalizadas com Insights de Aplicação .NET SDK](../azure-monitor/app/custom-operations-tracking.md) para rastreio manual e correlação.

### <a name="tracking-without-tracing-system"></a>Rastreio sem sistema de rastreio
Caso o seu sistema de rastreio não suporte o rastreio automático de chamadas de ônibus de serviço, você pode estar procurando adicionar esse suporte em um sistema de rastreio ou na sua aplicação. Esta secção descreve os eventos de diagnóstico enviados pelo cliente Service Bus .NET.  

Service Bus .NET Client é instrumentado usando .NET rastreio primitivos [System.Diagnostics.Activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) and [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md).

`Activity` serve de contexto de traços enquanto `DiagnosticSource` é um mecanismo de notificação. 

Se não houver ouvinte para os eventos DiagnosticSource, a instrumentação está desligada, mantendo zero custos de instrumentação. DiagnosticSource dá todo o controlo ao ouvinte:
- controlos ouvintes que fontes e eventos para ouvir
- ouvinte controla taxa de evento e amostragem
- eventos são enviados com uma carga útil que fornece o contexto completo para que você possa aceder e modificar objeto de mensagem durante o evento

Familiarize-se com [o DiagnosticSource User Guide](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) antes de prosseguir com a implementação.

Vamos criar um ouvinte para eventos de ônibus de serviço em ASP.NET aplicação Core que escreve logs com Microsoft.Extension.Logger.
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

Neste exemplo, o ouvinte regista a duração, o resultado, o identificador único e a hora de início de cada operação do Ônibus de Serviço.

#### <a name="events"></a>Eventos

Para cada operação, são enviados dois eventos: "Iniciar" e "Parar". Provavelmente, só está interessado em eventos de "Stop". Fornecem o resultado do funcionamento, bem como a hora e duração de início como propriedades da Atividade.

A carga útil do evento fornece um ouvinte com o contexto da operação, replica parâmetros de entrada de API e valor de retorno. A carga útil do evento 'Stop' tem todas as propriedades da carga útil do evento 'Start', para que possa ignorar completamente o evento 'Iniciar'.

Todos os eventos também têm propriedades 'Entidade' e 'Endpoint', são omitidas na tabela abaixo
  * `string Entity` - - Nome da entidade (fila, tópico, etc.)
  * `Uri Endpoint` - URL final de ônibus de serviço

Cada evento 'Stop' tem `Status` propriedade com `TaskStatus` operação de asincronização foi concluída, que também é omitida na tabela seguinte para simplicidade.

Aqui está a lista completa de operações instrumentadas:

| Nome da Operação | API rastreado | Propriedades específicas da carga útil|
|----------------|-------------|---------|
| Microsoft.Azure.ServiceBus.Send | [MessageSender.SendAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.sendasync) | `IList<Message> Messages` - Lista de mensagens enviadas |
| Microsoft.Azure.ServiceBus.ScheduleMessage | [MessageSender.ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.schedulemessageasync) | `Message Message` - Mensagem a ser processada<br/>`DateTimeOffset ScheduleEnqueueTimeUtc` - Compensam a mensagem programada<br/>`long SequenceNumber` - Número de sequência de mensagem programada (carga útil do evento 'Stop') |
| Microsoft.Azure.ServiceBus.Cancel | [MessageSender.CancelScheduledMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.cancelscheduledmessageasync) | `long SequenceNumber` - Número de sequência da mensagem te a ser cancelada | 
| Microsoft.Azure.ServiceBus.Receive | [MessageReceiver.ReceiveAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) | `int RequestedMessageCount` - O número máximo de mensagens que poderiam ser recebidas.<br/>`IList<Message> Messages` - Lista de mensagens recebidas (carga útil do evento 'Stop' |
| Microsoft.Azure.ServiceBus.Peek | [MessageReceiver.PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync) | `int FromSequenceNumber` - O ponto de partida a partir do qual navegar num lote de mensagens.<br/>`int RequestedMessageCount` - O número de mensagens para recuperar.<br/>`IList<Message> Messages` - Lista de mensagens recebidas (carga útil do evento 'Stop' |
| Microsoft.Azure.ServiceBus.ReceiveDeferred | [MessageReceiver.ReceiveDeferredMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receivedeferredmessageasync) | `IEnumerable<long> SequenceNumbers` - A lista que contém os números de sequência a receber.<br/>`IList<Message> Messages` - Lista de mensagens recebidas (carga útil do evento 'Stop' |
| Microsoft.Azure.ServiceBus.Complete | [MessageReceiver.CompleteAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.completeasync) | `IList<string> LockTokens` - A lista que contém as fichas de bloqueio das mensagens correspondentes para completar.|
| Microsoft.Azure.ServiceBus.Abandon | [MessageReceiver.AbandonAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.abandonasync) | `string LockToken` - O símbolo da mensagem correspondente para abandonar. |
| Microsoft.Azure.ServiceBus.Defer | [MessageReceiver.DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) | `string LockToken` - O símbolo da mensagem correspondente para adiar. | 
| Microsoft.Azure.ServiceBus.DeadLetter | [MessageReceiver.DeadLetterAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deadletterasync) | `string LockToken` - O símbolo da mensagem correspondente à letra morta. | 
| Microsoft.Azure.ServiceBus.RenewLock | [MessageReceiver.RenewLockAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync) | `string LockToken` - O símbolo da mensagem correspondente para renovar o bloqueio.<br/>`DateTime LockedUntilUtc` - Data e hora de validade do novo símbolo de bloqueio em formato UTC. ('Stop' de eventos)|
| Microsoft.Azure.ServiceBus.Process | Função Lambda manipulador de mensagens fornecida no [IReceiverClient.RegisterMessageHandler](/dotnet/api/microsoft.azure.servicebus.core.ireceiverclient.registermessagehandler) | `Message Message` - Mensagem a ser processada. |
| Microsoft.Azure.ServiceBus.ProcessSession | Função lambda de manipulador de sessão de mensagem fornecida no [IQueueClient.RegisterSessionHandler](/dotnet/api/microsoft.azure.servicebus.iqueueclient.registersessionhandler) | `Message Message` - Mensagem a ser processada.<br/>`IMessageSession Session` - Sessão a ser processada |
| Microsoft.Azure.ServiceBus.AddRule | [SubscriptionClient.AddRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.addruleasync) | `RuleDescription Rule` - A descrição da regra que fornece a regra a adicionar. |
| Microsoft.Azure.ServiceBus.RemoveRule | [SubscriptionClient.RemoveRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.removeruleasync) | `string RuleName` - Nome da regra a remover. |
| Microsoft.Azure.ServiceBus.GetRules | [SubscriptionClient.GetRulesAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.getrulesasync) | `IEnumerable<RuleDescription> Rules` - Todas as regras associadas à subscrição. ('Parar' apenas a carga útil) |
| Microsoft.Azure.ServiceBus.AcceptMessageSession | [ISessionClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.azure.servicebus.isessionclient.acceptmessagesessionasync) | `string SessionId` - A sessãoId presente nas mensagens. |
| Microsoft.Azure.ServiceBus.GetSessionState | [IMessageSession.GetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.getstateasync) | `string SessionId` - A sessãoId presente nas mensagens.<br/>`byte [] State` - Sessão ('Stop' de eventos) |
| Microsoft.Azure.ServiceBus.SetSessionState | [IMessageSession.SetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.setstateasync) | `string SessionId` - A sessãoId presente nas mensagens.<br/>`byte [] State` - Estado de sessão |
| Microsoft.Azure.ServiceBus.RenewSessionLock | [IMessageSession.RenewSessionLockAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.renewsessionlockasync) | `string SessionId` - A sessãoId presente nas mensagens. |
| Microsoft.Azure.ServiceBus.Exception | qualquer API instrumentada| `Exception Exception` - Instância de exceção |

Em todos os eventos, pode aceder a `Activity.Current` que detém o contexto de operação atual.

#### <a name="logging-additional-properties"></a>Exploração de propriedades adicionais

`Activity.Current` fornece um contexto detalhado da operação atual e dos seus pais. Para mais informações, consulte a [documentação da Atividade](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) para obter mais detalhes.
A instrumentação do ônibus de serviço fornece informações adicionais no `Activity.Current.Tags` - eles têm `MessageId` e `SessionId` sempre que estão disponíveis.

As atividades que acompanham o evento 'Receber', 'Peek' e 'ReceiveDeferred' também podem ter `RelatedTo` etiqueta. Detém uma lista distinta de `Diagnostic-Id`(s) de mensagens que foram recebidas como resultado.
Tal operação pode resultar em várias mensagens não relacionadas a serem recebidas. Além disso, o `Diagnostic-Id` não é conhecido quando a operação começa, pelo que as operações de "Receber" podem estar correlacionadas com operações de "Processo" utilizando apenas esta etiqueta. É útil quando se analisam os problemas de desempenho para verificar quanto tempo demorou a receber a mensagem.

Uma forma eficiente de registar tags é iterar sobre eles, por isso adicionar Tags ao exemplo anterior parece 

```csharp
Activity currentActivity = Activity.Current;
TaskStatus status = (TaskStatus)evnt.Value.GetProperty("Status");

var tagsList = new StringBuilder();
foreach (var tags in currentActivity.Tags)
{
    tagsList.Append($", "{tags.Key}={tags.Value}");
}

serviceBusLogger.LogInformation($"{currentActivity.OperationName} is finished, Duration={currentActivity.Duration}, Status={status}, Id={currentActivity.Id}, StartTime={currentActivity.StartTimeUtc}{tagsList}");
```

#### <a name="filtering-and-sampling"></a>Filtragem e amostragem

Em alguns casos, é desejável registar apenas parte dos eventos para reduzir o consumo de despesas ou armazenamento de desempenho. Só pode registar eventos 'Stop' (como no exemplo anterior) ou a percentagem de amostras dos eventos. 
`DiagnosticSource` fornecem forma de o conseguir com `IsEnabled` predicado. Para mais informações, consulte [a filtragem baseada em contexto no DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#context-based-filtering).

`IsEnabled` pode ser chamado várias vezes para uma única operação para minimizar o impacto do desempenho.

`IsEnabled` é chamado na seguinte sequência:

1. `IsEnabled(<OperationName>, string entity, null)`, por exemplo, `IsEnabled("Microsoft.Azure.ServiceBus.Send", "MyQueue1")`. Note que não há 'Iniciar' ou 'Parar' no final. Utilize-o para filtrar determinadas operações ou filas. Se o retorno de retorno de retorno `false`, eventos para a operação não são enviados

   * Para as operações 'Processo' e 'ProcessSession', também recebe `IsEnabled(<OperationName>, string entity, Activity activity)` chamada. Use-o para filtrar eventos com base nas propriedades `activity.Id` ou Tags.
  
2. `IsEnabled(<OperationName>.Start)`, por exemplo, `IsEnabled("Microsoft.Azure.ServiceBus.Send.Start")`. Verifica se o evento "Iniciar" deve ser despedido. O resultado só afeta o evento 'Iniciar', mas a instrumentação adicional não depende dele.

Não há `IsEnabled` para o evento "Stop".

Se algum resultado da operação for exceção, `IsEnabled("Microsoft.Azure.ServiceBus.Exception")` é chamado. Só pode subscrever eventos de "Exceção" e impedir o resto da instrumentação. Neste caso, ainda tens de lidar com estas exceções. Uma vez que outras instrumentações são desativadas, não deve esperar que o contexto de rastreio flua com as mensagens do consumidor para o produtor.

Pode utilizar `IsEnabled` também implementar estratégias de amostragem. A amostragem com base no `Activity.Id` ou `Activity.RootId` garante uma amostragem consistente em todos os pneus (desde que seja propagada pelo sistema de rastreio ou pelo seu próprio código).

Na presença de vários ouvintes `DiagnosticSource` para a mesma fonte, basta que apenas um ouvinte aceite o evento, por isso `IsEnabled` não é garantido ser chamado,

## <a name="next-steps"></a>Passos seguintes

* [Correlação de insights de aplicação](../azure-monitor/app/correlation.md)
* [Os Insights de Aplicação monitorizam as dependências](../azure-monitor/app/asp-net-dependencies.md) para ver se rest, SQL ou outros recursos externos estão a atrasá-lo.
* [Acompanhe as operações personalizadas com Insights de Aplicação .NET SDK](../azure-monitor/app/custom-operations-tracking.md)
