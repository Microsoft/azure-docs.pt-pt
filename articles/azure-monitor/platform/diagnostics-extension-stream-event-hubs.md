---
title: Dados de diagnóstico do Azure Stream para os Hubs de eventos
description: A configurar o diagnóstico do Azure com os Hubs de eventos ponto a ponto, incluindo documentação de orientação para cenários comuns.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 07/13/2017
ms.author: robb
ms.subservice: diagnostic-extension
ms.openlocfilehash: c5fc2199de8623dd3a9f2bc5faf23c7c40d67d75
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64922811"
---
# <a name="streaming-azure-diagnostics-data-in-the-hot-path-by-using-event-hubs"></a>Transmissão em fluxo de dados de diagnóstico do Azure no caminho de acesso frequente ao utilizar os Hubs de eventos
Diagnóstico do Azure fornece formas flexíveis para coletar métricas e registos provenientes de máquinas de virtuais de serviços cloud (VMs) e transferir os resultados para o armazenamento do Azure. A partir do intervalo de tempo de Março de 2016 (SDK 2.9), pode enviar diagnósticos para origens de dados personalizados e transferir dados de caminho mais utilizado em segundos, utilizando [os Hubs de eventos do Azure](https://azure.microsoft.com/services/event-hubs/).

Tipos de dados suportados incluem:

* Eventos de Rastreio de Eventos para o Windows (ETW)
* Contadores de desempenho
* Registos de eventos do Windows
* Registos de aplicações
* Registos da infraestrutura do Diagnóstico do Azure

Este artigo mostra-lhe como configurar o diagnóstico do Azure com os Hubs de eventos de ponta a ponta. Também são disponibilizadas orientações para os seguintes cenários comuns:

* Como personalizar os registos e métricas que sejam enviadas para os Hubs de eventos
* Como alterar configurações em cada ambiente
* Como ver dados de fluxo dos Hubs de eventos
* Como resolver problemas de ligação  

## <a name="prerequisites"></a>Pré-requisitos
Hubs de eventos receber dados de diagnóstico do Azure são suportados nos serviços Cloud, as VMs, conjuntos de dimensionamento de Máquina Virtual e o Service Fabric a partir do Azure SDK 2.9 e as ferramentas do Azure correspondente para o Visual Studio.

* Extensão de diagnóstico do Azure 1.6 ([Azure SDK para .NET 2.9 ou posterior](https://azure.microsoft.com/downloads/) destina-se isso por predefinição)
* [Visual Studio 2013 ou posterior](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* As configurações existentes do diagnóstico do Azure num aplicativo usando uma *.wadcfgx* ficheiro e um dos seguintes métodos:
  * Visual Studio: [Configuração de diagnósticos para serviços Cloud do Azure e máquinas virtuais](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines)
  * Windows PowerShell: [Ativar diagnósticos nos serviços de Cloud do Azure com o PowerShell](../../cloud-services/cloud-services-diagnostics-powershell.md)
* Espaço de nomes do Hubs de eventos aprovisionado por artigo, [introdução aos Hubs de eventos](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

## <a name="connect-azure-diagnostics-to-event-hubs-sink"></a>Ligar o diagnóstico do Azure para o sink de Hubs de eventos
Por predefinição, diagnóstico do Azure sempre envia registos e métricas para uma conta de armazenamento do Azure. Um aplicativo também pode enviar dados para os Hubs de eventos, adicionando um novo **coletores** secção sob a **PublicConfig** / **WadCfg** elemento do *. wadcfgx* ficheiro. No Visual Studio, o *.wadcfgx* arquivo é armazenado no seguinte caminho: **Projeto de serviço de nuvem** > **funções** >  **(RoleName)**  > **diagnostics.wadcfgx** ficheiro.

```xml
<SinksConfig>
  <Sink name="HotPath">
    <EventHub Url="https://diags-mycompany-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" />
  </Sink>
</SinksConfig>
```
```JSON
"SinksConfig": {
    "Sink": [
        {
            "name": "HotPath",
            "EventHub": {
                "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
                "SharedAccessKeyName": "SendRule"
            }
        }
    ]
}
```

Neste exemplo, o URL do hub de eventos é definido para o espaço de nomes totalmente qualificado do hub de eventos: Espaço de nomes de Hubs de eventos + "/" + nome do hub de eventos.  

O hub de eventos URL é apresentado no [portal do Azure](https://go.microsoft.com/fwlink/?LinkID=213885) no dashboard do Hubs de eventos.  

O **Sink** nome pode ser definido para qualquer cadeia de caracteres válida, desde que o mesmo valor é usado de maneira consistente em todo o ficheiro de configuração.

> [!NOTE]
> Pode haver sinks adicionais, como *Application Insights* configurado nesta secção. Diagnóstico do Azure permite Coletores de um ou mais ser definida se cada sink também for declarado em que o **PrivateConfig** secção.  
>
>

O coletor de Hubs de eventos também pode ser declarado e definido na **PrivateConfig** secção a *.wadcfgx* ficheiro de configuração.

```XML
<PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <StorageAccount name="{account name}" key="{account key}" endpoint="{optional storage endpoint}" />
  <EventHub Url="https://diags-mycompany-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />
</PrivateConfig>
```
```JSON
{
    "storageAccountName": "{account name}",
    "storageAccountKey": "{account key}",
    "storageAccountEndPoint": "{optional storage endpoint}",
    "EventHub": {
        "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "{base64 encoded key}"
    }
}
```

O `SharedAccessKeyName` tem de corresponder ao valor de uma chave de assinatura de acesso partilhado (SAS) e a política que foi definida no **Hubs de eventos** espaço de nomes. Navegue para o dashboard de Hubs de eventos no [portal do Azure](https://portal.azure.com), clique no **configurar** separador e configurar uma política com o nome (por exemplo, "SendRule") com *enviar* permissões. O **StorageAccount** também é declarado na **PrivateConfig**. Não é necessário para alterar os valores aqui se estão a trabalhar. Neste exemplo, vamos deixar os valores vazio, que é um sinal de que um recurso de downstream definirá os valores. Por exemplo, o *Serviceconfiguration* ficheiro de configuração do ambiente define os nomes apropriados de ambiente e as chaves.  

> [!WARNING]
> A chave de SAS de Hubs de eventos é armazenada em texto simples no *.wadcfgx* ficheiro. Muitas vezes, esta chave é verificada para controle de código de origem ou está disponível como um recurso no seu servidor de compilação, portanto, deve protegê-lo conforme adequado. Recomendamos que utilize uma chave SAS aqui com *enviar apenas* permissões para que um utilizador mal intencionado pode escrever para o hub de eventos, mas não escutar a ele ou geri-lo.
>
>

## <a name="configure-azure-diagnostics-to-send-logs-and-metrics-to-event-hubs"></a>Configurar os diagnósticos do Azure para enviar os registos e métricas para os Hubs de eventos
Como discutido anteriormente, todos os dados de diagnósticos personalizados e padrão, ou seja, métricas e registos, são enviados automaticamente para o armazenamento do Azure dos intervalos configurados. Com os Hubs de eventos e qualquer sink adicional, pode especificar qualquer nó folha ou de raiz da hierarquia sejam enviados para o hub de eventos. Isto inclui os eventos do ETW, contadores de desempenho, logs de eventos do Windows e registos de aplicações.   

É importante considerar o número de pontos de dados, na verdade, deve ser transferido para os Hubs de eventos. Normalmente, os desenvolvedores transferir dados de caminhos recorrentes de baixa latência que tem de ser consumidos e interpretados rapidamente. Sistemas que monitorizam os alertas ou regras de dimensionamento automático são exemplos. Um desenvolvedor também pode configurar um arquivo de análise alternativo ou procure na loja – por exemplo, Azure Stream Analytics, Elasticsearch, um sistema de monitorização personalizado ou um sistema de monitorização favorito de outros utilizadores.

Seguem-se algumas configurações de exemplo.

```xml
<PerformanceCounters scheduledTransferPeriod="PT1M" sinks="HotPath">
  <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
</PerformanceCounters>
```
```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "sinks": "HotPath",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        }
    ]
}
```

No exemplo acima, o coletor é aplicado ao pai **PerformanceCounters** nó da hierarquia, o que significa que todos os subordinados **PerformanceCounters** será enviado para os Hubs de eventos.  

```xml
<PerformanceCounters scheduledTransferPeriod="PT1M">
  <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" sinks="HotPath" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" sinks="HotPath"/>
  <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" sinks="HotPath"/>
</PerformanceCounters>
```
```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M",
            "sinks": "HotPath"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Rejected",
            "sampleRate": "PT3M",
            "sinks": "HotPath"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Queued",
            "sampleRate": "PT3M",
            "sinks": "HotPath"
        }
    ]
}
```

No exemplo anterior, o coletor é aplicado apenas três contadores: **Pedidos em fila**, **pedidos rejeitados**, e **% tempo do processador**.  

O exemplo seguinte mostra como um desenvolvedor pode limitar a quantidade de dados enviados para ser as métricas críticas que são utilizadas para o estado de funcionamento deste serviço.  

```XML
<Logs scheduledTransferPeriod="PT1M" sinks="HotPath" scheduledTransferLogLevelFilter="Error" />
```
```JSON
"Logs": {
    "scheduledTransferPeriod": "PT1M",
    "scheduledTransferLogLevelFilter": "Error",
    "sinks": "HotPath"
}
```

Neste exemplo, o coletor é aplicado aos registos e é filtrado apenas para o rastreio de nível de erro.

## <a name="deploy-and-update-a-cloud-services-application-and-diagnostics-config"></a>Implementar e atualizar uma configuração de aplicação e o diagnóstico de serviços Cloud
O Visual Studio fornece o caminho mais fácil de implementar a aplicação e a configuração de sink de Hubs de eventos. Para ver e editar o ficheiro, abra a *.wadcfgx* do ficheiro no Visual Studio, editá-lo e guardá-lo. O caminho é **projeto de serviço em nuvem** > **funções** >  **(RoleName)**  > **diagnostics.wadcfgx**.  

Neste momento, todos os implementação e implementação de atualização de ações no Visual Studio, Visual Studio Team System e todos os comandos ou scripts que são baseados nas MSBuild e utilize o **/t: publicar** destino incluem o *.wadcfgx* no processo de empacotamento. Além disso, Implantações e atualizações de implementar o ficheiro para o Azure com a extensão de agente de diagnóstico do Azure adequada nas suas VMs.

Depois de implementar a aplicação e a configuração de diagnósticos do Azure, irá ver atividade no dashboard do hub de eventos de imediato. Isto indica que está pronto para avançar para ver os dados de caminhos recorrentes na ferramenta de cliente ou análise de serviço de escuta da sua preferência.  

Na figura a seguir, o dashboard de Hubs de eventos mostra em bom estado de envio de dados de diagnóstico para o hub de eventos a partir de algum tempo após o 23 horas. Que é quando a aplicação foi implementada com um atualizada *.wadcfgx* ficheiro e o coletor foi configurado corretamente.

![][0]  

> [!NOTE]
> Ao fazer atualizações ao ficheiro de configuração de diagnósticos do Azure (.wadcfgx), recomenda-se que envia as atualizações para todo o aplicativo, bem como a configuração utilizando a publicação do Visual Studio, ou um script do Windows PowerShell.  
>
>

## <a name="view-hot-path-data"></a>Ver dados de caminhos recorrentes
Como discutido anteriormente, há muitos casos de utilização para ouvir e processar dados de Hubs de eventos.

Uma abordagem simples é criar uma aplicação de consola de teste pequeno para escutar o hub de eventos e imprimir o fluxo de saída. Pode colocar o código a seguir, que é explicado mais detalhadamente nas [introdução aos Hubs de eventos](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)), num aplicativo de console.  

Tenha em atenção que a aplicação de consola tem de incluir o [pacote NuGet de anfitrião do processador de eventos](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/).  

Não se esqueça de substituir os valores em ângulo Reto de fecho no **Main** função com os valores para os seus recursos.   

```csharp
//Console application code for EventHub test client
using System;
using System.Collections.Generic;
using System.Diagnostics;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using Microsoft.ServiceBus.Messaging;

namespace EventHubListener
{
    class SimpleEventProcessor : IEventProcessor
    {
        Stopwatch checkpointStopWatch;

        async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
            if (reason == CloseReason.Shutdown)
            {
                await context.CheckpointAsync();
            }
        }

        Task IEventProcessor.OpenAsync(PartitionContext context)
        {
            Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
            this.checkpointStopWatch = new Stopwatch();
            this.checkpointStopWatch.Start();
            return Task.FromResult<object>(null);
        }

        async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (EventData eventData in messages)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                    Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
                        context.Lease.PartitionId, data));

                foreach (var x in eventData.Properties)
                {
                    Console.WriteLine(string.Format("    {0} = {1}", x.Key, x.Value));
                }
            }

            //Call checkpoint every 5 minutes, so that worker can resume processing from 5 minutes back if it restarts.
            if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
            {
                await context.CheckpointAsync();
                this.checkpointStopWatch.Restart();
            }
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            string eventHubConnectionString = "Endpoint= <your connection string>";
            string eventHubName = "<Event hub name>";
            string storageAccountName = "<Storage account name>";
            string storageAccountKey = "<Storage account key>";
            string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);

            string eventProcessorHostName = Guid.NewGuid().ToString();
            EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
            Console.WriteLine("Registering EventProcessor...");
            var options = new EventProcessorOptions();
            options.ExceptionReceived += (sender, e) => { Console.WriteLine(e.Exception); };
            eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>(options).Wait();

            Console.WriteLine("Receiving. Press enter key to stop worker.");
            Console.ReadLine();
            eventProcessorHost.UnregisterEventProcessorAsync().Wait();
        }
    }
}
```

## <a name="troubleshoot-event-hubs-sinks"></a>Resolver problemas de Coletores de Hubs de eventos
* O hub de eventos não mostra a atividade de evento de entrada ou de saída, conforme o esperado.

    Verifique que o seu hub de eventos é aprovisionado com êxito. Todas as informações de ligação no **PrivateConfig** secção *.wadcfgx* tem de coincidir com os valores do seu recurso como visto no portal. Certifique-se de que tem uma política SAS definida ("SendRule" no exemplo) no portal e que *enviar* permissão é concedida.  
* Após uma atualização, o hub de eventos já não mostra a atividade de eventos de entrada ou de saída.

    Em primeiro lugar, certifique-se de que as informações de configuração e de hub de eventos estão correta conforme explicado anteriormente. Por vezes, o **PrivateConfig** é reposta numa atualização de implementação. A correção recomendada é fazer todas as alterações à *.wadcfgx* no projeto e, em seguida, uma atualização de aplicação completa de push. Se não for possível, certifique-se de que a atualização de diagnóstico envia por push uma completa **PrivateConfig** que inclui a chave SAS.  
* Tentei as sugestões e o hub de eventos ainda não está a funcionar.

    Experimente a aparência da tabela de armazenamento do Azure que contém erros e registos de diagnóstico do Azure em si: **WADDiagnosticInfrastructureLogsTable**. Uma opção é usar uma ferramenta como [Explorador de armazenamento do Azure](https://www.storageexplorer.com) para ligar a esta conta de armazenamento, ver esta tabela e adicionar uma consulta para TimeStamp nas últimas 24 horas. Pode utilizar a ferramenta para exportar um ficheiro. csv e abri-lo num aplicativo como o Microsoft Excel. Excel torna mais fácil de pesquisar cadeias de caracteres de cartão de chamar, como **EventHubs**, para ver o erro é comunicado.  

## <a name="next-steps"></a>Passos Seguintes
• [Saiba mais sobre os Hubs de eventos](https://azure.microsoft.com/services/event-hubs/)

## <a name="appendix-complete-azure-diagnostics-configuration-file-wadcfgx-example"></a>Apêndice: Concluir o exemplo de ficheiro (.wadcfgx) de configuração de diagnósticos do Azure
```xml
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="applicationInsights.errors">
        <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" />
        <Directories scheduledTransferPeriod="PT1M">
          <IISLogs containerName="wad-iis-logfiles" />
          <FailedRequestLogs containerName="wad-failedrequestlogs" />
        </Directories>
        <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="HotPath">
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
        </PerformanceCounters>
        <WindowsEventLog scheduledTransferPeriod="PT1M">
          <DataSource name="Application!*" />
        </WindowsEventLog>
        <CrashDumps>
          <CrashDumpConfiguration processName="WaIISHost.exe" />
          <CrashDumpConfiguration processName="WaWorkerHost.exe" />
          <CrashDumpConfiguration processName="w3wp.exe" />
        </CrashDumps>
        <Logs scheduledTransferPeriod="PT1M" sinks="HotPath" scheduledTransferLogLevelFilter="Error" />
      </DiagnosticMonitorConfiguration>
      <SinksConfig>
        <Sink name="HotPath">
          <EventHub Url="https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py" SharedAccessKeyName="SendRule" />
        </Sink>
        <Sink name="applicationInsights">
          <ApplicationInsights />
          <Channels>
            <Channel logLevel="Error" name="errors" />
          </Channels>
        </Sink>
      </SinksConfig>
    </WadCfg>
    <StorageAccount>ACCOUNT_NAME</StorageAccount>
  </PublicConfig>
  <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <StorageAccount name="{account name}" key="{account key}" endpoint="{storage endpoint}" />
    <EventHub Url="https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py" SharedAccessKeyName="SendRule" SharedAccessKey="YOUR_KEY_HERE" />
  </PrivateConfig>
  <IsEnabled>true</IsEnabled>
</DiagnosticsConfiguration>
```

O complementares *Serviceconfiguration* para este exemplo é semelhante ao seguinte.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceConfiguration serviceName="MyFixItCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="3" osVersion="*" schemaVersion="2015-04.2.6">
  <Role name="MyFixIt.WorkerRole">
    <Instances count="1" />
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="YOUR_CONNECTION_STRING" />
    </ConfigurationSettings>
  </Role>
</ServiceConfiguration>
```

Definições de JSON equivalentes para máquinas virtuais é o seguinte:

Definições de públicas:
```JSON
{
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 4096,
            "sinks": "applicationInsights.errors",
            "DiagnosticInfrastructureLogs": {
                "scheduledTransferLogLevelFilter": "Error"
            },
            "Directories": {
                "scheduledTransferPeriod": "PT1M",
                "IISLogs": {
                    "containerName": "wad-iis-logfiles"
                },
                "FailedRequestLogs": {
                    "containerName": "wad-failedrequestlogs"
                }
            },
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "sinks": "HotPath",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Memory\\Available MBytes",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\Web Service(_Total)\\Bytes Total/Sec",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\ASP.NET Applications(__Total__)\\Requests/Sec",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\ASP.NET Applications(__Total__)\\Errors Total/Sec",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\ASP.NET\\Requests Queued",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\ASP.NET\\Requests Rejected",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                        "sampleRate": "PT3M"
                    }
                ]
            },
            "WindowsEventLog": {
                "scheduledTransferPeriod": "PT1M",
                "DataSource": [
                    {
                        "name": "Application!*"
                    }
                ]
            },
            "Logs": {
                "scheduledTransferPeriod": "PT1M",
                "scheduledTransferLogLevelFilter": "Error",
                "sinks": "HotPath"
            }
        },
        "SinksConfig": {
            "Sink": [
                {
                    "name": "HotPath",
                    "EventHub": {
                        "Url": "https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py",
                        "SharedAccessKeyName": "SendRule"
                    }
                },
                {
                    "name": "applicationInsights",
                    "ApplicationInsights": "",
                    "Channels": {
                        "Channel": [
                            {
                                "logLevel": "Error",
                                "name": "errors"
                            }
                        ]
                    }
                }
            ]
        }
    },
    "StorageAccount": "{account name}"
}

```

Definições protegidas:
```JSON
{
    "storageAccountName": "{account name}",
    "storageAccountKey": "{account key}",
    "storageAccountEndPoint": "{storage endpoint}",
    "EventHub": {
        "Url": "https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "YOUR_KEY_HERE"
    }
}
```

## <a name="next-steps"></a>Passos Seguintes
Pode saber mais sobre os Hubs de Eventos ao aceder às seguintes ligações:

* [Descrição geral dos Hubs de Eventos](../../event-hubs/event-hubs-about.md)
* [Criar um hub de eventos](../../event-hubs/event-hubs-create.md)
* [FAQ dos Hubs de Eventos](../../event-hubs/event-hubs-faq.md)

<!-- Images. -->
[0]: ../../event-hubs/media/event-hubs-streaming-azure-diags-data/dashboard.png

