---
title: referência de host. JSON para Azure Functions 2. x
description: Documentação de referência para o arquivo Azure Functions host. JSON com o tempo de execução v2.
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 6c4cfbb40c0337a6141d3b1f9844a33227a4963f
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988707"
---
# <a name="hostjson-reference-for-azure-functions-2x-and-later"></a>referência de host. JSON para Azure Functions 2. x e posterior 

> [!div class="op_single_selector" title1="Selecione a versão do Azure Functions tempo de execução que você está usando: "]
> * [Versão 1](functions-host-json-v1.md)
> * [Versão 2+](functions-host-json.md)

O arquivo de metadados *host. JSON* contém opções de configuração globais que afetam todas as funções de um aplicativo de funções. Este artigo lista as configurações que estão disponíveis a partir da versão 2. x do tempo de execução de Azure Functions.  

> [!NOTE]
> Este artigo é para o Azure Functions 2. x e versões posteriores.  Para obter uma referência de Host. JSON nas funções 1.x, consulte [referência de Host. JSON para as funções do Azure 1.x](functions-host-json-v1.md).

Outras opções de configuração de aplicativo de funções são gerenciadas em suas [configurações de aplicativo](functions-app-settings.md).

Algumas configurações de host. JSON são usadas apenas ao executar localmente no arquivo [local. Settings. JSON](functions-run-local.md#local-settings-file) .

## <a name="sample-hostjson-file"></a>Arquivo host. JSON de exemplo

O ficheiro *host.json* da seguinte amostra para a versão 2.x+ tem todas as opções possíveis especificadas (excluindo quaisquer que sejam apenas para uso interno).

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
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
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
              "maxTelemetryItemsPerSecond" : 20,
              "evaluationInterval": "01:00:00",
              "initialSamplingPercentage": 1.0, 
              "samplingPercentageIncreaseTimeout" : "00:00:01",
              "samplingPercentageDecreaseTimeout" : "00:00:01",
              "minSamplingPercentage": 0.1,
              "maxSamplingPercentage": 0.1,
              "movingAverageRatio": 1.0
            },
            "samplingExcludedTypes" : "Dependency;Event",
            "samplingIncludedTypes" : "PageView;Trace",
            "enableLiveMetrics": true,
            "enableDependencyTracking": true,
            "enablePerformanceCountersCollection": true,            
            "httpAutoCollectionOptions": {
                "enableHttpTriggerExtendedInfoCollection": true,
                "enableW3CDistributedTracing": true,
                "enableResponseHeaderInjection": true
            },
            "snapshotConfiguration": {
                "agentEndpoint": null,
                "captureSnapshotMemoryWeight": 0.5,
                "failedRequestLimit": 3,
                "handleUntrackedExceptions": true,
                "isEnabled": true,
                "isEnabledInDeveloperMode": false,
                "isEnabledWhenProfiling": true,
                "isExceptionSnappointsEnabled": false,
                "isLowPrioritySnapshotUploader": true,
                "maximumCollectionPlanSize": 50,
                "maximumSnapshotsRequired": 3,
                "problemCounterResetInterval": "24:00:00",
                "provideAnonymousTelemetry": true,
                "reconnectInterval": "00:15:00",
                "shadowCopyFolder": null,
                "shareUploaderProcess": true,
                "snapshotInLowPriorityThread": true,
                "snapshotsPerDayLimit": 30,
                "snapshotsPerTenMinutesLimit": 1,
                "tempFolder": null,
                "thresholdForSnapshotting": 1,
                "uploaderProxy": null
            }
        }
    },
    "managedDependency": {
        "enabled": true
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

As seções a seguir deste artigo explicam cada propriedade de nível superior. Todos são opcionais, salvo indicação em contrário.

## <a name="aggregator"></a>agregador

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>applicationInsights

Essa configuração é um filho de [registro em log](#logging).

Controla opções para Application Insights, incluindo [Opções de amostragem](./functions-monitoring.md#configure-sampling).

Para obter a estrutura JSON completa, consulte o [exemplo anterior de arquivo host. JSON](#sample-hostjson-file).

> [!NOTE]
> A amostragem de log pode fazer com que algumas execuções não apareçam na folha Application Insights monitor. Para evitar a amostragem de log, adicione `samplingExcludedTypes: "Request"` ao valor de `applicationInsights`.

| Propriedade | Predefinição | Descrição |
| --------- | --------- | --------- | 
| samplingSettings | n/d | Consulte [applicationInsights. samplingSettings](#applicationinsightssamplingsettings). |
| samplingExcludedTypes | nulo | Uma lista delimitada por ponto e vírgula de tipos que você não deseja que sejam amostrados. Os tipos reconhecidos são: dependência, evento, exceção, PageView, solicitação, rastreamento. Todas as instâncias dos tipos especificados são transmitidas; os tipos que não são especificados são amostrados. |
| samplingIncludedTypes | nulo | Uma lista delimitada por ponto e vírgula dos tipos que você deseja que sejam amostrados; uma lista vazia implica todos os tipos. Tipo listado em `samplingExcludedTypes` tipos de substituição listados aqui. Os tipos reconhecidos são: dependência, evento, exceção, PageView, solicitação, rastreamento. Todas as instâncias dos tipos especificados são transmitidas; os tipos que não são especificados são amostrados. |
| enableLiveMetrics | true | Habilita a coleta de métricas em tempo real. |
| enableDependencyTracking | true | Habilita o rastreamento de dependência. |
| enablePerformanceCountersCollection | true | Habilita a coleta de contadores de desempenho kudu. |
| liveMetricsInitializationDelay | 00:00:15 | Apenas para uso interno. |
| httpAutoCollectionOptions | n/d | Consulte [applicationInsights. httpAutoCollectionOptions](#applicationinsightshttpautocollectionoptions). |
| snapshotConfiguration | n/d | Consulte [applicationInsights. snapshotConfiguration](#applicationinsightssnapshotconfiguration). |

### <a name="applicationinsightssamplingsettings"></a>applicationInsights. samplingSettings

|Propriedade | Predefinição | Descrição |
| --------- | --------- | --------- | 
| isEnabled | true | Habilita ou desabilita a amostragem. | 
| maxTelemetryItemsPerSecond | 20 | O número de destino de itens de telemetria registrados por segundo em cada host do servidor. Se seu aplicativo for executado em vários hosts, reduza esse valor para permanecer dentro da taxa de destino de tráfego geral. | 
| evaluationInterval | 01:00:00 | O intervalo no qual a taxa atual de telemetria é reavaliada. A avaliação é executada como uma média móvel. Talvez você queira encurtar esse intervalo se sua telemetria for responsável por picos repentinos. |
| initialSamplingPercentage| 1.0 | A porcentagem de amostragem inicial aplicada no início do processo de amostragem para variar dinamicamente a porcentagem. Não reduza o valor enquanto estiver depurando. |
| samplingPercentageIncreaseTimeout | 00:00:01 | Quando o valor da porcentagem de amostragem é alterado, essa propriedade determina quanto tempo depois Application Insights tem permissão para aumentar a porcentagem de amostragem novamente para capturar mais dados. |
| samplingPercentageDecreaseTimeout | 00:00:01 | Quando o valor da porcentagem de amostragem é alterado, essa propriedade determina quanto tempo depois Application Insights tem permissão para reduzir a porcentagem de amostragem novamente para capturar menos dados. |
| minSamplingPercentage | 0.1 | À medida que a porcentagem de amostragem varia, essa propriedade determina a porcentagem de amostragem mínima permitida. |
| maxSamplingPercentage | 0.1 | À medida que a porcentagem de amostragem varia, essa propriedade determina a porcentagem máxima de amostragem permitida. |
| movingAverageRatio | 1.0 | No cálculo da média móvel, o peso atribuído ao valor mais recente. Use um valor igual ou menor que 1. Valores menores tornam o algoritmo menos reativo para alterações repentinas. |

### <a name="applicationinsightshttpautocollectionoptions"></a>applicationInsights. httpAutoCollectionOptions

|Propriedade | Predefinição | Descrição |
| --------- | --------- | --------- | 
| enableHttpTriggerExtendedInfoCollection | true | Habilita ou desabilita informações de solicitação HTTP estendidas para gatilhos HTTP: cabeçalhos de correlação de solicitação de entrada, suporte para chaves de várias instrumentação, método HTTP, caminho e resposta. |
| enableW3CDistributedTracing | true | Habilita ou desabilita o suporte do protocolo de rastreamento distribuído W3C (e ativa o esquema de correlação herdado). Habilitado por padrão se `enableHttpTriggerExtendedInfoCollection` for true. Se `enableHttpTriggerExtendedInfoCollection` for false, esse sinalizador se aplicará somente a solicitações de saída, não a solicitações de entrada. |
| enableResponseHeaderInjection | true | Habilita ou desabilita a injeção de cabeçalhos de correlação de vários componentes em respostas. Habilitar a injeção permite que Application Insights Construa um mapa de aplicativo para quando várias chaves de instrumentação são usadas. Habilitado por padrão se `enableHttpTriggerExtendedInfoCollection` for true. Essa configuração não se aplica se `enableHttpTriggerExtendedInfoCollection` for false. |

### <a name="applicationinsightssnapshotconfiguration"></a>applicationInsights. snapshotConfiguration

Para obter mais informações sobre instantâneos, consulte [depurar instantâneos em exceções em aplicativos .net](/azure/azure-monitor/app/snapshot-debugger) e [solucionar problemas ao habilitar Application insights depurador de instantâneos ou exibir instantâneos](/azure/azure-monitor/app/snapshot-debugger-troubleshoot).

|Propriedade | Predefinição | Descrição |
| --------- | --------- | --------- | 
| agentEndpoint | nulo | O ponto de extremidade usado para se conectar ao serviço de Depurador de Instantâneos de Application Insights. Se for NULL, será usado um ponto de extremidade padrão. |
| captureSnapshotMemoryWeight | 0,5 | O peso dado ao tamanho da memória do processo atual ao verificar se há memória suficiente para tirar um instantâneo. O valor esperado é maior que 0 fração correta (0 < CaptureSnapshotMemoryWeight < 1). |
| failedRequestLimit | 3 | O limite do número de solicitações com falha para solicitar instantâneos antes que o processador de telemetria seja desabilitado.|
| handleUntrackedExceptions | true | Habilita ou desabilita o rastreamento de exceções que não são controladas pela telemetria do Application Insights. |
| isEnabled | true | Habilita ou desabilita a coleta de instantâneos | 
| isEnabledInDeveloperMode | false | Habilita ou desabilita a coleta de instantâneos habilitada no modo de desenvolvedor. |
| isEnabledWhenProfiling | true | Habilita ou desabilita a criação de instantâneos, mesmo se o Application Insights Profiler estiver coletando uma sessão detalhada de criação de perfil. |
| isExceptionSnappointsEnabled | false | Habilita ou desabilita a filtragem de exceções. |
| isLowPrioritySnapshotUploader | true | Determina se o processo SnapshotUploader deve ser executado na prioridade normal abaixo. |
| maximumCollectionPlanSize | 50 | O número máximo de problemas que podemos rastrear a qualquer momento em um intervalo de um a 9999. |
| maximumSnapshotsRequired | 3 | O número máximo de instantâneos coletados para um único problema, em um intervalo de um a 999. Um problema pode ser considerado uma instrução Throw individual em seu aplicativo. Depois que o número de instantâneos coletados para um problema atingir esse valor, nenhum instantâneo será coletado para esse problema até que os contadores de problema sejam redefinidos (consulte `problemCounterResetInterval`) e o limite de `thresholdForSnapshotting` seja atingido novamente. |
| problemCounterResetInterval | 24:00:00 | Com que frequência os contadores de problema são redefinidos em um intervalo de um minuto a sete dias. Quando esse intervalo é atingido, todas as contagens de problemas são redefinidas para zero. Problemas existentes que já atingiram o limite para fazer instantâneos, mas ainda não geraram o número de instantâneos em `maximumSnapshotsRequired`, permanecerão ativos. |
| provideAnonymousTelemetry | true | Determina se o uso anônimo e a telemetria de erro devem ser enviados à Microsoft. Essa telemetria poderá ser usada se você entrar em contato com a Microsoft para ajudar a solucionar problemas com o Depurador de Instantâneos. Ele também é usado para monitorar padrões de uso. |
| reconnectInterval | 00:15:00 | Com que frequência reconectamos ao ponto de extremidade de Depurador de Instantâneos. O intervalo permitido é de um minuto a um dia. |
| shadowCopyFolder | nulo | Especifica a pasta a ser usada para binários de cópia de sombra. Se não for definido, as pastas especificadas pelas seguintes variáveis ambientais são experimentadas por ordem: Fabric_Folder_App_Temp, LOCALAPPDATA, APPDATA, TEMP. |
| shareUploaderProcess | true | Se for true, apenas uma instância de SnapshotUploader coletará e carregará instantâneos para vários aplicativos que compartilham o InstrumentationKey. Se definido como false, o SnapshotUploader será exclusivo para cada tupla (ProcessName, InstrumentationKey). |
| snapshotInLowPriorityThread | true | Determina se os instantâneos devem ou não ser processados em um thread de prioridade baixa e/s. A criação de um instantâneo é uma operação rápida, mas, para carregar um instantâneo no serviço de Depurador de Instantâneos, ele deve ser gravado primeiro no disco como um minidespejo. Isso acontece no processo de SnapshotUploader. Definir esse valor como true usa a e/s de baixa prioridade para gravar o minidespejo, que não concorrerá em seu aplicativo para recursos. Definir esse valor como false acelera a criação de minidespejos com a despesa de reduzir o seu aplicativo. |
| snapshotsPerDayLimit | 30 | O número máximo de instantâneos permitido em um dia (24 horas). Esse limite também é imposto no lado do serviço Application Insights. Os carregamentos têm uma taxa limitada a 50 por dia por aplicativo (isto é, por chave de instrumentação). Esse valor ajuda a impedir a criação de instantâneos adicionais que eventualmente serão rejeitados durante o carregamento. Um valor de zero remove totalmente o limite, o que não é recomendado. |
| snapshotsPerTenMinutesLimit | 1 | O número máximo de instantâneos permitido em 10 minutos. Embora não haja nenhum limite superior nesse valor, tome cuidado ao aumentá-lo nas cargas de trabalho de produção, pois isso pode afetar o desempenho do seu aplicativo. Criar um instantâneo é rápido, mas criar um minidespejo do instantâneo e carregá-lo para o serviço de Depurador de Instantâneos é uma operação muito mais lenta que conseguirá competir com seu aplicativo para recursos (CPU e e/s). |
| tempFolder | nulo | Especifica a pasta para gravar minidespejos e arquivos de log do carregador. Se não estiver definido, então *são utilizados depósitos de despejos %TEMP%\.\.* |
| thresholdForSnapshotting | 1 | Quantas vezes Application Insights precisa ver uma exceção antes de solicitar instantâneos. |
| uploaderProxy | nulo | Substitui o servidor proxy usado no processo de carregador de instantâneo. Talvez seja necessário usar essa configuração se o aplicativo se conectar à Internet por meio de um servidor proxy. O Snapshot Collector é executado no processo do aplicativo e usará as mesmas configurações de proxy. No entanto, o carregador de instantâneos é executado como um processo separado e talvez seja necessário configurar o servidor proxy manualmente. Se esse valor for nulo, Snapshot Collector tentará detectar automaticamente o endereço do proxy examinando System .net. WebRequest. DefaultWebProxy e passando o valor para o carregador de instantâneos. Se esse valor não for nulo, a detecção automática não será usada e o servidor proxy especificado aqui deverá ser usado no carregador de instantâneos. |

## <a name="cosmosdb"></a>cosmosDb

O parâmetro de configuração pode ser encontrado em [Cosmos DB gatilhos e associações](functions-bindings-cosmosdb-v2.md#host-json).

## <a name="durabletask"></a>durableTask

O parâmetro de configuração pode ser encontrado em [associações para Durable Functions](durable/durable-functions-bindings.md#host-json).

## <a name="eventhub"></a>eventHub

As definições de configuração podem ser encontradas em [gatilhos e associações de Hub de eventos](functions-bindings-event-hubs.md#host-json). 

## <a name="extensions"></a>WMZ

Propriedade que retorna um objeto que contém todas as configurações específicas de associação, como [http](#http) e [eventHub](#eventhub).

## <a name="extensionbundle"></a>extensionBundle 

Os pacotes de extensão permitem que você adicione um conjunto compatível de extensões de associação de funções ao seu aplicativo de funções. Para saber mais, confira [pacotes de extensão para o desenvolvimento local](functions-bindings-register.md#extension-bundles).

[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

## <a name="functions"></a>functions

Uma lista de funções que o host de trabalho executa. Uma matriz vazia significa executar todas as funções. Destinado para uso somente quando [executado localmente](functions-run-local.md). Em aplicativos de funções no Azure, você deve seguir as etapas em [como desabilitar funções no Azure Functions](disable-function.md) para desabilitar funções específicas em vez de usar essa configuração.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Indica a duração do tempo limite para todas as funções. Ele segue o formato de cadeia de caracteres TimeSpan. Em um plano de consumo sem servidor, o intervalo válido é de 1 segundo a 10 minutos e o valor padrão é 5 minutos.  

No plano Premium, o intervalo válido é de 1 segundo a 60 minutos e o valor padrão é 30 minutos.

Em um plano dedicado (serviço de aplicativo), não há nenhum limite geral e o valor padrão é 30 minutos. Um valor de `-1` indica a execução não associada, mas é recomendável manter um limite superior fixo.

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
|comlimite|0.80|O limite no qual um contador de desempenho será considerado não íntegro.| 

## <a name="http"></a>http

As definições de configuração podem ser encontradas em [gatilhos e associações http](functions-bindings-http-webhook.md#hostjson-settings).

## <a name="logging"></a>exploração madeireira

Controla os comportamentos de log do aplicativo de funções, incluindo Application Insights.

```json
"logging": {
    "fileLoggingMode": "debugOnly"
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
|fileLoggingMode|debugOnly|Define o nível de registro em log de arquivo habilitado.  As opções são `never`, `always``debugOnly`. |
|logLevel|n/d|Objeto que define a filtragem de categoria de log para funções no aplicativo. As versões 2. x e posteriores seguem o layout de ASP.NET Core para filtragem de categorias de log. Essa configuração permite filtrar o registro em log para funções específicas. Para obter mais informações, consulte [filtragem de log](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering) na documentação do ASP.NET Core. |
|consola|n/d| O [console](#console) definição de registo. |
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

## <a name="manageddependency"></a>managedDependency

A dependência gerenciada é um recurso que atualmente só tem suporte com funções baseadas no PowerShell. Ele permite que as dependências sejam gerenciadas automaticamente pelo serviço. Quando a propriedade `enabled` é definida como `true`, o arquivo de `requirements.psd1` é processado. As dependências são atualizadas quando qualquer versão secundária é lançada. Para obter mais informações, consulte [dependência gerenciada](functions-reference-powershell.md#dependency-management) no artigo do PowerShell.

```json
{
    "managedDependency": {
        "enabled": true
    }
}
```

## <a name="queues"></a>filas

As definições de configuração podem ser encontradas em [gatilhos e associações de fila de armazenamento](functions-bindings-storage-queue.md#host-json).  

## <a name="sendgrid"></a>sendGrid

O parâmetro de configuração pode ser encontrado em [gatilhos e associações do SendGrid](functions-bindings-sendgrid.md#host-json).

## <a name="servicebus"></a>serviceBus

O parâmetro de configuração pode ser encontrado em [gatilhos e associações do barramento de serviço](functions-bindings-service-bus.md#host-json).

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

## <a name="version"></a>versão

Este valor indica a versão schema do host.json. A versão `"version": "2.0"` é necessária para uma aplicação de função que se direcione para o tempo de execução v2, ou uma versão posterior. Não há mudanças de esquema son.json entre v2 e v3.

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
