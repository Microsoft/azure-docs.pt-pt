---
title: Monitor ize eventos de cluster Linux em Tecido de Serviço Azure
description: Aprenda a monitorizar um cluster de cluster de Tecido de Serviço Linux escrevendo eventos da plataforma Service Fabric para syslog.
author: srrengar
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: srrengar
ms.openlocfilehash: 5bd3bda71943b2ba8a34cd4fbd0b20917b875670
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75645757"
---
# <a name="service-fabric-linux-cluster-events-in-syslog"></a>Eventos de cluster de tecido de serviço linux em Syslog

O Service Fabric expõe um conjunto de eventos de plataforma para informá-lo de atividades importantes no seu cluster. A lista completa de eventos expostos está disponível [aqui.](service-fabric-diagnostics-event-generation-operational.md) Há várias formas de consumir estes eventos. Neste artigo, vamos discutir como configurar o Service Fabric para escrever estes eventos à Syslog.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="introduction"></a>Introdução

No lançamento 6.4, o SyslogConsumer foi introduzido para enviar os eventos da plataforma Service Fabric para syslog para clusters Linux. Uma vez ligados, os eventos fluem automaticamente para o Syslog que pode ser recolhido e enviado pelo Agente de Análise de Registos.

Cada evento Syslog tem 4 componentes
* Instalação
* Identidade
* Mensagem
* Gravidade

O SyslogConsumer escreve todos os `Local0`eventos da plataforma usando o Facility . Pode atualizar qualquer instalação válida alterando o config config. A Identidade `ServiceFabric`utilizada é . O campo Mensagem contém todo o evento serializado em JSON para que possa ser consultado e consumido por uma variedade de ferramentas. 

## <a name="enable-syslogconsumer"></a>Ativar o SyslogConsumer

Para ativar o SyslogConsumer, é necessário efetuar uma atualização do seu cluster. A `fabricSettings` secção tem de ser atualizada com o seguinte código. Note que este código apenas inclui secções relacionadas com o SyslogConsumer

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
1. Na secção Comum, há um `LinuxStructuredTracesEnabled`novo parâmetro chamado . **Isto é necessário para ter eventos Linux estruturados e serializados quando enviados para syslog.**
2. Na secção de Diagnósticos, foi adicionado um novo ConsumerInstance: SyslogConsumer. Isto diz à plataforma que há outro consumidor dos eventos. 
3. A nova secção SyslogConsumer `IsEnabled` `true`precisa de ter como . Está configurado para utilizar automaticamente a instalação Local0. Pode anular isto adicionando outro parâmetro.

```json
    {
        "name": "New LogFacility",
        "value": "<Valid Syslog Facility>"
    }
```

## <a name="azure-monitor-logs-integration"></a>Integração de registos do Monitor Azure
Pode ler estes eventos Syslog numa ferramenta de monitorização, como os registos do Monitor Azure. Pode criar um espaço de trabalho log Analytics utilizando estas [instruções]. (.. /azure-monitor/learn/quick-create-workspace.md) Também precisa adicionar o agente Log Analytics ao seu cluster para recolher e enviar estes dados para o espaço de trabalho. Este é o mesmo agente usado para recolher contadores de desempenho. 

1. Navegue `Advanced Settings` para a lâmina

    ![Definições do espaço de trabalho](media/service-fabric-diagnostics-oms-syslog/workspace-settings.png)

2. Clique `Data`
3. Clique `Syslog`
4. Configure local0 como o Facility para rastrear. Pode adicionar outra Facilidade se a alterar em definições de tecido

    ![Configure Syslog](media/service-fabric-diagnostics-oms-syslog/syslog-configure.png)
5. Dirija-se ao explorador de `Logs` consultas clicando no menu do recurso do espaço de trabalho para começar a consultar

    ![Registos do espaço de trabalho](media/service-fabric-diagnostics-oms-syslog/workspace-logs.png)
6. Pode consultar contra `Syslog` a mesa `ServiceFabric` que procura como o Nome do Processo. A consulta abaixo é um exemplo de como analisar o JSON no evento e exibir o seu conteúdo

```kusto
    Syslog | where ProcessName == "ServiceFabric" | extend $payload = parse_json(SyslogMessage) | project $payload
```

![Consulta syslog](media/service-fabric-diagnostics-oms-syslog/syslog-query.png)

O exemplo acima é de um evento nodeDown. Pode ver a lista completa de eventos [aqui.](service-fabric-diagnostics-event-generation-operational.md)

## <a name="next-steps"></a>Passos seguintes
* [Coloque o agente Log Analytics](service-fabric-diagnostics-oms-agent.md) nos seus nódosos para recolher contadores de desempenho e recolher estatísticas e registos para os seus recipientes
* Familiarize-se com as funcionalidades de [pesquisa de registos e consulta](../log-analytics/log-analytics-log-searches.md) oferecidas como parte dos registos do Monitor Azure
* [Use o Designer de Vistas para criar vistas personalizadas nos registos do Monitor Azure](../log-analytics/log-analytics-view-designer.md)
* Referência para como a integração de [logs do Monitor Azure com o Syslog](../log-analytics/log-analytics-data-sources-syslog.md).
