---
title: Acompanhar operações personalizadas com o SDK de .NET do Azure Application Insights | Documentos da Microsoft
description: Controlar operações personalizadas com o SDK de .NET do Azure Application Insights
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
ms.openlocfilehash: 2c33c481d96a9edecc6360a9a91c095c2bca220b
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798337"
---
# <a name="track-custom-operations-with-application-insights-net-sdk"></a>Acompanhar operações personalizadas com o .NET SDK do Application Insights

SDKs do Application Insights do Azure regista automaticamente a entrada de pedidos de HTTP e chamadas para serviços dependentes, tais como pedidos de HTTP e consultas SQL. Controlo e a correlação de pedidos e dependências dão-lhe visibilidade para a capacidade de resposta e a confiabilidade do aplicativo inteiro em todos os microsserviços que combinam esta aplicação. 

Há uma classe de padrões de aplicação que não têm suporte genericamente. Monitorização adequada de tais padrões requer instrumentação manuais de código. Este artigo aborda alguns padrões que possam exigir a instrumentação manual, como a fila personalizada de processamento e executar tarefas em segundo plano de execução longa.

Este documento fornece orientações sobre como controlar operações personalizadas com o Application Insights SDK. Esta documentação é relevante para:

- O Application Insights para a versão (também conhecida como Base SDK) .NET 2.4 +.
- O Application Insights para a versão de aplicativos (ASP.NET executados) web 2.4 +.
- O Application Insights para ASP.NET Core versão 2.1 +.

## <a name="overview"></a>Descrição geral
Uma operação é um lógico trabalho executado por uma aplicação. Ele tem um nome, a hora de início, duração, resultado e um contexto de execução, como resultado, propriedades e nome de utilizador. Se a operação A foi iniciada pela operação B, as operação B, em seguida, é definida como um elemento principal para A. Uma operação pode ter apenas um elemento principal, mas ele pode ter muitas operações de subordinados. Para obter mais informações sobre operações e a correlação de telemetria, consulte [correlação de telemetria do Application Insights do Azure](correlation.md).

No Application Insights SDK de .NET, a operação é descrita pela classe abstrata [OperationTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/Extensibility/Implementation/OperationTelemetry.cs) e seus descendentes [RequestTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/DataContracts/RequestTelemetry.cs) e [DependencyTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/DataContracts/DependencyTelemetry.cs).

## <a name="incoming-operations-tracking"></a>Operações de entrada de controlo 
Application Insights web SDK recolhe automaticamente pedidos HTTP para aplicativos ASP.NET executados num pipeline do IIS e todos os aplicativos do ASP.NET Core. Existem soluções de suporte da Comunidade para outras plataformas e estruturas. No entanto, se o aplicativo não é suportado por qualquer uma das soluções de suporte da Comunidade ou padrão, pode instrumentá-lo manualmente.

Outro exemplo que requer o controle personalizado é a função de trabalho que recebe os itens da fila. Para algumas filas, a chamada para adicionar uma mensagem a esta fila é controlada como uma dependência. No entanto, a operação de alto nível que descreve o processamento de mensagens não é automaticamente recolhida.

Vamos ver como essas operações podem ser controladas.

Num alto nível, a tarefa é criar `RequestTelemetry` e definir as propriedades conhecidas. Depois de concluída a operação, rastrear a telemetria. O exemplo seguinte demonstra esta tarefa.

### <a name="http-request-in-owin-self-hosted-app"></a>Pedido HTTP na aplicação de hospedagem interna do Owin
Neste exemplo, o contexto de rastreio é propagado de acordo com o [protocolo HTTP para correlação](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md). Deve esperar para receber os cabeçalhos que são descritos aqui.

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

O protocolo HTTP para correlação também declara a `Correlation-Context` cabeçalho. No entanto, for omitida aqui para manter a simplicidade.

## <a name="queue-instrumentation"></a>Instrumentação de fila
Embora haja [protocolo HTTP para correlação](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md) para passar os detalhes da correlação com o pedido HTTP, todos os protocolos da fila tem de definir a forma como os detalhes da mesmos são transmitidos a mensagem de fila. Alguns protocolos de fila (por exemplo, o AMQP) permitem a passagem de metadados adicionais e alguns outros (essa fila de armazenamento do Azure) exigem o contexto a ser codificada no payload de mensagem.

### <a name="service-bus-queue"></a>Fila do Service Bus
O Application Insights monitoriza a chamadas de mensagens do Service Bus com o novo [ServiceBus cliente do Microsoft Azure para .NET](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/) versão 3.0.0 e superior.
Se usar [padrão de manipulador de mensagem](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler) para processar mensagens, terminar: todas as chamadas de Service Bus feitas pelo seu serviço são automaticamente controladas e correlacionadas com outros itens de telemetria. Consulte a [rastreio com o Microsoft Application Insights de cliente do Service Bus](../../service-bus-messaging/service-bus-end-to-end-tracing.md) se manualmente a processar mensagens.

Se usar [windowsazure. Servicebus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) pacote, leia - exemplos a seguir demonstra como controlar (e correlacionar) chama para o Service Bus, como a fila do Service Bus utiliza o protocolo AMQP e não do Application Insights regista automaticamente a operações de fila.
Identificadores de correlação são transmitidas como as propriedades da mensagem.

#### <a name="enqueue"></a>Colocar em fila

```csharp
public async Task Enqueue(string payload)
{
    // StartOperation is a helper method that initializes the telemetry item
    // and allows correlation of this operation with its parent and children.
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queueName);
    operation.Telemetry.Type = "Queue";
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
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "Dequeue " + queueName };

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
O exemplo seguinte mostra como controlar os [fila de armazenamento do Azure](../../storage/queues/storage-dotnet-how-to-use-queues.md) operações e telemetria correlacionar entre o produtor, consumidor e armazenamento do Azure. 

A fila de armazenamento tem uma API de HTTP. Todas as chamadas para a fila são controladas pelo Recoletor de dependência do Application Insights para pedidos HTTP.
Ele está configurado por predefinição em aplicativos ASP.NET e ASP.NET Core, com outros tipos de aplicação, pode consultar [documentação das aplicações de consola](../../azure-monitor/app/console.md)

Também pode querer correlacionar o ID de operação do Application Insights com o ID de pedido de armazenamento. Para obter informações sobre como definir e obter um cliente de solicitação de armazenamento e um ID de pedido do servidor, consulte [monitorizar, diagnosticar e resolver problemas do armazenamento do Azure](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#end-to-end-tracing).

#### <a name="enqueue"></a>Colocar em fila
Uma vez que as filas de armazenamento suportam a API de HTTP, todas as operações com a fila são automaticamente controladas pelo Application Insights. Em muitos casos, essa instrumentação deve ser suficientes. No entanto, para correlacionar rastreios no lado do consumidor com rastreios de produtor, tem de passar algum contexto de correlação da mesma forma como Vamos fazê-lo no protocolo HTTP para a correlação. 

Este exemplo mostra como controlar o `Enqueue` operação. Pode:

 - **Correlacionar as repetições (se houver)** : Todos eles têm um comum principal que tem o `Enqueue` operação. Caso contrário, estão registados como filhos do pedido a receber. Se existirem várias solicitações de lógicas para a fila, poderá ser difícil encontrar qual invocação resultou nas repetições.
 - **Correlacionar os registos de armazenamento (se sejam e quando necessário)** : Eles estão correlacionados com telemetria do Application Insights.

O `Enqueue` operação é o filho de uma operação de principal (por exemplo, uma solicitação HTTP recebida). A chamada de dependência HTTP é o filho do `Enqueue` operação e grandchild do pedido a receber:

```csharp
public async Task Enqueue(CloudQueue queue, string message)
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queue.Name);
    operation.Telemetry.Type = "Queue";
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

Para reduzir a quantidade de telemetria de seu aplicativo relatórios ou se não quiser controlar a `Enqueue` operação por outros motivos, utilize o `Activity` API diretamente:

- Criar (e começar a) um novo `Activity` em vez de iniciar a operação do Application Insights. Faz *não* tem de atribuir todas as propriedades no mesmo, exceto o nome da operação.
- Serializar `yourActivity.Id` para a carga da mensagem em vez de `operation.Telemetry.Id`. Também pode utilizar `Activity.Current.Id`.


#### <a name="dequeue"></a>Remover da fila
Da mesma forma para `Enqueue`, uma solicitação HTTP real para a fila de armazenamento é automaticamente controlada pelo Application Insights. No entanto, o `Enqueue` operação supostamente acontece no contexto principal, por exemplo, um contexto de solicitação de entrada. SDKs do Application Insights correlacionar automaticamente esse tipo de operação (e sua parte HTTP) com o pedido de principal e outra telemetria comunicado no mesmo âmbito.

O `Dequeue` operação é complicada. O Application Insights SDK controla automaticamente pedidos HTTP. No entanto, ele não sabe o contexto de correlação até que a mensagem é analisada. Não é possível correlacionar a solicitação HTTP para obter a mensagem com o restante de telemetria.

Em muitos casos, poderá ser útil correlacionar a solicitação HTTP para a fila com outros rastreios também. O exemplo seguinte demonstra como fazer isso:

```csharp
public async Task<MessagePayload> Dequeue(CloudQueue queue)
{
    var telemetry = new DependencyTelemetry
    {
        Type = "Queue",
        Name = "Dequeue " + queue.Name
    };

    telemetry.Start();

    try
    {
        var message = await queue.GetMessageAsync();

        if (message != null)
        {
            var payload = JsonConvert.DeserializeObject<MessagePayload>(message.AsString);

            // If there is a message, we want to correlate the Dequeue operation with processing.
            // However, we will only know what correlation ID to use after we get it from the message,
            // so we will report telemetry after we know the IDs.
            telemetry.Context.Operation.Id = payload.RootId;
            telemetry.Context.Operation.ParentId = payload.ParentId;

            // Delete the message.
            return payload;
        }
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
        telemetry.Stop();
        telemetryClient.Track(telemetry);
    }

    return null;
}
```

#### <a name="process"></a>Processo

No exemplo a seguir, uma mensagem de entrada é controlada-se da mesma forma de forma a solicitação HTTP recebida:

```csharp
public async Task Process(MessagePayload message)
{
    // After the message is dequeued from the queue, create RequestTelemetry to track its processing.
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "Dequeue " + queueName };
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

Da mesma forma, podem ser instrumentadas outras operações de fila. Uma operação de pré-visualização deve instrumentada, de forma semelhante, como uma operação de dequeue. Instrumentar operações de gestão de fila não é necessário. O Application Insights controla as operações, como HTTP e, na maioria dos casos, é suficiente.

Ao instrumentar exclusão da mensagem, certifique-se de que definir a operação de identificadores (correlação). Em alternativa, pode utilizar o `Activity` API. Em seguida, não precisa definir identificadores de operação nos itens de telemetria, porque o SDK do Application Insights faz tudo por si:

- Criar um novo `Activity` depois que tem um item da fila.
- Utilize `Activity.SetParentId(message.ParentId)` para correlacionar os registos de produtor e de consumidores.
- Iniciar o `Activity`.
- Roteiro da fila, processar e eliminar operações com `Start/StopOperation` auxiliares. Fazê-lo a partir do mesmo fluxo de controle assíncrono (contexto de execução). Dessa forma, eles correlacionado estiverem corretamente.
- Parar o `Activity`.
- Uso `Start/StopOperation`, ou ligue `Track` telemetria manualmente.

### <a name="batch-processing"></a>Processamento em lotes
Com algumas filas, pode remover da fila várias mensagens com uma solicitação. Essas mensagens de processamento é supostamente independente e pertence a das várias operações de lógicas. Neste caso, não é possível correlacionar a `Dequeue` operação para processamento de mensagens específico.

Cada mensagem deve ser processada no seu próprio fluxo de controle assíncrono. Para obter mais informações, consulte a [dependências de envio de controlo](#outgoing-dependencies-tracking) secção.

## <a name="long-running-background-tasks"></a>Tarefas em segundo plano de execução longa

Alguns aplicativos iniciar as operações de longa execução que podem ser causadas por pedidos de utilizador. Da perspectiva do/instrumentação de rastreamento, não é diferente da instrumentação pedido ou dependência: 

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

Neste exemplo, `telemetryClient.StartOperation` cria `DependencyTelemetry` e preenche o contexto de correlação. Digamos que tem uma operação de pai que foi criada por pedidos de entrada que agendada a operação. Enquanto `BackgroundTask` começa da mesma assíncrona controla o fluxo como uma solicitação de entrada, ele é correlacionado com essa operação principal. `BackgroundTask` e todos os itens de telemetria aninhada automaticamente estão correlacionados com o pedido que a sua causa, mesmo depois de termina o pedido.

Quando a tarefa começa a partir do thread de segundo plano que não tem qualquer operação (`Activity`) associado ao mesmo, `BackgroundTask` não tem qualquer principal. No entanto, ele pode ter aninhadas operações. Todos os itens de telemetria reportados a partir da tarefa são correlacionados para o `DependencyTelemetry` criado no `BackgroundTask`.

## <a name="outgoing-dependencies-tracking"></a>Dependências de saída de controlo
Pode controlar o seu próprio tipo de dependência ou uma operação que não é suportada pelo Application Insights.

O `Enqueue` método na fila do Service Bus ou a fila de armazenamento pode servir de exemplos para esse controle personalizado.

A abordagem geral para controlo de dependência personalizadas é:

- Chamar o `TelemetryClient.StartOperation` método (extensão), que preenche a `DependencyTelemetry` propriedades que são necessários para correlação e algumas outras propriedades (hora de início carimbo, duração).
- Definir outras propriedades personalizadas a `DependencyTelemetry`, como o nome e quaisquer outro contexto que precisa.
- Fazer uma dependência chamar e aguarde pela mesma.
- Parar a operação com `StopOperation` quando estiver concluída.
- Lidar com exceções.

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

Descartando a operação faz com que a operação ser parado, portanto, pode fazê-lo em vez de chamar `StopOperation`.

*Aviso*: em alguns casos poderá exceção unhanded [impedir](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/try-finally) `finally` a ser chamado para operações não podem ser controladas.

### <a name="parallel-operations-processing-and-tracking"></a>Processamento de operações paralelas e controlo

`StopOperation` só para a operação que foi iniciada. Se a operação de execução atual não corresponde ao que pretende parar, `StopOperation` não faz nada. Esta situação pode acontecer se iniciar várias operações em paralelo no mesmo contexto de execução:

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

Certifique-se de que sempre chamar `StartOperation` e processar a operação na mesma **async** método para isolar as operações em execução em paralelo. Se a operação é síncrona (ou não async), encapsular o processo e controlar com `Task.Run`:

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

## <a name="next-steps"></a>Passos Seguintes

- Conheça as noções básicas [correlação de telemetria](correlation.md) no Application Insights.
- Consulte a [modelo de dados](../../azure-monitor/app/data-model.md) para o modelo de tipos e dados do Application Insights.
- Relatório personalizado [métricas e eventos](../../azure-monitor/app/api-custom-events-metrics.md) para o Application Insights.
- Confira standard [configuração](configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet) para a coleção de propriedades de contexto.
- Verifique os [Guia do usuário System.Diagnostics.Activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) para ver como podemos correlacionar a telemetria.
