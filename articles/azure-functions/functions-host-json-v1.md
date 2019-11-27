---
title: referência de host. JSON para Azure Functions 1. x
description: Documentação de referência para o arquivo Azure Functions host. JSON com o tempo de execução v1.
ms.topic: conceptual
ms.date: 10/19/2018
ms.openlocfilehash: 99a571483086343d4e7d6188b2f401abc616c1bb
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230598"
---
# <a name="hostjson-reference-for-azure-functions-1x"></a>referência de host. JSON para Azure Functions 1. x

> [!div class="op_single_selector" title1="Selecione a versão do Azure Functions tempo de execução que você está usando: "]
> * [Versão 1](functions-host-json-v1.md)
> * [Versão 2](functions-host-json.md)

O arquivo de metadados *host. JSON* contém opções de configuração globais que afetam todas as funções de um aplicativo de funções. Este artigo lista as configurações que estão disponíveis para o tempo de execução v1. O esquema JSON está em http://json.schemastore.org/host.

> [!NOTE]
> Este artigo é para as funções do Azure 1.x.  Para obter uma referência de host. JSON nas funções 2. x, consulte [referência de host. JSON para Azure Functions 2. x](functions-host-json.md).

Outras opções de configuração de aplicativo de funções são gerenciadas em suas [configurações de aplicativo](functions-app-settings.md).

Algumas configurações de host. JSON são usadas apenas ao executar localmente no arquivo [local. Settings. JSON](functions-run-local.md#local-settings-file) .

## <a name="sample-hostjson-file"></a>Arquivo host. JSON de exemplo

Os arquivos *host. JSON* de exemplo a seguir têm todas as opções possíveis especificadas.


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
    "documentDB": {
        "connectionMode": "Gateway",
        "protocol": "Https",
        "leaseOptions": {
            "leasePrefix": "prefix"
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
    "sendGrid": {
        "from": "Contoso Group <admin@contoso.com>"
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

As seções a seguir deste artigo explicam cada propriedade de nível superior. Todos são opcionais, salvo indicação em contrário.

## <a name="aggregator"></a>agregador

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>applicationInsights

[!INCLUDE [applicationInsights](../../includes/functions-host-json-applicationinsights.md)]

## <a name="documentdb"></a>DocumentDB

Definições de configuração para o [gatilho Azure Cosmos DB e associações](functions-bindings-cosmosdb.md).

```json
{
    "documentDB": {
        "connectionMode": "Gateway",
        "protocol": "Https",
        "leaseOptions": {
            "leasePrefix": "prefix1"
        }
    }
}
```

|Propriedade  |Predefinição | Descrição |
|---------|---------|---------|
|GatewayMode|Gateway|O modo de conexão usado pela função ao se conectar ao serviço de Azure Cosmos DB. As opções são `Direct` e `Gateway`|
|Protocolo|Https|O protocolo de conexão usado pela função quando a conexão com o serviço de Azure Cosmos DB.  Leia [aqui para obter uma explicação dos dois modos](../cosmos-db/performance-tips.md#networking)|
|leasePrefix|n/d|Prefixo de concessão a ser usado em todas as funções em um aplicativo.|

## <a name="durabletask"></a>durableTask

[!INCLUDE [durabletask](../../includes/functions-host-json-durabletask.md)]

## <a name="eventhub"></a>eventHub

Definições de configuração para [gatilhos e associações de Hub de eventos](functions-bindings-event-hubs.md).

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="functions"></a>functions

Uma lista de funções que o host de trabalho executa. Uma matriz vazia significa executar todas as funções. Destinado para uso somente quando [executado localmente](functions-run-local.md). Em aplicativos de funções no Azure, você deve seguir as etapas em [como desabilitar funções no Azure Functions](disable-function.md) para desabilitar funções específicas em vez de usar essa configuração.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Indica a duração do tempo limite para todas as funções. Em um plano de consumo sem servidor, o intervalo válido é de 1 segundo a 10 minutos e o valor padrão é 5 minutos. Em um plano do serviço de aplicativo, não há nenhum limite geral e o padrão é _NULL_, o que indica que não há tempo limite.

```json
{
    "functionTimeout": "00:05:00"
}
```

## <a name="healthmonitor"></a>healthMonitor

Definições de configuração do [Monitor de integridade do host](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Host-Health-Monitor).

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
|enabled|true|Especifica se o recurso está habilitado. | 
|healthCheckInterval|10 segundos|O intervalo de tempo entre as verificações de integridade de segundo plano periódicas. | 
|healthCheckWindow|2 minutos|Uma janela de tempo deslizante usada em conjunto com a configuração de `healthCheckThreshold`.| 
|healthCheckThreshold|6|Número máximo de vezes que a verificação de integridade pode falhar antes que uma reciclagem de host seja iniciada.| 
|comlimite|0,80|O limite no qual um contador de desempenho será considerado não íntegro.| 

## <a name="http"></a>http

Definições de configuração para [gatilhos e associações http](functions-bindings-http-webhook.md).

```json
{
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 200,
        "maxConcurrentRequests": 100,
        "dynamicThrottlesEnabled": true
    }
}
```

|Propriedade  |Predefinição | Descrição |
|---------|---------|---------| 
|dynamicThrottlesEnabled|false|Quando habilitado, essa configuração faz com que o pipeline de processamento de solicitações Verifique periodicamente os contadores de desempenho do sistema como conexões/threads/processos/memória/CPU/etc. e, se qualquer um desses contadores estiver acima de um limite alto interno (80%), as solicitações serão rejeitadas com uma resposta 429 "muito ocupado" até que os contadores retornem aos níveis normais.|
|maxConcurrentRequests|Não associado (`-1`)|O número máximo de funções http que serão executadas em paralelo. Isso permite que você controle a simultaneidade, o que pode ajudar a gerenciar a utilização de recursos. Por exemplo, você pode ter uma função http que usa muitos recursos do sistema (memória/CPU/soquetes), de modo que isso cause problemas quando a simultaneidade for muito alta. Ou você pode ter uma função que faça solicitações de saída para um serviço de terceiros, e essas chamadas precisam ser limitadas por taxa. Nesses casos, a aplicação de uma limitação aqui pode ajudar.|
|maxOutstandingRequests|Não associado (`-1`)|O número máximo de solicitações pendentes que são mantidas em um determinado momento. Esse limite inclui solicitações que estão na fila, mas não iniciaram a execução, bem como qualquer execução em andamento. Todas as solicitações de entrada acima desse limite são rejeitadas com uma resposta de 429 "muito ocupado". Isso permite que os chamadores empreguem estratégias de repetição baseadas em tempo, além de ajudar você a controlar as latências de solicitação máximas. Isso controla somente o enfileiramento que ocorre no caminho de execução do host de script. Outras filas, como a fila de solicitações ASP.NET, ainda estarão em vigor e não serão afetadas por essa configuração.|
|routePrefix|api|O prefixo de rota que se aplica a todas as rotas. Use uma cadeia de caracteres vazia para remover o prefixo padrão. |

## <a name="id"></a>ID

A ID exclusiva para um host de trabalho. Pode ser um GUID de caso inferior com traços removidos. Necessário ao executar localmente. Ao executar no Azure, recomendamos que você não defina um valor de ID. Uma ID é gerada automaticamente no Azure quando `id` é omitida. 

Se você compartilhar uma conta de armazenamento em vários aplicativos de funções, certifique-se de que cada aplicativo de funções tem um `id`diferente. Você pode omitir a propriedade `id` ou definir manualmente cada `id` do aplicativo de funções para um valor diferente. O gatilho de temporizador usa um bloqueio de armazenamento para garantir que haverá apenas uma instância de temporizador quando um aplicativo de funções for dimensionado para várias instâncias. Se dois aplicativos de funções compartilharem o mesmo `id` e cada um usar um gatilho de temporizador, somente um temporizador será executado.

```json
{
    "id": "9f4ea53c5136457d883d685e57164f08"
}
```

## <a name="logger"></a>Digita

Controla a filtragem de logs gravados por um [objeto ILogger](functions-monitoring.md#write-logs-in-c-functions) ou por [Context. log](functions-monitoring.md#write-logs-in-javascript-functions).

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
|defaultLevel|Informações|Para todas as categorias não especificadas na matriz de `categoryLevels`, envie logs nesse nível e acima para Application Insights.| 
|categoryLevels|n/d|Uma matriz de categorias que especifica o nível de log mínimo a ser enviado para Application Insights para cada categoria. A categoria especificada aqui controla todas as categorias que começam com o mesmo valor e os valores mais longos têm precedência. No arquivo *host. JSON* de exemplo anterior, todas as categorias que começam com o log "host. agregador" no nível de `Information`. Todas as outras categorias que começam com "host", como "host. executor", fazem logon no nível de `Error`.| 

## <a name="queues"></a>filas

Definições de configuração para [gatilhos e associações de fila de armazenamento](functions-bindings-storage-queue.md).

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
|maxPollingInterval|60000|O intervalo máximo em milissegundos entre as pesquisas de fila.| 
|visibilityTimeout|0|O intervalo de tempo entre as repetições quando o processamento de uma mensagem falha.| 
|batchSize|16|O número de mensagens de fila que o tempo de execução do Functions recupera simultaneamente e processa em paralelo. Quando o número que está sendo processado chega à `newBatchThreshold`, o tempo de execução Obtém outro lote e começa a processar essas mensagens. Portanto, o número máximo de mensagens simultâneas sendo processadas por função é `batchSize` mais `newBatchThreshold`. Esse limite se aplica separadamente a cada função disparada por fila. <br><br>Se você quiser evitar a execução paralela para mensagens recebidas em uma fila, poderá definir `batchSize` como 1. No entanto, essa configuração elimina a simultaneidade somente contanto que seu aplicativo de funções seja executado em uma única máquina virtual (VM). Se o aplicativo de funções for dimensionado para várias VMs, cada VM poderá executar uma instância de cada função disparada por fila.<br><br>O `batchSize` máximo é 32. | 
|maxDequeueCount|5|O número de vezes para tentar processar uma mensagem antes de movê-la para a fila de suspeitas.| 
|newBatchThreshold|batchSize/2|Sempre que o número de mensagens sendo processadas simultaneamente chega a esse número, o tempo de execução recupera outro lote.| 

## <a name="sendgrid"></a>SendGrid

Parâmetro de configuração para a [Associação de saída SendGrind](functions-bindings-sendgrid.md)

```json
{
    "sendGrid": {
        "from": "Contoso Group <admin@contoso.com>"
    }
```

|Propriedade  |Predefinição | Descrição |
|---------|---------|---------| 
|from|n/d|O endereço de email do remetente em todas as funções.| 

## <a name="servicebus"></a>serviceBus

Configuração para [gatilhos e associações do barramento de serviço](functions-bindings-service-bus.md).

```json
{
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    }
}
```

|Propriedade  |Predefinição | Descrição |
|---------|---------|---------| 
|maxConcurrentCalls|16|O número máximo de chamadas simultâneas para o retorno de chamada que deve iniciar o bombardeamento de mensagens. Por predefinição, o runtime das funções processa várias mensagens em simultâneo. Para direcionar o tempo de execução para processar apenas uma única fila ou mensagem de tópico de cada vez, defina `maxConcurrentCalls` como 1. | 
|prefetchCount|n/d|A predefinição PrefetchCount que será utilizada pelo MessageReceiver subjacente.| 
|autoRenewTimeout|00:05:00|A duração máxima em que o bloqueio da mensagem será renovado automaticamente.| 

## <a name="singleton"></a>singleton

Definições de configuração para comportamento de bloqueio singleton. Para obter mais informações, consulte o [problema do GitHub sobre o suporte singleton](https://github.com/Azure/azure-webjobs-sdk-script/issues/912).

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
|lockPeriod|00:00:15|O período em que os bloqueios de nível de função são obtidos. Os bloqueios são renovados automaticamente.| 
|listenerLockPeriod|00:01:00|O período em que os bloqueios de ouvinte são obtidos.| 
|listenerLockRecoveryPollingInterval|00:01:00|O intervalo de tempo usado para recuperação de bloqueio de ouvinte se um bloqueio de ouvinte não pôde ser adquirido na inicialização.| 
|lockAcquisitionTimeout|00:01:00|A quantidade máxima de tempo que o tempo de execução tentará adquirir um bloqueio.| 
|lockAcquisitionPollingInterval|n/d|O intervalo entre as tentativas de aquisição de bloqueio.| 

## <a name="tracing"></a>rastreio

*Versão 1. x*

Definições de configuração para logs que você cria usando um objeto `TraceWriter`. Consulte [ C# log](functions-reference-csharp.md#logging) e registro em log do [node. js](functions-reference-node.md#writing-trace-output-to-the-console).

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
|consoleLevel|info|O nível de rastreamento do log do console. As opções são: `off`, `error`, `warning`, `info`e `verbose`.|
|fileLoggingMode|debugOnly|O nível de rastreamento para registro em log de arquivo. As opções são `never`, `always``debugOnly`.| 

## <a name="watchdirectories"></a>watchDirectories

Um conjunto de [diretórios de código compartilhado](functions-reference-csharp.md#watched-directories) que deve ser monitorado para alterações.  Garante que, quando o código nesses diretórios for alterado, as alterações sejam coletadas por suas funções.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba como atualizar o arquivo host. JSON](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [Consulte Configurações globais em variáveis de ambiente](functions-app-settings.md)
