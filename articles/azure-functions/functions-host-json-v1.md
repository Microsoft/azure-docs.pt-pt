---
title: referência de Host. JSON para as funções do Azure 1.x
description: Documentação de referência para o ficheiro de Host. JSON de funções do Azure com o tempo de execução do v1.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: glenga
ms.openlocfilehash: 6f93bbceacff3731206e5f98ba9a252d6a046ac4
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58200080"
---
# <a name="hostjson-reference-for-azure-functions-1x"></a>referência de Host. JSON para as funções do Azure 1.x

> [!div class="op_single_selector" title1="Select the version of the Azure Functions runtime you are using: "]
> * [Versão 1](functions-host-json-v1.md)
> * [Versão 2](functions-host-json.md)

O *Host. JSON* ficheiro de metadados contém opções de configuração globais que afetam todas as funções para uma aplicação de funções. Este artigo lista as definições que estão disponíveis para o tempo de execução do v1. O esquema JSON está no http://json.schemastore.org/host.

> [!NOTE]
> Este artigo é para as funções do Azure 1.x.  Para obter uma referência de Host. JSON nas funções 2.x, consulte [referência de Host. JSON para as funções do Azure 2.x](functions-host-json.md).

Outras opções de configuração de aplicação de função são geridas no seu [as definições da aplicação](functions-app-settings.md).

Algumas definições de Host. JSON só são utilizadas quando em execução localmente na [Settings](functions-run-local.md#local-settings-file) ficheiro.

## <a name="sample-hostjson-file"></a>Ficheiro de Host. JSON de exemplo

O exemplo a seguir *Host. JSON* ficheiros têm todas as opções possíveis especificadas.


```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    },
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    },
    "functions": [ "QueueProcessor", "GitHubWebHook" ],
    "functionTimeout": "00:05:00",
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    },
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 20,
        "maxConcurrentRequests": 10,
        "dynamicThrottlesEnabled": false
    },
    "id": "9f4ea53c5136457d883d685e57164f08",
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    },
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    },
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    },
    "watchDirectories": [ "Shared" ],
}
```

As secções seguintes deste artigo explicam cada propriedade de nível superior. Todas são opcionais, a menos que indicado de outra forma.

## <a name="aggregator"></a>aggregator

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>applicationInsights

[!INCLUDE [applicationInsights](../../includes/functions-host-json-applicationinsights.md)]

## <a name="durabletask"></a>durableTask

[!INCLUDE [durabletask](../../includes/functions-host-json-durabletask.md)]

## <a name="eventhub"></a>eventHub

Definições de configuração para [Hub de eventos de acionadores e enlaces](functions-bindings-event-hubs.md).

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="functions"></a>functions

Uma lista de funções que o anfitrião de tarefa é executada. Uma matriz vazia significa executar todas as funções. Foi concebido para utilização apenas quando [em execução localmente](functions-run-local.md). Nas aplicações de função no Azure, em vez disso, deve seguir os passos em [como desativar funções nas funções do Azure](disable-function.md) para desativar funções específicas, em vez de utilizar esta definição.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Indica o período de tempo limite para todas as funções. No plano de consumo sem servidor, o intervalo válido é entre 1 segundo para 10 minutos e o valor predefinido é de 5 minutos. Num plano do serviço de aplicações, não existe nenhum limite global e o padrão depende da versão de runtime.

```json
{
    "functionTimeout": "00:05:00"
}
```

## <a name="healthmonitor"></a>healthMonitor

Definições de configuração para [monitor de estado de funcionamento do anfitrião](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Host-Health-Monitor).

```
{
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    }
}
```

|Propriedade  |Predefinição | Descrição |
|---------|---------|---------| 
|enabled|true|Especifica se a funcionalidade está ativada. | 
|healthCheckInterval|10 segundos|Verifica o intervalo de tempo entre o estado de funcionamento periódica de segundo plano. | 
|healthCheckWindow|2 minutos|Uma janela deslizante de tempo utilizada em conjunto com o `healthCheckThreshold` definição.| 
|healthCheckThreshold|6|Número máximo de vezes que a verificação de estado de funcionamento pode falhar antes de uma reciclagem de anfitrião é iniciada.| 
|counterThreshold|0.80|O limiar em que um contador de desempenho será considerado problemático.| 

## <a name="http"></a>http

Definições de configuração para [http acionadores e enlaces](functions-bindings-http-webhook.md).

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="id"></a>ID

*Versão 1.x apenas.*

O ID exclusivo para um anfitrião de tarefa. Pode ser uma minúscula GUID com travessões removida. É necessário quando em execução localmente. Quando em execução no Azure, recomendamos que não defina um valor de ID. Um ID é gerado automaticamente no Azure quando `id` for omitido. 

Se partilhar uma conta de armazenamento entre várias aplicações de funções, certifique-se de que cada aplicação de função tem outra `id`. Pode omitir os `id` propriedade ou definir manualmente cada aplicação de função `id` para um valor diferente. O acionador de temporizador utiliza um bloqueio de armazenamento para se certificar de que haverá apenas uma instância de temporizador quando uma aplicação de funções aumenta horizontalmente para várias instâncias. Se duas aplicações de função partilham o mesmo `id` e cada utiliza um acionador de temporizador, apenas um temporizador será executado.

```json
{
    "id": "9f4ea53c5136457d883d685e57164f08"
}
```

## <a name="logger"></a>agente de log

Controlos de filtragem para os registos de escrita por um [objeto ILogger](functions-monitoring.md#write-logs-in-c-functions) ou pelo [context.log](functions-monitoring.md#write-logs-in-javascript-functions).

```json
{
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    }
}
```

|Propriedade  |Predefinição | Descrição |
|---------|---------|---------| 
|categoryFilter|n/d|Especifica a filtragem por categoria| 
|defaultLevel|Informações|Para as categorias não especificadas no `categoryLevels` matriz, enviar registos neste nível e superior para o Application Insights.| 
|categoryLevels|n/d|Uma matriz de categorias que especifica o nível de registo mínimo para enviar para o Application Insights para cada categoria. A categoria especificada aqui controla todas as categorias que começam com o mesmo valor e, mais tempo valores têm precedência. No exemplo anterior *Host. JSON* do ficheiro, todas as categorias que começam com "Host.Aggregator" registo em `Information` nível. Todas as outras categorias que começam com "Host", como "Host.Executor", inicie sessão em `Error` nível.| 

## <a name="queues"></a>filas

Definições de configuração para [acionadores de fila de armazenamento e enlaces](functions-bindings-storage-queue.md).

```json
{
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    }
}
```

|Propriedade  |Predefinição | Descrição |
|---------|---------|---------| 
|maxPollingInterval|60000|O intervalo máximo em milissegundos entre inquéritos de fila de mensagens em fila.| 
|visibilityTimeout|0|O intervalo de tempo entre tentativas durante o processamento de uma mensagem de falha.| 
|batchSize|16|O número de mensagens em fila que o runtime das funções em simultâneo recupera e processa em paralelo. Quando o número a ser processado obtém para o `newBatchThreshold`, o tempo de execução obtém outro lote e começa a processar essas mensagens. Portanto, é o número máximo de mensagens em simultâneo a ser processado por função `batchSize` plus `newBatchThreshold`. Este limite aplica-se em separado para cada função acionada por fila. <br><br>Se quiser evitar a execução paralela para mensagens recebidas numa fila, pode definir `batchSize` como 1. No entanto, esta definição elimina a simultaneidade apenas, desde que a aplicação de função for executada numa única máquina virtual (VM). Se a aplicação de funções aumenta horizontalmente para várias VMs, cada VM pode executar uma instância de cada função acionada por fila.<br><br>O máximo `batchSize` é 32. | 
|maxDequeueCount|5|O número de vezes para tentar processar uma mensagem antes de passar para a fila não processáveis.| 
|newBatchThreshold|batchSize/2|Sempre que obtém o número de mensagens a ser processadas em simultâneo para baixo para este número, o tempo de execução obtém outro lote.| 

## <a name="servicebus"></a>serviceBus

Definição de configuração para [do Service Bus acionadores e enlaces](functions-bindings-service-bus.md).

[!INCLUDE [functions-host-json-service-bus](../../includes/functions-host-json-service-bus.md)]

## <a name="singleton"></a>singleton

Definições de configuração para o comportamento de bloqueio de Singleton. Para obter mais informações, consulte [problema do GitHub sobre o suporte de singleton](https://github.com/Azure/azure-webjobs-sdk-script/issues/912).

```json
{
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    }
}
```

|Propriedade  |Predefinição | Descrição |
|---------|---------|---------| 
|lockPeriod|00:00:15|O período de que os bloqueios de nível de função são utilizados para. Os bloqueios de renovação automática.| 
|listenerLockPeriod|00:01:00|O período de que os bloqueios de serviço de escuta são utilizados para.| 
|listenerLockRecoveryPollingInterval|00:01:00|O intervalo de tempo utilizado para a recuperação de bloqueio do serviço de escuta, se um bloqueio do serviço de escuta não foi possível adquirir na inicialização.| 
|lockAcquisitionTimeout|00:01:00|A quantidade máxima de tempo, que o tempo de execução irá tentar adquirir um bloqueio.| 
|lockAcquisitionPollingInterval|n/d|O intervalo entre tentativas de aquisição de bloqueio.| 

## <a name="tracing"></a>rastreio

*Versão 1.x*

Definições de configuração para os registos que criar utilizando um `TraceWriter` objeto. Ver [c# registo](functions-reference-csharp.md#logging) e [registo de node. js](functions-reference-node.md#writing-trace-output-to-the-console).

```json
{
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    }
}
```

|Propriedade  |Predefinição | Descrição |
|---------|---------|---------| 
|consoleLevel|informações|O nível de rastreio para o registo da consola. As opções são: `off`, `error`, `warning`, `info`, e `verbose`.|
|fileLoggingMode|debugOnly|O nível de rastreio para o registo do ficheiro. As opções são `never`, `always`, `debugOnly`.| 

## <a name="watchdirectories"></a>watchDirectories

Um conjunto de [partilhados diretórios de código](functions-reference-csharp.md#watched-directories) que devem ser monitorizados para que as alterações.  Garante que quando o código desses diretórios é alterado, as alterações são captadas pelas suas funções.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Saiba como atualizar o ficheiro de Host. JSON](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [Ver definições globais em variáveis de ambiente](functions-app-settings.md)
