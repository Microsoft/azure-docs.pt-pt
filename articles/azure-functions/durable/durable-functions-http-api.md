---
title: APIs HTTP no Durable Functions-Azure
description: Saiba como implementar APIs HTTP na extensão de Durable Functions para Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: azfuncdf
ms.openlocfilehash: b34fd30b8e43e674b0b346672366d680d99ebd5c
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70734276"
---
# <a name="http-apis-in-durable-functions-azure-functions"></a>APIs HTTP no Durable Functions (Azure Functions)

A extensão de tarefa durável expõe um conjunto de APIs HTTP que podem ser usadas para executar as seguintes tarefas:

* Busque o status de uma instância de orquestração.
* Envie um evento para uma instância de orquestração em espera.
* Encerrar uma instância de orquestração em execução.

Cada uma dessas APIs HTTP é uma operação de webhook que é tratada diretamente pela extensão de tarefa durável. Eles não são específicos de nenhuma função no aplicativo de funções.

> [!NOTE]
> Essas operações também podem ser invocadas diretamente usando as APIs de gerenciamento de instâncias na classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) . Para obter mais informações, consulte [Gerenciamento de instâncias](durable-functions-instance-management.md).

## <a name="http-api-url-discovery"></a>Descoberta de URL de API HTTP

A classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) expõe uma API [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_) que pode ser usada para gerar uma carga de resposta http contendo links para todas as operações com suporte. Aqui está um exemplo de função de gatilho HTTP que demonstra como usar essa API:

### <a name="precompiled-c"></a>Pré-compiladoC#

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

### <a name="c-script"></a>C#Prescritiva

[!code-csharp[Main](~/samples-durable-functions/samples/csx/HttpStart/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (somente funções 2. x)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

Essas funções de exemplo produzem os seguintes dados de resposta JSON. O tipo de dados de todos os `string`campos é.

| Campo                   |Descrição                           |
|-----------------------------|--------------------------------------|
| **`id`**                    |A ID da instância de orquestração. |
| **`statusQueryGetUri`**     |A URL de status da instância de orquestração. |
| **`sendEventPostUri`**      |A URL "gerar evento" da instância de orquestração. |
| **`terminatePostUri`**      |A URL "Terminate" da instância de orquestração. |
| **`purgeHistoryDeleteUri`** |A URL "limpar histórico" da instância de orquestração. |
| **`rewindPostUri`**         |apresentação A URL de "retrocesso" da instância de orquestração. |

Aqui está um exemplo de resposta:

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
    "purgeHistoryDeleteUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX"
    "rewindPostUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2/rewind?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code=XXX"
}
```

> [!NOTE]
> O formato das URLs de webhook pode diferir dependendo da versão do host de Azure Functions que você está executando. O exemplo acima é para o host Azure Functions 2. x.

## <a name="async-operation-tracking"></a>Acompanhamento de operação assíncrona

A resposta HTTP mencionada anteriormente foi projetada para ajudar a implementar APIs assíncronas HTTP de longa execução com Durable Functions. Isso às vezes é chamado de *padrão de consumidor de sondagem*. O fluxo de cliente/servidor funciona da seguinte maneira:

1. O cliente emite uma solicitação HTTP para iniciar um processo de execução demorada, como uma função de orquestrador.
2. O gatilho http de destino retorna uma resposta http 202 com `Location` um cabeçalho com `statusQueryGetUri` o valor.
3. O cliente sonda a URL no `Location` cabeçalho. Ele continua vendo as respostas http 202 com um `Location` cabeçalho.
4. Quando a instância é concluída (ou falha), o ponto de extremidade `Location` no cabeçalho retorna http 200.

Esse protocolo permite coordenar processos de execução longa com clientes externos ou serviços que dão suporte à sondagem de um ponto `Location` de extremidade http e ao seguir o cabeçalho. As partes fundamentais já estão incorporadas ao Durable Functions APIs HTTP.

> [!NOTE]
> Por padrão, todas as ações baseadas em HTTP fornecidas pelos [aplicativos lógicos do Azure](https://azure.microsoft.com/services/logic-apps/) dão suporte ao padrão de operação assíncrona padrão. Esse recurso possibilita inserir uma função durável de execução longa como parte de um fluxo de trabalho de aplicativos lógicos. Mais detalhes sobre o suporte a aplicativos lógicos para padrões HTTP assíncronos podem ser encontrados na [documentação de ações e gatilhos de fluxo de trabalho dos aplicativos lógicos do Azure](../../logic-apps/logic-apps-workflow-actions-triggers.md#asynchronous-patterns).

## <a name="http-api-reference"></a>Referência de API HTTP

Todas as APIs HTTP implementadas pela extensão usam os seguintes parâmetros. O tipo de dados de todos os `string`parâmetros é.

| Parâmetro        | Tipo de parâmetro  | Descrição |
|------------------|-----------------|-------------|
| **`taskHub`**    | Cadeia de consulta    | O nome do [Hub de tarefas](durable-functions-task-hubs.md). Se não for especificado, o nome do hub de tarefas do aplicativo de funções atual será assumido. |
| **`connection`** | Cadeia de consulta    | O **nome** da cadeia de conexão para a conta de armazenamento. Se não for especificado, a cadeia de conexão padrão para o aplicativo de funções será assumida. |
| **`systemKey`**  | Cadeia de consulta    | A chave de autorização necessária para invocar a API. |

`systemKey`é uma chave de autorização gerada automaticamente pelo host Azure Functions. Ele concede especificamente acesso às APIs de extensão de tarefa durável e pode ser gerenciado da mesma forma que [outras chaves de autorização](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Key-management-API). A maneira mais simples de descobrir o `systemKey` valor é usando a `CreateCheckStatusResponse` API mencionada anteriormente.

As próximas seções abrangem as APIs HTTP específicas com suporte da extensão e fornecem exemplos de como elas podem ser usadas.

### <a name="get-instance-status"></a>Obter status da instância

Obtém o status de uma instância de orquestração especificada.

#### <a name="request"></a>Pedir

Para a versão 1. x do tempo de execução do functions, a solicitação é formatada da seguinte maneira (várias linhas são mostradas para maior clareza):

```http
GET /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
```

Na versão 2. x do tempo de execução do functions, o formato da URL tem todos os mesmos parâmetros, mas com um prefixo ligeiramente diferente:

```http
GET /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
```

Os parâmetros de solicitação para essa API incluem o conjunto padrão mencionado anteriormente, bem como os seguintes parâmetros exclusivos:

| Campo                   | Tipo de parâmetro  | Descrição |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | URL             | A ID da instância de orquestração. |
| **`showInput`**         | Cadeia de consulta    | Parâmetro opcional. Se definido como `false`, a entrada da função não será incluída na carga de resposta.|
| **`showHistory`**       | Cadeia de consulta    | Parâmetro opcional. Se definido como `true`, o histórico de execução de orquestração será incluído na carga de resposta.|
| **`showHistoryOutput`** | Cadeia de consulta    | Parâmetro opcional. Se definido como `true`, as saídas de função serão incluídas no histórico de execução de orquestração.|
| **`createdTimeFrom`**   | Cadeia de consulta    | Parâmetro opcional. Quando especificado, filtra a lista de instâncias retornadas que foram criadas no ou após o carimbo de data/hora ISO8601 fornecido.|
| **`createdTimeTo`**     | Cadeia de consulta    | Parâmetro opcional. Quando especificado, filtra a lista de instâncias retornadas que foram criadas no ou antes do carimbo de data/hora ISO8601 fornecido.|
| **`runtimeStatus`**     | Cadeia de consulta    | Parâmetro opcional. Quando especificado, filtra a lista de instâncias retornadas com base em seu status de tempo de execução. Para ver a lista de possíveis valores de status de tempo de execução, consulte o tópico [consultas de instâncias](durable-functions-instance-management.md) . |

#### <a name="response"></a>Resposta

Vários valores de código de status possíveis podem ser retornados.

* **HTTP 200 (OK)** : A instância especificada está em um estado concluído.
* **HTTP 202 (aceito)** : A instância especificada está em andamento.
* **HTTP 400 (solicitação inadequada)** : A instância especificada falhou ou foi encerrada.
* **HTTP 404 (não encontrado)** : A instância especificada não existe ou não iniciou a execução.
* **HTTP 500 (erro interno do servidor)** : A instância especificada falhou com uma exceção sem tratamento.

A carga de resposta para os casos **http 200** e **http 202** é um objeto JSON com os seguintes campos:

| Campo                 | Tipo de dados | Descrição |
|-----------------------|-----------|-------------|
| **`runtimeStatus`**   | Cadeia de caracteres    | O status de tempo de execução da instância. Os valores incluem *execução*, *pendente*, *falha*, *cancelado*, *encerrado*, *concluído*. |
| **`input`**           | JSON      | Os dados JSON usados para inicializar a instância. Esse campo é `null` se o `showInput` parâmetro da cadeia de caracteres de `false`consulta for definido como.|
| **`customStatus`**    | JSON      | Os dados JSON usados para o status de orquestração personalizado. Esse campo é `null` se não estiver definido. |
| **`output`**          | JSON      | A saída JSON da instância. Esse campo é `null` se a instância não estiver em um estado concluído. |
| **`createdTime`**     | Cadeia de caracteres    | A hora em que a instância foi criada. Usa a notação estendida ISO 8601. |
| **`lastUpdatedTime`** | Cadeia de caracteres    | A hora em que a instância foi persistida pela última vez. Usa a notação estendida ISO 8601. |
| **`historyEvents`**   | JSON      | Uma matriz JSON que contém o histórico de execução de orquestração. Esse campo é `null` a menos `showHistory` que o parâmetro de cadeia de `true`caracteres de consulta seja definido como. |

Aqui está um exemplo de conteúdo de resposta, incluindo o histórico de execução de orquestração e saídas de atividade (formatado para facilitar a leitura):

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

A resposta **http 202** também inclui um cabeçalho de resposta de **local** que faz referência à mesma `statusQueryGetUri` URL que o campo mencionado anteriormente.

### <a name="get-all-instances-status"></a>Obter status de todas as instâncias

Você também pode consultar o status de todas as instâncias removendo `instanceId` o da solicitação ' obter status da instância '. Nesse caso, os parâmetros básicos são os mesmos que o ' obter status da instância '. Também há suporte para parâmetros de cadeia de caracteres de consulta para filtragem.

Uma coisa a ser lembrada `connection` é `code` que e são opcionais. Se você tiver a autenticação anônima na função, o código não será necessário.
Se você não quiser usar uma cadeia de conexão de armazenamento diferente da definida na configuração de aplicativo AzureWebJobsStorage, poderá ignorar com segurança o parâmetro de cadeia de caracteres de consulta de conexão.

#### <a name="request"></a>Pedir

Para a versão 1. x do tempo de execução do functions, a solicitação é formatada da seguinte maneira (várias linhas são mostradas para maior clareza):

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

Na versão 2. x do tempo de execução do functions, o formato da URL tem todos os mesmos parâmetros, mas com um prefixo ligeiramente diferente:

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

Os parâmetros de solicitação para essa API incluem o conjunto padrão mencionado anteriormente, bem como os seguintes parâmetros exclusivos:

| Campo                   | Tipo de parâmetro  | Descrição |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | URL             | A ID da instância de orquestração. |
| **`showInput`**         | Cadeia de consulta    | Parâmetro opcional. Se definido como `false`, a entrada da função não será incluída na carga de resposta.|
| **`showHistory`**       | Cadeia de consulta    | Parâmetro opcional. Se definido como `true`, o histórico de execução de orquestração será incluído na carga de resposta.|
| **`showHistoryOutput`** | Cadeia de consulta    | Parâmetro opcional. Se definido como `true`, as saídas de função serão incluídas no histórico de execução de orquestração.|
| **`createdTimeFrom`**   | Cadeia de consulta    | Parâmetro opcional. Quando especificado, filtra a lista de instâncias retornadas que foram criadas no ou após o carimbo de data/hora ISO8601 fornecido.|
| **`createdTimeTo`**     | Cadeia de consulta    | Parâmetro opcional. Quando especificado, filtra a lista de instâncias retornadas que foram criadas no ou antes do carimbo de data/hora ISO8601 fornecido.|
| **`runtimeStatus`**     | Cadeia de consulta    | Parâmetro opcional. Quando especificado, filtra a lista de instâncias retornadas com base em seu status de tempo de execução. Para ver a lista de possíveis valores de status de tempo de execução, consulte o tópico [consultas de instâncias](durable-functions-instance-management.md) . |
| **`top`**               | Cadeia de consulta    | Parâmetro opcional. Quando especificado, limita o número de instâncias retornadas pela consulta. |

#### <a name="response"></a>Resposta

Aqui está um exemplo de cargas de resposta, incluindo o status de orquestração (formatado para legibilidade):

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
> Essa operação pode ser muito cara em termos de e/s do armazenamento do Azure se houver muitas linhas na tabela de instâncias. Mais detalhes sobre a tabela de instâncias podem ser encontrados no [desempenho e no dimensionamento na documentação do Durable Functions (Azure Functions)](durable-functions-perf-and-scale.md#instances-table) .
>

Se houver mais resultados, um token de continuação será retornado no cabeçalho de resposta.  O nome do cabeçalho é `x-ms-continuation-token`.

Se você definir o valor do token de continuação no próximo cabeçalho de solicitação, poderá obter a próxima página de resultados. O nome do cabeçalho da solicitação também `x-ms-continuation-token`é.

### <a name="purge-single-instance-history"></a>Limpar o histórico de instância única

Exclui o histórico e os artefatos relacionados para uma instância de orquestração especificada.

#### <a name="request"></a>Pedir

Para a versão 1. x do tempo de execução do functions, a solicitação é formatada da seguinte maneira (várias linhas são mostradas para maior clareza):

```http
DELETE /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

Na versão 2. x do tempo de execução do functions, o formato da URL tem todos os mesmos parâmetros, mas com um prefixo ligeiramente diferente:

```http
DELETE /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

Os parâmetros de solicitação para essa API incluem o conjunto padrão mencionado anteriormente, bem como os seguintes parâmetros exclusivos:

| Campo             | Tipo de parâmetro  | Descrição |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | A ID da instância de orquestração. |

#### <a name="response"></a>Resposta

Os valores de código de status HTTP a seguir podem ser retornados.

* **HTTP 200 (OK)** : O histórico de instâncias foi limpo com êxito.
* **HTTP 404 (não encontrado)** : A instância especificada não existe.

A carga de resposta para o caso de **HTTP 200** é um objeto JSON com o seguinte campo:

| Campo                  | Tipo de dados | Descrição |
|------------------------|-----------|-------------|
| **`instancesDeleted`** | integer   | O número de instâncias excluídas. Para o caso de instância única, esse valor sempre deve `1`ser. |

Aqui está um exemplo de carga de resposta (formatada para facilitar a leitura):

```json
{
    "instancesDeleted": 1
}
```

### <a name="purge-multiple-instance-history"></a>Limpar o histórico de várias instâncias

Você também pode excluir o histórico e os artefatos relacionados para várias instâncias em um hub de tarefas `{instanceId}` removendo o da solicitação ' limpar o histórico de instância única '. Para limpar seletivamente o histórico de instância, use os mesmos filtros descritos na solicitação "obter status de todas as instâncias".

#### <a name="request"></a>Pedir

Para a versão 1. x do tempo de execução do functions, a solicitação é formatada da seguinte maneira (várias linhas são mostradas para maior clareza):

```http
DELETE /admin/extensions/DurableTaskExtension/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

Na versão 2. x do tempo de execução do functions, o formato da URL tem todos os mesmos parâmetros, mas com um prefixo ligeiramente diferente:

```http
DELETE /runtime/webhooks/durabletask/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

Os parâmetros de solicitação para essa API incluem o conjunto padrão mencionado anteriormente, bem como os seguintes parâmetros exclusivos:

| Campo                 | Tipo de parâmetro  | Descrição |
|-----------------------|-----------------|-------------|
| **`createdTimeFrom`** | Cadeia de consulta    | Filtra a lista de instâncias limpas que foram criadas no ou após o carimbo de data/hora ISO8601 fornecido.|
| **`createdTimeTo`**   | Cadeia de consulta    | Parâmetro opcional. Quando especificado, filtra a lista de instâncias limpas que foram criadas no ou antes do carimbo de data/hora ISO8601 fornecido.|
| **`runtimeStatus`**   | Cadeia de consulta    | Parâmetro opcional. Quando especificado, filtra a lista de instâncias limpas com base em seu status de tempo de execução. Para ver a lista de possíveis valores de status de tempo de execução, consulte o tópico [consultas de instâncias](durable-functions-instance-management.md) . |

> [!NOTE]
> Essa operação pode ser muito cara em termos de e/s do armazenamento do Azure se houver muitas linhas nas tabelas de instâncias e/ou de histórico. Mais detalhes sobre essas tabelas podem ser encontrados na documentação de [desempenho e escala na Durable Functions (Azure Functions)](durable-functions-perf-and-scale.md#instances-table) .

#### <a name="response"></a>Resposta

Os valores de código de status HTTP a seguir podem ser retornados.

* **HTTP 200 (OK)** : O histórico de instâncias foi limpo com êxito.
* **HTTP 404 (não encontrado)** : Não foram encontradas instâncias que correspondam à expressão de filtro.

A carga de resposta para o caso de **HTTP 200** é um objeto JSON com o seguinte campo:

| Campo                   | Tipo de dados | Descrição |
|-------------------------|-----------|-------------|
| **`instancesDeleted`**  | integer   | O número de instâncias excluídas. |

Aqui está um exemplo de carga de resposta (formatada para facilitar a leitura):

```json
{
    "instancesDeleted": 250
}
```

### <a name="raise-event"></a>Gerar evento

Envia uma mensagem de notificação de evento para uma instância de orquestração em execução.

#### <a name="request"></a>Pedir

Para a versão 1. x do tempo de execução do functions, a solicitação é formatada da seguinte maneira (várias linhas são mostradas para maior clareza):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

Na versão 2. x do tempo de execução do functions, o formato da URL tem todos os mesmos parâmetros, mas com um prefixo ligeiramente diferente:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

Os parâmetros de solicitação para essa API incluem o conjunto padrão mencionado anteriormente, bem como os seguintes parâmetros exclusivos:

| Campo             | Tipo de parâmetro  | Descrição |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | A ID da instância de orquestração. |
| **`eventName`**   | URL             | O nome do evento que a instância de orquestração de destino está aguardando. |
| **`{content}`**   | Conteúdo da solicitação | A carga do evento em formato JSON. |

#### <a name="response"></a>Resposta

Vários valores de código de status possíveis podem ser retornados.

* **HTTP 202 (aceito)** : O evento gerado foi aceito para processamento.
* **HTTP 400 (solicitação inadequada)** : O conteúdo da solicitação não era do `application/json` tipo ou não era um JSON válido.
* **HTTP 404 (não encontrado)** : A instância especificada não foi encontrada.
* **HTTP 410 (ausente)** : A instância especificada foi concluída ou falhou e não pode processar nenhum evento gerado.

Aqui está um exemplo de solicitação que envia a cadeia `"incr"` de caracteres JSON para uma instância que aguarda um evento chamado **Operation**:

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code=XXX
Content-Type: application/json
Content-Length: 6

"incr"
```

As respostas para essa API não contêm nenhum conteúdo.

### <a name="terminate-instance"></a>Encerrar instância

Encerra uma instância de orquestração em execução.

#### <a name="request"></a>Pedir

Para a versão 1. x do tempo de execução do functions, a solicitação é formatada da seguinte maneira (várias linhas são mostradas para maior clareza):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Na versão 2. x do tempo de execução do functions, o formato da URL tem todos os mesmos parâmetros, mas com um prefixo ligeiramente diferente:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Os parâmetros de solicitação para essa API incluem o conjunto padrão mencionado anteriormente, bem como o seguinte parâmetro exclusivo.

| Campo             | Tipo de parâmetro  | Descrição |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | A ID da instância de orquestração. |
| **`reason`**      | Cadeia de consulta    | Opcional. O motivo para encerrar a instância de orquestração. |

#### <a name="response"></a>Resposta

Vários valores de código de status possíveis podem ser retornados.

* **HTTP 202 (aceito)** : A solicitação de encerramento foi aceita para processamento.
* **HTTP 404 (não encontrado)** : A instância especificada não foi encontrada.
* **HTTP 410 (ausente)** : A instância especificada foi concluída ou falhou.

Aqui está um exemplo de solicitação que encerra uma instância em execução e especifica um motivo de **bugs**:

```
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason=buggy&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

As respostas para essa API não contêm nenhum conteúdo.

### <a name="rewind-instance-preview"></a>Rebobinar instância (visualização)

Restaura uma instância de orquestração com falha em um estado de execução repetindo as operações com falha mais recentes.

#### <a name="request"></a>Pedir

Para a versão 1. x do tempo de execução do functions, a solicitação é formatada da seguinte maneira (várias linhas são mostradas para maior clareza):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Na versão 2. x do tempo de execução do functions, o formato da URL tem todos os mesmos parâmetros, mas com um prefixo ligeiramente diferente:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Os parâmetros de solicitação para essa API incluem o conjunto padrão mencionado anteriormente, bem como o seguinte parâmetro exclusivo.

| Campo             | Tipo de parâmetro  | Descrição |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | A ID da instância de orquestração. |
| **`reason`**      | Cadeia de consulta    | Opcional. O motivo para retroceder a instância de orquestração. |

#### <a name="response"></a>Resposta

Vários valores de código de status possíveis podem ser retornados.

* **HTTP 202 (aceito)** : A solicitação de retrocesso foi aceita para processamento.
* **HTTP 404 (não encontrado)** : A instância especificada não foi encontrada.
* **HTTP 410 (ausente)** : A instância especificada foi concluída ou foi encerrada.

Aqui está um exemplo de solicitação que rebobina uma instância com falha e especifica um motivo de **fixo**:

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/rewind?reason=fixed&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

As respostas para essa API não contêm nenhum conteúdo.

### <a name="signal-entity-preview"></a>Entidade Signal (visualização)

Envia uma mensagem de operação unidirecional para uma [entidade durável](durable-functions-types-features-overview.md#entity-functions). Se a entidade não existir, ela será criada automaticamente.

#### <a name="request"></a>Pedir

A solicitação HTTP é formatada da seguinte maneira (várias linhas são mostradas para maior clareza):

```http
POST /runtime/webhooks/durabletask/entities/{entityType}/{entityKey}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &op={operationName}
```

Os parâmetros de solicitação para essa API incluem o conjunto padrão mencionado anteriormente, bem como os seguintes parâmetros exclusivos:

| Campo             | Tipo de parâmetro  | Descrição |
|-------------------|-----------------|-------------|
| **`entityType`**  | URL             | O tipo da entidade. |
| **`entityKey`**   | URL             | O nome exclusivo da entidade. |
| **`op`**          | Cadeia de consulta    | Opcional. O nome da operação definida pelo usuário a ser invocada. |
| **`{content}`**   | Conteúdo da solicitação | A carga do evento em formato JSON. |

Aqui está uma solicitação de exemplo que envia uma mensagem de "adição" definida pelo usuário `Counter` a uma `steps`entidade chamada. O conteúdo da mensagem é o valor `5`. Se a entidade ainda não existir, ela será criada por essa solicitação:

```http
POST /runtime/webhooks/durabletask/entities/Counter/steps?op=Add
Content-Type: application/json

5
```

#### <a name="response"></a>Resposta

Esta operação tem várias respostas possíveis:

* **HTTP 202 (aceito)** : A operação de sinal foi aceita para processamento assíncrono.
* **HTTP 400 (solicitação inadequada)** : O conteúdo da solicitação não era do `application/json`tipo, não era um JSON válido ou tinha um `entityKey` valor inválido.
* **HTTP 404 (não encontrado)** : O especificado `entityType` não foi encontrado.

Uma solicitação HTTP bem-sucedida não contém nenhum conteúdo na resposta. Uma solicitação HTTP com falha pode conter informações de erro formatadas em JSON no conteúdo da resposta.

### <a name="query-entity-preview"></a>Entidade de consulta (visualização)

Obtém o estado da entidade especificada.

#### <a name="request"></a>Pedir

A solicitação HTTP é formatada da seguinte maneira (várias linhas são mostradas para maior clareza):

```http
GET /runtime/webhooks/durabletask/entities/{entityType}/{entityKey}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

#### <a name="response"></a>Resposta

Esta operação tem duas respostas possíveis:

* **HTTP 200 (OK)** : A entidade especificada existe.
* **HTTP 404 (não encontrado)** : A entidade especificada não foi encontrada.

Uma resposta bem-sucedida contém o estado serializado em JSON da entidade como seu conteúdo.

#### <a name="example"></a>Exemplo
Veja a seguir um exemplo de uma solicitação HTTP que obtém o estado de uma entidade `Counter` existente chamada `steps`:

```http
GET /runtime/webhooks/durabletask/entities/Counter/steps
```

Se a `Counter` entidade contiver simplesmente uma série de etapas salvas `currentValue` em um campo, o conteúdo da resposta poderá ser semelhante ao seguinte (formatado para facilitar a leitura):

```json
{
    "currentValue": 5
}
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Saiba como lidar com erros](durable-functions-error-handling.md)