---
title: APIs HTTP em Funções Duradouras - Funções Azure
description: Saiba como implementar APIs HTTP na extensão de Funções Duradouras para Funções Azure.
author: cgillum
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: azfuncdf
ms.openlocfilehash: 4e4081ecca4714c713d105d363a83a4f96a0d3fc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84697848"
---
# <a name="http-api-reference"></a>Referência HTTP API

A extensão Funções Duradouras expõe um conjunto de APIs HTTP incorporadas que podem ser usadas para executar tarefas de gestão em [orquestrações,](durable-functions-types-features-overview.md#orchestrator-functions) [entidades](durable-functions-types-features-overview.md#entity-functions)e centros de [tarefas.](durable-functions-task-hubs.md) Estas APIs HTTP são webhooks de extensibilidade que são autorizados pelo anfitrião Azure Functions, mas manuseados diretamente pela extensão funções duradouras.

Todas as APIs HTTP implementadas pela extensão requerem os seguintes parâmetros. O tipo de dados de todos os parâmetros é `string` .

| Parâmetro        | Tipo de parâmetro  | Descrição |
|------------------|-----------------|-------------|
| **`taskHub`**    | Cadeias de consulta    | O nome do centro de [tarefas.](durable-functions-task-hubs.md) Se não for especificado, assume-se o nome do centro de tarefas da aplicação de funções atual. |
| **`connection`** | Cadeias de consulta    | O **nome** da cadeia de ligação para a conta de armazenamento. Se não for especificado, assume-se a cadeia de ligação predefinida para a aplicação de funções. |
| **`systemKey`**  | Cadeias de consulta    | A chave de autorização necessária para invocar a API. |

`systemKey` é uma chave de autorização autogerida pelo anfitrião Azure Functions. Concede especificamente acesso às APIs de extensão de tarefa duradoura e pode ser gerida da mesma forma que [outras chaves de autorização.](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Key-management-API) Pode gerar URLs que contenham os `taskHub` `connection` valores corretos, e `systemKey` consultar valores de cadeia usando APIs de ligação ao cliente de [orquestração,](durable-functions-bindings.md#orchestration-client) tais como as `CreateCheckStatusResponse` APIs e `CreateHttpManagementPayload` APIs em .NET, ou as `createCheckStatusResponse` APIs e `createHttpManagementPayload` APIs em JavaScript.

As próximas secções abrangem as APIs http específicas suportadas pela extensão e fornecem exemplos de como podem ser utilizadas.

## <a name="start-orchestration"></a>Iniciar orquestração

Começa a executar uma nova instância da função orquestradora especificada.

### <a name="request"></a>Pedir

Para a versão 1.x do tempo de execução das Funções, o pedido é formatado da seguinte forma (várias linhas são mostradas para clareza):

```http
POST /admin/extensions/DurableTaskExtension/orchestrators/{functionName}/{instanceId?}
     ?taskHub={taskHub}
     &connection={connectionName}
     &code={systemKey}
```

Na versão 2.x do tempo de execução das Funções, o formato URL tem todos os mesmos parâmetros, mas com um prefixo ligeiramente diferente:

```http
POST /runtime/webhooks/durabletask/orchestrators/{functionName}/{instanceId?}
     ?taskHub={taskHub}
     &connection={connectionName}
     &code={systemKey}
```

Os parâmetros de pedido para esta API incluem o conjunto predefinido mencionado anteriormente, bem como os seguintes parâmetros únicos:

| Campo              | Tipo de parâmetro  | Descrição |
|--------------------|-----------------|-------------|
| **`functionName`** | URL             | O nome da função orquestradora para começar. |
| **`instanceId`**   | URL             | Parâmetro opcional. A identificação da instância de orquestração. Se não for especificada, a função do orquestrador começará com um ID de instância aleatória. |
| **`{content}`**    | Conteúdo do pedido | Opcional. A entrada da função orquestradora formatada pelo JSON. |

### <a name="response"></a>Resposta

Vários valores possíveis de código de estado podem ser devolvidos.

* **HTTP 202 (Aceite)**: A função orquestradora especificada estava programada para começar a funcionar. O `Location` cabeçalho de resposta contém uma URL para sondar o estado da orquestração.
* **HTTP 400 (Mau pedido)**: A função orquestradora especificada não existe, o ID de instância especificado não era válido, ou o conteúdo do pedido não era válido JSON.

Segue-se um pedido de exemplo que inicia uma `RestartVMs` função de orquestrador e inclui a carga útil do objeto JSON:

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
| **`id`**                    |A identificação da instância de orquestração. |
| **`statusQueryGetUri`**     |O estado URL da instância de orquestração. |
| **`sendEventPostUri`**      |A URL "raise event" da instância de orquestração. |
| **`terminatePostUri`**      |A URL "terminada" da instância de orquestração. |
| **`purgeHistoryDeleteUri`** |A URL "purgar a história" do exemplo da orquestração. |
| **`rewindPostUri`**         |(pré-visualização) A URL "rebobinar" da instância de orquestração. |

O tipo de dados de todos os campos `string` é.

Aqui está um exemplo de carga útil de resposta para uma instância de orquestração com `abc123` como o seu ID (formatado para a legibilidade):

```http
{
    "id": "abc123",
    "purgeHistoryDeleteUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/raiseEvent/{eventName}?code=XXX",
    "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/terminate?reason={text}&code=XXX"
}
```

A resposta HTTP destina-se a ser compatível com o *Padrão do Consumidor de Sondagens.* Inclui também os seguintes cabeçalhos de resposta notáveis:

* **Localização**: O URL do ponto final de estado. Este URL contém o mesmo valor que o `statusQueryGetUri` campo.
* **Reda o seguinte:** O número de segundos a esperar entre as operações de sondagem. O valor predefinido é `10`.

Para obter mais informações sobre o padrão de sondagem HTTP assíncronos, consulte a documentação [de rastreio da operação HTTP async.](durable-functions-http-features.md#async-operation-tracking)

## <a name="get-instance-status"></a>Obtenha o estado da instância

Obtém o estatuto de uma instância de orquestração especificada.

### <a name="request"></a>Pedir

Para a versão 1.x do tempo de execução das Funções, o pedido é formatado da seguinte forma (várias linhas são mostradas para clareza):

```http
GET /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
```

Na versão 2.x do tempo de execução das Funções, o formato URL tem todos os mesmos parâmetros, mas com um prefixo ligeiramente diferente:

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

| Campo                   | Tipo de parâmetro  | Descrição |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | URL             | A identificação da instância de orquestração. |
| **`showInput`**         | Cadeias de consulta    | Parâmetro opcional. Se for `false` definida, a entrada da função não será incluída na carga útil da resposta.|
| **`showHistory`**       | Cadeias de consulta    | Parâmetro opcional. Se for `true` definido, o histórico de execução da orquestração será incluído na carga útil da resposta.|
| **`showHistoryOutput`** | Cadeias de consulta    | Parâmetro opcional. Se for `true` definido, as saídas de funções serão incluídas no histórico de execução da orquestração.|
| **`createdTimeFrom`**   | Cadeias de consulta    | Parâmetro opcional. Quando especificado, filtra a lista de casos devolvidos que foram criados no ou após o determinado semp de tempo ISO8601.|
| **`createdTimeTo`**     | Cadeias de consulta    | Parâmetro opcional. Quando especificado, filtra a lista de casos devolvidos que foram criados no ou antes do determinado semp de tempo ISO8601.|
| **`runtimeStatus`**     | Cadeias de consulta    | Parâmetro opcional. Quando especificado, filtra a lista de casos devolvidos com base no seu estado de funcionação. Para ver a lista de possíveis valores de estado de funcionamento, consulte o artigo [casos de consulta.](durable-functions-instance-management.md) |

### <a name="response"></a>Resposta

Vários valores possíveis de código de estado podem ser devolvidos.

* **HTTP 200 (OK)**: A instância especificada encontra-se num estado completo.
* **HTTP 202 (Aceite)**: A instância especificada está em curso.
* **HTTP 400 (Mau Pedido)**: A instância especificada falhou ou foi encerrada.
* **HTTP 404 (Não Encontrado)**: A instância especificada não existe ou não começou a funcionar.
* **HTTP 500 (Erro do Servidor Interno)**: A instância especificada falhou com uma exceção não manipulada.

A carga útil de resposta para os casos **HTTP 200** e **HTTP 202** é um objeto JSON com os seguintes campos:

| Campo                 | Tipo de dados | Descrição |
|-----------------------|-----------|-------------|
| **`runtimeStatus`**   | cadeia (de carateres)    | O estado de funcionação do caso. Os valores incluem *Execução,* *Pendente,* *Falhado,* *Cancelado,* *Terminado,* *Concluído*. |
| **`input`**           | JSON      | Os dados do JSON usados para inicializar o caso. Este campo é `null` se o parâmetro de cadeia de consulta está definido para `showInput` `false` .|
| **`customStatus`**    | JSON      | Os dados JSON utilizados para o estado de orquestração personalizada. Este campo `null` é, se não estiver definido. |
| **`output`**          | JSON      | A saída JSON do caso. Este campo é `null` se o caso não estiver em estado completo. |
| **`createdTime`**     | string    | O momento em que o caso foi criado. Utiliza notação estendida ISO 8601. |
| **`lastUpdatedTime`** | string    | O momento em que o caso persistiu pela última vez. Utiliza notação estendida ISO 8601. |
| **`historyEvents`**   | JSON      | Uma matriz JSON contendo o histórico de execução da orquestração. Este campo `null` é, a menos `showHistory` que o parâmetro de cadeia de consulta esteja definido para `true` . |

Aqui está um exemplo de carga útil de resposta, incluindo o histórico de execução da orquestração e saídas de atividade (formatadas para a legibilidade):

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

A resposta **HTTP 202** também inclui um cabeçalho de resposta **de localização** que faz referência ao mesmo URL que o `statusQueryGetUri` campo mencionado anteriormente.

## <a name="get-all-instances-status"></a>Obtenha o estado de todas as instâncias

Também pode consultar o estado de todas as instâncias removendo `instanceId` o pedido de "Obter o estado da instância". Neste caso, os parâmetros básicos são os mesmos que o "Obter o estado da instância". Os parâmetros de cadeia de consulta para filtragem também são suportados.

Uma coisa a lembrar é que `connection` e `code` são opcionais. Se tem auth anónimo sobre a função, então `code` não é necessário.
Se não pretender utilizar um fio de ligação de armazenamento diferente do definido na definição da aplicação AzureWebJobsStorage, então pode ignorar com segurança o parâmetro de cadeia de consulta de ligação.

### <a name="request"></a>Pedir

Para a versão 1.x do tempo de execução das Funções, o pedido é formatado da seguinte forma (várias linhas são mostradas para clareza):

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

Na versão 2.x do tempo de execução das Funções, o formato URL tem todos os mesmos parâmetros, mas com um prefixo ligeiramente diferente:

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

| Campo                   | Tipo de parâmetro  | Descrição |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | URL             | A identificação da instância de orquestração. |
| **`showInput`**         | Cadeias de consulta    | Parâmetro opcional. Se for `false` definida, a entrada da função não será incluída na carga útil da resposta.|
| **`showHistory`**       | Cadeias de consulta    | Parâmetro opcional. Se for `true` definido, o histórico de execução da orquestração será incluído na carga útil da resposta.|
| **`showHistoryOutput`** | Cadeias de consulta    | Parâmetro opcional. Se for `true` definido, as saídas de funções serão incluídas no histórico de execução da orquestração.|
| **`createdTimeFrom`**   | Cadeias de consulta    | Parâmetro opcional. Quando especificado, filtra a lista de casos devolvidos que foram criados no ou após o determinado semp de tempo ISO8601.|
| **`createdTimeTo`**     | Cadeias de consulta    | Parâmetro opcional. Quando especificado, filtra a lista de casos devolvidos que foram criados no ou antes do determinado semp de tempo ISO8601.|
| **`runtimeStatus`**     | Cadeias de consulta    | Parâmetro opcional. Quando especificado, filtra a lista de casos devolvidos com base no seu estado de funcionação. Para ver a lista de possíveis valores de estado de funcionamento, consulte o artigo [casos de consulta.](durable-functions-instance-management.md) |
| **`top`**               | Cadeias de consulta    | Parâmetro opcional. Quando especificado, limita o número de casos devolvidos pela consulta. |

### <a name="response"></a>Resposta

Aqui está um exemplo de cargas de resposta, incluindo o estado de orquestração (formatado para a legibilidade):

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
> Esta operação pode ser muito cara em termos de Azure Storage I/O se houver muitas linhas na tabela Instâncias. Mais detalhes sobre a tabela Exemplo podem ser encontrados na documentação [performance e escala em Funções Duradouras (Funções Azure).](durable-functions-perf-and-scale.md#instances-table)
>

Se houver mais resultados, um sinal de continuação é devolvido no cabeçalho de resposta.  O nome do cabeçalho é `x-ms-continuation-token` .

Se definir o valor do token de continuação no próximo cabeçalho de pedido, pode obter a próxima página de resultados. Este nome do cabeçalho do pedido também é `x-ms-continuation-token` .

## <a name="purge-single-instance-history"></a>Purgar a história de um único caso

Elimina a história e artefactos relacionados para uma instância de orquestração especificada.

### <a name="request"></a>Pedir

Para a versão 1.x do tempo de execução das Funções, o pedido é formatado da seguinte forma (várias linhas são mostradas para clareza):

```http
DELETE /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

Na versão 2.x do tempo de execução das Funções, o formato URL tem todos os mesmos parâmetros, mas com um prefixo ligeiramente diferente:

```http
DELETE /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

Os parâmetros de pedido para esta API incluem o conjunto predefinido mencionado anteriormente, bem como os seguintes parâmetros únicos:

| Campo             | Tipo de parâmetro  | Descrição |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | A identificação da instância de orquestração. |

### <a name="response"></a>Resposta

Os seguintes valores de código de estado HTTP podem ser devolvidos.

* **HTTP 200 (OK)**: A história do caso foi purgada com sucesso.
* **HTTP 404 (Não Encontrado)**: A instância especificada não existe.

A carga útil de resposta para o caso **HTTP 200** é um objeto JSON com o seguinte campo:

| Campo                  | Tipo de dados | Descrição |
|------------------------|-----------|-------------|
| **`instancesDeleted`** | número inteiro   | O número de ocorrências apagadas. No caso de um único caso, este valor deve ser sempre `1` . |

Aqui está um exemplo de carga útil de resposta (formatada para a legibilidade):

```json
{
    "instancesDeleted": 1
}
```

## <a name="purge-multiple-instance-histories"></a>Purgue várias histórias de instâncias

Também pode eliminar o histórico e artefactos relacionados para múltiplas instâncias dentro de um centro de tarefas, removendo `{instanceId}` o pedido de "purgar o histórico de instâncias individuais". Para expurgar seletivamente o histórico de casos, utilize os mesmos filtros descritos no pedido "Obter todas as instâncias status".

### <a name="request"></a>Pedir

Para a versão 1.x do tempo de execução das Funções, o pedido é formatado da seguinte forma (várias linhas são mostradas para clareza):

```http
DELETE /admin/extensions/DurableTaskExtension/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

Na versão 2.x do tempo de execução das Funções, o formato URL tem todos os mesmos parâmetros, mas com um prefixo ligeiramente diferente:

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

| Campo                 | Tipo de parâmetro  | Descrição |
|-----------------------|-----------------|-------------|
| **`createdTimeFrom`** | Cadeias de consulta    | Filtra a lista de casos purgados que foram criados no ou após o determinado semp de tempo ISO8601.|
| **`createdTimeTo`**   | Cadeias de consulta    | Parâmetro opcional. Quando especificado, filtra a lista de casos purgados que foram criados no ou antes do determinado semp timetamp ISO8601.|
| **`runtimeStatus`**   | Cadeias de consulta    | Parâmetro opcional. Quando especificado, filtra a lista de casos purgados com base no seu estado de funcionação. Para ver a lista de possíveis valores de estado de funcionamento, consulte o artigo [casos de consulta.](durable-functions-instance-management.md) |

> [!NOTE]
> Esta operação pode ser muito cara em termos de Azure Storage I/O se houver muitas linhas nas tabelas Instâncias e/ou História. Mais detalhes sobre estas tabelas podem ser encontrados na documentação [Performance e escala em Funções Duradouras (Funções Azure).](durable-functions-perf-and-scale.md#instances-table)

### <a name="response"></a>Resposta

Os seguintes valores de código de estado HTTP podem ser devolvidos.

* **HTTP 200 (OK)**: A história do caso foi purgada com sucesso.
* **HTTP 404 (Não Encontrado)**: Não foram encontradas instâncias que correspondam à expressão do filtro.

A carga útil de resposta para o caso **HTTP 200** é um objeto JSON com o seguinte campo:

| Campo                   | Tipo de dados | Descrição |
|-------------------------|-----------|-------------|
| **`instancesDeleted`**  | número inteiro   | O número de ocorrências apagadas. |

Aqui está um exemplo de carga útil de resposta (formatada para a legibilidade):

```json
{
    "instancesDeleted": 250
}
```

## <a name="raise-event"></a>Aumentar evento

Envia uma mensagem de notificação de evento para uma instância de orquestração em execução.

### <a name="request"></a>Pedir

Para a versão 1.x do tempo de execução das Funções, o pedido é formatado da seguinte forma (várias linhas são mostradas para clareza):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

Na versão 2.x do tempo de execução das Funções, o formato URL tem todos os mesmos parâmetros, mas com um prefixo ligeiramente diferente:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

Os parâmetros de pedido para esta API incluem o conjunto predefinido mencionado anteriormente, bem como os seguintes parâmetros únicos:

| Campo             | Tipo de parâmetro  | Descrição |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | A identificação da instância de orquestração. |
| **`eventName`**   | URL             | O nome do evento que a instância de orquestração alvo está à espera. |
| **`{content}`**   | Conteúdo do pedido | A carga útil do evento formatada pelo JSON. |

### <a name="response"></a>Resposta

Vários valores possíveis de código de estado podem ser devolvidos.

* **HTTP 202 (Aceito)**: O evento levantado foi aceite para processamento.
* **HTTP 400 (Mau pedido)**: O conteúdo do pedido não era de tipo `application/json` ou não era JSON válido.
* **HTTP 404 (Não Encontrado)**: A instância especificada não foi encontrada.
* **HTTP 410 (Desaparecido)**: A instância especificada completou ou falhou e não pode processar quaisquer eventos levantados.

Aqui está um pedido de exemplo que envia a cadeia JSON `"incr"` para um caso à espera de um evento chamado **operação:**

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code=XXX
Content-Type: application/json
Content-Length: 6

"incr"
```

As respostas para esta API não contêm qualquer conteúdo.

## <a name="terminate-instance"></a>Instância de fim

Termina uma instância de orquestração em execução.

### <a name="request"></a>Pedir

Para a versão 1.x do tempo de execução das Funções, o pedido é formatado da seguinte forma (várias linhas são mostradas para clareza):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Na versão 2.x do tempo de execução das Funções, o formato URL tem todos os mesmos parâmetros, mas com um prefixo ligeiramente diferente:

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
| **`instanceId`**  | URL             | A identificação da instância de orquestração. |
| **`reason`**      | Cadeias de consulta    | Opcional. A razão para terminar o caso da orquestração. |

### <a name="response"></a>Resposta

Vários valores possíveis de código de estado podem ser devolvidos.

* **HTTP 202 (Aceito)**: O pedido de terminação foi aceite para processamento.
* **HTTP 404 (Não Encontrado)**: A instância especificada não foi encontrada.
* **HTTP 410 (Desaparecido)**: A instância especificada foi concluída ou falhou.

Aqui está um pedido de exemplo que termina uma instância de execução e especifica uma razão de **buggy**:

```
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason=buggy&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

As respostas para esta API não contêm qualquer conteúdo.

## <a name="rewind-instance-preview"></a>Instância de rebobinar (pré-visualização)

Restaura uma instância de orquestração falhada num estado de execução, reproduzindo as mais recentes operações falhadas.

### <a name="request"></a>Pedir

Para a versão 1.x do tempo de execução das Funções, o pedido é formatado da seguinte forma (várias linhas são mostradas para clareza):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Na versão 2.x do tempo de execução das Funções, o formato URL tem todos os mesmos parâmetros, mas com um prefixo ligeiramente diferente:

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
| **`instanceId`**  | URL             | A identificação da instância de orquestração. |
| **`reason`**      | Cadeias de consulta    | Opcional. A razão para rebobinar a instância de orquestração. |

### <a name="response"></a>Resposta

Vários valores possíveis de código de estado podem ser devolvidos.

* **HTTP 202 (Aceito)**: O pedido de rebobinar foi aceite para processamento.
* **HTTP 404 (Não Encontrado)**: A instância especificada não foi encontrada.
* **HTTP 410 (Desaparecido)**: A instância especificada foi concluída ou foi encerrada.

Aqui está um pedido de exemplo que rebobina uma instância falhada e especifica uma razão de **fixação:**

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/rewind?reason=fixed&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

As respostas para esta API não contêm qualquer conteúdo.

## <a name="signal-entity"></a>Entidade de sinal

Envia uma mensagem de operação unidireccionária para uma [Entidade Durável](durable-functions-types-features-overview.md#entity-functions). Se a entidade não existir, será criada automaticamente.

> [!NOTE]
> As entidades duráveis estão disponíveis a partir das Funções Duráveis 2.0.

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

| Campo             | Tipo de parâmetro  | Descrição |
|-------------------|-----------------|-------------|
| **`entityName`**  | URL             | O nome (tipo) da entidade. |
| **`entityKey`**   | URL             | A chave (ID único) da entidade. |
| **`op`**          | Cadeias de consulta    | Opcional. O nome da operação definida pelo utilizador para invocar. |
| **`{content}`**   | Conteúdo do pedido | A carga útil do evento formatada pelo JSON. |

Aqui está um pedido de exemplo que envia uma mensagem "Adicionar" definida pelo utilizador a uma `Counter` entidade chamada `steps` . O conteúdo da mensagem é o `5` valor. Se a entidade já não existir, será criada por este pedido:

```http
POST /runtime/webhooks/durabletask/entities/Counter/steps?op=Add
Content-Type: application/json

5
```

> [!NOTE]
> Por defeito com [entidades de base de classe em .NET,](durable-functions-dotnet-entities.md#defining-entity-classes)especificando o `op` valor de apagar o estado de uma `delete` entidade. Se a entidade definir uma operação denominada `delete` , no entanto, essa operação definida pelo utilizador será invocada.

### <a name="response"></a>Resposta

Esta operação tem várias respostas possíveis:

* **HTTP 202 (Aceito)**: A operação de sinal foi aceite para processamento assíncronos.
* **HTTP 400 (Mau pedido)**: O conteúdo do pedido não era do `application/json` tipo, não era JSON válido, nem tinha um valor inválido. `entityKey`
* **HTTP 404 (Não Encontrado)**: O especificado `entityName` não foi encontrado.

Um pedido HTTP bem sucedido não contém qualquer conteúdo na resposta. Um pedido HTTP falhado pode conter informações de erro formatadas por JSON no conteúdo de resposta.

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

* **HTTP 200 (OK)**: A entidade especificada existe.
* **HTTP 404 (Não Encontrado)**: A entidade especificada não foi encontrada.

Uma resposta bem sucedida contém o estado serializado JSON da entidade como seu conteúdo.

### <a name="example"></a>Exemplo
O seguinte exemplo http pedido obtém o estado de uma entidade existente `Counter` chamada `steps` :

```http
GET /runtime/webhooks/durabletask/entities/Counter/steps
```

Se a `Counter` entidade simplesmente contiver uma série de etapas guardadas num `currentValue` campo, o conteúdo da resposta pode parecer o seguinte (formatado para a legibilidade):

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

| Campo                       | Tipo de parâmetro  | Descrição |
|-----------------------------|-----------------|-------------|
| **`entityName`**            | URL             | Opcional. Quando especificado, filtra a lista de entidades devolvidas pelo nome da entidade (caso-insensível). |
| **`fetchState`**            | Cadeias de consulta    | Parâmetro opcional. Se for `true` definido, o estado da entidade será incluído na carga útil de resposta. |
| **`lastOperationTimeFrom`** | Cadeias de consulta    | Parâmetro opcional. Quando especificado, filtra a lista de entidades devolvidas que processaram as operações após o determinado semp iso8601. |
| **`lastOperationTimeTo`**   | Cadeias de consulta    | Parâmetro opcional. Quando especificado, filtra a lista de entidades devolvidas que processaram as operações antes do determinado semp timetamp ISO8601. |
| **`top`**                   | Cadeias de consulta    | Parâmetro opcional. Quando especificado, limita o número de entidades devolvidas pela consulta. |


### <a name="response"></a>Resposta

Uma resposta http 200 bem sucedida contém um conjunto de entidades serializadas por JSON e opcionalmente o estado de cada entidade.

Por predefinição, a operação devolve as primeiras 100 entidades que correspondem aos critérios de consulta. O chamador pode especificar um valor de parâmetro de cadeia de consulta `top` para devolver um número máximo diferente de resultados. Se houver mais resultados para além do que é devolvido, um sinal de continuação também é devolvido no cabeçalho de resposta. O nome do cabeçalho é `x-ms-continuation-token` .

Se definir o valor do token de continuação no próximo cabeçalho de pedido, pode obter a próxima página de resultados. Este nome do cabeçalho do pedido também é `x-ms-continuation-token` .

### <a name="example---list-all-entities"></a>Exemplo - listar todas as entidades

O seguinte exemplo http pedido lista todas as entidades no centro de tarefas:

```http
GET /runtime/webhooks/durabletask/entities
```

A resposta JSON pode parecer o seguinte (formatado para a legibilidade):

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

O seguinte exemplo HTTP solicita apenas as duas primeiras entidades do tipo `counter` e também requer o seu estado:

```http
GET /runtime/webhooks/durabletask/entities/counter?top=2&fetchState=true
```

A resposta JSON pode parecer o seguinte (formatado para a legibilidade):

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
> [Saiba como utilizar o Application Insights para monitorizar as suas funções duráveis](durable-functions-diagnostics.md)