---
title: Diagnóstico e rastreamento de ponta a ponta do Service Bus do Azure | Documentos da Microsoft
description: Descrição geral do diagnóstico do cliente do Service Bus e rastreamento de ponta a ponta
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
ms.openlocfilehash: 6e5895392db1d75a985674bf2f878a84bc8dd926
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58107040"
---
# <a name="distributed-tracing-and-correlation-through-service-bus-messaging"></a>Rastreio distribuído e correlação por meio de mensagens do Service Bus

Um dos problemas comuns no desenvolvimento de microsserviços é a capacidade de operação de rastreio a partir de um cliente por meio de todos os serviços que estão envolvidos no processamento. É útil para depuração, análise de desempenho, A / B testes e outros cenários de diagnóstico típico.
Uma parte deste problema é monitorizar lógicos partes do trabalho. Ele inclui o resultado e a latência e chamadas de dependência externa de processamento de mensagens. Outra parte é correlação desses eventos de diagnóstico para além dos limites de processos.

Quando um produtor envia uma mensagem através de uma fila, ele ocorre normalmente no âmbito da outra operação lógica, iniciada por outro cliente ou serviço. A mesma operação é continuada pelo consumidor depois de receber uma mensagem. O produtor e consumidor (e outros serviços que processam a operação), presumivelmente emitir eventos de telemetria para rastrear o fluxo de operação e o resultado. Para correlacionar tais eventos e a operação de rastreio ponto-a-ponto, cada serviço que comuniquem a telemetria tem carimbo todos os eventos com um contexto de rastreio.

Mensagens do Service Bus do Microsoft Azure tem definido as propriedades de payload que produtores e consumidores, devem utilizar para passar esse contexto de rastreio.
O protocolo se baseia a [protocolo HTTP correlação](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md).

| Nome da Propriedade        | Descrição                                                 |
|----------------------|-------------------------------------------------------------|
|  Id de diagnóstico       | Identificador exclusivo de uma chamada externa de produtor para a fila. Consulte a [Request-ID indicado no protocolo HTTP](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#request-id) para a lógica, considerações e formato |
|  Contexto de correlação | Contexto de operação, que é propagado em todos os serviços envolvidos na operação em processamento. Para obter mais informações, consulte [correlação-contexto no protocolo HTTP](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#correlation-context) |

## <a name="service-bus-net-client-auto-tracing"></a>Rastreio de automática de cliente de .NET do Service Bus

A partir da versão 3.0.0 [ServiceBus cliente do Microsoft Azure para .NET](/dotnet/api/microsoft.azure.servicebus.queueclient) fornece pontos de instrumentação de rastreamento que possam ser conectados por sistemas de rastreamento, ou como parte do código de cliente.
A instrumentação permite que todas as chamadas para o barramento de serviço de controlo de mensagens serviço a partir do lado do cliente. Se o processamento de mensagens é feito com o [padrão de manipulador de mensagem](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler), processamento de mensagens também foi instrumentado

### <a name="tracking-with-azure-application-insights"></a>Controlo com o Azure Application Insights

[Microsoft Application Insights](https://azure.microsoft.com/services/application-insights/) fornece desempenho avançado, incluindo automagical pedido e rastreamento de dependências de capacidades de monitorização.

Consoante o tipo de projeto, instale o SDK do Application Insights:
- [ASP.NET](../azure-monitor/app/asp-net.md) -instalar a versão 2.5-beta2 ou superior
- [ASP.NET Core](../azure-monitor/app/asp-net-core.md) -instalar a versão 2.2.0-beta2 ou superior.
Estas ligações fornecem detalhes sobre como instalar o SDK, criação de recursos e configurar o SDK (se necessário). Para aplicativos não são do ASP.NET, consulte [do Azure Application Insights para aplicações de consola](../azure-monitor/app/console.md) artigo.

Se usar [padrão de manipulador de mensagem](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler) para processar mensagens, terminar: todas as chamadas de Service Bus feitas pelo seu serviço são automaticamente controladas e correlacionadas com outros itens de telemetria. Caso contrário, consulte o exemplo a seguir para o controlo de processamento de mensagens manual.

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

Neste exemplo, `RequestTelemetry` é comunicada para cada mensagem processada, ter um timestamp, a duração e o resultado (êxito). A telemetria também tem um conjunto de propriedades de correlação.
Aninhada rastreios e exceções reportadas durante o processamento de mensagem também são marcadas com propriedades de correlação, que representa-los como filhos do `RequestTelemetry`.

No caso de fazer chamadas para componentes externos suportados durante o processamento da mensagem, são também automaticamente controladas e correlacionados. Consulte a [controlar operações personalizadas com o .NET SDK do Application Insights](../azure-monitor/app/custom-operations-tracking.md) para o controlo manual e a correlação.

### <a name="tracking-without-tracing-system"></a>Sem o sistema de rastreamento de controlo
No caso de seu sistema de rastreamento não suporta automática de Service Bus de chamadas de controlo que esteja procurando para adicionar esse suporte num sistema de rastreamento ou na sua aplicação. Esta secção descreve os eventos de diagnóstico enviados pelo cliente .NET do Service Bus.  

Cliente de .NET do Service Bus é instrumentado usando primitivos de rastreamento do .NET [System.Diagnostics.Activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) e [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md).

`Activity` serve como um contexto de rastreio ao `DiagnosticSource` é um mecanismo de notificação. 

Se não houver nenhum serviço de escuta para os eventos de DiagnosticSource, a instrumentação é desativada, mantendo zero os custos de instrumentação. DiagnosticSource fornece controle de todos os para o serviço de escuta:
- serviço de escuta controla qual origens e eventos para ouvir
- taxa de eventos de controles do serviço de escuta e amostragem
- os eventos são enviados com um payload que fornece o contexto completo para que possa aceder e modificar o objeto de mensagem durante o evento

Familiarize-se com [Guia do usuário DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) antes de continuar com a implementação.

Vamos criar um serviço de escuta de eventos do Service Bus na aplicação ASP.NET Core, que escreve os registos com Microsoft.Extension.Logger.
Ele usa [System.Reactive.Core](https://www.nuget.org/packages/System.Reactive.Core) biblioteca para subscrever DiagnosticSource (também é fácil subscrever DiagnosticSource sem-la)

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

Neste exemplo, o serviço de escuta registra a duração, o resultado, o identificador exclusivo e a hora de início para cada operação do Service Bus.

#### <a name="events"></a>Eventos

Para cada operação, são enviados por dois eventos: 'Iniciar' e 'Stop'. Muito provavelmente, só estiver interessado em eventos 'Stop'. Eles fornecem o resultado da operação, bem como hora de início e duração como propriedades de uma atividade.

Payload de evento fornece um serviço de escuta com o contexto da operação, ele replica os parâmetros de entrada de API e retornar o valor. Payload de evento 'Stop' tem todas as propriedades do payload do evento de "Iniciar", pelo que pode ignorar o evento de "Início" completamente.

Todos os eventos também possuem propriedades "Entidade" e "Endpoint", eles são omitidos na tabela abaixo
  * `string Entity` – O nome da entidade (fila, tópico, etc.)
  * `Uri Endpoint` -URL de ponto final do Service Bus

Cada evento 'Stop' tem `Status` propriedade com o `TaskStatus` operação assíncrona foi concluída com, que também é omitido na tabela a seguir para manter a simplicidade.

Eis a lista completa das operações instrumentadas:

| Nome da Operação | API controlada | Propriedades de Payload específico|
|----------------|-------------|---------|
| Microsoft.Azure.ServiceBus.Send | [MessageSender.SendAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.sendasync) | `IList<Message> Messages` -Lista de mensagens sendo enviadas |
| Microsoft.Azure.ServiceBus.ScheduleMessage | [MessageSender.ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.schedulemessageasync) | `Message Message` -Mensagem a ser processado<br/>`DateTimeOffset ScheduleEnqueueTimeUtc` -Deslocamento de mensagem agendada<br/>`long SequenceNumber` -Número de sequência de mensagem agendada ('Stop' payload do evento) |
| Microsoft.Azure.ServiceBus.Cancel | [MessageSender.CancelScheduledMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.cancelscheduledmessageasync) | `long SequenceNumber` -Número de sequência de mensagem de te de ser cancelada | 
| Microsoft.Azure.ServiceBus.Receive | [MessageReceiver.ReceiveAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) | `int RequestedMessageCount` -O número máximo de mensagens que poderiam ser recebida.<br/>`IList<Message> Messages` -Lista de mensagens recebidas ('Stop' payload do evento) |
| Microsoft.Azure.ServiceBus.Peek | [MessageReceiver.PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync) | `int FromSequenceNumber` -O ponto de partida partir da qual pretende procurar um lote de mensagens.<br/>`int RequestedMessageCount` -O número de mensagens a obter.<br/>`IList<Message> Messages` -Lista de mensagens recebidas ('Stop' payload do evento) |
| Microsoft.Azure.ServiceBus.ReceiveDeferred | [MessageReceiver.ReceiveDeferredMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receivedeferredmessageasync) | `IEnumerable<long> SequenceNumbers` -A lista que contém os números de sequência para receber.<br/>`IList<Message> Messages` -Lista de mensagens recebidas ('Stop' payload do evento) |
| Microsoft.Azure.ServiceBus.Complete | [MessageReceiver.CompleteAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.completeasync) | `IList<string> LockTokens` -A lista que contém os tokens de bloqueio das mensagens correspondentes para concluir.|
| Microsoft.Azure.ServiceBus.Abandon | [MessageReceiver.AbandonAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.abandonasync) | `string LockToken` -O token de bloqueio da mensagem a abandonar correspondente. |
| Microsoft.Azure.ServiceBus.Defer | [MessageReceiver.DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) | `string LockToken` -O token de bloqueio da mensagem a diferir correspondente. | 
| Microsoft.Azure.ServiceBus.DeadLetter | [MessageReceiver.DeadLetterAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deadletterasync) | `string LockToken` -O token de bloqueio da mensagem correspondente a entregues. | 
| Microsoft.Azure.ServiceBus.RenewLock | [MessageReceiver.RenewLockAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync) | `string LockToken` -O token de bloqueio da mensagem para renovar bloqueio na correspondente.<br/>`DateTime LockedUntilUtc` -O bloqueio novo data de expiração do token e a hora no formato UTC. ("Parar" payload do evento)|
| Microsoft.Azure.ServiceBus.Process | Função de lambda de manipulador de mensagem fornecida no [IReceiverClient.RegisterMessageHandler](/dotnet/api/microsoft.azure.servicebus.core.ireceiverclient.registermessagehandler) | `Message Message` -Mensagem a ser processado. |
| Microsoft.Azure.ServiceBus.ProcessSession | Função de lambda de manipulador de sessão de mensagem fornecida no [IQueueClient.RegisterSessionHandler](/dotnet/api/microsoft.azure.servicebus.iqueueclient.registersessionhandler) | `Message Message` -Mensagem a ser processado.<br/>`IMessageSession Session` -Sessão a ser processado |
| Microsoft.Azure.ServiceBus.AddRule | [SubscriptionClient.AddRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.addruleasync) | `RuleDescription Rule` -A descrição da regra que fornece a regra para adicionar. |
| Microsoft.Azure.ServiceBus.RemoveRule | [SubscriptionClient.RemoveRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.removeruleasync) | `string RuleName` -Nome da regra para remover. |
| Microsoft.Azure.ServiceBus.GetRules | [SubscriptionClient.GetRulesAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.getrulesasync) | `IEnumerable<RuleDescription> Rules` -Todas as regras associadas à subscrição. (Apenas para payload "Parar") |
| Microsoft.Azure.ServiceBus.AcceptMessageSession | [ISessionClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.azure.servicebus.isessionclient.acceptmessagesessionasync) | `string SessionId` -SessionId presente nas mensagens. |
| Microsoft.Azure.ServiceBus.GetSessionState | [IMessageSession.GetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.getstateasync) | `string SessionId` -SessionId presente nas mensagens.<br/>`byte [] State` -Estado de sessão ('Stop' payload do evento) |
| Microsoft.Azure.ServiceBus.SetSessionState | [IMessageSession.SetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.setstateasync) | `string SessionId` -SessionId presente nas mensagens.<br/>`byte [] State` -Estado da sessão |
| Microsoft.Azure.ServiceBus.RenewSessionLock | [IMessageSession.RenewSessionLockAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.renewsessionlockasync) | `string SessionId` -SessionId presente nas mensagens. |
| Microsoft.Azure.ServiceBus.Exception | qualquer API instrumentado| `Exception Exception` -Instância de exceção |

Cada evento, pode acessar `Activity.Current` que contém o contexto de operação atual.

#### <a name="logging-additional-properties"></a>Propriedades de registo adicionais

`Activity.Current` Fornece um contexto detalhado da operação atual e respetivas classes principais. Para obter mais informações, consulte [documentação de atividade](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) para obter mais detalhes.
Instrumentação do Service Bus fornece informações adicionais sobre o `Activity.Current.Tags` – elas se aplicam `MessageId` e `SessionId` sempre que estiverem disponíveis.

Atividades que controlam a "Receber", "Pré-visualização" e "ReceiveDeferred" evento também pode ter `RelatedTo` marca. Ele contém uma lista distinta de `Diagnostic-Id`(s) de mensagens que foram recebidas como resultado.
Essa operação pode resultar em várias mensagens não relacionadas a serem recebidos. Além disso, o `Diagnostic-Id` não se sabe quando inicia a operação, para que as operações de "Receive" poderiam ser correlacionadas com operações de "Processo" com esta etiqueta só. É útil ao analisar problemas de desempenho para verificar a quantidade de tempo necessária para receber a mensagem.

É de maneira eficiente de etiquetas de registo iterar sobre os mesmos, para que adicionar etiquetas ao exemplo anterior é semelhante a 

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

#### <a name="filtering-and-sampling"></a>Filtragem e de amostragem

Em alguns casos, é desejável para iniciar sessão apenas uma parte dos eventos para reduzir o consumo de armazenamento ou a sobrecarga de desempenho. Pode iniciar eventos 'Stop' apenas (como no exemplo anterior) ou a percentagem de exemplo dos eventos. 
`DiagnosticSource` fornecer a forma de conseguir com `IsEnabled` predicado. Para obter mais informações, consulte [filtragem baseada em contexto na DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#context-based-filtering).

`IsEnabled` pode ser chamado várias vezes para uma única operação minimizar o impacto no desempenho.

`IsEnabled` denomina-se na seguinte sequência:

1. `IsEnabled(<OperationName>, string entity, null)` Por exemplo, `IsEnabled("Microsoft.Azure.ServiceBus.Send", "MyQueue1")`. Tenha em atenção de que não existe 'Start' ou 'Stop' no final. Usá-lo para filtrar operações específicas ou filas. Se o retorno de chamada retorna `false`, os eventos para a operação não são enviados

   * As operações de "Processo" e "ProcessSession", também receberá `IsEnabled(<OperationName>, string entity, Activity activity)` retorno de chamada. Usá-lo para filtrar eventos com base em `activity.Id` ou propriedades de etiquetas.
  
2. `IsEnabled(<OperationName>.Start)` Por exemplo, `IsEnabled("Microsoft.Azure.ServiceBus.Send.Start")`. Verifica se o evento de "Iniciar" deve ser disparado. O resultado afeta apenas o evento de "Iniciar", mas ainda mais instrumentação não dependem dele.

Não existe nenhum `IsEnabled` evento 'Stop'.

Se algum resultado da operação é a exceção, `IsEnabled("Microsoft.Azure.ServiceBus.Exception")` é chamado. Apenas pode subscrever a eventos de 'exceção do ' e impedir que o resto da instrumentação. Neste caso, ainda terá de lidar com essas exceções. Uma vez que outros tipos de instrumentação é desabilitado, não espere contexto de rastreio para o fluxo com as mensagens do consumidor para o produtor.

Pode usar `IsEnabled` também implementar estratégias de amostragem. Amostragem com base na `Activity.Id` ou `Activity.RootId` garante a amostragem consistente em avaliar atentamente todos os (desde que ele é propagado pelo sistema de rastreamento ou por seu próprio código).

Na presença de vários `DiagnosticSource` serviços de escuta para a mesma origem, é o suficiente para apenas um serviço de escuta aceitar o evento, portanto, `IsEnabled` não é garantido que serão chamados,

## <a name="next-steps"></a>Passos Seguintes

* [Correlação de informações da aplicação](../azure-monitor/app/correlation.md)
* [Monitorizar dependências do Application Insights](../azure-monitor/app/asp-net-dependencies.md) para ver se REST, SQL ou outros recursos externos estão a atrasar.
* [Acompanhar operações personalizadas com o .NET SDK do Application Insights](../azure-monitor/app/custom-operations-tracking.md)
