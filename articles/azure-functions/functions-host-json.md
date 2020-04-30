---
title: referência host.json para funções Azure 2.x
description: Documentação de referência para o ficheiro host.json funções azure com o tempo de funcionamento v2.
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 7967cdc7f5f7cbb92c12de15d31471fda8aa6569
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81758845"
---
# <a name="hostjson-reference-for-azure-functions-2x-and-later"></a>referência host.json para funções Azure 2.x e mais tarde 

> [!div class="op_single_selector" title1="Selecione a versão do tempo de funcionamento das Funções Azure que está a utilizar: "]
> * [Versão 1](functions-host-json-v1.md)
> * [Versão 2+](functions-host-json.md)

O ficheiro de metadados *host.json* contém opções de configuração global que afetam todas as funções para uma aplicação de função. Este artigo lista as definições disponíveis a partir da versão 2.x do tempo de funcionamento das Funções Azure.  

> [!NOTE]
> Este artigo é para funções Azure 2.x e versões posteriores.  Para uma referência do host.json nas funções 1.x, consulte a [referência host.json para as funções Azure 1.x](functions-host-json-v1.md).

Outras opções de configuração de aplicações de função são geridas nas definições da sua [aplicação](functions-app-settings.md) (para aplicações implementadas) ou no ficheiro [local.settings.json](functions-run-local.md#local-settings-file) (para desenvolvimento local).

As configurações em host.json relacionadas com encadernações são aplicadas igualmente a cada função na aplicação de função. 

## <a name="sample-hostjson-file"></a>Arquivo host.json da amostra

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

As seguintes secções deste artigo explicam cada propriedade de alto nível. Todos são opcionais, salvo indicação em contrário.

## <a name="aggregator"></a>agregador

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>aplicaçãoInsights

Esta definição é uma criança de [exploração madeireira.](#logging)

Controla as opções de Insights de Aplicação, incluindo [opções de amostragem.](./functions-monitoring.md#configure-sampling)

Para obter a estrutura json completa, consulte o [ficheiro host.json](#sample-hostjson-file)exemplo anterior .

> [!NOTE]
> A amostragem de registo pode fazer com que algumas execuções não apareçam na lâmina do monitor Deinsights de Aplicação. Para evitar a `excludedTypes: "Request"` amostragem `samplingSettings` de registo, adicione ao valor.

| Propriedade | Predefinição | Descrição |
| --------- | --------- | --------- | 
| amostragemDefinições | n/d | Consulte [applicationInsights.samplingSettings](#applicationinsightssamplingsettings). |
| enableLiveMetrics | true | Permite a recolha de métricas ao vivo. |
| enableDependencyTracking | true | Permite o rastreio da dependência. |
| enablePerformanceCountersCollection | true | Permite a coleção de contadores de desempenho kudu. |
| liveMetricsInicializaçãoDelay | 00:00:15 | Apenas para utilização interna. |
| httpAutoCollectionOptions | n/d | Consulte [applicationInsights.httpAutoCollectionOptions](#applicationinsightshttpautocollectionoptions). |
| configuração instantânea | n/d | Consulte [applicationInsights.snapshotConfiguration](#applicationinsightssnapshotconfiguration). |

### <a name="applicationinsightssamplingsettings"></a>aplicaInsights.samplingSettings

|Propriedade | Predefinição | Descrição |
| --------- | --------- | --------- | 
| isEnabled | true | Permite ou desativa a amostragem. | 
| maxTelemettryItemsPerSecond | 20 | O número de alvos de artigos de telemetria registados por segundo em cada anfitrião do servidor. Se a sua aplicação for executado em muitos anfitriões, reduza este valor para permanecer dentro da sua taxa de tráfego geral. | 
| avaliaçãoInterval | 01:00:00 | O intervalo em que a taxa atual de telemetria é reavaliada. A avaliação é feita como uma média móvel. Talvez queira encurtar este intervalo se a sua telemetria for suscetível de explosões repentinas. |
| inicialSamplingPercentage| 1.0 | A percentagem inicial de amostragem aplicada no início do processo de amostragem para variar dinamicamente a percentagem. Não reduza o valor enquanto está a depurar. |
| amostragemPercentualIncreaseTimeout | 00:00:01 | Quando o valor percentual de amostragem muda, este imóvel determina quanto tempo depois os Insights de Aplicação são autorizados a aumentar a percentagem de amostragem novamente para capturar mais dados. |
| amostragemPercentageDiminuiTimeout | 00:00:01 | Quando o valor percentual de amostragem muda, esta propriedade determina quanto tempo depois os Insights de Aplicação são autorizados a reduzir novamente a percentagem de amostragem para capturar menos dados. |
| minSamplingPercentage | 0.1 | Como a percentagem de amostragem varia, este imóvel determina a percentagem mínima de amostragem permitida. |
| maxSamplingPercentage | 0.1 | Como a percentagem de amostragem varia, este imóvel determina a percentagem máxima permitida de amostragem. |
| movingAverageRatio | 1.0 | No cálculo da média móvel, o peso atribuído ao valor mais recente. Use um valor igual ou inferior a 1. Valores menores tornam o algoritmo menos reativo a mudanças repentinas. |
| excluídosTipos | nulo | Uma lista de tipos deslimitadas do cólon que não queres ser amostradas. Os tipos `Dependency`reconhecidos `PageView`são: `Trace`, `Event` `Exception`, e `Request`. Todas as instâncias dos tipos especificados são transmitidas; os tipos que não estão especificados são amostrados. |
| incluídoTipos | nulo | Uma lista de tipos deslimitada seletiva que pretende ser amostrada; uma lista vazia implica todos os tipos. Tipo listado `excludedTypes` em tipos de substituição listados aqui. Os tipos `Dependency`reconhecidos `PageView`são: `Trace`, `Event` `Exception`, e `Request`. Os casos dos tipos especificados são amostrados; os tipos que não estão especificados ou implícitos são transmitidos sem amostragem. |

### <a name="applicationinsightshttpautocollectionoptions"></a>aplicaçõesInsights.httpAutoCollectionOptions

|Propriedade | Predefinição | Descrição |
| --------- | --------- | --------- | 
| enableHttpTriggerExtendedInfoCollection | true | Permite ou desativa informações estendidas sobre pedidos http para os gatilhos HTTP: cabeçalhos de correlação de pedido de entrada, suporte de chaves multi-instrumentação, método HTTP, caminho e resposta. |
| enableW3CDistributedTracing | true | Permite ou desativa o suporte do protocolo de rastreio distribuído w3C (e liga o esquema de correlação do legado). Ativado por `enableHttpTriggerExtendedInfoCollection` padrão se for verdade. Se `enableHttpTriggerExtendedInfoCollection` for falso, esta bandeira aplica-se apenas aos pedidos de saída, e não aos pedidos de entrada. |
| enableResponseHeaderInjection | true | Permite ou desativa a injeção de cabeçalhos de correlação multicomponentes em respostas. Permitir a injeção permite que os Insights de Aplicação construam um Mapa de Aplicação para quando várias teclas de instrumentação são usadas. Ativado por `enableHttpTriggerExtendedInfoCollection` padrão se for verdade. Esta definição não `enableHttpTriggerExtendedInfoCollection` se aplica se for falsa. |

### <a name="applicationinsightssnapshotconfiguration"></a>aplicaInsights.snapshotConfiguration

Para obter mais informações sobre instantâneos, consulte [as imagens do Debug sobre exceções em aplicações .NET](/azure/azure-monitor/app/snapshot-debugger) e [problemas de Troubleshoot que permitem a aplicação Insights Snapshot Debugger ou visualização de instantâneos](/azure/azure-monitor/app/snapshot-debugger-troubleshoot).

|Propriedade | Predefinição | Descrição |
| --------- | --------- | --------- | 
| agenteEndpoint | nulo | O ponto final usado para ligar ao serviço Application Insights Snapshot Debugger. Se nulidade, é utilizado um ponto final predefinido. |
| capturarSnapshotMemoryWeight | 0,5 | O peso dado ao tamanho da memória do processo atual ao verificar se há memória suficiente para tirar uma foto. O valor esperado é de uma fração superior a 0 (0 < CaptureSnapshotMemoryWeight < 1). |
| falhadoRequestLimit | 3 | O limite do número de pedidos falhados para solicitar instantâneos antes de o processador de telemetria ser desativado.|
| handleUntrackedExceps | true | Permite ou desativa o rastreio de exceções que não são rastreadas pela telemetria Application Insights. |
| isEnabled | true | Permite ou desativa a recolha de instantâneos | 
| isEnabledInDeveloperMode | false | Ativa ou desativa a recolha de instantâneos no modo de desenvolvimento. |
| isEnabledWhenProfiling | true | Permite ou desativa a criação de instantâneos mesmo que o Profiler de Insights de Aplicação esteja a recolher uma sessão de perfis detalhadas. |
| isExceptionSnappointsEnabled | false | Permite ou desativa a filtragem de exceções. |
| isLowPrioritySnapshotUploader | true | Determina se deve executar o processo SnapshotUploader abaixo da prioridade normal. |
| maximumCollectionPlanSize | 50 | O número máximo de problemas que podemos rastrear a qualquer momento num intervalo de um a 9999. |
| máximoSnapshotsNecessários | 3 | O número máximo de instantâneos recolhidos para um único problema, num intervalo de um a 999. Um problema pode ser considerado como uma declaração individual de lançamento na sua aplicação. Uma vez que o número de instantâneos recolhidos para um problema atinge este valor, não serão recolhidos mais instantâneos para esse problema até que os contadores de problemas sejam reiniciados (ver) `problemCounterResetInterval`e o `thresholdForSnapshotting` limite seja novamente atingido. |
| problemaCounterResetInterval | 24:00:00 | Com que frequência repor os contadores de problemas num intervalo de um minuto a sete dias. Quando este intervalo é atingido, todas as contagens de problemas são redefinidas para zero. Os problemas existentes que já atingiram o limiar para fazer instantâneos, mas `maximumSnapshotsRequired`que ainda não geraram o número de instantâneos, permanecem ativos. |
| fornecerTelemetria Anonymous | true | Determina se deve enviar telemetria de uso anónimo e erro para a Microsoft. Esta telemetria pode ser usada se contactar a Microsoft para ajudar a resolver problemas com o Snapshot Debugger. Também é usado para monitorizar os padrões de utilização. |
| reconectarIntervalado | 00:15:00 | Quantas vezes nos reconectamos ao ponto final do Debugger Instantâneo. O alcance permitido é de um minuto a um dia. |
| shadowCopyFolder | nulo | Especifica a pasta a utilizar para copiar binários de cópia de sombras. Se não for definido, as pastas especificadas pelas seguintes variáveis ambientais são experimentadas por ordem: Fabric_Folder_App_Temp, LOCALAPPDATA, APPDATA, TEMP. |
| shareUploaderProcess | true | Se for verdade, apenas uma instância do SnapshotUploader irá recolher e carregar fotografias para várias aplicações que partilham a InstrumentationKey. Se for definido como falso, o SnapshotUploader será único para cada tuple (ProcessName, InstrumentationKey). |
| snapshotInLowPriorityThread | true | Determina se processa ou não instantâneos num fio prioritário de IO baixo. Criar um instantâneo é uma operação rápida, mas, para fazer o upload de uma foto para o serviço Snapshot Debugger, deve primeiro ser escrito em disco como uma mini-lixeira. Isso acontece no processo SnapshotUploader. Definir este valor como verdadeiro usa IO de baixa prioridade para escrever o minidump, que não vai competir com a sua aplicação de recursos. Definir este valor para falsas velocidades de criação de minidump em detrimento de abrandar a sua aplicação. |
| snapshotsPerDayLimit | 30 | O número máximo de instantâneos permitidos num dia (24 horas). Este limite também é aplicado no lado do serviço Application Insights. Os uploads são limitados a 50 por dia por aplicação (isto é, por chave de instrumentação). Este valor ajuda a evitar a criação de instantâneos adicionais que eventualmente serão rejeitados durante o upload. Um valor de zero remove completamente o limite, o que não é recomendado. |
| snapshotsPerTenMinutesLimit | 1 | O número máximo de instantâneos permitidos em 10 minutos. Embora não exista um limite superior a este valor, tenha cuidado ao aumtá-lo nas cargas de trabalho de produção, pois pode afetar o desempenho da sua aplicação. Criar um instantâneo é rápido, mas criar uma mini-dump do instantâneo e carregá-lo para o serviço Snapshot Debugger é uma operação muito mais lenta que competirá com a sua aplicação de recursos (cpU e I/O). |
| tempFolder | nulo | Especifica a pasta para escrever minidumps e ficheiros de registo de uploader. Se não estiver definido, então *são utilizados depósitos de despejos %TEMP%\.\.* |
| limiarForSnapshotting | 1 | Quantas vezes a Application Insights precisa de ver uma exceção antes de pedir instantâneos. |
| uploaderProxy | nulo | Substitui o servidor proxy utilizado no processo de uploader snapshot. Pode ser necessário utilizar esta definição se a sua aplicação se ligar à internet através de um servidor proxy. O Snapshot Collector funciona dentro do processo da sua aplicação e utilizará as mesmas definições de procuração. No entanto, o Uploader Snapshot funciona como um processo separado e poderá ser necessário configurar manualmente o servidor proxy. Se este valor for nulo, então o Snapshot Collector tentará detetar automaticamente o endereço do proxy examinando System.Net.WebRequest.DefaultWebProxy e transmitindo o valor para o Uploader snapshot. Se este valor não for nulo, então a deteção automática não é usada e o servidor proxy aqui especificado será utilizado no Uploader Snapshot. |

## <a name="cosmosdb"></a>cosmosDb

A configuração de configuração pode ser encontrada em [gatilhos e encadernações Cosmos DB](functions-bindings-cosmosdb-v2-output.md#host-json).

## <a name="durabletask"></a>tarefa durável

A definição de configuração pode ser encontrada em [encadernações para funções duráveis](durable/durable-functions-bindings.md#host-json).

## <a name="eventhub"></a>eventoHub

As definições de configuração podem ser encontradas em [gatilhos e encadernações do Event Hub](functions-bindings-event-hubs-trigger.md#host-json). 

## <a name="extensions"></a>extensões

Propriedade que devolve um objeto que contém todas as configurações específicas de ligação, tais como [http](#http) e [eventHub](#eventhub).

## <a name="extensionbundle"></a>extensãoBundle 

Os pacotes de extensão permitem-lhe adicionar um conjunto compatível de extensões de ligação de funções à sua aplicação de função. Para saber mais, consulte [pacotes de extensão para o desenvolvimento local.](functions-bindings-register.md#extension-bundles)

[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

## <a name="functions"></a>funções

Uma lista de funções que o anfitrião do emprego dirige. Uma matriz vazia significa executar todas as funções. Destinado a ser utilizado apenas quando [se funciona localmente](functions-run-local.md). Nas aplicações de função em Azure, deve seguir os passos em [Como desativar funções em Funções Azure](disable-function.md) para desativar funções específicas em vez de utilizar esta definição.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>funçãoTimeout

Indica a duração do tempo de paragem para todas as funções. Segue o formato de corda timepan. Num plano de consumo sem servidor, o intervalo válido é de 1 segundo a 10 minutos, e o valor predefinido é de 5 minutos.  

No plano Premium, o intervalo válido é de 1 segundo a 60 minutos, e o valor predefinido é de 30 minutos.

Num plano dedicado (App Service), não existe um limite global, e o valor padrão é de 30 minutos. Recomenda-se `-1` um valor de execução não limitada, mas manter um limite superior fixo é recomendado.

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

As definições de configuração podem ser encontradas em [gatilhos e encadernações de http](functions-bindings-http-webhook-output.md#hostjson-settings).

## <a name="logging"></a>registo

Controla os comportamentos de registo da aplicação de função, incluindo os Insights da Aplicação.

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
|fileLoggingMode|debugsó|Define o nível de registo de ficheiros ativado.  As `never`opções são, `always`. . `debugOnly` |
|logLevel|n/d|Objeto que define a categoria de log filtrando funções na aplicação. Versões 2.x e, posteriormente, siga o layout ASP.NET Core para filtragem da categoria de log. Esta definição permite filtrar o registo para funções específicas. Para mais informações, consulte a [filtragem](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering) de Registo na documentação do Núcleo de ASP.NET. |
|consola|n/d| A definição de registo da [consola.](#console) |
|aplicaçãoInsights|n/d| A definição [de applicationInsights.](#applicationinsights) |

## <a name="console"></a>consola

Esta definição é uma criança de [exploração madeireira.](#logging) Controla o registo da consola quando não está em modo de depuração.

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
|isEnabled|false|Permite ou desativa o registo da consola.| 

## <a name="manageddependency"></a>managedDependência

A dependência gerida é uma funcionalidade que atualmente é suportada apenas com funções baseadas na PowerShell. Permite que as dependências sejam geridas automaticamente pelo serviço. Quando `enabled` a propriedade `true`está `requirements.psd1` definida, o ficheiro é processado. As dependências são atualizadas quando quaisquer versões menores são lançadas. Para mais informações, consulte a [dependência gerida](functions-reference-powershell.md#dependency-management) no artigo powerShell.

```json
{
    "managedDependency": {
        "enabled": true
    }
}
```

## <a name="queues"></a>filas

As definições de configuração podem ser encontradas nos [gatilhos e encadernações](functions-bindings-storage-queue-output.md#host-json)da fila de armazenamento.  

## <a name="sendgrid"></a>sendGrid

A definição de configuração pode ser encontrada nos [gatilhos e encadernações sendGrid](functions-bindings-sendgrid.md#host-json).

## <a name="servicebus"></a>serviçoBus

A definição de configuração pode ser encontrada em [gatilhos e encadernações](functions-bindings-service-bus-output.md#host-json)do Ônibus de serviço .

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

## <a name="version"></a>versão

Este valor indica a versão schema do host.json. A cadeia `"version": "2.0"` de versão é necessária para uma aplicação de função que direciona o tempo de execução v2, ou uma versão posterior. Não há mudanças de esquema son.json entre v2 e v3.

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
