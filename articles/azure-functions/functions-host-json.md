---
title: referência de host. JSON para Azure Functions 2. x
description: Documentação de referência para o arquivo Azure Functions host. JSON com o tempo de execução v2.
author: ggailey777
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/08/2018
ms.author: glenga
ms.openlocfilehash: 2a61a2ba74ccdaa69b26cae65dd4f74a7b837ccf
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72927452"
---
# <a name="hostjson-reference-for-azure-functions-2x"></a>referência de host. JSON para Azure Functions 2. x  

> [!div class="op_single_selector" title1="Selecione a versão do Azure Functions tempo de execução que você está usando: "]
> * [Versão 1](functions-host-json-v1.md)
> * [Versão 2](functions-host-json.md)

O arquivo de metadados *host. JSON* contém opções de configuração globais que afetam todas as funções de um aplicativo de funções. Este artigo lista as configurações que estão disponíveis para o tempo de execução v2.  

> [!NOTE]
> Este artigo é para o Azure Functions 2. x.  Para obter uma referência de host. JSON nas funções 1. x, consulte [referência de host. JSON para Azure Functions 1. x](functions-host-json-v1.md).

Outras opções de configuração de aplicativo de funções são gerenciadas em suas [configurações de aplicativo](functions-app-settings.md).

Algumas configurações de host. JSON são usadas apenas ao executar localmente no arquivo [local. Settings. JSON](functions-run-local.md#local-settings-file) .

## <a name="sample-hostjson-file"></a>Arquivo host. JSON de exemplo

Os arquivos *host. JSON* de exemplo a seguir têm todas as opções possíveis especificadas.

```json
{
    "version": "2.0",
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "extensions": {
        "cosmosDb": {},
        "durableTask": {},
        "eventHubs": {},
        "http": {},
        "queues": {},
        "sendGrid": {},
        "serviceBus": {}
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
    "logging": {
        "fileLoggingMode": "debugOnly",
        "logLevel": {
          "Function.MyFunction": "Information",
          "default": "None"
        },
        "applicationInsights": {
            "samplingSettings": {
              "isEnabled": true,
              "maxTelemetryItemsPerSecond" : 20
            }
        }
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "watchDirectories": [ "Shared", "Test" ],
    "managedDependency": {
        "enabled": true
    }
}
```

As seções a seguir deste artigo explicam cada propriedade de nível superior. Todos são opcionais, salvo indicação em contrário.

## <a name="aggregator"></a>agregador

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>applicationInsights

Essa configuração é um filho de [registro em log](#logging).

Controla o [recurso de amostragem no Application insights](./functions-monitoring.md#configure-sampling).

```json
{
    "applicationInsights": {
        "samplingSettings": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 20
        }
    }
}
```

> [!NOTE]
> A amostragem de log pode fazer com que algumas execuções não apareçam na folha Application Insights monitor.

|Propriedade  |Predefinição | Descrição |
|---------|---------|---------| 
|isEnabled|true|Habilita ou desabilita a amostragem.| 
|maxTelemetryItemsPerSecond|20|O limite no qual a amostragem começa.| 
|EnableLiveMetrics |true|Habilita a coleta de métricas em tempo real.|
|EnableDependencyTracking|true|Habilita o rastreamento de dependência.|
|EnablePerformanceCountersCollection|true|Habilita a coleta de contadores de desempenho kudu.|

## <a name="cosmosdb"></a>cosmosDb

O parâmetro de configuração pode ser encontrado em [Cosmos DB gatilhos e associações](functions-bindings-cosmosdb-v2.md#host-json).

## <a name="durabletask"></a>durableTask

O parâmetro de configuração pode ser encontrado em [associações para Durable Functions](durable/durable-functions-bindings.md#host-json).

## <a name="eventhub"></a>eventHub

As definições de configuração podem ser encontradas em [gatilhos e associações de Hub de eventos](functions-bindings-event-hubs.md#host-json). 

## <a name="extensions"></a>WMZ

Propriedade que retorna um objeto que contém todas as configurações específicas de associação, como [http](#http) e [eventHub](#eventhub).

## <a name="functions"></a>functions

Uma lista de funções que o host de trabalho executa. Uma matriz vazia significa executar todas as funções. Destinado para uso somente quando [executado localmente](functions-run-local.md). Em aplicativos de funções no Azure, você deve seguir as etapas em [como desabilitar funções no Azure Functions](disable-function.md) para desabilitar funções específicas em vez de usar essa configuração.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Indica a duração do tempo limite para todas as funções. Ele segue o formato de cadeia de caracteres TimeSpan. Em um plano de consumo sem servidor, o intervalo válido é de 1 segundo a 10 minutos e o valor padrão é 5 minutos.  
Em um plano dedicado (serviço de aplicativo), não há nenhum limite geral e o valor padrão é 30 minutos. Um valor de `-1` indica execução não associada.

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
|Habilitado|true|Especifica se o recurso está habilitado. | 
|healthCheckInterval|10 segundos|O intervalo de tempo entre as verificações de integridade de segundo plano periódicas. | 
|healthCheckWindow|2 minutos|Uma janela de tempo deslizante usada em conjunto com a configuração `healthCheckThreshold`.| 
|healthCheckThreshold|6|Número máximo de vezes que a verificação de integridade pode falhar antes que uma reciclagem de host seja iniciada.| 
|comlimite|0,80|O limite no qual um contador de desempenho será considerado não íntegro.| 

## <a name="http"></a>http

As definições de configuração podem ser encontradas em [gatilhos e associações http](functions-bindings-http-webhook.md).

```json
{
    "extensions": {
        "http": {
            "routePrefix": "api",
            "maxOutstandingRequests": 200,
            "maxConcurrentRequests": 100,
            "dynamicThrottlesEnabled": true
        }
    }
}
```


[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="logging"></a>Logout

Controla os comportamentos de log do aplicativo de funções, incluindo Application Insights.

```json
"logging": {
    "fileLoggingMode": "debugOnly",
    "logLevel": {
      "Function.MyFunction": "Information",
      "default": "None"
    },
    "console": {
        ...
    },
    "applicationInsights": {
        ...
    }
}
```

|Propriedade  |Predefinição | Descrição |
|---------|---------|---------|
|filelogmode|debugOnly|Define o nível de registro em log de arquivo habilitado.  As opções são `never`, `always`, `debugOnly`. |
|logLevel|n/d|Objeto que define a filtragem de categoria de log para funções no aplicativo. A versão 2. x segue o layout de ASP.NET Core para filtragem de categorias de log. Isso permite filtrar o registro em log para funções específicas. Para obter mais informações, consulte [filtragem de log](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering) na documentação do ASP.NET Core. |
|consola|n/d| A configuração de log do [console](#console) . |
|applicationInsights|n/d| A configuração [applicationInsights](#applicationinsights) . |

## <a name="console"></a>consola

Essa configuração é um filho de [registro em log](#logging). Ele controla o log do console quando não está no modo de depuração.

```json
{
    "logging": {
    ...
        "console": {
          "isEnabled": "false"
        },
    ...
    }
}
```

|Propriedade  |Predefinição | Descrição |
|---------|---------|---------| 
|isEnabled|false|Habilita ou desabilita o log do console.| 

## <a name="queues"></a>filas

As definições de configuração podem ser encontradas em [gatilhos e associações de fila de armazenamento](functions-bindings-storage-queue.md#host-json).  

## <a name="sendgrid"></a>sendGrid

O parâmetro de configuração pode ser encontrado em [gatilhos e associações do SendGrid](functions-bindings-sendgrid.md#host-json).

## <a name="servicebus"></a>serviceBus

O parâmetro de configuração pode ser encontrado em [gatilhos e associações do barramento de serviço](functions-bindings-service-bus.md#host-json).

## <a name="singleton"></a>único

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

## <a name="version"></a>versão

A cadeia de caracteres de versão `"version": "2.0"` é necessária para um aplicativo de funções destinado ao tempo de execução v2.

## <a name="watchdirectories"></a>watchDirectories

Um conjunto de [diretórios de código compartilhado](functions-reference-csharp.md#watched-directories) que deve ser monitorado para alterações.  Garante que, quando o código nesses diretórios for alterado, as alterações sejam coletadas por suas funções.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="manageddependency"></a>managedDependency

A dependência gerenciada é um recurso de visualização que atualmente só tem suporte com funções baseadas no PowerShell. Ele permite que as dependências sejam gerenciadas automaticamente pelo serviço. Quando a propriedade Enabled é definida como true, o arquivo [requirements. psd1](functions-reference-powershell.md#dependency-management) será processado. As dependências serão atualizadas quando qualquer versão secundária for lançada.

```json
{
    "managedDependency": {
        "enabled": true
    }
}
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba como atualizar o arquivo host. JSON](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [Consulte Configurações globais em variáveis de ambiente](functions-app-settings.md)
