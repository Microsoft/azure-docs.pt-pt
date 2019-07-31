---
title: Transmitir dados de Diagnóstico do Azure para os hubs de eventos
description: Configurar Diagnóstico do Azure com hubs de eventos de ponta a ponta, incluindo diretrizes para cenários comuns.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 07/13/2017
ms.author: robb
ms.subservice: diagnostic-extension
ms.openlocfilehash: c5fc2199de8623dd3a9f2bc5faf23c7c40d67d75
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "64922811"
---
# <a name="streaming-azure-diagnostics-data-in-the-hot-path-by-using-event-hubs"></a>Streaming de dados de Diagnóstico do Azure no Hot Path usando hubs de eventos
Diagnóstico do Azure fornece maneiras flexíveis de coletar métricas e logs de VMs (máquinas virtuais) de serviços de nuvem e transferir resultados para o armazenamento do Azure. A partir do período de março de 2016 (SDK 2,9), você pode enviar diagnósticos para fontes de dados personalizadas e transferir dados de caminho quente em segundos usando os [hubs de eventos do Azure](https://azure.microsoft.com/services/event-hubs/).

Os tipos de dados com suporte incluem:

* Eventos de Rastreio de Eventos para o Windows (ETW)
* Contadores de desempenho
* Registos de eventos do Windows
* Registos de aplicações
* Registos da infraestrutura do Diagnóstico do Azure

Este artigo mostra como configurar Diagnóstico do Azure com hubs de eventos de ponta a ponta. As diretrizes também são fornecidas para os seguintes cenários comuns:

* Como personalizar os logs e as métricas que são enviados aos hubs de eventos
* Como alterar as configurações em cada ambiente
* Como exibir dados de fluxo de hubs de eventos
* Como solucionar problemas de conexão  

## <a name="prerequisites"></a>Pré-requisitos
Os hubs de eventos que recebem dados do Diagnóstico do Azure têm suporte em serviços de nuvem, VMs, conjuntos de dimensionamento de máquinas virtuais e Service Fabric a partir do SDK 2,9 do Azure e as ferramentas do Azure correspondentes para o Visual Studio.

* Extensão de Diagnóstico do Azure 1,6 ([SDK do Azure para .net 2,9 ou posterior](https://azure.microsoft.com/downloads/) destina-se a isso por padrão)
* [Visual Studio 2013 ou posterior](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* Configurações existentes de Diagnóstico do Azure em um aplicativo usando um arquivo *. wadcfgx* e um dos seguintes métodos:
  * Visual Studio: [Configurando o diagnóstico para os serviços de nuvem do Azure e máquinas virtuais](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines)
  * Windows PowerShell: [Habilitar o diagnóstico nos serviços de nuvem do Azure usando o PowerShell](../../cloud-services/cloud-services-diagnostics-powershell.md)
* Namespace de hubs de eventos provisionados de acordo com o artigo, [introdução aos hubs de eventos](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

## <a name="connect-azure-diagnostics-to-event-hubs-sink"></a>Conectar Diagnóstico do Azure ao coletor de hubs de eventos
Por padrão, Diagnóstico do Azure sempre envia logs e métricas para uma conta de armazenamento do Azure. Um aplicativo também pode enviar dados para os hubs de eventos adicionando uma nova seção de coletores no elemento **PublicConfig** / **WadCfg** do arquivo *. wadcfgx* . No Visual Studio, o arquivo *. wadcfgx* é armazenado no seguinte caminho: > Arquivo**Diagnostics. wadcfgx** de**funções** > **de projeto do serviço de nuvem (roleName)**  > .

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

Neste exemplo, a URL do hub de eventos é definida como o namespace totalmente qualificado do hub de eventos: Namespace de hubs de eventos + "/" + nome do hub de eventos.  

A URL do hub de eventos é exibida na [portal do Azure](https://go.microsoft.com/fwlink/?LinkID=213885) no painel de hubs de eventos.  

O nome do **coletor** pode ser definido como qualquer cadeia de caracteres válida, desde que o mesmo valor seja usado consistentemente em todo o arquivo de configuração.

> [!NOTE]
> Pode haver coletores adicionais, como *applicationInsights* configurados nesta seção. Diagnóstico do Azure permite que um ou mais coletores sejam definidos se cada coletor também for declarado na seção **PrivateConfig** .  
>
>

O coletor de hubs de eventos também deve ser declarado e definido na seção **PrivateConfig** do arquivo de configuração *. wadcfgx* .

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

O `SharedAccessKeyName` valor deve corresponder a uma chave de SAS (assinatura de acesso compartilhado) e à política que foi definida no namespace de **hubs de eventos** . Navegue até o painel de hubs de eventos na [portal do Azure](https://portal.azure.com), clique na guia **Configurar** e configure uma política nomeada (por exemplo, "SendRule") que tenha permissões de *envio* . O **StorageAccount** também é declarado em **PrivateConfig**. Não é necessário alterar os valores aqui se eles estiverem funcionando. Neste exemplo, deixamos os valores vazios, que é um sinal de que um ativo de downstream definirá os valores. Por exemplo, o arquivo de configuração do ambiente *imconfiguration. Cloud. cscfg* define os nomes e as chaves apropriados do ambiente.  

> [!WARNING]
> A chave SAS dos hubs de eventos é armazenada em texto sem formatação no arquivo *. wadcfgx* . Geralmente, essa chave é verificada no controle do código-fonte ou está disponível como um ativo em seu servidor de compilação, portanto, você deve protegê-la conforme apropriado. Recomendamos que você use uma chave SAS aqui com as permissões *Enviar somente* para que um usuário mal-intencionado possa gravar no Hub de eventos, mas não ouvi-lo nem gerenciá-lo.
>
>

## <a name="configure-azure-diagnostics-to-send-logs-and-metrics-to-event-hubs"></a>Configurar Diagnóstico do Azure para enviar logs e métricas para os hubs de eventos
Conforme discutido anteriormente, todos os dados de diagnóstico padrão e personalizados, ou seja, métricas e logs, são enviados automaticamente para o armazenamento do Azure nos intervalos configurados. Com os hubs de eventos e qualquer coletor adicional, você pode especificar qualquer nó raiz ou folha na hierarquia a ser enviado ao Hub de eventos. Isso inclui eventos ETW, contadores de desempenho, logs de eventos do Windows e logs de aplicativos.   

É importante considerar quantos pontos de dados devem realmente ser transferidos para os hubs de eventos. Normalmente, os desenvolvedores transferem dados de Hot-Path de baixa latência que devem ser consumidos e interpretados rapidamente. Os sistemas que monitoram alertas ou regras de dimensionamento automático são exemplos. Um desenvolvedor também pode configurar um repositório de análise alternativo ou um repositório de pesquisa, por exemplo, Azure Stream Analytics, Elasticsearch, um sistema de monitoramento personalizado ou um sistema de monitoramento favorito de outros.

Veja a seguir alguns exemplos de configurações.

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

No exemplo acima, o coletor é aplicado ao nó pai **PerformanceCounters** na hierarquia, o que significa que todos os **PerformanceCounters** filho serão enviados para os hubs de eventos.  

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

No exemplo anterior, o coletor é aplicado a apenas três contadores: **Solicitações**enfileiradas, **solicitações rejeitadas**e **% de tempo do processador**.  

O exemplo a seguir mostra como um desenvolvedor pode limitar a quantidade de dados enviados para serem as métricas críticas que são usadas para a integridade desse serviço.  

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

Neste exemplo, o coletor é aplicado aos logs e é filtrado somente para rastreamento de nível de erro.

## <a name="deploy-and-update-a-cloud-services-application-and-diagnostics-config"></a>Implantar e atualizar um aplicativo de serviços de nuvem e configuração de diagnóstico
O Visual Studio fornece o caminho mais fácil para implantar o aplicativo e a configuração do coletor de hubs de eventos. Para exibir e editar o arquivo, abra o arquivo *. wadcfgx* no Visual Studio, edite-o e salve-o. O caminho é**roleName (**  > **funções** > de **projeto** > de serviço de nuvem)**Diagnostics. wadcfgx**.  

Neste ponto, todas as ações de implantação e atualização de implantação no Visual Studio, no Visual Studio Team System e em todos os comandos ou scripts baseados no MSBuild e usam o destino **/t: publish** incluem o *. wadcfgx* no processo de empacotamento. Além disso, as implantações e atualizações implantam o arquivo no Azure usando a extensão apropriada do agente de Diagnóstico do Azure em suas VMs.

Depois de implantar o aplicativo e Diagnóstico do Azure configuração, você verá imediatamente a atividade no painel do hub de eventos. Isso indica que você está pronto para passar para a exibição dos dados de caminho inativo no cliente de escuta ou na ferramenta de análise de sua escolha.  

Na figura a seguir, o painel de hubs de eventos mostra o envio íntegro de dados de diagnóstico para o Hub de eventos a partir de um dia após 11 P.M. Isso ocorre quando o aplicativo foi implantado com um arquivo *. wadcfgx* atualizado e o coletor foi configurado corretamente.

![][0]  

> [!NOTE]
> Quando você faz atualizações no arquivo de configuração Diagnóstico do Azure (. wadcfgx), é recomendável que você envie por push as atualizações para todo o aplicativo, bem como a configuração usando a publicação do Visual Studio ou um script do Windows PowerShell.  
>
>

## <a name="view-hot-path-data"></a>Exibir dados de Hot-Path
Conforme discutido anteriormente, há muitos casos de uso para escutar e processar dados de hubs de eventos.

Uma abordagem simples é criar um pequeno aplicativo de console de teste para escutar o Hub de eventos e imprimir o fluxo de saída. Você pode colocar o código a seguir, que é explicado em mais detalhes em introdução [aos hubs de eventos](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)) em um aplicativo de console.  

Observe que o aplicativo de console deve incluir o [pacote NuGet do host do processador de eventos](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/).  

Lembre-se de substituir os valores entre colchetes angulares na função **principal** pelos valores de seus recursos.   

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

## <a name="troubleshoot-event-hubs-sinks"></a>Solucionar problemas de coletores de hubs de eventos
* O Hub de eventos não mostra a atividade de entrada ou saída do evento conforme o esperado.

    Verifique se o Hub de eventos foi provisionado com êxito. Todas as informações de conexão na seção **PrivateConfig** de *. wadcfgx* devem corresponder aos valores de seu recurso, como visto no Portal. Verifique se você tem uma política de SAS definida ("SendRule" no exemplo) no portal e se a permissão de *envio* foi concedida.  
* Após uma atualização, o Hub de eventos não mostra mais a atividade de entrada ou saída de eventos.

    Primeiro, verifique se o Hub de eventos e as informações de configuração estão corretos, conforme explicado anteriormente. Às vezes, o **PrivateConfig** é redefinido em uma atualização de implantação. A correção recomendada é fazer todas as alterações em *. wadcfgx* no projeto e, em seguida, enviar por push uma atualização completa do aplicativo. Se isso não for possível, verifique se a atualização de diagnóstico envia por push um **PrivateConfig** completo que inclui a chave SAS.  
* Tentei as sugestões e o Hub de eventos ainda não está funcionando.

    Tente examinar a tabela de armazenamento do Azure que contém logs e erros para Diagnóstico do Azure si mesmo: **WADDiagnosticInfrastructureLogsTable**. Uma opção é usar uma ferramenta como [Gerenciador de armazenamento do Azure](https://www.storageexplorer.com) para se conectar a essa conta de armazenamento, exibir essa tabela e adicionar uma consulta de carimbo de data/hora nas últimas 24 horas. Você pode usar a ferramenta para exportar um arquivo. csv e abri-lo em um aplicativo como o Microsoft Excel. O Excel facilita a pesquisa de cadeias de caracteres de cartão de chamada, como **EventHubs**, para ver qual erro é relatado.  

## <a name="next-steps"></a>Passos Seguintes
• [Saiba mais sobre os hubs de eventos](https://azure.microsoft.com/services/event-hubs/)

## <a name="appendix-complete-azure-diagnostics-configuration-file-wadcfgx-example"></a>Anexo Exemplo de arquivo de configuração Diagnóstico do Azure (. wadcfgx) completo
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

O *inconfiguration. Cloud. cscfg* complementar para este exemplo é semelhante ao seguinte.

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

As configurações de JSON equivalentes para máquinas virtuais são as seguintes:

Configurações públicas:
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

Configurações protegidas:
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

## <a name="next-steps"></a>Passos seguintes
Pode saber mais sobre os Hubs de Eventos ao aceder às seguintes ligações:

* [Descrição geral dos Hubs de Eventos](../../event-hubs/event-hubs-about.md)
* [Criar um hub de eventos](../../event-hubs/event-hubs-create.md)
* [FAQ dos Hubs de Eventos](../../event-hubs/event-hubs-faq.md)

<!-- Images. -->
[0]: ../../event-hubs/media/event-hubs-streaming-azure-diags-data/dashboard.png

