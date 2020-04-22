---
title: referência host.json para funções Azure 1.x
description: Documentação de referência para o ficheiro host.json funções azure com o tempo de funcionamento v1.
ms.topic: conceptual
ms.date: 10/19/2018
ms.openlocfilehash: 36d028d09c94ae28e77404297bd576f5e20404c6
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81757512"
---
# <a name="hostjson-reference-for-azure-functions-1x"></a>referência host.json para funções Azure 1.x

> [!div class="op_single_selector" title1="Selecione a versão do tempo de funcionamento das Funções Azure que está a utilizar: "]
> * [Versão 1](functions-host-json-v1.md)
> * [Versão 2](functions-host-json.md)

O ficheiro de metadados *host.json* contém opções de configuração global que afetam todas as funções para uma aplicação de função. Este artigo lista as definições disponíveis para o tempo de execução v1. O esquema da JSON http://json.schemastore.org/hostestá em .

> [!NOTE]
> Este artigo é para funções Azure 1.x.  Para uma referência do host.json nas funções 2.x e posteriormente, consulte a [referência host.json para as Funções Azure 2.x](functions-host-json.md).

Outras opções de configuração de aplicações de função são geridas nas definições da sua [aplicação](functions-app-settings.md).

Algumas definições host.json só são usadas quando são feitas localmente no ficheiro [local.settings.json.](functions-run-local.md#local-settings-file)

## <a name="sample-hostjson-file"></a>Arquivo host.json da amostra

Os *seguintes ficheiros host.json* da amostra têm todas as opções possíveis especificadas.


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

As seguintes secções deste artigo explicam cada propriedade de alto nível. Todos são opcionais, salvo indicação em contrário.

## <a name="aggregator"></a>agregador

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>aplicaçãoInsights

[!INCLUDE [applicationInsights](../../includes/functions-host-json-applicationinsights.md)]

## <a name="documentdb"></a>DocumentDB

Configurações de configuração para o [gatilho e encadernação Azure Cosmos DB](functions-bindings-cosmosdb.md).

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
|GatewayMode|Gateway|O modo de ligação utilizado pela função ao ligar-se ao serviço Azure Cosmos DB. As `Direct` opções são e`Gateway`|
|Protocolo|Https|O protocolo de ligação utilizado pela função quando a ligação ao serviço Azure Cosmos DB.  Leia [aqui para uma explicação de ambos os modos](../cosmos-db/performance-tips.md#networking)|
|arrendamentoPrefix|n/d|Alugar prefixo para usar em todas as funções numa aplicação.|

## <a name="durabletask"></a>tarefa durável

[!INCLUDE [durabletask](../../includes/functions-host-json-durabletask.md)]

## <a name="eventhub"></a>eventoHub

Definições de configuração para [gatilhos e encadernações](functions-bindings-event-hubs-trigger.md#functions-1x)do Centro de Eventos .

## <a name="functions"></a>funções

Uma lista de funções que o anfitrião do emprego dirige. Uma matriz vazia significa executar todas as funções. Destinado a ser utilizado apenas quando [se funciona localmente](functions-run-local.md). Nas aplicações de função em Azure, deve seguir os passos em [Como desativar funções em Funções Azure](disable-function.md) para desativar funções específicas em vez de utilizar esta definição.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>funçãoTimeout

Indica a duração do tempo de paragem para todas as funções. Num plano de consumo sem servidor, o intervalo válido é de 1 segundo a 10 minutos, e o valor predefinido é de 5 minutos. Num plano de Serviço de Aplicações, não existe um limite global e o predefinido é _nulo_, o que indica que não há prazo.

```json
{
    "functionTimeout": "00:05:00"
}
```

## <a name="healthmonitor"></a>healthMonitor

Configurações de configuração para monitor de [saúde anfitrião](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Host-Health-Monitor).

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
|ativado|true|Especifica se a funcionalidade está ativada. | 
|saúdeCheckInterval|10 segundos|O intervalo de tempo entre as verificações periódicas de saúde de fundo. | 
|healthCheckWindow|2 minutos|Uma janela de tempo deslizante `healthCheckThreshold` utilizada em conjunto com a definição.| 
|saúdeCheckThreshold|6|Número máximo de vezes que o exame de saúde pode falhar antes de iniciar um ciclo de acolhimento.| 
|contralimiar|0.80|O limiar em que um contador de desempenho será considerado insalubre.| 

## <a name="http"></a>http

Definições de configuração para [gatilhos e encadernações de http](functions-bindings-http-webhook.md).

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
|dynamicThrottlesEnabled|false|Quando ativada, esta definição faz com que o gasoduto de processamento de pedidos verifique periodicamente os contadores de desempenho do sistema, tais como ligações/fios/processos/memória/cpu/etc. e se algum desses contadores estiver acima de um limiar elevado incorporado (80%), os pedidos serão rejeitados com uma resposta de 429 "Demasiado Ocupado" até que o contador(s) volte aos níveis normais.|
|maxConcurrentRequests|sem limites (`-1`)|O número máximo de funções HTTP que serão executadas em paralelo. Isto permite controlar a conmoeda, o que pode ajudar a gerir a utilização de recursos. Por exemplo, pode ter uma função HTTP que utiliza muitos recursos do sistema (memória/cpu/tomadas) de tal forma que causa problemas quando a moeda é demasiado elevada. Ou pode ter uma função que faz pedidos de saída para um serviço de terceiros, e essas chamadas precisam de ser limitadas. Nestes casos, aplicar um acelerador aqui pode ajudar.|
|maxOutstandingRequests|sem limites (`-1`)|O número máximo de pedidos pendentes que são realizados a qualquer momento. Este limite inclui pedidos que estão em fila mas que não começaram a executar, bem como quaisquer execuções em curso. Quaisquer pedidos de entrada acima deste limite são rejeitados com uma resposta 429 "Demasiado Ocupada". Isso permite que os chamadores empreguem estratégias de retry baseadas no tempo, e também o ajuda a controlar as lateências máximas de pedido. Isto apenas controla a fila que ocorre dentro do caminho de execução do hospedeiro script. Outras filas, como a fila de pedidos de ASP.NET, continuarão em vigor e não serão afetadas por este cenário.|
|RotaPrefixo|api|O prefixo da rota que se aplica a todas as rotas. Utilize uma corda vazia para remover o prefixo predefinido. |

## <a name="id"></a>ID

A identificação única para um anfitrião de emprego. Pode ser um GUIA minúsculo com traços removidos. Necessário quando correr localmente. Ao correr em Azure, recomendamos que não detete um valor de identificação. Um ID é gerado automaticamente em `id` Azure quando é omitido. 

Se partilhar uma conta de Armazenamento em várias aplicações `id`de função, certifique-se de que cada aplicação de função tem uma diferente . Pode omitir `id` a propriedade ou definir manualmente cada aplicação de função `id` para um valor diferente. O gatilho do temporizador utiliza um bloqueio de armazenamento para garantir que haverá apenas uma instância temporizador quando uma aplicação de função se esescala para várias instâncias. Se duas aplicações `id` de função partilharem o mesmo e cada usar um gatilho temporizador, apenas um temporizador será executado.

```json
{
    "id": "9f4ea53c5136457d883d685e57164f08"
}
```

## <a name="logger"></a>madeireiro

Controla a filtragem de registos escritos por um [objeto ILogger](functions-monitoring.md#write-logs-in-c-functions) ou por [context.log](functions-monitoring.md#write-logs-in-javascript-functions).

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
|categoriaFilter|n/d|Especifica a filtragem por categoria| 
|padrãoNLevel|Informações|Para quaisquer categorias não `categoryLevels` especificadas na matriz, envie registos a este nível e acima para Informações de Aplicação.| 
|categoriaNíveis|n/d|Um conjunto de categorias que especifica o nível mínimo de registo a enviar para Insights de Aplicação para cada categoria. A categoria aqui especificada controla todas as categorias que começam com o mesmo valor, e os valores mais longos têm precedência. No ficheiro *host.json* da amostra anterior, todas as categorias que começam `Information` com o log "Host.Aggregator" ao nível. Todas as outras categorias que começam com "Host",, `Error` como "Host.Executor", registam-se ao nível.| 

## <a name="queues"></a>filas

Configurações de configuração para [gatilhos e encadernações](functions-bindings-storage-queue.md)de fila de armazenamento .

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
|maxPollingInterval|60000|O intervalo máximo em milissegundos entre as sondagens.| 
|visibilidadeTimeout|0|O intervalo de tempo entre as tentativas de repetição ao processamento de uma mensagem falha.| 
|batchSize|16|O número de mensagens de fila que as Funções executam o tempo de funcionamento recupera simultaneamente e processa em paralelo. Quando o número que está `newBatchThreshold`a ser processado chega ao , o tempo de execução recebe outro lote e começa a processar essas mensagens. Assim, o número máximo de mensagens simultâneas que são processadas por função é `batchSize` mais `newBatchThreshold`. Este limite aplica-se separadamente a cada função acionada pela fila. <br><br>Se quiser evitar a execução paralela de mensagens `batchSize` recebidas numa fila, pode definir para 1. No entanto, esta definição elimina a conmoeda apenas enquanto a sua aplicação de função funcionar numa única máquina virtual (VM). Se a aplicação de função se dimensionar para vários VMs, cada VM pode executar uma instância de cada função desencadeada pela fila.<br><br>O `batchSize` máximo é de 32. | 
|maxDequeueCount|5|O número de vezes para tentar processar uma mensagem antes de movê-la para a fila do veneno.| 
|novo BatchThreshold|loteTamanho/2|Sempre que o número de mensagens que estão a ser processadas simultaneamente chega a este número, o tempo de execução recupera outro lote.| 

## <a name="sendgrid"></a>SendGrid

Definição de configuração para a ligação de [saída SendGrind](functions-bindings-sendgrid.md)

```json
{
    "sendGrid": {
        "from": "Contoso Group <admin@contoso.com>"
    }
```

|Propriedade  |Predefinição | Descrição |
|---------|---------|---------| 
|De|n/d|O endereço de e-mail do remetente em todas as funções.| 

## <a name="servicebus"></a>serviçoBus

Definição de configuração para [gatilhos e encadernações](functions-bindings-service-bus.md)de ônibus de serviço .

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
|maxConcurrentCalls|16|O número máximo de chamadas simultâneas para o backback que a bomba de mensagem deve iniciar. Por predefinição, o tempo de execução das Funções processa várias mensagens simultaneamente. Para direcionar o tempo de execução para processar apenas `maxConcurrentCalls` uma única fila ou mensagem de tópico de cada vez, definida para 1. | 
|pré-fetchCount|n/d|O PrefetchCount prefetchCount predefinido que será utilizado pelo Recetor de Mensagens subjacente.| 
|autoRenovarTimeout|00:05:00|A duração máxima dentro da qual o bloqueio da mensagem será renovado automaticamente.| 

## <a name="singleton"></a>singleton

Definições de configuração para o comportamento de bloqueio de Singleton. Para mais informações, consulte a [questão do GitHub sobre o suporte singleton.](https://github.com/Azure/azure-webjobs-sdk-script/issues/912)

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
|lockPeriod|00:00:15|O período para o período em que as fechaduras de nível de função são tomadas. As fechaduras renovam automaticamente.| 
|listenerLockPeriod|00:01:00|O período para o que as fechaduras dos ouvintes são tomados.| 
|listenerLockRecoveryPollingIntervalo|00:01:00|O intervalo de tempo utilizado para a recuperação do bloqueio do ouvinte se não fosse adquirido um bloqueio de ouvintes no arranque.| 
|lockAcquisitionTimeout|00:01:00|O tempo máximo de tempo que o tempo de execução tentará adquirir um cadeado.| 
|lockAcquisitionPollingInterval|n/d|O intervalo entre tentativas de aquisição de fechaduras.| 

## <a name="tracing"></a>rastreio

*Versão 1.x*

Configurações de configuração para registos que cria utilizando um `TraceWriter` objeto. Ver [C# Logging](functions-reference-csharp.md#logging) e [Node.js Logging](functions-reference-node.md#writing-trace-output-to-the-console).

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
|consolaLevel|informações|O nível de rastreio para o registo de consolas. As opções `error` `warning`são: `off` `verbose`, , `info`e .|
|fileLoggingMode|debugsó|O nível de rastreio para a exploração de ficheiros. As `never`opções são, `always`. . `debugOnly`| 

## <a name="watchdirectories"></a>watchDirectories

Um conjunto de [diretórios](functions-reference-csharp.md#watched-directories) de código partilhados que devem ser monitorizados para alterações.  Garante que quando o código nestas diretórios é alterado, as alterações são captadas pelas suas funções.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba como atualizar o ficheiro host.json](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [Ver configurações globais em variáveis ambientais](functions-app-settings.md)
