---
title: HTTP APIs em Funções Duráveis - Funções Azure
description: Saiba como implementar HTTP APIs na extensão funções duráveis para funções azure.
author: cgillum
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: azfuncdf
ms.openlocfilehash: 4e4081ecca4714c713d105d363a83a4f96a0d3fc
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79278171"
---
# <a name="http-api-reference"></a>Referência http API

A extensão Das Funções Duráveis expõe um conjunto de APIs HTTP incorporados que podem ser usados para executar tarefas de gestão em [orquestrações,](durable-functions-types-features-overview.md#orchestrator-functions) [entidades](durable-functions-types-features-overview.md#entity-functions)e centros de [tarefas.](durable-functions-task-hubs.md) Estes HTTP APIs são webhooks de extebilidade que são autorizados pelo anfitrião funções Azure mas manuseados diretamente pela extensão Funções Duráveis.

Todas as APIs HTTP implementadas pela extensão requerem os seguintes parâmetros. O tipo de dados de todos os parâmetros é `string`.

| Parâmetro        | Tipo de parâmetro  | Descrição |
|------------------|-----------------|-------------|
| **`taskHub`**    | Corda de consulta    | O nome do centro de [tarefas.](durable-functions-task-hubs.md) Caso não seja especificado, assume-se o nome do centro de tarefas da aplicação de função atual. |
| **`connection`** | Corda de consulta    | O **nome** da corda de ligação para a conta de armazenamento. Se não especificado, assume-se a cadeia de ligação predefinida para a aplicação de função. |
| **`systemKey`**  | Corda de consulta    | A chave de autorização necessária para invocar a API. |

`systemKey` é uma chave de autorização autogerada pelo anfitrião funções Azure. Concede especificamente acesso às APIs de extensão de tarefas duráveis e pode ser gerido da mesma forma que [outras chaves](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Key-management-API)de autorização . Pode gerar URLs que contenham os `taskHub`corretos, `connection`, e `systemKey` valores de cordas de consulta utilizando APIs de ligação ao [cliente de orquestração,](durable-functions-bindings.md#orchestration-client) tais como as `CreateCheckStatusResponse` e `CreateHttpManagementPayload` APIs em .NET, ou as `createCheckStatusResponse` e `createHttpManagementPayload` APIs no JavaScript.

As próximas secções cobrem as APIs http específicas suportadas pela extensão e fornecem exemplos de como podem ser usadas.

## <a name="start-orchestration"></a>Iniciar a orquestração

Começa a executar uma nova instância da função orquestradora especificada.

### <a name="request"></a>Pedir

Para a versão 1.x do tempo de funcionamento das Funções, o pedido é formatado da seguinte forma (várias linhas são mostradas para clareza):

```http
POST /admin/extensions/DurableTaskExtension/orchestrators/{functionName}/{instanceId?}
     ?taskHub={taskHub}
     &connection={connectionName}
     &code={systemKey}
```

Na versão 2.x do tempo de funcionamento das Funções, o formato URL tem todos os mesmos parâmetros, mas com um prefixo ligeiramente diferente:

```http
POST /runtime/webhooks/durabletask/orchestrators/{functionName}/{instanceId?}
     ?taskHub={taskHub}
     &connection={connectionName}
     &code={systemKey}
```

Os parâmetros de pedido para esta API incluem o conjunto predefinido mencionado anteriormente, bem como os seguintes parâmetros únicos:

| Campo              | Tipo parâmetro  | Descrição |
|--------------------|-----------------|-------------|
| **`functionName`** | do IdP             | O nome da função orquestradora para começar. |
| **`instanceId`**   | do IdP             | Parâmetro opcional. A identificação da instância da orquestração. Se não especificada, a função de orquestrador começará com um ID de instância aleatória. |
| **`{content}`**    | Solicitar conteúdos | Opcional. A entrada da função de orquestrador formada jSON. |

### <a name="response"></a>Resposta

Vários possíveis valores de código de estado podem ser devolvidos.

* **HTTP 202 (Aceite)** : A função de orquestrador especificada estava programada para começar a funcionar. O cabeçalho de resposta `Location` contém um URL para sondar o estado da orquestração.
* **HTTP 400 (Mau pedido)** : A função de orquestrador especificado não existe, o ID da instância especificado não era válido, ou o conteúdo de pedido não era válido.

Segue-se um pedido de exemplo que inicia uma função orquestradora `RestartVMs` e inclui a carga útil do objeto JSON:

```http
POST /runtime/webhooks/durabletask/orchestrators/RestartVMs?code=XXX
Content-Type: application/json
Content-Length: 83

{
    "resourceGroup": "myRG",
    "subscriptionId": "111deb5d-09df-4604-992e-a968345530a9"
}
```

A carga útil de resposta para os casos **HTTP 202** é um objeto JSON com os seguintes campos:

| Campo                       | Descrição                          |
|-----------------------------|--------------------------------------|
| **`id`**                    |A identificação da instância da orquestração. |
| **`statusQueryGetUri`**     |O estado URL da instância de orquestração. |
| **`sendEventPostUri`**      |O URL "raise event" da instância de orquestração. |
| **`terminatePostUri`**      |O URL "terminate" da instância de orquestração. |
| **`purgeHistoryDeleteUri`** |O URL "purpurhistory" da instância de orquestração. |
| **`rewindPostUri`**         |(pré-visualização) O URL "rebobinar" da instância de orquestração. |

O tipo de dados de todos os campos é `string`.

Aqui está uma carga de resposta exemplo para um caso de orquestração com `abc123` como id (formatado para a legibilidade):

```http
{
    "id": "abc123",
    "purgeHistoryDeleteUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/raiseEvent/{eventName}?code=XXX",
    "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/terminate?reason={text}&code=XXX"
}
```

A resposta HTTP destina-se a ser compatível com o *Padrão do Consumidor de Sondagens*. Também inclui os seguintes cabeçalhos de resposta notáveis:

* **Localização**: O URL do ponto final do estado. Este URL contém o mesmo valor que o campo `statusQueryGetUri`.
* **Retry-After**: O número de segundos para esperar entre as operações de votação. O valor predefinido é `10`.

Para obter mais informações sobre o padrão de sondagem assíncrono http, consulte a documentação de rastreio da [operação http async.](durable-functions-http-features.md#async-operation-tracking)

## <a name="get-instance-status"></a>Obtenha o estado da instância

Obtém o estatuto de uma instância de orquestração especificada.

### <a name="request"></a>Pedir

Para a versão 1.x do tempo de funcionamento das Funções, o pedido é formatado da seguinte forma (várias linhas são mostradas para clareza):

```http
GET /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
```

Na versão 2.x do tempo de funcionamento das Funções, o formato URL tem todos os mesmos parâmetros, mas com um prefixo ligeiramente diferente:

```http
GET /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
```

Os parâmetros de pedido para esta API incluem o conjunto predefinido mencionado anteriormente, bem como os seguintes parâmetros únicos:

| Campo                   | Tipo parâmetro  | Descrição |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | do IdP             | A identificação da instância da orquestração. |
| **`showInput`**         | Corda de consulta    | Parâmetro opcional. Se for definido para `false`, a entrada de função não será incluída na carga útil da resposta.|
| **`showHistory`**       | Corda de consulta    | Parâmetro opcional. Se for definido para `true`, o histórico de execução da orquestração será incluído na carga útil da resposta.|
| **`showHistoryOutput`** | Corda de consulta    | Parâmetro opcional. Se for definido para `true`, as saídas de função serão incluídas na história da execução da orquestração.|
| **`createdTimeFrom`**   | Corda de consulta    | Parâmetro opcional. Quando especificado, filtra a lista de instâncias devolvidas que foram criadas na ou após a data-limite iso8601 dada.|
| **`createdTimeTo`**     | Corda de consulta    | Parâmetro opcional. Quando especificado, filtra a lista de instâncias devolvidas que foram criadas na ou antes da data-limite iso8601 dada.|
| **`runtimeStatus`**     | Corda de consulta    | Parâmetro opcional. Quando especificado, filtra a lista de instâncias devolvidas com base no seu estado de tempo de execução. Para ver a lista de possíveis valores de estado de execução, consulte o artigo [de casos de consulta.](durable-functions-instance-management.md) |

### <a name="response"></a>Resposta

Vários possíveis valores de código de estado podem ser devolvidos.

* **HTTP 200 (OK)** : A instância especificada encontra-se em estado concluído.
* **HTTP 202 (Aceite)** : A instância especificada está em curso.
* **HTTP 400 (Pedido Mau)** : A instância especificada falhou ou foi encerrada.
* **HTTP 404 (Não Encontrado)** : A instância especificada não existe ou não começou a funcionar.
* **HTTP 500 (Erro interno**do servidor) : A instância especificada falhou com uma exceção não tratada.

A carga útil de resposta para os casos **HTTP 200** e **HTTP 202** é um objeto JSON com os seguintes campos:

| Campo                 | Tipo de dados | Descrição |
|-----------------------|-----------|-------------|
| **`runtimeStatus`**   | string    | O estado de execução da ocorrência. Os valores incluem *Execução,* *Pendente,* *Falhado,* *Cancelado,* *Terminado,* *Concluído*. |
| **`input`**           | JSON      | Os dados da JSON utilizados para inicializar a instância. Este campo é `null` se o parâmetro de corda de consulta `showInput` estiver definido para `false`.|
| **`customStatus`**    | JSON      | Os dados da JSON utilizados para o estatuto de orquestração personalizada. Este campo é `null` se não for definido. |
| **`output`**          | JSON      | A saída json da ocorrência. Este campo é `null` se a instância não estiver em um estado completo. |
| **`createdTime`**     | string    | O momento em que a instância foi criada. Utiliza notação estendida ISO 8601. |
| **`lastUpdatedTime`** | string    | O tempo em que o caso persistiu pela última vez. Utiliza notação estendida ISO 8601. |
| **`historyEvents`**   | JSON      | Uma matriz JSON contendo o histórico de execução da orquestração. Este campo é `null` a menos que o parâmetro de corda de consulta `showHistory` esteja definido para `true`. |

Aqui está uma carga de resposta exemplo, incluindo o histórico de execução da orquestração e saídas de atividade (formatados para a legibilidade):

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

A resposta **HTTP 202** também inclui **um** cabeçalho de resposta localização que refere o mesmo URL que o campo `statusQueryGetUri` mencionado anteriormente.

## <a name="get-all-instances-status"></a>Obtenha o estatuto de todos os casos

Também pode consultar o estado de todas as instâncias removendo o `instanceId` do pedido de "Obter o estado da instância". Neste caso, os parâmetros básicos são os mesmos que o "Obter o estatuto de instância". Também são suportados parâmetros de corda de consulta para filtragem.

Uma coisa a lembrar é que `connection` e `code` são opcionais. Se tens um auth anónimo na função, então não é necessário `code`.
Se não quiser utilizar uma cadeia de ligação de armazenamento diferente da definida na definição da aplicação AzureWebJobsStorage, então pode ignorar com segurança o parâmetro de cadeia de consulta de ligação.

### <a name="request"></a>Pedir

Para a versão 1.x do tempo de funcionamento das Funções, o pedido é formatado da seguinte forma (várias linhas são mostradas para clareza):

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

Na versão 2.x do tempo de funcionamento das Funções, o formato URL tem todos os mesmos parâmetros, mas com um prefixo ligeiramente diferente:

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

Os parâmetros de pedido para esta API incluem o conjunto predefinido mencionado anteriormente, bem como os seguintes parâmetros únicos:

| Campo                   | Tipo parâmetro  | Descrição |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | do IdP             | A identificação da instância da orquestração. |
| **`showInput`**         | Corda de consulta    | Parâmetro opcional. Se for definido para `false`, a entrada de função não será incluída na carga útil da resposta.|
| **`showHistory`**       | Corda de consulta    | Parâmetro opcional. Se for definido para `true`, o histórico de execução da orquestração será incluído na carga útil da resposta.|
| **`showHistoryOutput`** | Corda de consulta    | Parâmetro opcional. Se for definido para `true`, as saídas de função serão incluídas na história da execução da orquestração.|
| **`createdTimeFrom`**   | Corda de consulta    | Parâmetro opcional. Quando especificado, filtra a lista de instâncias devolvidas que foram criadas na ou após a data-limite iso8601 dada.|
| **`createdTimeTo`**     | Corda de consulta    | Parâmetro opcional. Quando especificado, filtra a lista de instâncias devolvidas que foram criadas na ou antes da data-limite iso8601 dada.|
| **`runtimeStatus`**     | Corda de consulta    | Parâmetro opcional. Quando especificado, filtra a lista de instâncias devolvidas com base no seu estado de tempo de execução. Para ver a lista de possíveis valores de estado de execução, consulte o artigo [de casos de consulta.](durable-functions-instance-management.md) |
| **`top`**               | Corda de consulta    | Parâmetro opcional. Quando especificado, limita o número de instâncias devolvidas pela consulta. |

### <a name="response"></a>Resposta

Aqui está um exemplo de cargas de resposta, incluindo o estatuto de orquestração (formatado para a legibilidade):

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
> Esta operação pode ser muito cara em termos de Armazenamento Azure I/O se houver muitas linhas na tabela Instances. Mais detalhes sobre a tabela Instance podem ser encontrados na documentação [performance e escala em Funções Duráveis (Funções Azure).](durable-functions-perf-and-scale.md#instances-table)
>

Se existirem mais resultados, um token de continuação é devolvido no cabeçalho de resposta.  O nome do cabeçalho é `x-ms-continuation-token`.

Se definir o valor do token de continuação no próximo cabeçalho de pedido, pode obter a próxima página de resultados. Este nome do cabeçalho de pedido também é `x-ms-continuation-token`.

## <a name="purge-single-instance-history"></a>Expurgar a história de uma instância única

Elimina a história e os artefactos relacionados para uma determinada instância de orquestração.

### <a name="request"></a>Pedir

Para a versão 1.x do tempo de funcionamento das Funções, o pedido é formatado da seguinte forma (várias linhas são mostradas para clareza):

```http
DELETE /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

Na versão 2.x do tempo de funcionamento das Funções, o formato URL tem todos os mesmos parâmetros, mas com um prefixo ligeiramente diferente:

```http
DELETE /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

Os parâmetros de pedido para esta API incluem o conjunto predefinido mencionado anteriormente, bem como os seguintes parâmetros únicos:

| Campo             | Tipo parâmetro  | Descrição |
|-------------------|-----------------|-------------|
| **`instanceId`**  | do IdP             | A identificação da instância da orquestração. |

### <a name="response"></a>Resposta

Os seguintes valores de código de estado HTTP podem ser devolvidos.

* **HTTP 200 (OK)** : A história da instância foi expurgada com sucesso.
* **HTTP 404 (Não Encontrado)** : A instância especificada não existe.

A carga útil de resposta para o caso **HTTP 200** é um objeto JSON com o seguinte campo:

| Campo                  | Tipo de dados | Descrição |
|------------------------|-----------|-------------|
| **`instancesDeleted`** | número inteiro   | O número de instâncias suprimidas. Para o caso de um caso único, este valor deve ser sempre `1`. |

Aqui está uma carga útil de resposta de exemplo (formatada para a legibilidade):

```json
{
    "instancesDeleted": 1
}
```

## <a name="purge-multiple-instance-histories"></a>Expurgar histórias de múltiplas instâncias

Também pode eliminar a história e artefactos relacionados por vários casos dentro de um centro de tarefas, removendo o `{instanceId}` do pedido de "Purpur de instância única". Para expurgar seletivamente o histórico de instâncias, utilize os mesmos filtros descritos no pedido "Obter todos os casos" pedido.

### <a name="request"></a>Pedir

Para a versão 1.x do tempo de funcionamento das Funções, o pedido é formatado da seguinte forma (várias linhas são mostradas para clareza):

```http
DELETE /admin/extensions/DurableTaskExtension/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

Na versão 2.x do tempo de funcionamento das Funções, o formato URL tem todos os mesmos parâmetros, mas com um prefixo ligeiramente diferente:

```http
DELETE /runtime/webhooks/durabletask/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

Os parâmetros de pedido para esta API incluem o conjunto predefinido mencionado anteriormente, bem como os seguintes parâmetros únicos:

| Campo                 | Tipo parâmetro  | Descrição |
|-----------------------|-----------------|-------------|
| **`createdTimeFrom`** | Corda de consulta    | Filtra a lista de instâncias purgadas que foram criadas na ou após a data-carimbo ISO8601.|
| **`createdTimeTo`**   | Corda de consulta    | Parâmetro opcional. Quando especificado, filtra a lista de instâncias purgadas que foram criadas na ou antes da data-limite iso8601 dada.|
| **`runtimeStatus`**   | Corda de consulta    | Parâmetro opcional. Quando especificado, filtra a lista de instâncias purgadas com base no seu estado de execução. Para ver a lista de possíveis valores de estado de execução, consulte o artigo [de casos de consulta.](durable-functions-instance-management.md) |

> [!NOTE]
> Esta operação pode ser muito cara em termos de Armazenamento Azure I/O se houver muitas linhas nas tabelas De Instâncias e/ou História. Mais detalhes sobre estas tabelas podem ser encontrados na documentação [performance e escala em Funções Duráveis (Funções Azure).](durable-functions-perf-and-scale.md#instances-table)

### <a name="response"></a>Resposta

Os seguintes valores de código de estado HTTP podem ser devolvidos.

* **HTTP 200 (OK)** : A história da instância foi expurgada com sucesso.
* **HTTP 404 (Não Encontrado)** : Não foram encontrados casos que correspondam à expressão do filtro.

A carga útil de resposta para o caso **HTTP 200** é um objeto JSON com o seguinte campo:

| Campo                   | Tipo de dados | Descrição |
|-------------------------|-----------|-------------|
| **`instancesDeleted`**  | número inteiro   | O número de instâncias suprimidas. |

Aqui está uma carga útil de resposta de exemplo (formatada para a legibilidade):

```json
{
    "instancesDeleted": 250
}
```

## <a name="raise-event"></a>Evento de aumento

Envia uma mensagem de notificação de evento para uma instância de orquestração em execução.

### <a name="request"></a>Pedir

Para a versão 1.x do tempo de funcionamento das Funções, o pedido é formatado da seguinte forma (várias linhas são mostradas para clareza):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

Na versão 2.x do tempo de funcionamento das Funções, o formato URL tem todos os mesmos parâmetros, mas com um prefixo ligeiramente diferente:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

Os parâmetros de pedido para esta API incluem o conjunto predefinido mencionado anteriormente, bem como os seguintes parâmetros únicos:

| Campo             | Tipo parâmetro  | Descrição |
|-------------------|-----------------|-------------|
| **`instanceId`**  | do IdP             | A identificação da instância da orquestração. |
| **`eventName`**   | do IdP             | O nome do evento que a instância de orquestração alvo está à espera. |
| **`{content}`**   | Solicitar conteúdos | A carga útil do evento formatado jSON. |

### <a name="response"></a>Resposta

Vários possíveis valores de código de estado podem ser devolvidos.

* **HTTP 202 (Aceite)** : O evento levantado foi aceite para processamento.
* **HTTP 400 (Pedido mau)** : O conteúdo do pedido não era de tipo `application/json` ou não era válido.
* **HTTP 404 (Não Encontrado)** : A instância especificada não foi encontrada.
* **HTTP 410 (Desaparecido)** : A instância especificada completou ou falhou e não pode processar quaisquer eventos levantados.

Aqui está um pedido de exemplo que envia a cadeia JSON `"incr"` a uma instância à espera de um evento chamado **operação:**

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code=XXX
Content-Type: application/json
Content-Length: 6

"incr"
```

As respostas para esta API não contêm qualquer conteúdo.

## <a name="terminate-instance"></a>Encerrar instância

Termina uma instância de orquestração.

### <a name="request"></a>Pedir

Para a versão 1.x do tempo de funcionamento das Funções, o pedido é formatado da seguinte forma (várias linhas são mostradas para clareza):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Na versão 2.x do tempo de funcionamento das Funções, o formato URL tem todos os mesmos parâmetros, mas com um prefixo ligeiramente diferente:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Os parâmetros de pedido para esta API incluem o conjunto predefinido mencionado anteriormente, bem como o seguinte parâmetro único.

| Campo             | Tipo de parâmetro  | Descrição |
|-------------------|-----------------|-------------|
| **`instanceId`**  | do IdP             | A identificação da instância da orquestração. |
| **`reason`**      | Corda de consulta    | Opcional. A razão para terminar o caso da orquestração. |

### <a name="response"></a>Resposta

Vários possíveis valores de código de estado podem ser devolvidos.

* **HTTP 202 (Aceite)** : O pedido de rescisão foi aceite para processamento.
* **HTTP 404 (Não Encontrado)** : A instância especificada não foi encontrada.
* **HTTP 410 (Desaparecido)** : A instância especificada completou ou falhou.

Aqui está um pedido de exemplo que termina uma instância de execução e especifica uma razão de **buggy:**

```
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason=buggy&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

As respostas para esta API não contêm qualquer conteúdo.

## <a name="rewind-instance-preview"></a>Caso de rebobinar (pré-visualização)

Restaura um caso de orquestração falhado em estado de corrida, reproduzindo as mais recentes operações falhadas.

### <a name="request"></a>Pedir

Para a versão 1.x do tempo de funcionamento das Funções, o pedido é formatado da seguinte forma (várias linhas são mostradas para clareza):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Na versão 2.x do tempo de funcionamento das Funções, o formato URL tem todos os mesmos parâmetros, mas com um prefixo ligeiramente diferente:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Os parâmetros de pedido para esta API incluem o conjunto predefinido mencionado anteriormente, bem como o seguinte parâmetro único.

| Campo             | Tipo de parâmetro  | Descrição |
|-------------------|-----------------|-------------|
| **`instanceId`**  | do IdP             | A identificação da instância da orquestração. |
| **`reason`**      | Corda de consulta    | Opcional. A razão para rebobinar o caso da orquestração. |

### <a name="response"></a>Resposta

Vários possíveis valores de código de estado podem ser devolvidos.

* **HTTP 202 (Aceite)** : O pedido de rebobinar foi aceite para processamento.
* **HTTP 404 (Não Encontrado)** : A instância especificada não foi encontrada.
* **HTTP 410 (Desaparecido)** : A instância especificada já terminou ou foi encerrada.

Aqui está um pedido de exemplo que rebobina uma instância falhada e especifica uma razão de **fixação:**

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/rewind?reason=fixed&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

As respostas para esta API não contêm qualquer conteúdo.

## <a name="signal-entity"></a>Entidade de sinalização

Envia uma mensagem de operação de ida para uma [Entidade Durável.](durable-functions-types-features-overview.md#entity-functions) Se a entidade não existir, será criada automaticamente.

> [!NOTE]
> Entidades duráveis estão disponíveis a partir de Funções Duráveis 2.0.

### <a name="request"></a>Pedir

O pedido HTTP é formatado da seguinte forma (várias linhas são mostradas para clareza):

```http
POST /runtime/webhooks/durabletask/entities/{entityName}/{entityKey}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &op={operationName}
```

Os parâmetros de pedido para esta API incluem o conjunto predefinido mencionado anteriormente, bem como os seguintes parâmetros únicos:

| Campo             | Tipo parâmetro  | Descrição |
|-------------------|-----------------|-------------|
| **`entityName`**  | do IdP             | O nome (tipo) da entidade. |
| **`entityKey`**   | do IdP             | A chave (ID única) da entidade. |
| **`op`**          | Corda de consulta    | Opcional. O nome da operação definida pelo utilizador para invocar. |
| **`{content}`**   | Solicitar conteúdos | A carga útil do evento formatado jSON. |

Aqui está um pedido de exemplo que envia uma mensagem "Adicionar" definida pelo utilizador a uma entidade `Counter` chamada `steps`. O conteúdo da mensagem é o valor `5`. Se a entidade ainda não existir, será criada por este pedido:

```http
POST /runtime/webhooks/durabletask/entities/Counter/steps?op=Add
Content-Type: application/json

5
```

> [!NOTE]
> Por defeito com [entidades de classe em .NET,](durable-functions-dotnet-entities.md#defining-entity-classes)especificando o valor `op` de `delete` eliminará o estado de uma entidade. Se a entidade definir uma operação denominada `delete`, no entanto, essa operação definida pelo utilizador será invocada.

### <a name="response"></a>Resposta

Esta operação tem várias respostas possíveis:

* **HTTP 202 (Aceite)** : A operação de sinalização foi aceite para processamento assíncrono.
* **HTTP 400 (Pedido mau)** : O conteúdo do pedido não era de tipo `application/json`, não era válido, nem tinha um valor `entityKey` inválido.
* **HTTP 404 (Não Encontrado)** : A `entityName` especificada não foi encontrada.

Um pedido http bem sucedido não contém qualquer conteúdo na resposta. Um pedido http falhado pode conter informações de erro formatadas pela JSON no conteúdo da resposta.

## <a name="get-entity"></a>Obter entidade

Obtém o estado da entidade especificada.

### <a name="request"></a>Pedir

O pedido HTTP é formatado da seguinte forma (várias linhas são mostradas para clareza):

```http
GET /runtime/webhooks/durabletask/entities/{entityName}/{entityKey}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

### <a name="response"></a>Resposta

Esta operação tem duas respostas possíveis:

* **HTTP 200 (OK)** : A entidade especificada existe.
* **HTTP 404 (Não Encontrado)** : A entidade especificada não foi encontrada.

Uma resposta bem sucedida contém o estado serializado da JSON da entidade como seu conteúdo.

### <a name="example"></a>Exemplo
O seguinte pedido http recebe o estado de uma entidade `Counter` existente chamada `steps`:

```http
GET /runtime/webhooks/durabletask/entities/Counter/steps
```

Se a entidade `Counter` simplesmente continha uma série de passos guardados num campo `currentValue`, o conteúdo de resposta pode parecer o seguinte (formatado para a legibilidade):

```json
{
    "currentValue": 5
}
```

## <a name="list-entities"></a>Listar entidades

Pode consultar várias entidades pelo nome da entidade ou até à última data de operação.

### <a name="request"></a>Pedir

O pedido HTTP é formatado da seguinte forma (várias linhas são mostradas para clareza):

```http
GET /runtime/webhooks/durabletask/entities/{entityName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &lastOperationTimeFrom={timestamp}
    &lastOperationTimeTo={timestamp}
    &fetchState=[true|false]
    &top={integer}
```

Os parâmetros de pedido para esta API incluem o conjunto predefinido mencionado anteriormente, bem como os seguintes parâmetros únicos:

| Campo                       | Tipo parâmetro  | Descrição |
|-----------------------------|-----------------|-------------|
| **`entityName`**            | do IdP             | Opcional. Quando especificado, filtra a lista de entidades devolvidas pelo seu nome de entidade (caso insensível). |
| **`fetchState`**            | Corda de consulta    | Parâmetro opcional. Se for definido para `true`, o estado da entidade será incluído na carga útil da resposta. |
| **`lastOperationTimeFrom`** | Corda de consulta    | Parâmetro opcional. Quando especificado, filtra a lista de entidades devolvidas que processaram operações após a data-limite iso8601 dada. |
| **`lastOperationTimeTo`**   | Corda de consulta    | Parâmetro opcional. Quando especificado, filtra a lista de entidades devolvidas que processavam as operações antes da data-limite iso8601 dada. |
| **`top`**                   | Corda de consulta    | Parâmetro opcional. Quando especificado, limita o número de entidades devolvidas pela consulta. |


### <a name="response"></a>Resposta

Uma resposta bem sucedida http 200 contém um conjunto de entidades sériedas com JSON e opcionalmente o estado de cada entidade.

Por defeito, a operação devolve as primeiras 100 entidades que correspondem aos critérios de consulta. O chamador pode especificar um valor de parâmetro de corda de consulta para `top` devolver um número máximo diferente de resultados. Se existirem mais resultados para além do que é devolvido, um token de continuação também é devolvido no cabeçalho de resposta. O nome do cabeçalho é `x-ms-continuation-token`.

Se definir o valor do token de continuação no próximo cabeçalho de pedido, pode obter a próxima página de resultados. Este nome do cabeçalho de pedido também é `x-ms-continuation-token`.

### <a name="example---list-all-entities"></a>Exemplo - listar todas as entidades

O seguinte exemplo de pedido HTTP lista todas as entidades no centro de tarefas:

```http
GET /runtime/webhooks/durabletask/entities
```

A resposta JSON pode parecer a seguinte (formatada para a legibilidade):

```json
[
    {
        "entityId": { "key": "cats", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:45:44.6326361Z",
    },
    {
        "entityId": { "key": "dogs", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:46:01.9477382Z"
    },
    {
        "entityId": { "key": "mice", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:46:15.4626159Z"
    },
    {
        "entityId": { "key": "radio", "name": "device" },
        "lastOperationTime": "2019-12-18T21:46:18.2616154Z"
    },
]
```

### <a name="example---filtering-the-list-of-entities"></a>Exemplo - filtrar a lista de entidades

O seguinte exemplo http request lista apenas as duas primeiras entidades do tipo `counter` e também requer o seu estado:

```http
GET /runtime/webhooks/durabletask/entities/counter?top=2&fetchState=true
```

A resposta JSON pode parecer a seguinte (formatada para a legibilidade):

```json
[
    {
        "entityId": { "key": "cats", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:45:44.6326361Z",
        "state": { "value": 9 }
    },
    {
        "entityId": { "key": "dogs", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:46:01.9477382Z",
        "state": { "value": 10 }
    }
]
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba como usar os Insights de Aplicação para monitorizar as suas funções duradouras](durable-functions-diagnostics.md)