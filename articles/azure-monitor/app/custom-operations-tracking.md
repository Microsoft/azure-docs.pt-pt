---
title: Acompanhe as operações personalizadas com insights de aplicação Azure .NET SDK
description: Rastreio de operações personalizadas com Insights de Aplicação Azure .NET SDK
ms.topic: conceptual
ms.date: 11/26/2019
ms.reviewer: sergkanz
ms.openlocfilehash: 31c1fb366e7b109ea1fa4977d8e2f908e766e0f2
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79276104"
---
# <a name="track-custom-operations-with-application-insights-net-sdk"></a>Acompanhe as operações personalizadas com Insights de Aplicação .NET SDK

Os SDKs da Aplicação Azure acompanham automaticamente os pedidos de HTTP e chamadas para serviços dependentes, tais como pedidos HTTP e consultas SQL. O rastreio e a correlação de pedidos e dependências dão-lhe visibilidade em toda a capacidade de resposta e fiabilidade de toda a aplicação em todos os microserviços que combinam esta aplicação. 

Há uma classe de padrões de aplicação que não podem ser suportados genericamente. Uma monitorização adequada destes padrões requer uma instrumentação manual do código. Este artigo abrange alguns padrões que podem exigir instrumentação manual, tais como processamento de fila personalizada e tarefas de fundo de longa duração.

Este documento fornece orientações sobre como rastrear operações personalizadas com o SDK de Insights de Aplicação. Esta documentação é relevante para:

- Informações de aplicação para .NET (também conhecido como Base SDK) versão 2.4+.
- Informações de aplicação para aplicações web (executando ASP.NET) versão 2.4+.
- Informações de aplicação para ASP.NET versão Core 2.1+.

## <a name="overview"></a>Descrição geral
Uma operação é uma peça de trabalho lógica gerida por uma aplicação. Tem um nome, hora de início, duração, resultado, e um contexto de execução como nome de utilizador, propriedades e resultado. Se a operação A foi iniciada pela operação B, então a operação B é definida como um pai para A. Uma operação pode ter apenas um pai, mas pode ter muitas operações infantis. Para obter mais informações sobre operações e correlação de telemetria, consulte a correlação de [telemetria Azure Application Insights](correlation.md).

Na Aplicação Insights .NET SDK, a operação é descrita pela classe abstrata [OperationTelemetria](https://github.com/microsoft/ApplicationInsights-dotnet/blob/7633ae849edc826a8547745b6bf9f3174715d4bd/BASE/src/Microsoft.ApplicationInsights/Extensibility/Implementation/OperationTelemetry.cs) e pelos seus descendentes [RequestTelemettry](https://github.com/microsoft/ApplicationInsights-dotnet/blob/7633ae849edc826a8547745b6bf9f3174715d4bd/BASE/src/Microsoft.ApplicationInsights/DataContracts/RequestTelemetry.cs) e [DependencyTelemettry](https://github.com/microsoft/ApplicationInsights-dotnet/blob/7633ae849edc826a8547745b6bf9f3174715d4bd/BASE/src/Microsoft.ApplicationInsights/DataContracts/DependencyTelemetry.cs).

## <a name="incoming-operations-tracking"></a>Rastreio de operações de entrada 
O Web SDK da Application Insights recolhe automaticamente pedidos de HTTP para aplicações ASP.NET que funcionam num pipeline IIS e todas as aplicações ASP.NET Core. Existem soluções apoiadas pela comunidade para outras plataformas e quadros. No entanto, se a aplicação não for suportada por nenhuma das soluções standard ou apoiadas pela comunidade, pode instrumentá-la manualmente.

Outro exemplo que requer rastreio personalizado é o trabalhador que recebe itens da fila. Para algumas filas, a chamada para adicionar uma mensagem a esta fila é rastreada como uma dependência. No entanto, a operação de alto nível que descreve o processamento de mensagens não é recolhida automaticamente.

Vamos ver como tais operações podem ser rastreadas.

Em um alto nível, a tarefa é criar `RequestTelemetry` e definir propriedades conhecidas. Depois da operação estar terminada, rastreia a telemetria. O exemplo que se segue demonstra esta tarefa.

### <a name="http-request-in-owin-self-hosted-app"></a>Pedido http na app auto-hospedada em Owin
Neste exemplo, o contexto do vestígio é propagado de acordo com o [Protocolo HTTP para a Correlação](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md). Deve esperar receber cabeçalhos que sejam descritos lá.

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

O Protocolo HTTP para a Correlação também declara o cabeçalho `Correlation-Context`. No entanto, é omitido aqui por simplicidade.

## <a name="queue-instrumentation"></a>Instrumentação de fila
Embora existam [o W3C Trace Context](https://www.w3.org/TR/trace-context/) e o HTTP Protocol for Correlation [para](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md) passar detalhes de correlação com o pedido http, cada protocolo de fila tem que definir como os mesmos detalhes são passados ao longo da mensagem de fila. Alguns protocolos de fila (como amqp) permitem a passagem de metadados adicionais e alguns outros (tal Fila de Armazenamento Azure) exigem que o contexto seja codificado na carga útil da mensagem.

> [!NOTE]
> * **O rastreio de componentes cruzados ainda não é suportado para filas** Com o HTTP, se o seu produtor e consumidor enviarem telemetria para diferentes recursos de Insights de Aplicação, a Experiência de Diagnóstico de Transações e o Mapa de Aplicações mostram transações e mapas de ponta a ponta. Em caso de filas, isso ainda não é apoiado. 

### <a name="service-bus-queue"></a>Fila do Service Bus
Application Insights rastreia chamadas de Mensagens de Ônibus de serviço com o novo [Cliente Microsoft Azure ServiceBus para a](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/) versão 3.0.0 e superior.
Se utilizar o padrão de manipulador de [mensagens](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler) para processar mensagens, está feito: todas as chamadas de Ônibus de serviço feitas pelo seu serviço são automaticamente rastreadas e correlacionadas com outros itens de telemetria. Consulte o [cliente do Service Bus a rastrear com o Microsoft Application Insights](../../service-bus-messaging/service-bus-end-to-end-tracing.md) se processar manualmente as mensagens.

Se utilizar o pacote [WindowsAzure.ServiceBus,](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) leia mais - seguindo exemplos demonstram como rastrear (e correlacionar) chamadas para o Ônibus de serviço, uma vez que a fila de ônibus de serviço utiliza o protocolo AMQP e os Insights de Aplicação não rastreiam automaticamente as operações de fila.
Os identificadores de correlação são passados nas propriedades da mensagem.

#### <a name="enqueue"></a>Enfila

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

### <a name="azure-storage-queue"></a>Fila de armazenamento azure
O exemplo que se segue mostra como acompanhar as operações de fila de [armazenamento azure](../../storage/queues/storage-dotnet-how-to-use-queues.md) e correlacionar a telemetria entre o produtor, o consumidor e o Armazenamento Azure. 

A fila de armazenamento tem um HTTP API. Todas as chamadas para a fila são rastreadas pelo Application Insights Dependency Collector para pedidos HTTP.
É configurado por padrão em aplicações ASP.NET e ASP.NET Core, com outros tipos de aplicações, pode consultar a documentação de [aplicações](../../azure-monitor/app/console.md) de consola

Também pode querer correlacionar a operação Application Insights ID com o ID do pedido de armazenamento. Para obter informações sobre como definir e obter um cliente de pedido de armazenamento e um id de pedido de servidor, consulte [Monitor, diagnosticar e resolver problemas no Armazenamento Azure](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#end-to-end-tracing).

#### <a name="enqueue"></a>Enfila
Como as filas de armazenamento suportam a Http API, todas as operações com a fila são automaticamente rastreadas por Insights de Aplicação. Em muitos casos, esta instrumentação deve ser suficiente. No entanto, para correlacionar os vestígios do lado do consumidor com os vestígios dos produtores, deve passar um contexto de correlação semelhante à forma como o fazemos no Protocolo http para a Correlação. 

Este exemplo mostra como rastrear a operação `Enqueue`. Pode:

 - **Correlacionar as tentativas (se houver)** : Todos têm um pai comum que é a operação `Enqueue`. Caso contrário, são seguidos como crianças do pedido de entrada. Se houver múltiplos pedidos lógicos para a fila, pode ser difícil encontrar qual chamada resultou em repetições.
 - **Registos de armazenamento correlacionados (se e quando necessário)** : Estão correlacionados com a telemetria de Insights de Aplicação.

A operação `Enqueue` é o filho de uma operação dos pais (por exemplo, um pedido http vindo). A chamada de dependência http é a criança da operação `Enqueue` e o neto do pedido de entrada:

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

Para reduzir a quantidade de telemetria dos seus relatórios de candidatura ou se não quiser acompanhar a operação `Enqueue` por outras razões, utilize a API `Activity` diretamente:

- Criar (e iniciar) um novo `Activity` em vez de iniciar a operação Application Insights. Não *precisa* de atribuir propriedades, exceto o nome de operação.
- Serialize `yourActivity.Id` na carga útil da mensagem em vez de `operation.Telemetry.Id`. Também pode usar `Activity.Current.Id`.


#### <a name="dequeue"></a>Defila
Da mesma forma que `Enqueue`, um pedido http real para a fila de armazenamento é automaticamente rastreado por Insights de Aplicação. No entanto, a operação `Enqueue` presumivelmente acontece no contexto dos pais, como um contexto de pedido de entrada. Os SDKs de Aplicação correlacionaram automaticamente tal operação (e a sua parte HTTP) com o pedido dos pais e outra telemetria reportada no mesmo âmbito.

A operação `Dequeue` é complicada. O SDK de Insights de Aplicação rastreia automaticamente os pedidos http. No entanto, não conhece o contexto de correlação até que a mensagem seja analisada. Não é possível correlacionar o pedido http para obter a mensagem com o resto da telemetria especialmente quando mais de uma mensagem é recebida.

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

No exemplo seguinte, uma mensagem de entrada é rastreada de forma semelhante ao pedido http:

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

Da mesma forma, outras operações de fila podem ser instrumentadas. Uma operação de espreitar deve ser instrumentada de forma semelhante à de uma operação de desfila. A instrumentação de operações de gestão de filas não é necessária. Application Insights rastreia operações como HTTP, e na maioria dos casos, é suficiente.

Quando a eliminação da mensagem do instrumento, certifique-se de que define os identificadores de funcionamento (correlação). Em alternativa, pode utilizar a `Activity` API. Em seguida, não precisa de definir identificadores de operação nos itens de telemetria porque o Application Insights SDK faz isso por si:

- Crie um novo `Activity` depois de ter um item da fila.
- Utilize `Activity.SetParentId(message.ParentId)` para correlacionar os registos dos consumidores e dos produtores.
- Inicie a `Activity`.
- Acompanhe a desfila, processe e elimine as operações utilizando `Start/StopOperation` ajudantes. Faça-o a partir do mesmo fluxo de controlo assíncrono (contexto de execução). Desta forma, estão devidamente correlacionados.
- Pare o `Activity`.
- Utilize `Start/StopOperation`ou ligue para `Track` telemetria manualmente.

### <a name="dependency-types"></a>Tipos de Dependência

Application Insights usa tipo de dependência para cusomizar experiências de UI. Para as filas reconhece os seguintes tipos de `DependencyTelemetry` que melhoram a experiência de [diagnóstico de transações:](/azure/azure-monitor/app/transaction-diagnostics)
- `Azure queue` para filas de armazenamento azure
- `Azure Event Hubs` para hubs de eventos azure
- `Azure Service Bus` para o ônibus de serviço azure

### <a name="batch-processing"></a>Processamento em lotes
Com algumas filas, pode fazer fila múltiplas com um pedido. O processamento destas mensagens é presumivelmente independente e pertence às diferentes operações lógicas. Não é possível relacionar a operação `Dequeue` a uma determinada mensagem que está a ser processada.

Cada mensagem deve ser processada no seu próprio fluxo de controlo assíncrono. Para mais informações, consulte a secção de rastreio de [dependências de saída.](#outgoing-dependencies-tracking)

## <a name="long-running-background-tasks"></a>Tarefas de fundo de longa duração

Algumas aplicações iniciam operações de longo prazo que podem ser causadas por pedidos de utilizador. Do ponto de vista do rastreio/instrumentação, não é diferente do pedido ou da instrumentação da dependência: 

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

Neste exemplo, `telemetryClient.StartOperation` cria `DependencyTelemetry` e preenche o contexto de correlação. Digamos que tem uma operação aos pais que foi criada por pedidos que programaram a operação. Desde que `BackgroundTask` comece no mesmo fluxo de controlo assíncrono que um pedido de entrada, está correlacionado com a operação dos pais. `BackgroundTask` e todos os itens de telemetria aninhados estão automaticamente correlacionados com o pedido que o causou, mesmo após o fim do pedido.

Quando a tarefa começa a partir do fio de fundo que não tem qualquer operação (`Activity`) associada a ela, `BackgroundTask` não tem nenhum progenitor. No entanto, pode ter operações aninhadas. Todos os artigos de telemetria relatados da tarefa estão correlacionados com o `DependencyTelemetry` criado em `BackgroundTask`.

## <a name="outgoing-dependencies-tracking"></a>Rastreio de dependências de saída
Pode rastrear o seu próprio tipo de dependência ou uma operação que não seja suportada pela Application Insights.

O método `Enqueue` na fila do Ônibus de serviço ou na fila de armazenamento pode servir como exemplos para tal rastreio personalizado.

A abordagem geral para o rastreio da dependência personalizada é:

- Ligue para o método `TelemetryClient.StartOperation` (extensão) que preenche as propriedades `DependencyTelemetry` que são necessárias para a correlação e algumas outras propriedades (carimbo de tempo de início, duração).
- Detete outras propriedades personalizadas no `DependencyTelemetry`, como o nome e qualquer outro contexto que você precisar.
- Faça uma ligação de dependência e espere por isso.
- Pare a operação com `StopOperation` quando terminar.
- Manuseie exceções.

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

A eliminação da operação faz com que a operação seja interrompida, pelo que pode fazê-lo em vez de chamar `StopOperation`.

*Aviso:* em alguns casos, uma exceção não handedpode [impedir](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/try-finally) que `finally` de serem chamados para que as operações não possam ser rastreadas.

### <a name="parallel-operations-processing-and-tracking"></a>Processamento e rastreio de operações paralelas

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

Certifique-se de que chama sempre `StartOperation` e processa o funcionamento no mesmo método **de sincronização** para isolar as operações em paralelo. Se o funcionamento for sincronizado (ou não asincronizar), envolvê-lo e acompanhar com `Task.Run`:

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

## <a name="applicationinsights-operations-vs-systemdiagnosticsactivity"></a>ApplicationInsights operações vs System.Diagnostics.Activity
`System.Diagnostics.Activity` representa o contexto de rastreio distribuído e é utilizado por quadros e bibliotecas para criar e propagar o contexto dentro e fora do processo e correlacionar itens de telemetria. A atividade funciona em conjunto com `System.Diagnostics.DiagnosticSource` - o mecanismo de notificação entre o quadro/biblioteca para notificar sobre eventos interessantes (pedidos de entrada ou saída, exceções, etc.).

As atividades são cidadãos de primeira classe em Insights de Aplicação e dependência automática e a recolha de pedidos depende fortemente deles juntamente com eventos `DiagnosticSource`. Se criar atividade na sua aplicação - não resultaria na criação de telemetria de Insights de Aplicação. Application Insights precisa receber eventos DiagnosticSource e conhecer os nomes dos eventos e cargas para traduzir a Atividade em telemetria.

Cada operação de Insights de Aplicação (pedido ou dependência) envolve `Activity` - quando `StartOperation` é chamado, cria atividade por baixo. `StartOperation` é a forma recomendada de rastrear o pedido ou as telemetrias de dependência manualmente e garantir que tudo está correlacionado.

## <a name="next-steps"></a>Passos seguintes

- Aprenda o básico da correlação de [telemetria](correlation.md) em Insights de Aplicação.
- Confira como os poderes de dados correlacionados Experiência de Diagnóstico de [Transações](../../azure-monitor/app/transaction-diagnostics.md) e Mapa de [Aplicações](../../azure-monitor/app/app-map.md).
- Consulte o modelo de [dados](../../azure-monitor/app/data-model.md) para tipos de Insights de Aplicação e modelo de dados.
- Reporte [eventos e métricas personalizados](../../azure-monitor/app/api-custom-events-metrics.md) para Application Insights.
- Confira a [configuração](configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet) padrão para a recolha de propriedades de contexto.
- Consulte o [System.Diagnostics.Activity User Guide](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) para ver como correlacionar a telemetria.
