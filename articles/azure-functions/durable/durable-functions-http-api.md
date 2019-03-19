---
title: APIs de HTTP nas funções duráveis - Azure
description: Saiba como implementar HTTP APIs na extensão de funções duráveis para as funções do Azure.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: azfuncdf
ms.openlocfilehash: 5bd977826f489ca8452432babe6126b8553450fb
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58137713"
---
# <a name="http-apis-in-durable-functions-azure-functions"></a>APIs de HTTP nas funções duráveis (funções do Azure)

A extensão de tarefas durável expõe um conjunto de APIs HTTP que podem ser utilizados para realizar as seguintes tarefas:

* Obter o estado de uma instância de orquestração.
* Envie um evento para uma instância de orquestração espera.
* Termine uma instância de orquestração em execução.

Cada uma dessas APIs de HTTP é uma operação de webhook diretamente manipulada pela extensão de tarefas durável. Eles não são específicos para qualquer função na function app.

> [!NOTE]
> Estas operações também podem ser invocadas diretamente usando as APIs de gestão de instância no [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) classe. Para obter mais informações, consulte [gerenciamento de instância](durable-functions-instance-management.md).

## <a name="http-api-url-discovery"></a>Deteção de URL da API de HTTP

O [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) classe expõe um [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_) API que pode ser utilizado para gerar um payload de resposta HTTP que contém ligações para todas as operações suportadas. Eis um exemplo de função de Acionador de HTTP que demonstra como utilizar esta API:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/HttpStart/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (funciona apenas 2.x)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

Estas funções de exemplo produzem os dados de resposta JSON seguintes. O tipo de dados de todos os campos é `string`.

| Campo                   |Descrição                           |
|-------------------------|--------------------------------------|
| **`id`**                |O ID da instância de orquestração. |
| **`statusQueryGetUri`** |O URL de estado da instância de orquestração. |
| **`sendEventPostUri`**  |O URL de "emitir um evento" da instância de orquestração. |
| **`terminatePostUri`**  |O URL da instância de orquestração "terminar". |
| **`rewindPostUri`**     |O URL de "o recuo" da instância de orquestração. |

Esta é uma resposta de exemplo:

```http
HTTP/1.1 202 Accepted
Content-Length: 923
Content-Type: application/json; charset=utf-8
Location: https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX

{
    "id":"34ce9a28a6834d8492ce6a295f1a80e2",
    "statusQueryGetUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "sendEventPostUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "terminatePostUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "rewindPostUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2/rewind?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code=XXX"
}
```

> [!NOTE]
> O formato dos URLs de webhook poderá ser diferente dependendo de qual versão do anfitrião de funções do Azure está a executar. O exemplo acima é para o anfitrião de 2.x as funções do Azure.

## <a name="async-operation-tracking"></a>Operação assíncrona de controlo

A resposta HTTP mencionada anteriormente, foi concebida para ajudar a implementar longa HTTP async APIs com funções duráveis. Isso às vezes, é referido como o *de consulta padrão de consumidor*. O fluxo de cliente/servidor funciona da seguinte forma:

1. O cliente emite uma solicitação HTTP para iniciar um processo de execução longa, como uma função de orquestrador.
2. O acionador HTTP de destino devolve uma resposta de HTTP 202 com um `Location` cabeçalho com o `statusQueryGetUri` valor.
3. O cliente consulta no URL no `Location` cabeçalho. Ele continua ver respostas HTTP 202 com um `Location` cabeçalho.
4. Quando a instância é concluída (ou falha), o ponto final no `Location` retorna de cabeçalho HTTP 200.

Esse protocolo permite coordenar processos de execução longa com clientes externos ou serviços que suportam um ponto final HTTP a consultar e seguir o `Location` cabeçalho. As partes fundamentais já são criadas para as APIs de HTTP de funções durável.

> [!NOTE]
> Por predefinição, todas as ações com base em HTTP fornecida pela [do Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) suportam o padrão de operação assíncrona padrão. Esta capacidade torna possível incorporar uma função de durável de longa execução como parte de um fluxo de trabalho do Logic Apps. Obter mais detalhes sobre o Logic Apps suportam para padrões HTTP assíncronos podem ser encontrados no [documentação de ações e acionadores de fluxo de trabalho do Azure Logic Apps](../../logic-apps/logic-apps-workflow-actions-triggers.md#asynchronous-patterns).

## <a name="http-api-reference"></a>Referência da API de HTTP

Todas as APIs de HTTP implementados, com a extensão take, os seguintes parâmetros. O tipo de dados de todos os parâmetros é `string`.

| Parâmetro        | Tipo de parâmetro  | Descrição |
|------------------|-----------------|-------------|
| **`taskHub`**    | Cadeia de consulta    | O nome da [hub tarefas](durable-functions-task-hubs.md). Se não for especificado, é assumido o nome do hub de tarefas da aplicação de função atual. |
| **`connection`** | Cadeia de consulta    | O **nome** a cadeia de ligação para a conta de armazenamento. Se não for especificado, é assumida a cadeia de ligação predefinido para a aplicação de funções. |
| **`systemKey`**  | Cadeia de consulta    | A chave de autorização necessária para invocar a API. |

`systemKey` é uma chave de autorização gerado automaticamente pelo host as funções do Azure. Concede acesso para a extensão de tarefas durável APIs especificamente e podem ser gerida da mesma forma que [outras chaves de autorização](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Key-management-API). A forma mais simples para detetar os `systemKey` valor é usando o `CreateCheckStatusResponse` API mencionado anteriormente.

As secções seguintes abordam específicos de HTTP APIs suportada pela extensão e fornecem exemplos de como pode ser utilizados.

### <a name="get-instance-status"></a>Obter estado da instância

Obtém o estado de uma instância da orquestração especificado.

#### <a name="request"></a>Pedir

Para a versão 1.x do runtime das funções, o pedido é formatado da seguinte forma (várias linhas são exibidas por motivos de clareza):

```http
GET /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
```

Na versão 2.x do runtime das funções, o formato de URL tem todos os mesmos parâmetros, mas com um prefixo de um pouco diferente:

```http
GET /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
```

Parâmetros para esta API incluem o conjunto padrão mencionado anteriormente, bem como os seguintes parâmetros exclusivos do pedido:

| Campo                   | Tipo de parâmetro  | Descrição |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | do IdP             | O ID da instância de orquestração. |
| **`showInput`**         | Cadeia de consulta    | Parâmetro opcional. Se definido como `false`, a função de entrada não será incluído no payload de resposta.|
| **`showHistory`**       | Cadeia de consulta    | Parâmetro opcional. Se definido como `true`, o histórico de execução da orquestração será incluído no payload de resposta.|
| **`showHistoryOutput`** | Cadeia de consulta    | Parâmetro opcional. Se definido como `true`, a função devolve serão incluídos no histórico de execução de orquestração.|
| **`createdTimeFrom`**   | Cadeia de consulta    | Parâmetro opcional. Quando especificado, filtra a lista de instâncias devolvidas que foram criados em ou depois do carimbo de hora ISO8601 determinado.|
| **`createdTimeTo`**     | Cadeia de consulta    | Parâmetro opcional. Quando especificado, filtra a lista de instâncias devolvidas que foram criados em ou antes do carimbo de hora ISO8601 determinado.|
| **`runtimeStatus`**     | Cadeia de consulta    | Parâmetro opcional. Quando especificado, filtros a lista de instâncias devolvidas com base no respetivo estado de runtime. Para ver a lista de valores de estado de runtime possíveis, consulte a [consultar instâncias](durable-functions-instance-management.md) tópico. |

#### <a name="response"></a>Resposta

Vários valores de código de estado possível podem ser devolvidos.

* **HTTP 200 (OK)**: A instância especificada é um estado concluído.
* **HTTP 202 (aceite)**: A instância especificada está em curso.
* **O HTTP 400 (pedido incorreto)**: A instância especificada falhou ou foi terminada.
* **HTTP 404 (não encontrado)**: A instância especificada não existe ou não iniciado em execução.
* **HTTP 500 (erro de servidor interno)**: A instância especificada falhou com uma exceção não processada.

O payload de resposta para o **HTTP 200** e **HTTP 202** casos é um objeto JSON com os seguintes campos:

| Campo                 | Tipo de dados | Descrição |
|-----------------------|-----------|-------------|
| **`runtimeStatus`**   | string    | O estado de tempo de execução da instância. Os valores incluem *em execução*, *pendente*, *falha*, *cancelado*, *Terminated*, *Concluída*. |
| **`input`**           | JSON      | Os dados JSON utilizados para inicializar a instância. Este campo é `null` se o `showInput` parâmetro de cadeia de caracteres de consulta está definido como `false`.|
| **`customStatus`**    | JSON      | Os dados JSON utilizados para o estado de orquestração personalizado. Este campo é `null` se não for definido. |
| **`output`**          | JSON      | A saída JSON da instância. Este campo é `null` se a instância não é um estado concluído. |
| **`createdTime`**     | string    | A hora em que a instância foi criada. Utiliza o ISO 8601 estendido notação. |
| **`lastUpdatedTime`** | string    | A hora em que a instância mantidas pela última vez. Utiliza o ISO 8601 estendido notação. |
| **`historyEvents`**   | JSON      | Uma matriz JSON que contém o histórico de execução da orquestração. Este campo é `null` , a menos que o `showHistory` parâmetro de cadeia de caracteres de consulta está definido como `true`. |

Aqui está um payload de resposta de exemplo incluindo as orquestração histórico e a atividade saídas de execução (formatadas para legibilidade):

```json
{
  "createdTime": "2018-02-28T05:18:49Z",
  "historyEvents": [
      {
          "EventType": "ExecutionStarted",
          "FunctionName": "E1_HelloSequence",
          "Timestamp": "2018-02-28T05:18:49.3452372Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello Tokyo!",
          "ScheduledTime": "2018-02-28T05:18:51.3939873Z",
          "Timestamp": "2018-02-28T05:18:52.2895622Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello Seattle!",
          "ScheduledTime": "2018-02-28T05:18:52.8755705Z",
          "Timestamp": "2018-02-28T05:18:53.1765771Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello London!",
          "ScheduledTime": "2018-02-28T05:18:53.5170791Z",
          "Timestamp": "2018-02-28T05:18:53.891081Z"
      },
      {
          "EventType": "ExecutionCompleted",
          "OrchestrationStatus": "Completed",
          "Result": [
              "Hello Tokyo!",
              "Hello Seattle!",
              "Hello London!"
          ],
          "Timestamp": "2018-02-28T05:18:54.3660895Z"
      }
  ],
  "input": null,
  "customStatus": { "nextActions": ["A", "B", "C"], "foo": 2 },
  "lastUpdatedTime": "2018-02-28T05:18:54Z",
  "output": [
      "Hello Tokyo!",
      "Hello Seattle!",
      "Hello London!"
  ],
  "runtimeStatus": "Completed"
}
```

O **HTTP 202** resposta também inclui um **localização** cabeçalho de resposta que referencia o mesmo URL que o `statusQueryGetUri` campo mencionado anteriormente.

### <a name="get-all-instances-status"></a>Obter o estado de todas as instâncias

Também pode consultar o estado de todas as instâncias, removendo o `instanceId` no pedido "Obter estado da instância". Neste caso, os parâmetros básicos são os mesmos, como o "Estado de instância de Get". Também são suportados parâmetros de cadeia de caracteres de consulta para filtragem.

É importante lembrar que é que `connection` e `code` são opcionais. Se tiver a autenticação anónima a função de código não é necessário.
Se não pretender utilizar uma cadeia de ligação de armazenamento diferente que definido na definição da aplicação de AzureWebJobsStorage, em seguida, pode ignorar o parâmetro de cadeia de caracteres de consulta de ligação.

#### <a name="request"></a>Pedir

Para a versão 1.x do runtime das funções, o pedido é formatado da seguinte forma (várias linhas são exibidas por motivos de clareza):

```http
GET /admin/extensions/DurableTaskExtension/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
    &showInput=[true|false]
    &top={integer}
```

Na versão 2.x do runtime das funções, o formato de URL tem todos os mesmos parâmetros, mas com um prefixo de um pouco diferente:

```http
GET /runtime/webhooks/durableTask/instances?
    taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
    &showInput=[true|false]
    &top={integer}
```

Parâmetros para esta API incluem o conjunto padrão mencionado anteriormente, bem como os seguintes parâmetros exclusivos do pedido:

| Campo                   | Tipo de parâmetro  | Descrição |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | do IdP             | O ID da instância de orquestração. |
| **`showInput`**         | Cadeia de consulta    | Parâmetro opcional. Se definido como `false`, a função de entrada não será incluído no payload de resposta.|
| **`showHistory`**       | Cadeia de consulta    | Parâmetro opcional. Se definido como `true`, o histórico de execução da orquestração será incluído no payload de resposta.|
| **`showHistoryOutput`** | Cadeia de consulta    | Parâmetro opcional. Se definido como `true`, a função devolve serão incluídos no histórico de execução de orquestração.|
| **`createdTimeFrom`**   | Cadeia de consulta    | Parâmetro opcional. Quando especificado, filtra a lista de instâncias devolvidas que foram criados em ou depois do carimbo de hora ISO8601 determinado.|
| **`createdTimeTo`**     | Cadeia de consulta    | Parâmetro opcional. Quando especificado, filtra a lista de instâncias devolvidas que foram criados em ou antes do carimbo de hora ISO8601 determinado.|
| **`runtimeStatus`**     | Cadeia de consulta    | Parâmetro opcional. Quando especificado, filtros a lista de instâncias devolvidas com base no respetivo estado de runtime. Para ver a lista de valores de estado de runtime possíveis, consulte a [consultar instâncias](durable-functions-instance-management.md) tópico. |
| **`top`**               | Cadeia de consulta    | Parâmetro opcional. Quando especificado, limita o número de instâncias devolvido pela consulta. |

#### <a name="response"></a>Resposta

Eis um exemplo de payloads de resposta, incluindo o estado de orquestração (formatado para legibilidade):

```json
[
    {
        "instanceId": "7af46ff000564c65aafbfe99d07c32a5",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-06-04T10:46:39Z",
        "lastUpdatedTime": "2018-06-04T10:46:47Z"
    },
    {
        "instanceId": "80eb7dd5c22f4eeba9f42b062794321e",
        "runtimeStatus": "Running",
        "input": null,
        "customStatus": null,
        "output": null,
        "createdTime": "2018-06-04T15:18:28Z",
        "lastUpdatedTime": "2018-06-04T15:18:38Z"
    },
    {
        "instanceId": "9124518926db408ab8dfe84822aba2b1",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-06-04T10:46:54Z",
        "lastUpdatedTime": "2018-06-04T10:47:03Z"
    },
    {
        "instanceId": "d100b90b903c4009ba1a90868331b11b",
        "runtimeStatus": "Pending",
        "input": null,
        "customStatus": null,
        "output": null,
        "createdTime": "2018-06-04T15:18:39Z",
        "lastUpdatedTime": "2018-06-04T15:18:39Z"
    }
]
```

> [!NOTE]
> Esta operação pode ser muito cara em termos de e/s de armazenamento de Azure se existirem muitas linhas da tabela de instâncias. Podem encontrar mais detalhes na tabela de instância na [desempenho e dimensionamento nas funções durável (funções do Azure)](durable-functions-perf-and-scale.md#instances-table) documentação.
>

Se existirem mais resultados, é devolvido um token de continuação no cabeçalho de resposta.  O nome do cabeçalho é `x-ms-continuation-token`.

Se definir o valor de token de continuação no cabeçalho do pedido seguinte, pode obter a próxima página de resultados. Este nome do cabeçalho do pedido é também `x-ms-continuation-token`.

### <a name="purge-single-instance-history"></a>Histórico de instância única

Elimina o histórico e artefactos relacionados para uma instância da orquestração especificado.

#### <a name="request"></a>Pedir

Para a versão 1.x do runtime das funções, o pedido é formatado da seguinte forma (várias linhas são exibidas por motivos de clareza):

```http
DELETE /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

Na versão 2.x do runtime das funções, o formato de URL tem todos os mesmos parâmetros, mas com um prefixo de um pouco diferente:

```http
DELETE /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

Parâmetros para esta API incluem o conjunto padrão mencionado anteriormente, bem como os seguintes parâmetros exclusivos do pedido:

| Campo             | Tipo de parâmetro  | Descrição |
|-------------------|-----------------|-------------|
| **`instanceId`**  | do IdP             | O ID da instância de orquestração. |

#### <a name="response"></a>Resposta

Os seguintes valores de código de estado HTTP podem ser devolvidos.

* **HTTP 200 (OK)**: O histórico de instância foi removido com êxito.
* **HTTP 404 (não encontrado)**: A instância especificada não existe.

O payload de resposta para o **HTTP 200** caso é um objeto JSON com o seguinte campo:

| Campo                  | Tipo de dados | Descrição |
|------------------------|-----------|-------------|
| **`instancesDeleted`** | inteiro   | O número de instâncias eliminado. Para o caso de instância única, este valor deve ser sempre `1`. |

Aqui está um payload de resposta de exemplo (formatado para legibilidade):

```json
{
    "instancesDeleted": 1
}
```

### <a name="purge-multiple-instance-history"></a>Vários histórico de instância

Também pode eliminar o histórico e artefactos relacionados para múltiplas instâncias dentro de um hub de tarefas, removendo o `{instanceId}` no pedido "Remover o histórico de instância única". Para remover seletivamente o histórico de instância, utilize os mesmos filtros descritos no pedido "Obter todos os Estados de instâncias".

#### <a name="request"></a>Pedir

Para a versão 1.x do runtime das funções, o pedido é formatado da seguinte forma (várias linhas são exibidas por motivos de clareza):

```http
DELETE /admin/extensions/DurableTaskExtension/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

Na versão 2.x do runtime das funções, o formato de URL tem todos os mesmos parâmetros, mas com um prefixo de um pouco diferente:

```http
DELETE /runtime/webhooks/durabletask/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

Parâmetros para esta API incluem o conjunto padrão mencionado anteriormente, bem como os seguintes parâmetros exclusivos do pedido:

| Campo                 | Tipo de parâmetro  | Descrição |
|-----------------------|-----------------|-------------|
| **`createdTimeFrom`** | Cadeia de consulta    | Parâmetro opcional. Quando especificado, filtra a lista de instâncias eliminadas que foram criados em ou depois do carimbo de hora ISO8601 determinado.|
| **`createdTimeTo`**   | Cadeia de consulta    | Parâmetro opcional. Quando especificado, filtra a lista de instâncias eliminadas que foram criados em ou antes do carimbo de hora ISO8601 determinado.|
| **`runtimeStatus`**   | Cadeia de consulta    | Parâmetro opcional. Quando especificado, filtros a lista de instâncias eliminadas com base no respetivo estado de runtime. Para ver a lista de valores de estado de runtime possíveis, consulte a [consultar instâncias](durable-functions-instance-management.md) tópico. |

Se forem especificados sem parâmetros, todas as instâncias no hub de tarefa serão removidas.

> [!NOTE]
> Esta operação pode ser muito cara em termos de e/s de armazenamento de Azure se houver muito de linhas nas instâncias de e/ou do histórico de tabelas. Obter mais detalhes sobre estas tabelas podem ser encontradas no [desempenho e dimensionamento nas funções durável (funções do Azure)](durable-functions-perf-and-scale.md#instances-table) documentação.

#### <a name="response"></a>Resposta

Os seguintes valores de código de estado HTTP podem ser devolvidos.

* **HTTP 200 (OK)**: O histórico de instância foi removido com êxito.
* **HTTP 404 (não encontrado)**: Não foram encontradas instâncias que correspondam a expressão de filtro.

O payload de resposta para o **HTTP 200** caso é um objeto JSON com o seguinte campo:

| Campo                   | Tipo de dados | Descrição |
|-------------------------|-----------|-------------|
| **`instancesDeleted`**  | inteiro   | O número de instâncias eliminado. |

Aqui está um payload de resposta de exemplo (formatado para legibilidade):

```json
{
    "instancesDeleted": 250
}
```

### <a name="raise-event"></a>Gerar evento

Envia uma mensagem de notificação de evento para uma instância de orquestração em execução.

#### <a name="request"></a>Pedir

Para a versão 1.x do runtime das funções, o pedido é formatado da seguinte forma (várias linhas são exibidas por motivos de clareza):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

Na versão 2.x do runtime das funções, o formato de URL tem todos os mesmos parâmetros, mas com um prefixo de um pouco diferente:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

Parâmetros para esta API incluem o conjunto padrão mencionado anteriormente, bem como os seguintes parâmetros exclusivos do pedido:

| Campo             | Tipo de parâmetro  | Descrição |
|-------------------|-----------------|-------------|
| **`instanceId`**  | do IdP             | O ID da instância de orquestração. |
| **`eventName`**   | do IdP             | O nome do evento que esteja aguardando a instância de orquestração de destino. |
| **`{content}`**   | Conteúdo do pedido | O payload do evento formatada em JSON. |

#### <a name="response"></a>Resposta

Vários valores de código de estado possível podem ser devolvidos.

* **HTTP 202 (aceite)**: O evento registado foi aceite para processamento.
* **O HTTP 400 (pedido incorreto)**: O conteúdo do pedido não era do tipo `application/json` ou não era um JSON válido.
* **HTTP 404 (não encontrado)**: A instância especificada não foi encontrada.
* **HTTP 410 (ficaram no passado)**: A instância especificada foi concluída ou falhada e não pode processar todos os eventos gerados.

Eis um exemplo de solicitação que envia a cadeia de caracteres do JSON `"incr"` a uma instância à espera de um evento chamado **operação**:

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code=XXX
Content-Type: application/json
Content-Length: 6

"incr"
```

As respostas para esta API não contêm qualquer conteúdo.

### <a name="terminate-instance"></a>Encerrar instância

Termina uma instância de orquestração em execução.

#### <a name="request"></a>Pedir

Para a versão 1.x do runtime das funções, o pedido é formatado da seguinte forma (várias linhas são exibidas por motivos de clareza):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Na versão 2.x do runtime das funções, o formato de URL tem todos os mesmos parâmetros, mas com um prefixo de um pouco diferente:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

O pedido parâmetros para esta API incluem o conjunto padrão mencionado anteriormente, bem como o seguinte parâmetro exclusivo.

| Campo             | Tipo de parâmetro  | Descrição |
|-------------------|-----------------|-------------|
| **`instanceId`**  | do IdP             | O ID da instância de orquestração. |
| **`reason`**      | Cadeia de consulta    | Opcional. O motivo para a instância da orquestração a terminar. |

#### <a name="response"></a>Resposta

Vários valores de código de estado possível podem ser devolvidos.

* **HTTP 202 (aceite)**: O pedido de encerramento foi aceite para processamento.
* **HTTP 404 (não encontrado)**: A instância especificada não foi encontrada.
* **HTTP 410 (ficaram no passado)**: A instância especificada foi concluída ou falhada.

Eis um exemplo de solicitação que termina uma instância em execução e especifica um motivo de **buggy**:

```
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason=buggy&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

As respostas para esta API não contêm qualquer conteúdo.

## <a name="rewind-instance-preview"></a>Retroceder a instância (pré-visualização)

Restaura uma instância da orquestração com falha num Estado de execução ao reproduzir as operações com falha mais recentes.

### <a name="request"></a>Pedir

Para a versão 1.x do runtime das funções, o pedido é formatado da seguinte forma (várias linhas são exibidas por motivos de clareza):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Na versão 2.x do runtime das funções, o formato de URL tem todos os mesmos parâmetros, mas com um prefixo de um pouco diferente:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

O pedido parâmetros para esta API incluem o conjunto padrão mencionado anteriormente, bem como o seguinte parâmetro exclusivo.

| Campo             | Tipo de parâmetro  | Descrição |
|-------------------|-----------------|-------------|
| **`instanceId`**  | do IdP             | O ID da instância de orquestração. |
| **`reason`**      | Cadeia de consulta    | Opcional. O motivo de avanço rápido a instância de orquestração. |

### <a name="response"></a>Resposta

Vários valores de código de estado possível podem ser devolvidos.

* **HTTP 202 (aceite)**: O pedido de recuo foi aceite para processamento.
* **HTTP 404 (não encontrado)**: A instância especificada não foi encontrada.
* **HTTP 410 (ficaram no passado)**: A instância especificada foi concluída ou foi terminada.

Eis um exemplo de solicitação que rewinds uma instância com falha e especifica um motivo de **fixo**:

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/rewind?reason=fixed&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

As respostas para esta API não contêm qualquer conteúdo.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Saiba como lidar com erros](durable-functions-error-handling.md)
