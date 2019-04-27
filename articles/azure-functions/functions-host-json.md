---
title: referência de Host. JSON para as funções do Azure 2.x
description: Documentação de referência para o ficheiro de Host. JSON de funções do Azure com o tempo de execução do v2.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/08/2018
ms.author: glenga
ms.openlocfilehash: e24c5b2be1df41d84fa4461250f51cb009f77529
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60737214"
---
# <a name="hostjson-reference-for-azure-functions-2x"></a>referência de Host. JSON para as funções do Azure 2.x  

> [!div class="op_single_selector" title1="Select the version of the Azure Functions runtime you are using: "]
> * [Versão 1](functions-host-json-v1.md)
> * [Versão 2](functions-host-json.md)

O *Host. JSON* ficheiro de metadados contém opções de configuração globais que afetam todas as funções para uma aplicação de funções. Este artigo lista as definições que estão disponíveis para o tempo de execução do v2.  

> [!NOTE]
> Este artigo é para as funções do Azure 2.x.  Para obter uma referência de Host. JSON nas funções 1.x, consulte [referência de Host. JSON para as funções do Azure 1.x](functions-host-json-v1.md).

Outras opções de configuração de aplicação de função são geridas no seu [as definições da aplicação](functions-app-settings.md).

Algumas definições de Host. JSON só são utilizadas quando em execução localmente na [Settings](functions-run-local.md#local-settings-file) ficheiro.

## <a name="sample-hostjson-file"></a>Ficheiro de Host. JSON de exemplo

O exemplo a seguir *Host. JSON* ficheiros têm todas as opções possíveis especificadas.


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
              "maxTelemetryItemsPerSecond" : 5
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
    "watchDirectories": [ "Shared", "Test" ]
}
```

As secções seguintes deste artigo explicam cada propriedade de nível superior. Todas são opcionais, a menos que indicado de outra forma.

## <a name="aggregator"></a>aggregator

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>applicationInsights

Esta definição é um elemento subordinado [registo](#logging).

Controles a [funcionalidade de amostragem no Application Insights](./functions-monitoring.md#configure-sampling).

```json
{
    "applicationInsights": {
        "samplingSettings": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    }
}
```

> [!NOTE]
> Amostragem de registo pode fazer com que algumas execuções a não aparecer no painel de monitorização do Application Insights.

|Propriedade  |Predefinição | Descrição |
|---------|---------|---------| 
|isEnabled|true|Ativa ou desativa a amostragem.| 
|maxTelemetryItemsPerSecond|5|O limiar em que amostragem começa.| 

## <a name="cosmosdb"></a>cosmosDb

Definição de configuração pode ser encontrada na [Cosmos DB acionadores e enlaces](functions-bindings-cosmosdb-v2.md#host-json).

## <a name="durabletask"></a>durableTask

Definição de configuração pode ser encontrada na [enlaces para as funções duráveis](durable/durable-functions-bindings.md#host-json).

## <a name="eventhub"></a>eventHub

Definições de configuração podem ser encontradas na [Hub de eventos de acionadores e enlaces](functions-bindings-event-hubs.md#host-json). 

## <a name="extensions"></a>Extensões

Propriedade que retorna um objeto que contém todas as definições de ligação específica, como [http](#http) e [eventHub](#eventhub).

## <a name="functions"></a>functions

Uma lista de funções que o anfitrião de tarefa é executada. Uma matriz vazia significa executar todas as funções. Foi concebido para utilização apenas quando [em execução localmente](functions-run-local.md). Nas aplicações de função no Azure, em vez disso, deve seguir os passos em [como desativar funções nas funções do Azure](disable-function.md) para desativar funções específicas, em vez de utilizar esta definição.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Indica o período de tempo limite para todas as funções. No plano de consumo sem servidor, o intervalo válido é entre 1 segundo para 10 minutos e o valor predefinido é de 5 minutos. Num plano do serviço de aplicações, não existe nenhum limite global e o padrão depende da versão de runtime. Na versão 2.x, o valor predefinido para um serviço de aplicações plano é 30 minutos. Na versão 1.x, é *nulo*, que indica sem tempo limite.

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

Definições de configuração podem ser encontradas na [http acionadores e enlaces](functions-bindings-http-webhook.md).

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="logging"></a>Registro em log

Controla os comportamentos de registo da aplicação de função, incluindo o Application Insights.

```json
"logging": {
    "fileLoggingMode": "debugOnly",
    "logLevel": {
      "Function.MyFunction": "Information",
      "default": "None"
    },
    "applicationInsights": {
        ...
    }
}
```

|Propriedade  |Predefinição | Descrição |
|---------|---------|---------|
|fileLoggingMode|debugOnly|Define o nível de registo de ficheiros está ativado.  As opções são `never`, `always`, `debugOnly`. |
|logLevel|n/d|Objeto que define a categoria de registo de filtragem para as funções na aplicação. Versão 2.x segue o layout do ASP.NET Core para filtragem de categoria do registo. Isto permite-lhe filtrar o registo para funções específicas. Para obter mais informações, consulte [filtragem de Log](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering) na documentação do ASP.NET Core. |
|consola|n/d| O [consola](#console) definição de registo. |
|applicationInsights|n/d| O [Application Insights](#applicationinsights) definição. |

## <a name="console"></a>consola

Esta definição é um elemento subordinado [registo](#logging). Ele controla a consola do registo quando não estiver no modo de depuração.

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
|isEnabled|false|Ativa ou desativa o registo da consola.| 

## <a name="queues"></a>filas

Definições de configuração podem ser encontradas na [acionadores de fila de armazenamento e enlaces](functions-bindings-storage-queue.md#host-json).  

## <a name="sendgrid"></a>sendGrid

Definição de configuração pode ser encontrada na [SendGrid acionadores e enlaces](functions-bindings-sendgrid.md#host-json).

## <a name="servicebus"></a>serviceBus

Definição de configuração pode ser encontrada na [do Service Bus acionadores e enlaces](functions-bindings-service-bus.md#host-json).

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

## <a name="version"></a>versão

A cadeia de versão `"version": "2.0"` é necessária para uma aplicação de função que tenha como destino o tempo de execução do v2.

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
