---
title: Enviar dados da extensão de diagnóstico do Windows Azure para os Hubs de Eventos Azure
description: Configure a extensão de diagnóstico no Azure Monitor para enviar dados para o Azure Event Hub para que possa reencaguhá-lo para locais fora de Azure.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/18/2020
ms.openlocfilehash: 6aa960eb7aed7a579c3fc95748b6e32601201a61
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305128"
---
# <a name="send-data-from-windows-azure-diagnostics-extension-to-azure-event-hubs"></a>Enviar dados da extensão de diagnóstico do Windows Azure para os Hubs de Eventos Azure
A extensão de diagnóstico Azure é um agente no Azure Monitor que recolhe dados de monitorização do sistema operativo convidado e cargas de trabalho de máquinas virtuais Azure e outros recursos compute. Este artigo descreve como enviar dados da extensão de diagnóstico do Windows Azure (WAD) para [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) para que possa encaminhar para locais fora do Azure.

## <a name="supported-data"></a>Dados suportados

Os dados recolhidos do sistema operativo convidado que podem ser enviados para o Event Hubs incluem o seguinte. Outras fontes de dados recolhidas pelo WAD, incluindo registos IIS e depósitos de colisão, não podem ser enviadas para os Centros de Eventos.

* Eventos de Rastreio de Eventos para o Windows (ETW)
* Contadores de desempenho
* Registos de eventos do Windows, incluindo registos de aplicações no registo de eventos do Windows
* Registos da infraestrutura do Diagnóstico do Azure

## <a name="prerequisites"></a>Pré-requisitos

* Extensão de diagnóstico do Windows 1.6 ou superior. Consulte [as versões de esquema de configuração de extensão de Azure Diagnostics e histórico](diagnostics-extension-versions.md) para uma história de versão e [visão geral da extensão do Azure Diagnostics](diagnostics-extension-overview.md) para recursos suportados.
* O espaço de nomes do Event Hubs deve ser sempre a provisionado. Consulte [o Event Hubs](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md) para mais detalhes.


## <a name="configuration-schema"></a>Esquema de configuração
Consulte [a extensão de diagnóstico do Windows Azure (WAD)](diagnostics-extension-windows-install.md) para diferentes opções para ativar e configurar a extensão de diagnóstico e o esquema de [configuração do Azure Diagnostics](diagnostics-extension-schema-windows.md) para uma referência do esquema de configuração. O resto deste artigo descreverá como usar esta configuração para enviar dados para um centro de eventos. 

O Azure Diagnostics envia sempre registos e métricas para uma conta de Armazenamento Azure. Pode configurar um ou mais *sumidouros de dados* que enviam dados para locais adicionais. Cada pia é definida no [elemento SinksConfig](diagnostics-extension-schema-windows.md#sinksconfig-element) da configuração pública com informação sensível na configuração privada. Esta configuração para centros de eventos utiliza os valores na tabela seguinte.

| Propriedade | Descrição |
|:---|:---|
| Nome | Nome descritivo para a pia. Usado na configuração para especificar quais fontes de dados enviar para a pia. |
| Url  | Url do centro de eventos no formulário \<event-hubs-namespace\> .servicebus.windows.net/ \<event-hub-name\> .          |
| SharedAccessKeyName | Nome de uma política de acesso partilhado para o centro de eventos que tem pelo menos **enviar** autoridade. |
| SharedAccessKey     | Chave primária ou secundária da política de acesso partilhado para o centro do evento. |

Abaixo são mostradas configurações públicas e privadas. Esta é uma configuração mínima com um único contador de desempenho e registo de eventos para ilustrar como configurar e usar o afundanço de dados do centro de eventos. Consulte [o esquema de configuração do Azure Diagnostics](diagnostics-extension-schema-windows.md) para obter um exemplo mais complexo.

### <a name="public-configuration"></a>Configuração pública

```JSON
{
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 5120,
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "sinks": "myEventHub",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                        "sampleRate": "PT3M"
                    }
                ]
            },
            "WindowsEventLog": {
                "scheduledTransferPeriod": "PT1M",
                "sinks": "myEventHub",
                    "DataSource": [
                    {
                        "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
                    }
                ]
            }
        },
        "SinksConfig": {
            "Sink": [
                {
                    "name": "myEventHub",
                    "EventHub": {
                        "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
                        "SharedAccessKeyName": "SendRule"
                    }
                }
            ]
        }
    },
    "StorageAccount": "mystorageaccount",
}
```


### <a name="private-configuration"></a>Configuração privada

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "{base64 encoded key}",
    "storageAccountEndPoint": "https://core.windows.net",
    "EventHub": {
        "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "{base64 encoded key}"
    }
}
```



## <a name="configuration-options"></a>Opções de configuração
Para enviar dados para um lavatório de **dados, especifique** o atributo de pias no nó da fonte de dados. Onde coloca o atributo **pias** determina o âmbito da atribuição. No exemplo seguinte, o atributo **de pias** é definido para o nó **PerformanceCounters** que fará com que todos os contadores de desempenho da criança sejam enviados para o centro de eventos.

```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "sinks": "MyEventHub",
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


No exemplo seguinte, o atributo **pias** é aplicado diretamente em três balcões, o que fará com que apenas esses contadores de desempenho sejam enviados para o centro de eventos. 

```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
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
            "sinks": "MyEventHub"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Queued",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        }
    ]
}
```

## <a name="validating-configuration"></a>Validação da configuração
Você pode usar uma variedade de métodos para validar que os dados estão sendo enviados para o centro de eventos. o método ne straightforward é utilizar a captura de Event Hubs como descrito em [eventos de captura através de Azure Event Hubs em Azure Blob Storage ou Azure Data Lake Storage](../../event-hubs/event-hubs-capture-overview.md). 


## <a name="troubleshoot-event-hubs-sinks"></a>Troubleshoot Event Hubs afunda

- Veja a tabela de armazenamento **Azure WADDiagnosticInfrastructureLogsTable** que contém registos e erros para o próprio Azure Diagnostics. Uma opção é utilizar uma ferramenta como [o Azure Storage Explorer](https://www.storageexplorer.com) para ligar a esta conta de armazenamento, ver esta tabela e adicionar uma consulta para o TimeStamp nas últimas 24 horas. Pode utilizar a ferramenta para exportar um ficheiro .csv e abri-lo numa aplicação como o Microsoft Excel. O Excel facilita a procura de cordas de cartão de chamada, como o **EventHubs,** para ver que erro é relatado.  

- Verifique se o seu centro de eventos está a ser a provisionado com sucesso. Todas as informações de ligação na secção **PrivateConfig** da configuração devem corresponder aos valores do seu recurso, como visto no portal. Certifique-se de que tem uma política SAS definida *(SendRule* no exemplo) no portal e que a permissão *de envio* é concedida.  

## <a name="next-steps"></a>Passos seguintes

* [Descrição geral dos Event Hubs](../../event-hubs/event-hubs-about.md)
* [Criar um centro de eventos](../../event-hubs/event-hubs-create.md)
* [FAQ dos Hubs de Eventos](../../event-hubs/event-hubs-faq.yml)

<!-- Images. -->
[0]: ../../event-hubs/media/event-hubs-streaming-azure-diags-data/dashboard.png



