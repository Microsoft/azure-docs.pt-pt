---
title: Monitor linux eventos de cluster em Azure Service Fabric
description: Aprenda a monitorizar um cluster de serviço Linux, escrevendo eventos da plataforma Service Fabric para o Syslog.
ms.topic: conceptual
ms.date: 10/23/2018
ms.openlocfilehash: 895ff340c1b649c1ba8a20bf95edcefb9a72e246
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105626967"
---
# <a name="service-fabric-linux-cluster-events-in-syslog"></a>Eventos de cluster de tecido de serviço Linux em Syslog

A Service Fabric expõe um conjunto de eventos de plataforma para o informar de uma atividade importante no seu cluster. A lista completa dos eventos expostos está disponível [aqui.](service-fabric-diagnostics-event-generation-operational.md) Há uma variedade de maneiras através das quais estes eventos podem ser consumidos. Neste artigo, vamos discutir como configurar a Service Fabric para escrever estes eventos à Syslog.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="introduction"></a>Introdução

No lançamento 6.4, o SyslogConsumer foi introduzido para enviar os eventos da plataforma Service Fabric para o Syslog para clusters Linux. Uma vez ligados, os eventos fluirão automaticamente para o Syslog, que pode ser recolhido e enviado pelo Agente De analítica de registo.

Cada evento Syslog tem 4 componentes
* Instalação
* Identidade
* Mensagem
* Gravidade

O SyslogConsumer escreve todos os eventos da plataforma utilizando o `Local0` Facility. Pode atualizar para qualquer instalação válida alterando o config config. A Identidade utilizada `ServiceFabric` é. O campo Mensagem contém todo o evento serializado em JSON para que possa ser consultado e consumido por uma variedade de ferramentas. 

## <a name="enable-syslogconsumer"></a>Ativar o SyslogConsumer

Para ativar o SyslogConsumer, precisa de realizar uma atualização do seu cluster. A `fabricSettings` secção tem de ser atualizada com o seguinte código. Note que este código apenas inclui secções relacionadas com o SyslogConsumer

```json
    "fabricSettings": [
        {
            "name": "Diagnostics",
            "parameters": [
            {
                "name": "ConsumerInstances",
                "value": "AzureWinFabCsv, AzureWinFabCrashDump, AzureTableWinFabEtwQueryable, SyslogConsumer"
            }
            ]
        },
        {
            "name": "SyslogConsumer",
            "parameters": [
            {
                "name": "ProducerInstance",
                "value": "WinFabLttProducer"
            },
            {
            "name": "ConsumerType",
            "value": "SyslogConsumer"
            },
            {
                "name": "IsEnabled",
                "value": "true"
            }
            ]
        },
        {
            "name": "Common",
            "parameters": [
            {
                "name": "LinuxStructuredTracesEnabled",
                "value": "true"
            }
            ]
        }
    ],
```

Aqui estão as alterações para chamar
1. Na secção Comum, há um novo parâmetro chamado `LinuxStructuredTracesEnabled` . **Isto é necessário para ter eventos Linux estruturados e serializados quando enviados para o Syslog.**
2. Na secção de Diagnóstico, foi adicionado um novo ConsumerInstance: SyslogConsumer. Isto diz à plataforma que há outro consumidor dos eventos. 
3. A nova secção SyslogConsumer precisa de ter `IsEnabled` como `true` . Está configurado para utilizar automaticamente as instalações locais. Pode anular isto adicionando outro parâmetro.

```json
    {
        "name": "New LogFacility",
        "value": "<Valid Syslog Facility>"
    }
```

## <a name="azure-monitor-logs-integration"></a>Integração de registos do Monitor Azure
Pode ler estes eventos Syslog numa ferramenta de monitorização, como registos do Azure Monitor. Pode criar um espaço de trabalho Log Analytics utilizando o Mercado Azure utilizando estas [instruções]. (.. /azure-monitor/logs/quick-create-workspace.md) Também é necessário adicionar o agente Log Analytics ao seu cluster para recolher e enviar estes dados para o espaço de trabalho. Este é o mesmo agente usado para recolher contadores de desempenho. 

1. Navegue até a `Advanced Settings` lâmina

    ![Definições de espaço de trabalho](media/service-fabric-diagnostics-oms-syslog/workspace-settings.png)

2. Clique `Data`
3. Clique `Syslog`
4. Configure local0 como a facilidade de rastrear. Pode adicionar outra Facilidade se a alterar em fabricSettings

    ![Configurar syslog](media/service-fabric-diagnostics-oms-syslog/syslog-configure.png)
5. Dirija-se ao explorador de consultas clicando `Logs` no menu do recurso do espaço de trabalho para começar a consultar

    ![Troncos de espaço de trabalho](media/service-fabric-diagnostics-oms-syslog/workspace-logs.png)
6. Pode consultar-se contra a `Syslog` mesa à procura de `ServiceFabric` "ProcessName". A consulta abaixo é um exemplo de como analisar o JSON no evento e mostrar o seu conteúdo

```kusto
    Syslog | where ProcessName == "ServiceFabric" | extend $payload = parse_json(SyslogMessage) | project $payload
```

![Consulta de syslog](media/service-fabric-diagnostics-oms-syslog/syslog-query.png)

O exemplo acima é de um evento NodeDown. Pode ver a lista completa de eventos [aqui.](service-fabric-diagnostics-event-generation-operational.md)

## <a name="next-steps"></a>Passos seguintes
* [Coloque o agente Log Analytics](service-fabric-diagnostics-oms-agent.md) nos seus nós para recolher contadores de desempenho e recolher estatísticas e registos de estivadores para os seus contentores
* Familiarize-se com as funcionalidades [de pesquisa e consulta](../azure-monitor/logs/log-query-overview.md) de registo oferecidas como parte dos registos do Azure Monitor
* [Use o View Designer para criar vistas personalizadas em registos do Monitor Azure](../azure-monitor/visualize/view-designer.md)
* Referência para como a [Azure Monitor regista a integração com o Syslog](../azure-monitor/agents/data-sources-syslog.md).
