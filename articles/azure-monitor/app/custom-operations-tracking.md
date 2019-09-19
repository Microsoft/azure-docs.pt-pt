---
title: Acompanhar operações personalizadas com o Aplicativo Azure SDK do .NET do insights | Microsoft Docs
description: Controlando operações personalizadas com o SDK do .NET do Aplicativo Azure insights
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/30/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: d966ff3bc00d5190ebc163d4f4bfa35ba73d21ab
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71087663"
---
# <a name="track-custom-operations-with-application-insights-net-sdk"></a>Acompanhar operações personalizadas com Application Insights SDK do .NET

Aplicativo Azure os SDKs do insights rastreiam automaticamente as solicitações HTTP de entrada e chamadas para serviços dependentes, como solicitações HTTP e consultas SQL. O acompanhamento e a correlação de solicitações e dependências proporcionam visibilidade da capacidade de resposta e da confiabilidade do aplicativo inteiro em todos os microserviços que combinam esse aplicativo. 

Há uma classe de padrões de aplicativo que não pode ser suportada genericamente. O monitoramento adequado de tais padrões requer a instrumentação de código manual. Este artigo aborda alguns padrões que podem exigir a instrumentação manual, como o processamento de filas personalizado e a execução de tarefas em segundo plano de execução longa.

Este documento fornece orientação sobre como controlar operações personalizadas com o SDK do Application Insights. Esta documentação é relevante para:

- Application Insights para .NET (também conhecido como SDK base) versão 2.4 +.
- Application Insights para aplicativos Web (executando ASP.NET) versão 2.4 +.
- Application Insights para ASP.NET Core versão 2.1 +.

## <a name="overview"></a>Descrição geral
Uma operação é uma parte lógica do trabalho executada por um aplicativo. Ele tem um nome, uma hora de início, uma duração, um resultado e um contexto de execução, como nome de usuário, propriedades e resultado. Se A operação A foi iniciada pela operação B, A operação B é definida como um pai para um. Uma operação pode ter apenas um pai, mas pode ter muitas operações filhas. Para obter mais informações sobre a correlação de operações e telemetria, consulte [correlação de telemetria do aplicativo Azure insights](correlation.md).

No SDK Application Insights .NET, a operação é descrita pela classe abstrata [OperationTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/Extensibility/Implementation/OperationTelemetry.cs) e seus descendentes [RequestTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/DataContracts/RequestTelemetry.cs) e [DependencyTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/DataContracts/DependencyTelemetry.cs).

## <a name="incoming-operations-tracking"></a>Acompanhamento de operações de entrada 
O SDK do Application Insights Web coleta automaticamente solicitações HTTP para aplicativos ASP.NET executados em um pipeline do IIS e todos os aplicativos ASP.NET Core. Há soluções com suporte da Comunidade para outras plataformas e estruturas. No entanto, se o aplicativo não tiver suporte de nenhuma das soluções padrão ou com suporte da Comunidade, você poderá instrumentá-lo manualmente.

Outro exemplo que requer acompanhamento personalizado é o trabalhador que recebe itens da fila. Para algumas filas, a chamada para adicionar uma mensagem a essa fila é controlada como uma dependência. No entanto, a operação de alto nível que descreve o processamento de mensagens não é coletada automaticamente.

Vamos ver como essas operações poderiam ser rastreadas.

Em um alto nível, a tarefa é criar `RequestTelemetry` e definir propriedades conhecidas. Depois que a operação for concluída, você acompanhará a telemetria. O exemplo a seguir demonstra essa tarefa.

### <a name="http-request-in-owin-self-hosted-app"></a>Solicitação HTTP no aplicativo Owin auto-hospedado
Neste exemplo, o contexto de rastreamento é propagado de acordo com o [protocolo http para correlação](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md). Você deve esperar receber cabeçalhos descritos lá.

```csharp
public class ApplicationInsightsMiddleware : OwinMiddleware
{
    // you may create a new TelemetryConfiguration instance, reuse one you already have
    // or fetch the instance created by Application Insights SDK.
    private readonly TelemetryConfiguration telemetryConfiguration = TelemetryConfiguration.CreateDefault();
    private readonly TelemetryClient telemetryClient = new TelemetryClient(telemetryConfiguration);
    
    public ApplicationInsightsMiddleware(OwinMiddleware next) : base(next) {}

    public override async Task Invoke(IOwinContext context)
    {
        // Let's create and start RequestTelemetry.
        var requestTelemetry = new RequestTelemetry
        {
            Name = $"{context.Request.Method} {context.Request.Uri.GetLeftPart(UriPartial.Path)}"
        };

        // If there is a Request-Id received from the upstream service, set the telemetry context accordingly.
        if (context.Request.Headers.ContainsKey("Request-Id"))
        {
            var requestId = context.Request.Headers.Get("Request-Id");
            // Get the operation ID from the Request-Id (if you follow the HTTP Protocol for Correlation).
            requestTelemetry.Context.Operation.Id = GetOperationId(requestId);
            requestTelemetry.Context.Operation.ParentId = requestId;
        }

        // StartOperation is a helper method that allows correlation of 
        // current operations with nested operations/telemetry
        // and initializes start time and duration on telemetry items.
        var operation = telemetryClient.StartOperation(requestTelemetry);

        // Process the request.
        try
        {
            await Next.Invoke(context);
        }
        catch (Exception e)
        {
            requestTelemetry.Success = false;
            telemetryClient.TrackException(e);
            throw;
        }
        finally
        {
            // Update status code and success as appropriate.
            if (context.Response != null)
            {
                requestTelemetry.ResponseCode = context.Response.StatusCode.ToString();
                requestTelemetry.Success = context.Response.StatusCode >= 200 && context.Response.StatusCode <= 299;
            }
            else
            {
                requestTelemetry.Success = false;
            }

            // Now it's time to stop the operation (and track telemetry).
            telemetryClient.StopOperation(operation);
        }
    }
    
    public static string GetOperationId(string id)
    {
        // Returns the root ID from the '|' to the first '.' if any.
        int rootEnd = id.IndexOf('.');
        if (rootEnd < 0)
            rootEnd = id.Length;

        int rootStart = id[0] == '|' ? 1 : 0;
        return id.Substring(rootStart, rootEnd - rootStart);
    }
}
```

O protocolo http para correlação também declara o `Correlation-Context` cabeçalho. No entanto, ele é omitido aqui para simplificar.

## <a name="queue-instrumentation"></a>Instrumentação de fila
Embora haja um [contexto de rastreamento do W3C](https://www.w3.org/TR/trace-context/) e o [protocolo http para correlação](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md) para passar detalhes de correlação com a solicitação HTTP, cada protocolo de fila tem que definir como os mesmos detalhes são passados pela mensagem da fila. Alguns protocolos de fila (como AMQP) permitem passar metadados adicionais e alguns outros (como a fila de armazenamento do Azure) exigem que o contexto seja codificado na carga da mensagem.

> [!NOTE]
> * O **rastreamento entre componentes ainda não tem suporte para filas** Com o HTTP, se o produtor e o consumidor enviarem telemetria a diferentes recursos de Application Insights, a experiência de diagnóstico de transação e o mapa do aplicativo mostrarão as transações e o mapa de ponta a ponta. No caso de filas, isso ainda não é suportado. 

### <a name="service-bus-queue"></a>Fila do Service Bus
Application Insights rastreia chamadas de mensagens do barramento de serviço com o novo [cliente do ServiceBus Microsoft Azure para .net](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/) versão 3.0.0 e superior.
Se você usar o [padrão de manipulador de mensagens](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler) para processar mensagens, você concluiu: todas as chamadas do barramento de serviço feitas pelo serviço são automaticamente rastreadas e correlacionadas com outros itens de telemetria. Consulte o [rastreamento de cliente do barramento de serviço com o Microsoft Application insights](../../service-bus-messaging/service-bus-end-to-end-tracing.md) se você processar mensagens manualmente.

Se você usar o pacote [WindowsAzure. ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) , leia mais-os exemplos a seguir demonstram como rastrear (e correlacionar) chamadas para o barramento de serviço como a fila do barramento de serviço usa o protocolo AMQP e Application insights não rastreia a fila automaticamente das.
Os identificadores de correlação são passados nas propriedades da mensagem.

#### <a name="enqueue"></a>Enfileirar

```csharp
public async Task Enqueue(string payload)
{
    // StartOperation is a helper method that initializes the telemetry item
    // and allows correlation of this operation with its parent and children.
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queueName);
    
    operation.Telemetry.Type = "Azure Service Bus";
    operation.Telemetry.Data = "Enqueue " + queueName;

    var message = new BrokeredMessage(payload);
    // Service Bus queue allows the property bag to pass along with the message.
    // We will use them to pass our correlation identifiers (and other context)
    // to the consumer.
    message.Properties.Add("ParentId", operation.Telemetry.Id);
    message.Properties.Add("RootId", operation.Telemetry.Context.Operation.Id);

    try
    {
        await queue.SendAsync(message);
        
        // Set operation.Telemetry Success and ResponseCode here.
        operation.Telemetry.Success = true;
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        // Set operation.Telemetry Success and ResponseCode here.
        operation.Telemetry.Success = false;
        throw;
    }
    finally
    {
        telemetryClient.StopOperation(operation);
    }
}
```

#### <a name="process"></a>Processo
```csharp
public async Task Process(BrokeredMessage message)
{
    // After the message is taken from the queue, create RequestTelemetry to track its processing.
    // It might also make sense to get the name from the message.
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "process " + queueName };

    var rootId = message.Properties["RootId"].ToString();
    var parentId = message.Properties["ParentId"].ToString();
    // Get the operation ID from the Request-Id (if you follow the HTTP Protocol for Correlation).
    requestTelemetry.Context.Operation.Id = rootId;
    requestTelemetry.Context.Operation.ParentId = parentId;

    var operation = telemetryClient.StartOperation(requestTelemetry);

    try
    {
        await ProcessMessage();
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        throw;
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}
```

### <a name="azure-storage-queue"></a>Fila de armazenamento do Azure
O exemplo a seguir mostra como controlar as operações de [fila do armazenamento do Azure](../../storage/queues/storage-dotnet-how-to-use-queues.md) e correlacionar a telemetria entre o produtor, o consumidor e o armazenamento do Azure. 

A fila de armazenamento tem uma API HTTP. Todas as chamadas para a fila são rastreadas pelo coletor de dependência Application Insights para solicitações HTTP.
Ele é configurado por padrão em aplicativos ASP.NET e ASP.NET Core, com outros tipos de aplicativo, você pode consultar a [documentação de aplicativos de console](../../azure-monitor/app/console.md)

Você também pode querer correlacionar a ID da operação de Application Insights com a ID de solicitação de armazenamento. Para obter informações sobre como definir e obter um cliente de solicitação de armazenamento e uma ID de solicitação do servidor, consulte [monitorar, diagnosticar e solucionar problemas do armazenamento do Azure](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#end-to-end-tracing).

#### <a name="enqueue"></a>Enfileirar
Como as filas de armazenamento dão suporte à API HTTP, todas as operações com a fila são automaticamente controladas pelo Application Insights. Em muitos casos, essa instrumentação deve ser suficiente. No entanto, para correlacionar rastreamentos no lado do consumidor com rastreamentos de produtor, você deve passar um contexto de correlação de forma semelhante a como fazemos isso no protocolo HTTP para correlação. 

Este exemplo mostra como acompanhar a `Enqueue` operação. Pode:

 - **Correlacione as repetições (se houver)** : Todos eles têm um pai comum que é a `Enqueue` operação. Caso contrário, eles serão rastreados como filhos da solicitação de entrada. Se houver várias solicitações lógicas para a fila, talvez seja difícil descobrir qual chamada resultou em repetições.
 - **Correlacione os logs de armazenamento (se e quando necessário)** : Eles estão correlacionados com a telemetria de Application Insights.

A `Enqueue` operação é o filho de uma operação pai (por exemplo, uma solicitação HTTP de entrada). A chamada de dependência http é o filho da `Enqueue` operação e o neto da solicitação de entrada:

```csharp
public async Task Enqueue(CloudQueue queue, string message)
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queue.Name);
    operation.Telemetry.Type = "Azure queue";
    operation.Telemetry.Data = "Enqueue " + queue.Name;

    // MessagePayload represents your custom message and also serializes correlation identifiers into payload.
    // For example, if you choose to pass payload serialized to JSON, it might look like
    // {'RootId' : 'some-id', 'ParentId' : '|some-id.1.2.3.', 'message' : 'your message to process'}
    var jsonPayload = JsonConvert.SerializeObject(new MessagePayload
    {
        RootId = operation.Telemetry.Context.Operation.Id,
        ParentId = operation.Telemetry.Id,
        Payload = message
    });
    
    CloudQueueMessage queueMessage = new CloudQueueMessage(jsonPayload);

    // Add operation.Telemetry.Id to the OperationContext to correlate Storage logs and Application Insights telemetry.
    OperationContext context = new OperationContext { ClientRequestID = operation.Telemetry.Id};

    try
    {
        await queue.AddMessageAsync(queueMessage, null, null, new QueueRequestOptions(), context);
    }
    catch (StorageException e)
    {
        operation.Telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        operation.Telemetry.Success = false;
        operation.Telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
        telemetryClient.TrackException(e);
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}  
```

Para reduzir a quantidade de telemetria que seu aplicativo relata ou se você não deseja controlar `Enqueue` a operação por outros motivos, use `Activity` a API diretamente:

- Crie (e inicie) um novo `Activity` em vez de iniciar a operação de Application insights. Você *não* precisa atribuir nenhuma propriedade a ele, exceto o nome da operação.
- Serialize `yourActivity.Id` na carga da mensagem em vez `operation.Telemetry.Id`de. Você também pode usar `Activity.Current.Id`o.


#### <a name="dequeue"></a>Remoção da fila
Da mesma forma, uma solicitação HTTP real para a fila de armazenamento é automaticamente controlada por Application insights. `Enqueue` No entanto `Enqueue` , a operação supostamente acontece no contexto pai, como um contexto de solicitação de entrada. Os SDKs de Application Insights correlacionam automaticamente tal operação (e sua parte HTTP) com a solicitação pai e outra telemetria relatada no mesmo escopo.

A `Dequeue` operação é complicada. O SDK do Application Insights rastreia automaticamente as solicitações HTTP. No entanto, ele não sabe o contexto de correlação até que a mensagem seja analisada. Não é possível correlacionar a solicitação HTTP para obter a mensagem com o restante da telemetria, especialmente quando mais de uma mensagem é recebida.

```csharp
public async Task<MessagePayload> Dequeue(CloudQueue queue)
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("dequeue " + queue.Name);
    operation.Telemetry.Type = "Azure queue";
    operation.Telemetry.Data = "Dequeue " + queue.Name;
    
    try
    {
        var message = await queue.GetMessageAsync();
    }
    catch (StorageException e)
    {
        telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        telemetry.Success = false;
        telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
        telemetryClient.TrackException(e);
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }

    return null;
}
```

#### <a name="process"></a>Processo

No exemplo a seguir, uma mensagem de entrada é rastreada de maneira semelhante à solicitação HTTP de entrada:

```csharp
public async Task Process(MessagePayload message)
{
    // After the message is dequeued from the queue, create RequestTelemetry to track its processing.
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "process " + queueName };
    
    // It might also make sense to get the name from the message.
    requestTelemetry.Context.Operation.Id = message.RootId;
    requestTelemetry.Context.Operation.ParentId = message.ParentId;

    var operation = telemetryClient.StartOperation(requestTelemetry);

    try
    {
        await ProcessMessage();
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        throw;
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}
```

Da mesma forma, outras operações de fila podem ser instrumentadas. Uma operação de inspeção deve ser instrumentada de forma semelhante à de uma operação de remoção da fila. A instrumentação de operações de gerenciamento de filas não é necessária. Application Insights rastreia operações como HTTP e, na maioria dos casos, é suficiente.

Quando você instrumenta a exclusão de mensagens, certifique-se de definir os identificadores de operação (correlação). Como alternativa, você pode usar a `Activity` API. Em seguida, você não precisa definir identificadores de operação nos itens de telemetria porque Application Insights SDK faz isso para você:

- Crie um novo `Activity` depois de obter um item da fila.
- Use `Activity.SetParentId(message.ParentId)` para correlacionar os logs de produtor e de consumidor.
- Inicie o `Activity`.
- Acompanhe as operações de remoção da fila, processo e exclusão `Start/StopOperation` usando auxiliares. Faça isso no mesmo fluxo de controle assíncrono (contexto de execução). Dessa forma, eles são correlacionados corretamente.
- Pare o `Activity`.
- Use `Start/StopOperation`ou chame `Track` a telemetria manualmente.

### <a name="dependency-types"></a>Tipos de dependência

Application Insights usa o tipo de dependência para experiências de interface do usuário do Personalizar. Para filas, ele reconhece os seguintes tipos `DependencyTelemetry` de que melhoram a [experiência de diagnóstico de transação](/azure/azure-monitor/app/transaction-diagnostics):
- `Azure queue`para filas do armazenamento do Azure
- `Azure Event Hubs`para hubs de eventos do Azure
- `Azure Service Bus`para o barramento de serviço do Azure

### <a name="batch-processing"></a>Processamento em lotes
Com algumas filas, você pode remover a fila de várias mensagens com uma solicitação. O processamento dessas mensagens é supostamente independente e pertence às diferentes operações lógicas. Não é possível correlacionar a `Dequeue` operação a uma mensagem específica que está sendo processada.

Cada mensagem deve ser processada em seu próprio fluxo de controle assíncrono. Para obter mais informações, consulte a seção [acompanhamento de dependências de saída](#outgoing-dependencies-tracking) .

## <a name="long-running-background-tasks"></a>Tarefas em segundo plano de execução longa

Alguns aplicativos iniciam operações de longa execução que podem ser causadas por solicitações do usuário. Da perspectiva de rastreamento/instrumentação, não é diferente da instrumentação de solicitação ou de dependência: 

```csharp
async Task BackgroundTask()
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>(taskName);
    operation.Telemetry.Type = "Background";
    try
    {
        int progress = 0;
        while (progress < 100)
        {
            // Process the task.
            telemetryClient.TrackTrace($"done {progress++}%");
        }
        // Update status code and success as appropriate.
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        // Update status code and success as appropriate.
        throw;
    }
    finally
    {
        telemetryClient.StopOperation(operation);
    }
}
```

Neste exemplo, `telemetryClient.StartOperation` cria `DependencyTelemetry` e preenche o contexto de correlação. Digamos que você tenha uma operação pai que foi criada por solicitações de entrada que agendaram a operação. Desde que o comece no mesmo fluxo de controle assíncrono que uma solicitação de entrada, ele está correlacionado a essa operação pai. `BackgroundTask` `BackgroundTask`e todos os itens de telemetria aninhados são automaticamente correlacionados com a solicitação que o causou, mesmo após o término da solicitação.

Quando a tarefa é iniciada a partir do thread em segundo plano que`Activity`não tem nenhuma operação ( `BackgroundTask` ) associada a ela, não tem nenhum pai. No entanto, ele pode ter operações aninhadas. Todos os itens de telemetria relatados da tarefa estão `DependencyTelemetry` correlacionados `BackgroundTask`ao criado em.

## <a name="outgoing-dependencies-tracking"></a>Rastreamento de dependências de saída
Você pode acompanhar seu próprio tipo de dependência ou uma operação que não tem suporte pelo Application Insights.

O `Enqueue` método na fila do barramento de serviço ou na fila de armazenamento pode servir como exemplos para esse acompanhamento personalizado.

A abordagem geral para o acompanhamento de dependência personalizado é:

- Chame o `TelemetryClient.StartOperation` método (Extension) que preenche as `DependencyTelemetry` propriedades que são necessárias para a correlação e outras propriedades (carimbo de data/hora de início, duração).
- Defina outras propriedades personalizadas no `DependencyTelemetry`, como o nome e qualquer outro contexto necessário.
- Faça uma chamada de dependência e aguarde.
- Pare a operação com `StopOperation` quando ela for concluída.
- Tratar exceções.

```csharp
public async Task RunMyTaskAsync()
{
    using (var operation = telemetryClient.StartOperation<DependencyTelemetry>("task 1"))
    {
        try 
        {
            var myTask = await StartMyTaskAsync();
            // Update status code and success as appropriate.
        }
        catch(...) 
        {
            // Update status code and success as appropriate.
        }
    }
}
```

A operação de descarte faz com que a operação seja interrompida, portanto, você `StopOperation`pode fazer isso em vez de chamar.

*Aviso*: em alguns casos, uma exceção informada pode [impedir](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/try-finally) `finally` ser chamada para que as operações não sejam rastreadas.

### <a name="parallel-operations-processing-and-tracking"></a>Processamento e acompanhamento de operações paralelas

`StopOperation`interrompe apenas a operação que foi iniciada. Se a operação em execução atual não corresponder à que você deseja parar, `StopOperation` o não fará nada. Essa situação pode acontecer se você iniciar várias operações em paralelo no mesmo contexto de execução:

```csharp
var firstOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
var firstTask = RunMyTaskAsync();

var secondOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 2");
var secondTask = RunMyTaskAsync();

await firstTask;

// FAILURE!!! This will do nothing and will not report telemetry for the first operation
// as currently secondOperation is active.
telemetryClient.StopOperation(firstOperation); 

await secondTask;
```

Certifique-se de sempre `StartOperation` chamar e processar a operação no mesmo método **assíncrono** para isolar as operações em execução em paralelo. Se a operação for síncrona (ou não assíncrona), empacote o processo `Task.Run`e controle com:

```csharp
public void RunMyTask(string name)
{
    using (var operation = telemetryClient.StartOperation<DependencyTelemetry>(name))
    {
        Process();
        // Update status code and success as appropriate.
    }
}

public async Task RunAllTasks()
{
    var task1 = Task.Run(() => RunMyTask("task 1"));
    var task2 = Task.Run(() => RunMyTask("task 2"));
    
    await Task.WhenAll(task1, task2);
}
```

## <a name="applicationinsights-operations-vs-systemdiagnosticsactivity"></a>Operações de ApplicationInsights vs System. Diagnostics. Activity
`System.Diagnostics.Activity`representa o contexto de rastreamento distribuído e é usado por estruturas e bibliotecas para criar e propagar o contexto dentro e fora do processo e correlacionar os itens de telemetria. A atividade funciona junto `System.Diagnostics.DiagnosticSource` com o mecanismo de notificação entre a estrutura/biblioteca para notificar sobre eventos interessantes (solicitações de entrada ou de saída, exceções, etc.).

As atividades são cidadãos de primeira classe no Application insights, e a coleção de solicitações e a dependência automática depende muito `DiagnosticSource` delas junto com os eventos. Se você criar atividade em seu aplicativo, ele não resultará na criação de Application Insights telemetria. Application Insights precisa receber eventos de diagnóstico e conhecer os nomes de eventos e as cargas para converter a atividade na telemetria.

Cada operação de Application insights (solicitação ou dependência) `Activity` envolve- `StartOperation` se-quando é chamado, ele cria a atividade abaixo. `StartOperation`é a maneira recomendada para controlar a telemetrias de solicitação ou dependência manualmente e garantir que tudo esteja correlacionado.

## <a name="next-steps"></a>Passos Seguintes

- Aprenda os conceitos básicos da [correlação de telemetria](correlation.md) no Application insights.
- Confira como os dados correlacionados alimentam a [experiência de diagnóstico de transação](../../azure-monitor/app/transaction-diagnostics.md) e o mapa do [aplicativo](../../azure-monitor/app/app-map.md).
- Consulte o [modelo de dados](../../azure-monitor/app/data-model.md) para tipos de Application insights e modelo de dados.
- Relatar [eventos e métricas](../../azure-monitor/app/api-custom-events-metrics.md) personalizados para Application insights.
- Confira a [configuração](configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet) padrão da coleção de propriedades de contexto.
- Verifique o [Guia do usuário System. Diagnostics. Activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) para ver como correlacionamos a telemetria.
