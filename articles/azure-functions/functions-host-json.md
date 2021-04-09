---
title: host.jsem referência para Funções Azure 2.x
description: A documentação de referência para as Funções Azure host.jsem arquivo com o tempo de execução v2.
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: cbedf2212c52d8f1996d3cce0d96d494313ea525
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102608823"
---
# <a name="hostjson-reference-for-azure-functions-2x-and-later"></a>Referência host.json das Funções do Azure 2.x e posterior 

> [!div class="op_single_selector" title1="Selecione a versão do tempo de execução das Funções Azure que está a utilizar: "]
> * [Versão 1](functions-host-json-v1.md)
> * [Versão 2+](functions-host-json.md)

A *host.jsno* ficheiro metadados contém opções de configuração global que afetam todas as funções de uma aplicação de função. Este artigo lista as definições disponíveis a partir da versão 2.x do tempo de funcionamento das Funções Azure.  

> [!NOTE]
> Este artigo destina-se a Azure Functions 2.x e versões posteriores.  Para obter uma referência de host.jsem Funções 1.x, consulte [host.jsem referência para as Funções Azure 1.x](functions-host-json-v1.md).

Outras opções de configuração de aplicações de função são geridas nas definições da sua aplicação (para aplicações [implementadas)](functions-app-settings.md) ou na sua [local.settings.jsem](functions-run-local.md#local-settings-file) ficheiro (para desenvolvimento local).

As configurações em host.jsrelacionadas com encadernações são aplicadas igualmente a cada função na aplicação de função. 

Também pode [sobrepor-se ou aplicar definições por ambiente](#override-hostjson-values) utilizando as definições de aplicação.

## <a name="sample-hostjson-file"></a>host.jsde amostra no arquivo

A amostra que se *seguehost.jsno* ficheiro para a versão 2.x+ tem todas as opções possíveis especificadas (excluindo as que são apenas para uso interno).

```json
{
    "version": "2.0",
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "extensions": {
        "blobs": {},
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
              "initialSamplingPercentage": 100.0, 
              "samplingPercentageIncreaseTimeout" : "00:00:01",
              "samplingPercentageDecreaseTimeout" : "00:00:01",
              "minSamplingPercentage": 0.1,
              "maxSamplingPercentage": 100.0,
              "movingAverageRatio": 1.0,
              "excludedTypes" : "Dependency;Event",
              "includedTypes" : "PageView;Trace"
            },
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
    "retry": {
      "strategy": "fixedDelay",
      "maxRetryCount": 5,
      "delayInterval": "00:00:05"
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "watchDirectories": [ "Shared", "Test" ],
    "watchFiles": [ "myFile.txt" ]
}
```

As seguintes secções deste artigo explicam cada propriedade de alto nível. Todos são opcionais, salvo indicação em contrário.

## <a name="aggregator"></a>agregador

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>aplicaçõesInsights

Esta definição é uma criança de [registo.](#logging)

Controla as opções de Insights de Aplicação, incluindo [opções de amostragem.](./configure-monitoring.md#configure-sampling)

Para obter a estrutura JSON completa, consulte o exemplo anterior [host.jsem arquivo](#sample-hostjson-file).

> [!NOTE]
> A amostragem de registo pode fazer com que algumas execuções não apareçam na lâmina do monitor Application Insights. Para evitar a amostragem de registo, adicione `excludedTypes: "Request"` ao `samplingSettings` valor.

| Propriedade | Predefinição | Description |
| --------- | --------- | --------- | 
| amostragemS | n/a | Ver [aplicaçõesInsights.samplingSettings](#applicationinsightssamplingsettings). |
| enableLiveMetrics | true | Permite a recolha de métricas ao vivo. |
| enableDependencyTracking | true | Permite o rastreio da dependência. |
| enablePerformanceCountersCollection | true | Permite a coleção de contadores de desempenho kudu. |
| liveMetricsInitializaçãoDela | 00:00:15 | Apenas para utilização interna. |
| httpAutoCollectionOptions | n/a | Ver [aplicaçõesInsights.httpAutoCollectionOptions](#applicationinsightshttpautocollectionoptions). |
| snapshotConfiguration | n/a | Ver [aplicaçãoInsights.snapshotConfiguration](#applicationinsightssnapshotconfiguration). |

### <a name="applicationinsightssamplingsettings"></a>applicationInsights.samplingSettings

Para obter mais informações sobre estas definições, consulte [a Amostragem em Insights de Aplicação](../azure-monitor/app/sampling.md). 

|Propriedade | Predefinição | Description |
| --------- | --------- | --------- | 
| isEnabled | true | Permite ou desativa a amostragem. | 
| maxTelemetryItemsPerSecond | 20 | O número-alvo de itens de telemetria registados por segundo em cada anfitrião do servidor. Se a sua aplicação for executado em muitos anfitriões, reduza este valor para permanecer dentro da sua taxa de tráfego geral. | 
| avaliaçãoInterval | 01:00:00 | O intervalo em que a taxa atual de telemetria é reavaliada. A avaliação é realizada como uma média móvel. É melhor encurtar este intervalo se a sua telemetria for suscetível de explosões repentinas. |
| iniciaisSamplingPercentage| 100.0 | A percentagem inicial de amostragem aplicada no início do processo de amostragem varia dinamicamente a percentagem. Não reduza o valor enquanto está a depurar. |
| amostragemPercentageIncreasTimeout | 00:00:01 | Quando o valor percentual de amostragem muda, esta propriedade determina quão cedo a Application Insights é permitida a aumentar a percentagem de amostragem novamente para capturar mais dados. |
| amostragemPercentageDecreaseTimeout | 00:00:01 | Quando o valor percentual de amostragem muda, esta propriedade determina quão cedo os Insights de Aplicação são autorizados a baixar novamente a percentagem de amostragem para capturar menos dados. |
| minSamplingPercentage | 0.1 | Como a percentagem de amostragem varia, esta propriedade determina a percentagem mínima de amostragem permitida. |
| maxSamplingPercentage | 100.0 | Como a percentagem de amostragem varia, esta propriedade determina a percentagem máxima de amostragem permitida. |
| movingAverageRatio | 1.0 | No cálculo da média móvel, o peso atribuído ao valor mais recente. Utilize um valor igual ou inferior a 1. Valores menores tornam o algoritmo menos reativo a mudanças repentinas. |
| Tipos excluídos | nulo | Uma lista de tipos delimitados semi-cólon que não quer ser amostrado. Os tipos reconhecidos são: `Dependency` , , , , e `Event` `Exception` `PageView` `Request` `Trace` . Todas as instâncias dos tipos especificados são transmitidas; os tipos que não são especificados são amostrados. |
| incluídosTipos | nulo | Uma lista de tipos delimitados de pontos semi-cólon que pretende ser amostrado; uma lista vazia implica todos os tipos. Digite listado em `excludedTypes` tipos de substituição listados aqui. Os tipos reconhecidos são: `Dependency` , , , , e `Event` `Exception` `PageView` `Request` `Trace` . São amostradas as instâncias dos tipos especificados; os tipos que não são especificados ou implícitos são transmitidos sem amostragem. |

### <a name="applicationinsightshttpautocollectionoptions"></a>applicationInsights.httpAutoCollectionOptions

|Propriedade | Predefinição | Description |
| --------- | --------- | --------- | 
| enableHttpTriggerExtendedInfoCollection | true | Ativa ou desativa informações de pedidos de HTTP para gatilhos HTTP: cabeçalhos de correlação de pedidos de entrada, suporte de chaves de múltiplos instrumentações, método, caminho e resposta HTTP. |
| enableW3CDistributedTracing | true | Permite ou desativa o suporte do protocolo de rastreio distribuído W3C (e liga o esquema de correlação do legado). Ativado por padrão se `enableHttpTriggerExtendedInfoCollection` for verdade. Se `enableHttpTriggerExtendedInfoCollection` for falsa, esta bandeira aplica-se apenas aos pedidos de saída, não aos pedidos de entrada. |
| permitir a RespostaHeaderInjection | true | Permite ou desativa a injeção de cabeçalhos de correlação de vários componentes em respostas. Ativar a injeção permite que os Insights de Aplicação construam um Mapa de Aplicações até quando são utilizadas várias teclas de instrumentação. Ativado por padrão se `enableHttpTriggerExtendedInfoCollection` for verdade. Esta definição não se aplica se `enableHttpTriggerExtendedInfoCollection` for falsa. |

### <a name="applicationinsightssnapshotconfiguration"></a>applicationInsights.snapshotConfiguration

Para obter mais informações sobre instantâneos, consulte [instantâneos de Debug sobre exceções em aplicações .NET](../azure-monitor/app/snapshot-debugger.md) e [problemas de resolução de problemas que permitem insights de aplicação Snapshot Debugger ou visualização de instantâneos](../azure-monitor/app/snapshot-debugger-troubleshoot.md).

|Propriedade | Predefinição | Description |
| --------- | --------- | --------- | 
| agenteEndpoint | nulo | O ponto final utilizado para ligar ao serviço Debugger Debugger Do Application Insights Snapshot. Se for nulo, é utilizado um ponto final predefinido. |
| capturaSnapshotMemoryWeight | 0,5 | O peso dado ao tamanho atual da memória do processo ao verificar se há memória suficiente para tirar uma foto. O valor esperado é superior a 0 frações adequadas (0 < CaptureSnapshotMemoryWeight < 1). |
| failedRequestLimit | 3 | O limite do número de pedidos falhados para solicitar instantâneos antes do processador de telemetria ser desativado.|
| handleUntrackedExceptions | true | Permite ou desativa o rastreio de exceções que não são rastreadas pela telemetria Application Insights. |
| isEnabled | true | Permite ou desativa a recolha de instantâneos | 
| isEnabledInDeveloperMode | false | Ativa ou desativa a recolha de instantâneos está ativada no modo de desenvolvimento. |
| isEnabledWhenProfiling | true | Permite ou desativa a criação de instantâneos mesmo que o Application Insights Profiler esteja a recolher uma sessão de perfis detalhado. |
| isExceptionSnappointsEnabled | false | Permite ou desativa a filtragem de exceções. |
| isPrioritySnapshotUploader | true | Determina se deve executar o processo SnapshotUploader abaixo da prioridade normal. |
| máximoCollectionPlanSize | 50 | O número máximo de problemas que podemos rastrear a qualquer momento num intervalo de um a 9999. |
| máximasSnapshotsRequired | 3 | O número máximo de instantâneos recolhidos para um único problema, num intervalo de um a 999. Um problema pode ser considerado como uma declaração individual na sua aplicação. Uma vez que o número de instantâneos recolhidos para um problema atinge este valor, não serão recolhidos mais instantâneos para esse problema até que os contadores de problemas sejam reiniciados (ver `problemCounterResetInterval` ) e o limite seja `thresholdForSnapshotting` novamente atingido. |
| problemaCounterResetInterval | 24:00:00 | Com que frequência repõe os contadores de problemas num intervalo de um minuto a sete dias. Quando este intervalo é atingido, todas as contagens de problemas são repostas a zero. Problemas existentes que já atingiram o limiar para fazer snapshots, mas ainda não geraram o número de instantâneos em `maximumSnapshotsRequired` , permanecem ativos. |
| provideAnonymousTelemetry | true | Determina se envia telemetria de utilização anónima e erro para a Microsoft. Esta telemetria pode ser utilizada se contactar a Microsoft para ajudar a resolver problemas com o Debugger Snapshot. Também é usado para monitorizar padrões de utilização. |
| religarInterenteInterval | 00:15:00 | Quantas vezes nos reconectamos ao ponto final do Snapshot Debugger. O alcance permitido é de um minuto para um dia. |
| ShadowCopyFolder | nulo | Especifica a pasta a utilizar para binários de cópia de sombras. Caso não esteja definida, as pastas especificadas pelas seguintes variáveis ambientais são experimentadas por ordem: Fabric_Folder_App_Temp, LOCALAPPDATA, APPDATA, TEMP. |
| shareUploaderProcess | true | Se for verdade, apenas uma instância do SnapshotUploader irá recolher e carregar instantâneos para várias aplicações que partilham o InstrumentationKey. Se for definido como falso, o SnapshotUploader será único para cada tuple (ProcessName, InstrumentationKey). |
| snapshotInLowPriorityThread | true | Determina se processa ou não instantâneos num fio de prioridade de IO baixo. Criar um instantâneo é uma operação rápida mas, para enviar uma imagem para o serviço Snapshot Debugger, deve primeiro ser escrita para o disco como uma minidump. Isso acontece no processo SnapshotUploader. Definir este valor para o verdadeiro usa IO de baixa prioridade para escrever a minidump, que não competirá com a sua aplicação de recursos. Definir este valor para falsas velocidades até a criação de minidump em detrimento de abrandar a sua aplicação. |
| snapshotsPerDayLimit | 30 | O número máximo de instantâneos permitidos num dia (24 horas). Este limite também é aplicado no lado do serviço Application Insights. Os uploads são limitados a 50 por dia por aplicação (isto é, por chave de instrumentação). Este valor ajuda a evitar a criação de instantâneos adicionais que serão eventualmente rejeitados durante o upload. Um valor de zero remove completamente o limite, o que não é recomendado. |
| snapshotsPerTenMinutesLimit | 1 | O número máximo de instantâneos permitidos em 10 minutos. Embora não exista um limite superior sobre este valor, tenha cuidado ao aussê-lo nas cargas de trabalho de produção porque pode afetar o desempenho da sua aplicação. Criar um instantâneo é rápido, mas criar uma minidump do instantâneo e carregá-lo para o serviço Snapshot Debugger é uma operação muito mais lenta que irá competir com a sua aplicação de recursos (CPU e I/O). |
| tempFolder | nulo | Especifica a pasta para escrever minidumps e ficheiros de registo de uploader. Se não estiver definido, então é utilizado *um depósito de %TEMP\\Despejos.* |
| limiarForSnapshotting | 1 | Quantas vezes a Application Insights precisa de ver uma exceção antes de pedir instantâneos. |
| uploaderProxy | nulo | Substitui o servidor proxy utilizado no processo Snapshot Uploader. Poderá ter de utilizar esta definição se a sua aplicação se ligar à internet através de um servidor proxy. O Snapshot Collector funciona dentro do processo da sua aplicação e utilizará as mesmas definições de procuração. No entanto, o Uploader Snapshot funciona como um processo separado e poderá ser necessário configurar manualmente o servidor proxy. Se este valor for nulo, então o Snapshot Collector tentará automatizar o endereço do proxy examinando System.Net.WebRequest.DefaultWebProxy e transmitindo o valor ao Uploader Instantâneo. Se este valor não for nulo, então a autodetecção não é utilizada e o servidor proxy especificado aqui será utilizado no Uploader Snapshot. |

## <a name="blobs"></a>bolhas

As definições de configuração podem ser encontradas nos [gatilhos e encadernações](functions-bindings-storage-blob.md#hostjson-settings)de bolhas de armazenamento .  

## <a name="cosmosdb"></a>cosmosDb

A configuração pode ser encontrada nos [gatilhos e encadernações do Cosmos DB](functions-bindings-cosmosdb-v2-output.md#host-json).

## <a name="customhandler"></a>personalizadoHandler

Configurações de configuração para um manipulador personalizado. Para obter mais informações, consulte [os manipuladores personalizados Azure Functions](functions-custom-handlers.md#configuration).

```json
"customHandler": {
  "description": {
    "defaultExecutablePath": "server",
    "workingDirectory": "handler",
    "arguments": [ "--port", "%FUNCTIONS_CUSTOMHANDLER_PORT%" ]
  },
  "enableForwardingHttpRequest": false
}
```

|Propriedade | Predefinição | Description |
| --------- | --------- | --------- |
| defaultExecutablePath | n/a | O executável para começar como o processo de manipulador personalizado. É uma definição necessária quando se utilizam manipuladores personalizados e o seu valor é relativo à raiz da aplicação de função. |
| workingDirectory | *raiz de aplicativo de função* | O diretório de trabalho no qual iniciará o processo de manipulador personalizado. É uma definição opcional e o seu valor é relativo à raiz da aplicação de função. |
| argumentos | n/a | Uma série de argumentos de linha de comando para passar para o processo de manipulador personalizado. |
| enableForwardingHttpRequest | false | Se definido, todas as funções que consistem apenas num gatilho HTTP e saída HTTP são reencaminhadas o pedido HTTP original em vez do pedido de pedido de [encomenda](functions-custom-handlers.md#request-payload)personalizado . |

## <a name="durabletask"></a>DurableTask

A definição de configuração pode ser encontrada em [encadernações para funções duradouras](durable/durable-functions-bindings.md#host-json).

## <a name="eventhub"></a>eventHub

As definições de configuração podem ser encontradas nos [gatilhos e encadernações do Event Hub](functions-bindings-event-hubs.md#host-json). 

## <a name="extensions"></a>extensões

Propriedade que devolve um objeto que contém todas as definições específicas de encadernação, tais como [http](#http) e [eventHub](#eventhub).

## <a name="extensionbundle"></a>extensãoBundle 

Os pacotes de extensão permitem adicionar um conjunto compatível de extensões de ligação de funções à sua aplicação de função. Para saber mais, consulte [pacotes de extensão para o desenvolvimento local.](functions-bindings-register.md#extension-bundles)

[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

## <a name="functions"></a>funções

Uma lista de funções que o anfitrião de trabalho dirige. Uma matriz vazia significa executar todas as funções. Destina-se a ser utilizado apenas quando [correr localmente.](functions-run-local.md) Em aplicações de função em Azure, deve em vez disso seguir os passos em [Como desativar funções em Funções Azure](disable-function.md) para desativar funções específicas em vez de utilizar esta definição.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>funçãoTimeout

Indica a duração do tempo limite para todas as funções. Segue o formato de cadeia de tempo. 

| Tipo de plano | Padrão (min) | Máximo (min) |
| -- | -- | -- |
| Consumo | 5 | 10 |
| Premium<sup>1</sup> | 30 | -1 (sem limites)<sup>2</sup> |
| Dedicado (Serviço de Aplicações) | 30 | -1 (sem limites)<sup>2</sup> |

<sup>1</sup> A execução do plano premium só é garantida por 60 minutos, mas tecnicamente sem limites.   
<sup>2</sup> Recomenda-se um valor de `-1` execução sem limites, mas recomenda-se manter um limite superior fixo.

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

As definições de configuração podem ser encontradas em [gatilhos e encadernações http](functions-bindings-http-webhook-output.md#hostjson-settings).

## <a name="logging"></a>registos

Controla os comportamentos de registo da aplicação de função, incluindo o Application Insights.

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

|Propriedade  |Predefinição | Description |
|---------|---------|---------|
|fileLoggingMode|depuraronamente|Define o nível de registo de ficheiros ativado.  As opções `never` `always` são, `debugOnly` . . |
|logLevel|n/a|Objeto que define a filtragem da categoria de registo para funções na aplicação. Esta definição permite filtrar o início de sessão para funções específicas. Para obter mais informações, consulte [os níveis de registo de configuração](configure-monitoring.md#configure-log-levels). |
|consola|n/a| A definição de registo da [consola.](#console) |
|aplicaçõesInsights|n/a| A [definição de aplicaçõesInsights.](#applicationinsights) |

## <a name="console"></a>consola

Esta definição é uma criança de [registo.](#logging) Controla o registo da consola quando não está em modo de depuramento.

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

|Propriedade  |Predefinição | Description |
|---------|---------|---------| 
|isEnabled|false|Ativa ou desativa a sessão de registo de consolas.| 

## <a name="manageddependency"></a>geridoDependência

A dependência gerida é uma funcionalidade que atualmente só é suportada com funções baseadas em PowerShell. Permite que as dependências sejam geridas automaticamente pelo serviço. Quando a `enabled` propriedade está definida `true` para, o ficheiro é `requirements.psd1` processado. As dependências são atualizadas quando quaisquer versões menores são lançadas. Para obter mais informações, consulte [Dependência Gerida](functions-reference-powershell.md#dependency-management) no artigo PowerShell.

```json
{
    "managedDependency": {
        "enabled": true
    }
}
```

## <a name="queues"></a>filas

As definições de configuração podem ser encontradas nos [gatilhos e encadernações da fila de armazenamento](functions-bindings-storage-queue.md#host-json).  

## <a name="retry"></a>retry

Controla as opções [de política de re-tentar](./functions-bindings-error-pages.md#retry-policies-preview) para todas as execuções na app.

```json
{
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 2,
        "delayInterval": "00:00:03"  
    }
}
```

|Propriedade  |Predefinição | Description |
|---------|---------|---------| 
|estratégia|nulo|Obrigatório. A estratégia de repetição a utilizar. Valores válidos são `fixedDelay` ou `exponentialBackoff` .|
|maxRetryCount|nulo|Obrigatório. O número máximo de retrós assim que é permitido por execução de função. `-1` significa voltar a tentar indefinidamente.|
|atrasoInterval|nulo|O atraso que é usado entre as retróssias com uma `fixedDelay` estratégia.|
|mínimoInterval|nulo|O mínimo de atraso na utilização da `exponentialBackoff` estratégia.|
|máximoInterval|nulo|O máximo de atraso na utilização da `exponentialBackoff` estratégia.| 

## <a name="sendgrid"></a>sendGrid

A definição de configuração pode ser encontrada nos [gatilhos e encadernações SendGrid](functions-bindings-sendgrid.md#host-json).

## <a name="servicebus"></a>serviceBus

A definição de configuração pode ser encontrada nos [gatilhos e encadernações do Service Bus](functions-bindings-service-bus-output.md#host-json).

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

## <a name="version"></a>versão

Este valor indica a versão de esquema de host.js. A cadeia de versão `"version": "2.0"` é necessária para uma aplicação de função que direcione o tempo de execução v2, ou uma versão posterior. Não há host.jssobre as alterações de esquema entre v2 e v3.

## <a name="watchdirectories"></a>verdireias

Um conjunto de [diretórios](functions-reference-csharp.md#watched-directories) de código partilhados que devem ser monitorizados para alterações.  Garante que quando o código nestes diretórios é alterado, as alterações são captadas pelas suas funções.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="watchfiles"></a>watchFiles

Um conjunto de um ou mais nomes de ficheiros que são monitorizados para alterações que exigem que a sua aplicação reinicie.  Isto garante que quando o código nestes ficheiros é alterado, as atualizações são captadas pelas suas funções.

```json
{
    "watchFiles": [ "myFile.txt" ]
}
```

## <a name="override-hostjson-values"></a>Sobrepor host.jssobre valores

Pode haver casos em que deseja configurar ou modificar configurações específicas num host.jsem ficheiro para um ambiente específico, sem alterar a host.jsno próprio ficheiro.  Pode anular host.jsespecíficas sobre valores estar a criar um valor equivalente como configuração de aplicação. Quando o tempo de execução encontra uma definição de aplicação no `AzureFunctionsJobHost__path__to__setting` formato, substitui o equivalente host.jsna definição localizada `path.to.setting` no JSON. Quando expresso como uma definição de aplicação, o ponto ( `.` ) utilizado para indicar a hierarquia JSON é substituído por um duplo sublinhado ( `__` ). 

Por exemplo, diga que queria desativar a amostragem de Insight de Aplicação quando executasse localmente. Se alterou a host.jslocal no ficheiro para desativar o Application Insights, esta alteração poderá ser empurrada para a sua aplicação de produção durante a implementação. A forma mais segura de o fazer é criar uma definição de aplicação como `"AzureFunctionsJobHost__logging__applicationInsights__samplingSettings__isEnabled":"false"` no `local.settings.json` ficheiro. Pode ver isto no seguinte `local.settings.json` ficheiro, que não é publicado:

```json
{
    "IsEncrypted": false,
    "Values": {
        "AzureWebJobsStorage": "{storage-account-connection-string}",
        "FUNCTIONS_WORKER_RUNTIME": "{language-runtime}",
        "AzureFunctionsJobHost__logging__applicationInsights__samplingSettings__isEnabled":"false"
    }
}
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba como atualizar o host.jsno ficheiro](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [Ver configurações globais em variáveis ambientais](functions-app-settings.md)
