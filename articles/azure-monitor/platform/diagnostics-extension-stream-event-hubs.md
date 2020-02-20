---
title: Envie dados da extensão de diagnóstico do Windows Azure aos Hubs de Eventos Azure
description: Configure a extensão de diagnóstico no Azure Monitor para enviar dados para o Azure Event Hub para que possa encaminhar para locais fora do Azure.
ms.service: azure-monitor
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/18/2020
ms.openlocfilehash: 573a56c537e48687e310acff8639c50d0d0c6e3d
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77467968"
---
# <a name="send-data-from-windows-azure-diagnostics-extension-to-azure-event-hubs"></a>Envie dados da extensão de diagnóstico do Windows Azure aos Hubs de Eventos Azure
A extensão de diagnóstico do Azure é um agente no Azure Monitor que recolhe dados de monitorização do sistema operativo convidado e cargas de trabalho de máquinas virtuais Azure e outros recursos computacionais. Este artigo descreve como enviar dados da extensão de diagnóstico do Windows Azure (WAD) para [o Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) para que possa encaminhar para locais fora do Azure.

## <a name="supported-data"></a>Dados suportados

Os dados recolhidos do sistema operativo convidado que podem ser enviados para os Centros de Eventos incluem os seguintes. Outras fontes de dados recolhidas pela WAD, incluindo registos IIS e despejos de acidentes, não podem ser enviadas para centros de eventos.

* Eventos de Rastreio de Eventos para o Windows (ETW)
* Contadores de desempenho
* Registos de eventos do Windows, incluindo registos de aplicações no registo de eventos do Windows
* Registos da infraestrutura do Diagnóstico do Azure

## <a name="prerequisites"></a>Pré-requisitos

* Extensão de diagnóstico do Windows 1.6 ou superior. Consulte [as versões de configuração de configuração de configuração de configuração de configuração de diagnóstico sinuosos e histórico](diagnostics-extension-versions.md) para um histórico de versão e visão geral de [extensão do Azure Diagnostics](diagnostics-extension-overview.md) para recursos suportados.
* O espaço de nome do Event Hubs deve ser sempre provisionado. Veja [Começar com Hubs](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md) de Eventos para mais detalhes.


## <a name="configuration-schema"></a>Esquema de configuração
Consulte instalar e configurar a extensão de diagnóstico do [Windows Azure (WAD)](diagnostics-extension-windows-install.md) para diferentes opções para permitir e configurar a extensão de diagnóstico e configuração de diagnóstico sinuoso para uma referência do esquema de configuração. [](diagnostics-extension-schema-windows.md) O resto deste artigo descreverá como usar esta configuração para enviar dados para um centro de eventos. 

A Azure Diagnostics envia sempre registos e métricas para uma conta de Armazenamento Azure. Pode configurar um ou mais *afundados* de dados que enviam dados para locais adicionais. Cada pia é definida no [elemento SinksConfig](diagnostics-extension-schema-windows.md#sinksconfig-element) da configuração pública com informações sensíveis na configuração privada. Esta configuração para centros de eventos utiliza os valores na tabela seguinte.

| Propriedade | Descrição |
|:---|:---|
| Nome | Nome descritivo para a pia. Utilizado na configuração para especificar quais as fontes de dados a enviar para o lavatório. |
| Url  | Url do centro de eventos em forma \<espaço de nome de eventos-hubs\>.servicebus.windows.net/\<\>de nome de eventos.          |
| Nome de acesso partilhado | Nome de uma política de acesso partilhado para o centro de eventos que tem pelo menos **enviar** autoridade. |
| Chave de Acesso Partilhado     | Chave primária ou secundária da política de acesso partilhado para o centro do evento. |

Exemplo de configurações públicas e privadas são mostradas abaixo. Esta é uma configuração mínima com um único contador de desempenho e registo de eventos para ilustrar como configurar e usar o sumidouro de dados do centro de eventos. Consulte o esquema de [configuração do Azure Diagnostics](diagnostics-extension-schema-windows.md) para um exemplo mais complexo.

### <a name="public-configuration"></a>Configuração pública

```JSON
{
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 5120
        },
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
Para enviar dados para um sumidouro de dados, especifice o atributo dos **lavatórios** no nó da fonte de dados. Onde coloca o atributo da **pia** determina o âmbito da atribuição. No exemplo seguinte, o atributo dos **lavatórios** é definido para o nó **PerformanceCounters,** o que fará com que todos os contadores de desempenho da criança sejam enviados para o centro do evento.

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


No exemplo seguinte, o atributo dos **lavatórios** é aplicado diretamente a três balcões, o que fará com que apenas esses contadores de desempenho sejam enviados para o centro do evento. 

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
Pode utilizar uma variedade de métodos para validar que os dados estão a ser enviados para o centro do evento. ne método simples é usar a captura de Hubs de Eventos como descrito em eventos de captura através de Hubs de [Eventos Azure em Armazenamento De Blob Azure ou Armazenamento](../../event-hubs/event-hubs-capture-overview.md)de Lago de Dados Azure . 


## <a name="troubleshoot-event-hubs-sinks"></a>Centros de eventos de resolução de problemas afundam

- Veja a tabela de armazenamento Azure **WADDiagnosticInfrastructureLogsTable** que contém registos e erros para o próprio Azure Diagnostics. Uma opção é usar uma ferramenta como o [Azure Storage Explorer](https://www.storageexplorer.com) para se conectar a esta conta de armazenamento, ver esta tabela e adicionar uma consulta para timestamp nas últimas 24 horas. Pode utilizar a ferramenta para exportar um ficheiro .csv e abri-lo numa aplicação como o Microsoft Excel. O Excel facilita a procura de cordas de cartões de visita, como **eventHubs,** para ver que erro é relatado.  

- Verifique se o seu centro de eventos está aprovisionado com sucesso. Todas as informações de ligação na secção **PrivateConfig** da configuração devem corresponder aos valores do seu recurso, como visto no portal. Certifique-se de que tem uma política SAS definida *(SendRule* no exemplo) no portal e que a permissão *enviar* é concedida.  

## <a name="next-steps"></a>Passos seguintes

* [Descrição geral dos Hubs de Eventos](../../event-hubs/event-hubs-about.md)
* [Criar um hub de eventos](../../event-hubs/event-hubs-create.md)
* [FAQ dos Hubs de Eventos](../../event-hubs/event-hubs-faq.md)

<!-- Images. -->
[0]: ../../event-hubs/media/event-hubs-streaming-azure-diags-data/dashboard.png



