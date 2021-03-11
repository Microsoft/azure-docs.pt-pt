---
title: host.jsem referência para Funções Azure 1.x
description: A documentação de referência para as Funções Azure host.jsarquivada com o tempo de execução v1.
ms.topic: conceptual
ms.date: 10/19/2018
ms.openlocfilehash: 48dba50b384731befdc7fba7c418e542994cedd9
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102608959"
---
# <a name="hostjson-reference-for-azure-functions-1x"></a>host.jsem referência para Funções Azure 1.x

> [!div class="op_single_selector" title1="Selecione a versão do tempo de execução das Funções Azure que está a utilizar: "]
> * [Versão 1](functions-host-json-v1.md)
> * [Versão 2](functions-host-json.md)

A *host.jsno* ficheiro metadados contém opções de configuração global que afetam todas as funções de uma aplicação de função. Este artigo lista as definições disponíveis para o tempo de execução v1. O esquema do JSON está em http://json.schemastore.org/host .

> [!NOTE]
> Este artigo é para Funções Azure 1.x.  Para obter uma referência de host.jsem Funções 2.x e posteriormente, consulte [host.jsem referência para Funções Azure 2.x](functions-host-json.md).

Outras opções de configuração de aplicações de função são geridas nas definições da sua [aplicação.](functions-app-settings.md)

Algumas host.jsnas definições só são utilizadas quando são feitas localmente no [local.settings.jsficheiro.](functions-run-local.md#local-settings-file)

## <a name="sample-hostjson-file"></a>host.jsde amostra no arquivo

As seguintes *host.jsde* amostra em ficheiros têm todas as opções possíveis especificadas.


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
      "autoRenewTimeout": "00:05:00",
      "autoComplete": true
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

## <a name="applicationinsights"></a>aplicaçõesInsights

[!INCLUDE [applicationInsights](../../includes/functions-host-json-applicationinsights.md)]

## <a name="documentdb"></a>DocumentDB

Definições de configuração para o [gatilho DB Azure Cosmos e encadernações](functions-bindings-cosmosdb.md).

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

|Propriedade  |Predefinição | Description |
|---------|---------|---------|
|GatewayMode|Gateway|O modo de ligação utilizado pela função ao ligar-se ao serviço DB Azure Cosmos. As opções são `Direct` e `Gateway`|
|Protocolo|Https|O protocolo de ligação utilizado pela função quando ligado ao serviço DB Azure Cosmos.  Leia [aqui para uma explicação de ambos os modos](../cosmos-db/performance-tips.md#networking)|
|leasePrefix|n/a|Alugar prefixo para usar em todas as funções numa aplicação.|

## <a name="durabletask"></a>DurableTask

[!INCLUDE [durabletask](../../includes/functions-host-json-durabletask.md)]

## <a name="eventhub"></a>eventHub

Definições de configuração para [gatilhos e encadernações do Centro de Eventos](functions-bindings-event-hubs.md#functions-1x).

## <a name="functions"></a>funções

Uma lista de funções que o anfitrião de trabalho dirige. Uma matriz vazia significa executar todas as funções. Destina-se a ser utilizado apenas quando [correr localmente.](functions-run-local.md) Em aplicações de função em Azure, deve em vez disso seguir os passos em [Como desativar funções em Funções Azure](disable-function.md) para desativar funções específicas em vez de utilizar esta definição.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>funçãoTimeout

Indica a duração do tempo limite para todas as funções. Num plano de consumo sem servidor, o intervalo válido é de 1 segundo a 10 minutos, e o valor padrão é de 5 minutos. Num plano de Serviço de Aplicações, não existe um limite global e o padrão é _nulo_, o que indica que não há tempo limite.

```json
{
    "functionTimeout": "00:05:00"
}
```

## <a name="healthmonitor"></a>healthMonitor

Definições de configuração para [monitor de saúde host](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Host-Health-Monitor).

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

|Propriedade  |Predefinição | Description |
|---------|---------|---------| 
|ativado|true|Especifica se a funcionalidade está ativada. | 
|healthCheckInterval|10 segundos|O intervalo de tempo entre as verificações de saúde de fundo periódicos. | 
|healthCheckWindow|2 minutos|Uma janela de tempo deslizante utilizada em conjunto com a `healthCheckThreshold` regulação.| 
|healthCheckThreshold|6|O número máximo de vezes que o exame de saúde pode falhar antes de uma reciclagem do hospedeiro ser iniciada.| 
|contraThreshold|0.80|O limiar em que um contador de desempenho será considerado insalubre.| 

## <a name="http"></a>http

Definições de configuração para [gatilhos e encadernações http](functions-bindings-http-webhook.md).

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

|Propriedade  |Predefinição | Description |
|---------|---------|---------| 
|dynamicThrottlesEnabled|false|Quando ativado, esta definição faz com que o gasoduto de processamento de pedidos verifique periodicamente os contadores de desempenho do sistema, como ligações/fios/processos/memória/cpu/etc. e se algum desses contadores estiver acima de um limiar elevado incorporado (80%), os pedidos serão rejeitados com uma resposta 429 "Demasiado Ocupado" até que o contador volte aos níveis normais.|
|maxConcurrentRequests|desvinculado ( `-1` )|O número máximo de funções HTTP que serão executadas em paralelo. Isto permite-lhe controlar a concordância, o que pode ajudar a gerir a utilização de recursos. Por exemplo, pode ter uma função HTTP que utiliza muitos recursos do sistema (memória/cpu/tomadas) de modo a causar problemas quando a contusão é demasiado elevada. Ou pode ter uma função que faz pedidos de saída para um serviço de terceiros, e essas chamadas precisam de ser limitadas. Nestes casos, aplicar um acelerador aqui pode ajudar.|
|maxOutstandingRequests|desvinculado ( `-1` )|O número máximo de pedidos pendentes que são realizados a qualquer momento. Este limite inclui pedidos que estão em fila mas que não começaram a executar, bem como quaisquer execuções em curso. Quaisquer pedidos de entrada acima deste limite são rejeitados com uma resposta 429 "Demasiado Ocupado". Isso permite que os chamadores utilizem estratégias de relíndi baseadas no tempo, e também ajuda-o a controlar o máximo de atrasos de pedido. Isto apenas controla a fila que ocorre dentro do caminho de execução do anfitrião do script. Outras filas, como a fila de pedidos ASP.NET ainda estarão em vigor e não serão afetadas por esta definição.|
|rotaPrefix|api|O prefixo da rota que se aplica a todas as rotas. Utilize uma corda vazia para remover o prefixo predefinido. |

## <a name="id"></a>ID

A identificação única para um hospedeiro de emprego. Pode ser um GUID de caixa inferior com traços removidos. Obrigatório quando correr localmente. Ao correr em Azure, recomendamos que não desacorda um valor de ID. Um ID é gerado automaticamente em Azure quando `id` é omitido. 

Se partilhar uma conta de Armazenamento em várias aplicações de funções, certifique-se de que cada aplicação de função tem uma diferente `id` . Pode omitir a `id` propriedade ou definir manualmente cada aplicação de função `id` para um valor diferente. O gatilho do temporizador utiliza um bloqueio de armazenamento para garantir que haverá apenas uma instância temporizador quando uma aplicação de função se escala para várias instâncias. Se duas aplicações de função partilharem o mesmo `id` e cada uma utilizar um gatilho do temporizador, apenas um temporizador será executado.

```json
{
    "id": "9f4ea53c5136457d883d685e57164f08"
}
```

## <a name="logger"></a>madeireiro

Controla a filtragem de registos escritos por um objeto [ILogger](functions-dotnet-class-library.md#ilogger) ou por [contexto.log](functions-reference-node.md#contextlog-method).

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

|Propriedade  |Predefinição | Description |
|---------|---------|---------| 
|categoriaFiltro|n/a|Especifica a filtragem por categoria| 
|defaultLevel|Informações|Para quaisquer categorias não especificadas na `categoryLevels` matriz, envie registos a este nível e acima para Insights de Aplicação.| 
|categoriaLevels|n/a|Um conjunto de categorias que especifica o nível mínimo de registo a enviar para Insights de Aplicação para cada categoria. A categoria aqui especificada controla todas as categorias que começam com o mesmo valor, e os valores mais longos têm precedência. Na amostra anterior *host.jsem* ficheiro, todas as categorias que começam com o registo "Host.A agregador" a `Information` nível. Todas as outras categorias que começam com "Host", como "cutor Host.Exe", registam-se a `Error` nível.| 

## <a name="queues"></a>filas

Definições de configuração para [acionadores e encadernações](functions-bindings-storage-queue.md)de fila de armazenamento .

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

|Propriedade  |Predefinição | Description |
|---------|---------|---------| 
|maxPollingInterval|60000|O intervalo máximo em milissegundos entre as sondagens.| 
|visibilidadeTimeout|0|O intervalo de tempo entre as recauchutagens ao processamento de uma mensagem falha.| 
|batchSize|16|O número de mensagens de fila que o tempo de funcionamento das Funções recupera simultaneamente e processa em paralelo. Quando o número que está a ser processado chega ao `newBatchThreshold` , o tempo de funcionamento recebe outro lote e começa a processar essas mensagens. Assim, o número máximo de mensagens simultâneas a serem processadas por função é `batchSize` mais `newBatchThreshold` . Este limite aplica-se separadamente a cada função acionada pela fila. <br><br>Se quiser evitar a execução paralela das mensagens recebidas numa fila, pode definir `batchSize` para 1. No entanto, esta definição elimina a conusncy apenas desde que a sua aplicação de função seja executado numa única máquina virtual (VM). Se a aplicação de função se dimensionar para vários VMs, cada VM pode executar uma instância de cada função desencadeada pela fila.<br><br>O máximo `batchSize` é 32. | 
|maxDequeueCount|5|O número de vezes para tentar processar uma mensagem antes de movê-la para a fila de veneno.| 
|newBatchThreshold|lotEsize/2|Sempre que o número de mensagens a ser processadas se descientemente a este número, o tempo de funcionamento recupera outro lote.| 

## <a name="sendgrid"></a>SendGrid

Definição de configuração para a [ligação de saída SendGrind](functions-bindings-sendgrid.md)

```json
{
    "sendGrid": {
        "from": "Contoso Group <admin@contoso.com>"
    }
}    
```

|Propriedade  |Predefinição | Description |
|---------|---------|---------| 
|De|n/a|O endereço de e-mail do remetente em todas as funções.| 

## <a name="servicebus"></a>serviceBus

Definição de configuração para [acionadores e encadernações do Service Bus](functions-bindings-service-bus.md).

```json
{
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00",
      "autoComplete": true
    }
}
```

|Propriedade  |Predefinição | Description |
|---------|---------|---------| 
|maxConcurrentCalls|16|O número máximo de chamadas simultâneas para a chamada que a bomba de mensagem deve iniciar. Por predefinição, o tempo de execução de Funções processa várias mensagens simultaneamente. Para direcionar o tempo de execução para processar apenas uma fila ou mensagem de tópico de cada vez, definido `maxConcurrentCalls` para 1. | 
|prefetchCount|n/a|O PrefetchCount prefetch padrão que será usado pelo MensagemReceiver subjacente.| 
|autoRenewTimeout|00:05:00|A duração máxima dentro da qual o bloqueio de mensagem será renovado automaticamente.|
|autoComplete|true|Quando for verdade, o gatilho completará automaticamente o processamento da mensagem na execução bem sucedida da operação. Quando falsa, é da responsabilidade da função completar a mensagem antes de regressar.|

## <a name="singleton"></a>singleton

Definições de configuração para o comportamento de bloqueio de Singleton. Para mais informações, consulte [a questão do GitHub sobre o suporte singleton](https://github.com/Azure/azure-webjobs-sdk-script/issues/912).

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

|Propriedade  |Predefinição | Description |
|---------|---------|---------| 
|lockPeriod|00:00:15|O período para o que os bloqueios de nível de função são tomados. As fechaduras renovam-se automaticamente.| 
|ouvinteLockPeriod|00:01:00|O período para o que os ouvintes bloqueiam.| 
|ouvinteLockRecoveryPollingInterval|00:01:00|O intervalo de tempo utilizado para a recuperação do bloqueio do ouvinte se não puder ser adquirido um bloqueio de ouvinte no arranque.| 
|lockAcquisitionTimeout|00:01:00|O tempo máximo de tempo que o tempo de execução tentará adquirir uma fechadura.| 
|lockAcquisitionPollingInterval|n/a|O intervalo entre tentativas de aquisição de bloqueio.| 

## <a name="tracing"></a>traçamento

*Versão 1.x*

Configurações de configuração para registos que cria utilizando um `TraceWriter` objeto. Para saber mais, consulte [C# Logging].

```json
{
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    }
}
```

|Propriedade  |Predefinição | Description |
|---------|---------|---------| 
|consoleLevel|informações|O nível de rastreio para a registo de consolas. As opções são: `off` , , , e `error` `warning` `info` `verbose` .|
|fileLoggingMode|depuraronamente|O nível de rastreio para a extração de ficheiros. As opções `never` `always` são, `debugOnly` . .| 

## <a name="watchdirectories"></a>verdireias

Um conjunto de [diretórios](functions-reference-csharp.md#watched-directories) de código partilhados que devem ser monitorizados para alterações.  Garante que quando o código nestes diretórios é alterado, as alterações são captadas pelas suas funções.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba como atualizar o host.jsno ficheiro](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [Ver configurações globais em variáveis ambientais](functions-app-settings.md)
