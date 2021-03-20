---
title: Acompanhe as operações personalizadas com a Azure Application Insights .NET SDK
description: Rastreio de operações personalizadas com Azure Application Insights .NET SDK
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 11/26/2019
ms.reviewer: sergkanz
ms.openlocfilehash: 42a5318325f9961483465357403089755feb130d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88933312"
---
# <a name="track-custom-operations-with-application-insights-net-sdk"></a>Acompanhe as operações personalizadas com o Application Insights .NET SDK

A azure Application Insights SDKs rastreia automaticamente os pedidos e chamadas HTTP recebidas para serviços dependentes, tais como pedidos HTTP e consultas SQL. O rastreio e a correlação de pedidos e dependências dão-lhe visibilidade para toda a capacidade de resposta e fiabilidade da aplicação em todos os microserviços que combinam esta aplicação. 

Há uma classe de padrões de aplicação que não podem ser suportados genericamente. A monitorização adequada de tais padrões requer uma instrumentação manual do código. Este artigo abrange alguns padrões que podem exigir instrumentação manual, como o processamento personalizado da fila e tarefas de fundo de longa duração.

Este documento fornece orientações sobre como rastrear as operações personalizadas com o Application Insights SDK. Esta documentação é relevante para:

- Aplicações Insights para .NET (também conhecido como Base SDK) versão 2.4+.
- Application Insights para aplicações web (executando ASP.NET) versão 2.4+.
- Insights de aplicação para ASP.NET versão Core 2.1+.

## <a name="overview"></a>Descrição Geral
Uma operação é uma peça lógica de trabalho gerida por uma aplicação. Tem um nome, hora de início, duração, resultado, e um contexto de execução como nome de utilizador, propriedades e resultado. Se a operação A foi iniciada pela operação B, então a operação B será definida como pai para A. Uma operação pode ter apenas um pai, mas pode ter muitas operações infantis. Para obter mais informações sobre operações e correlação de telemetria, consulte [a correlação de telemetria Azure Application Insights](correlation.md).

No Application Insights .NET SDK, a operação é descrita pela classe abstrata [OperationTelemetry](https://github.com/microsoft/ApplicationInsights-dotnet/blob/7633ae849edc826a8547745b6bf9f3174715d4bd/BASE/src/Microsoft.ApplicationInsights/Extensibility/Implementation/OperationTelemetry.cs) e seus descendentes [RequestTelemetry](https://github.com/microsoft/ApplicationInsights-dotnet/blob/7633ae849edc826a8547745b6bf9f3174715d4bd/BASE/src/Microsoft.ApplicationInsights/DataContracts/RequestTelemetry.cs) and [DependencyTelemetry](https://github.com/microsoft/ApplicationInsights-dotnet/blob/7633ae849edc826a8547745b6bf9f3174715d4bd/BASE/src/Microsoft.ApplicationInsights/DataContracts/DependencyTelemetry.cs).

## <a name="incoming-operations-tracking"></a>Rastreio de operações de entrada 
A Web SDK do Application Insights recolhe automaticamente pedidos HTTP para aplicações ASP.NET que funcionam num pipeline IIS e todas as aplicações core ASP.NET. Existem soluções apoiadas pela comunidade para outras plataformas e quadros. No entanto, se a aplicação não for suportada por nenhuma das soluções standard ou apoiadas pela comunidade, pode instrumentá-la manualmente.

Outro exemplo que requer rastreio personalizado é o trabalhador que recebe itens da fila. Para algumas filas, a chamada para adicionar uma mensagem a esta fila é rastreada como uma dependência. No entanto, a operação de alto nível que descreve o processamento de mensagens não é recolhida automaticamente.

Vamos ver como estas operações podem ser rastreadas.

A um nível elevado, a tarefa é criar `RequestTelemetry` e definir propriedades conhecidas. Depois da operação terminar, rastreia a telemetria. O exemplo que se segue demonstra esta tarefa.

### <a name="http-request-in-owin-self-hosted-app"></a>PEDIDO HTTP na app auto-hospedada da Owin
Neste exemplo, o contexto de rastreio é propagado de acordo com o [Protocolo HTTP para a Correlação](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md). Deve esperar receber cabeçalhos que são descritos lá.

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

O Protocolo HTTP para a Correlação também declara o `Correlation-Context` cabeçalho. No entanto, é omitido aqui para a simplicidade.

## <a name="queue-instrumentation"></a>Instrumentação de fila
Embora existam [O Contexto de Rastreio W3C](https://www.w3.org/TR/trace-context/) e Protocolo HTTP para [Correlação para](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md) passar detalhes de correlação com pedido HTTP, todos os protocolos de fila têm de definir como os mesmos detalhes são transmitidos ao longo da mensagem de fila. Alguns protocolos de fila (como AMQP) permitem a passagem de metadados adicionais e outros (tal Azure Storage Queue) exigem que o contexto seja codificado na carga útil da mensagem.

> [!NOTE]
> * **O rastreio de componentes cruzados ainda não é suportado para filas** Com HTTP, se o seu produtor e consumidor enviar telemetria para diferentes recursos de Insights de Aplicação, Experiência de Diagnóstico de Transações e Mapa de Aplicações mostram transações e mapa de mapas de ponta a ponta. No caso de filas, isto ainda não é suportado. 

### <a name="service-bus-queue"></a>Fila do Service Bus
Application Insights rastreia chamadas de mensagens de autocarro de serviço com o novo [Cliente Microsoft Azure ServiceBus para a](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/) versão .NET 3.0.0 e superior.
Se utilizar o [padrão do manipulador de mensagens](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler) para processar mensagens, está feito: todas as chamadas de Service Bus efetuadas pelo seu serviço são automaticamente rastreadas e correlacionadas com outros itens de telemetria. Consulte o rastreio do [cliente do Service Bus com o Microsoft Application Insights](../../service-bus-messaging/service-bus-end-to-end-tracing.md) se processar mensagens manualmente.

Se utilizar o pacote [WindowsAzure.ServiceBus,](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) leia mais - os exemplos que seguem demonstram como rastrear (e correlacionar) chamadas para o Service Bus, uma vez que a fila do Service Bus utiliza o protocolo AMQP e a Application Insights não rastreia automaticamente as operações de fila.
Os identificadores de correlação são passados nas propriedades da mensagem.

#### <a name="enqueue"></a>Enqueue

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

### <a name="azure-storage-queue"></a>Fila de armazenamento Azure
O exemplo a seguir mostra como rastrear as operações de fila do [Azure Storage](../../storage/queues/storage-dotnet-how-to-use-queues.md) e correlacionar a telemetria entre o produtor, o consumidor e o Azure Storage. 

A fila de armazenamento tem uma API HTTP. Todas as chamadas para a fila são rastreadas pelo Colecionador de Dependência de Insights de Aplicação para pedidos HTTP.
É configurado por padrão em aplicações ASP.NET e ASP.NET Core, com outros tipos de aplicação, pode consultar a documentação de aplicações de [consolas](./console.md)

Também pode querer correlacionar o ID de operação Application Insights com o ID do pedido de armazenamento. Para obter informações sobre como definir e obter um cliente de pedido de armazenamento e um ID de pedido de servidor, consulte [Monitor, diagnóstico e resolução de problemas Azure Storage](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#end-to-end-tracing).

#### <a name="enqueue"></a>Enqueue
Como as filas de armazenamento suportam a API HTTP, todas as operações com a fila são automaticamente rastreadas por Application Insights. Em muitos casos, esta instrumentação deve ser suficiente. No entanto, para correlacionar vestígios do lado do consumidor com os vestígios do produtor, deve passar algum contexto de correlação semelhante ao que o fazemos no Protocolo HTTP para a Correlação. 

Este exemplo mostra como rastrear a `Enqueue` operação. Pode:

 - **Correlacionar as recauchutagens (se houver)**: Todos eles têm um pai comum que é a `Enqueue` operação. Caso contrário, são seguidos como crianças do pedido de entrada. Se houver vários pedidos lógicos para a fila, pode ser difícil encontrar qual a chamada que resultou em retrações.
 - **Correlacionar os registos de armazenamento (se e quando necessário)**: Estão correlacionados com a telemetria Application Insights.

A `Enqueue` operação é filha de uma operação dos pais (por exemplo, um pedido HTTP de entrada). A chamada de dependência HTTP é o filho da `Enqueue` operação e o neto do pedido de entrada:

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

Para reduzir a quantidade de telemetria que os seus relatórios de aplicação ou se não quiser acompanhar a `Enqueue` operação por outras razões, utilize `Activity` a API diretamente:

- Criar (e iniciar) uma nova `Activity` em vez de iniciar a operação Application Insights. *Não* é necessário atribuir quaisquer propriedades, exceto o nome da operação.
- Serialize `yourActivity.Id` na carga útil da mensagem em vez de `operation.Telemetry.Id` . Também pode usar `Activity.Current.Id` .


#### <a name="dequeue"></a>Dequeue
Da mesma `Enqueue` forma, um pedido HTTP real para a fila de armazenamento é automaticamente rastreado por Insights de Aplicação. No entanto, a `Enqueue` operação ocorre presumivelmente no contexto dos pais, como um contexto de pedido de entrada. Os SDKs application Insights correlacionam automaticamente tal operação (e a sua parte HTTP) com o pedido dos pais e outras telemetrias reportadas no mesmo âmbito.

A `Dequeue` operação é complicada. O Application Insights SDK rastreia automaticamente os pedidos HTTP. No entanto, não conhece o contexto de correlação até que a mensagem seja analisada. Não é possível correlacionar o pedido HTTP para obter a mensagem com o resto da telemetria especialmente quando mais de uma mensagem é recebida.

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
        operation.telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        operation.telemetry.Success = false;
        operation.telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
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

No exemplo seguinte, uma mensagem recebida é rastreada de forma semelhante ao pedido HTTP de entrada:

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

Da mesma forma, outras operações de fila podem ser instrumentadas. Uma operação de esprevação deve ser instrumentada de forma semelhante à de uma operação de deques. Instrumentar operações de gestão de filas não é necessário. Application Insights rastreia operações como HTTP, e na maioria dos casos, é suficiente.

Quando eliminar a mensagem do instrumento, certifique-se de que define os identificadores de operação (correlação). Em alternativa, pode utilizar a `Activity` API. Em seguida, não precisa de definir identificadores de operação nos itens de telemetria porque a Application Insights SDK faz isso por si:

- Crie um novo `Activity` depois de ter um item da fila.
- Utilizar `Activity.SetParentId(message.ParentId)` para correlacionar os registos de consumo e de produtor.
- Inicie o `Activity` .
- Rastreia a dequeue, processe e elimine as operações utilizando `Start/StopOperation` ajudantes. Faça-o a partir do mesmo fluxo de controlo assíncronos (contexto de execução). Desta forma, estão devidamente correlacionados.
- Pare o `Activity` .
- Utilize `Start/StopOperation` , ou ligue para a `Track` telemetria manualmente.

### <a name="dependency-types"></a>Tipos de Dependência

Application Insights usa tipo de dependência para personalizar experiências de UI. Para filas reconhece os seguintes tipos de que melhoram a experiência de `DependencyTelemetry` diagnóstico de [transações:](./transaction-diagnostics.md)
- `Azure queue` para filas de armazenamento Azure
- `Azure Event Hubs` para Azure Event Hubs
- `Azure Service Bus` para a Azure Service Bus

### <a name="batch-processing"></a>Processamento em lotes
Com algumas filas, pode descodionar várias mensagens com um pedido. O processamento destas mensagens é presumivelmente independente e pertence às diferentes operações lógicas. Não é possível correlacionar a `Dequeue` operação com uma mensagem em particular a ser processada.

Cada mensagem deve ser processada no seu próprio fluxo de controlo assíncronos. Para mais informações, consulte a secção [de rastreio de dependências outgoing.](#outgoing-dependencies-tracking)

## <a name="long-running-background-tasks"></a>Tarefas de fundo de longa duração

Algumas aplicações iniciam operações de longo prazo que podem ser causadas por pedidos do utilizador. Do ponto de vista do rastreio/instrumentação, não é diferente do pedido ou da instrumentação da dependência: 

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

Neste exemplo, `telemetryClient.StartOperation` cria e preenche o contexto de `DependencyTelemetry` correlação. Digamos que tem uma operação de pais que foi criada por pedidos que marcaram a operação. Desde que `BackgroundTask` comece no mesmo fluxo de controlo assíncronos como um pedido de entrada, está correlacionado com a operação dos pais. `BackgroundTask` e todos os itens de telemetria aninhados estão automaticamente correlacionados com o pedido que o causou, mesmo após o fim do pedido.

Quando a tarefa começa a partir do fio de fundo que não tem nenhuma operação ( `Activity` ) associada a ela, não tem nenhum `BackgroundTask` pai. No entanto, pode ter operações aninhadas. Todos os itens de telemetria comunicados da tarefa estão correlacionados com os `DependencyTelemetry` criados em `BackgroundTask` .

## <a name="outgoing-dependencies-tracking"></a>Rastreio de dependências de saída
Pode rastrear o seu próprio tipo de dependência ou uma operação que não seja suportada pela Application Insights.

O `Enqueue` método na fila do Service Bus ou na fila de armazenamento pode servir de exemplo para tal rastreio personalizado.

A abordagem geral para o rastreio de dependência personalizada é:

- Ligue para o `TelemetryClient.StartOperation` método (extensão) que preenche as `DependencyTelemetry` propriedades necessárias para a correlação e algumas outras propriedades (selo de início, duração).
- Desa estade outras propriedades personalizadas no `DependencyTelemetry` , como o nome e qualquer outro contexto que você precisa.
- Faça uma chamada de dependência e espere por isso.
- Pare a operação `StopOperation` quando estiver terminado.
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

A operação de eliminação faz com que a operação seja interrompida, pelo que pode fazê-lo em vez de ligar `StopOperation` .

*Aviso:* em alguns casos, a exceção não foralizada pode [impedir](/dotnet/csharp/language-reference/keywords/try-finally) `finally` a sua chamada, pelo que as operações não podem ser rastreadas.

### <a name="parallel-operations-processing-and-tracking"></a>Processamento e acompanhamento de operações paralelas

`StopOperation` só para a operação que foi iniciada. Se a operação atual não corresponder à que queres parar, `StopOperation` não faz nada. Esta situação pode acontecer se iniciar várias operações paralelas no mesmo contexto de execução:

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

Certifique-se de que liga `StartOperation` e processa sempre o funcionamento no mesmo método **async** para isolar as operações em paralelo. Se a operação for sincronizada (ou não async), envolva o processo e acompanhe `Task.Run` com:

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

## <a name="applicationinsights-operations-vs-systemdiagnosticsactivity"></a>AplicaçõesInsights operações vs System.Diagnostics.Activity
`System.Diagnostics.Activity` representa o contexto de rastreio distribuído e é utilizado por quadros e bibliotecas para criar e propagar o contexto dentro e fora do processo e correlacionar itens de telemetria. A atividade funciona em conjunto com `System.Diagnostics.DiagnosticSource` - o mecanismo de notificação entre o quadro/biblioteca para notificar sobre eventos interessantes (pedidos de entrada ou saída, exceções, etc).

As atividades são cidadãos de primeira classe em Application Insights e a dependência automática e a recolha de pedidos depende fortemente deles, juntamente com `DiagnosticSource` eventos. Se criar Atividade na sua aplicação - não resultaria na criação de telemetria application Insights. O Application Insights precisa de receber eventos DiagnosticSource e conhecer os nomes e cargas de eventos para traduzir a Atividade em telemetria.

Cada operação de Insights de Aplicação (pedido ou dependência) envolve `Activity` - quando `StartOperation` é chamado, cria Atividade por baixo. `StartOperation` é a forma recomendada de rastrear manualmente o pedido ou a dependência das telemetrias e garantir que tudo está correlacionado.

## <a name="next-steps"></a>Passos seguintes

- Aprenda os fundamentos da correlação de [telemetria](correlation.md) em Insights de Aplicação.
- Confira como os dados correlacionados conferem experiência e mapa de aplicações de diagnóstico [de transações.](./app-map.md) [](./transaction-diagnostics.md)
- Consulte o [modelo de dados](./data-model.md) para os tipos de Insights de Aplicação e modelo de dados.
- Reporte [eventos personalizados e métricas](./api-custom-events-metrics.md) à Application Insights.
- Confira a [configuração](configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet) padrão para a recolha de propriedades de contexto.
- Consulte o [System.Diagnostics.Activity User Guide](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) para ver como correlacionamos a telemetria.

