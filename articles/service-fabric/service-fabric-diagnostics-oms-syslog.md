---
title: Monitorar eventos de cluster do Linux no Azure Service Fabric
description: Saiba como monitorar um Service Fabric eventos de cluster do Linux gravando Service Fabric eventos de plataforma no syslog.
author: srrengar
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: srrengar
ms.openlocfilehash: 5bd3bda71943b2ba8a34cd4fbd0b20917b875670
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645757"
---
# <a name="service-fabric-linux-cluster-events-in-syslog"></a>Service Fabric eventos de cluster do Linux no syslog

Service Fabric expõe um conjunto de eventos de plataforma para informá-lo sobre atividades importantes em seu cluster. A lista completa de eventos expostos está disponível [aqui](service-fabric-diagnostics-event-generation-operational.md). Há várias maneiras pelas quais esses eventos podem ser consumidos. Neste artigo, vamos discutir como configurar Service Fabric para gravar esses eventos no syslog.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="introduction"></a>Introdução

Na versão 6,4, o SyslogConsumer foi introduzido para enviar os eventos da plataforma Service Fabric para o syslog para clusters do Linux. Depois de ativado, os eventos fluirão automaticamente para o syslog, que pode ser coletado e enviado pelo agente de Log Analytics.

Cada evento de syslog tem quatro componentes
* Recurso
* Identidade
* Mensagem
* Gravidade

O SyslogConsumer grava todos os eventos de plataforma usando o recurso `Local0`. Você pode atualizar para qualquer recurso válido alterando a configuração config. A identidade usada é `ServiceFabric`. O campo mensagem contém o evento inteiro serializado em JSON para que ele possa ser consultado e consumido por uma variedade de ferramentas. 

## <a name="enable-syslogconsumer"></a>Habilitar SyslogConsumer

Para habilitar o SyslogConsumer, você precisa executar uma atualização do cluster. A seção `fabricSettings` precisa ser atualizada com o código a seguir. Observe que esse código inclui apenas seções relacionadas a SyslogConsumer

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
1. Na seção comum, há um novo parâmetro chamado `LinuxStructuredTracesEnabled`. **Isso é necessário para que os eventos do Linux sejam estruturados e serializados quando enviados para o syslog.**
2. Na seção de diagnóstico, um novo ConsumerInstance: SyslogConsumer foi adicionado. Isso informa à plataforma que há outro consumidor dos eventos. 
3. A nova seção SyslogConsumer precisa ter `IsEnabled` como `true`. Ele é configurado para usar o recurso Local0 automaticamente. Você pode substituir isso adicionando outro parâmetro.

```json
    {
        "name": "New LogFacility",
        "value": "<Valid Syslog Facility>"
    }
```

## <a name="azure-monitor-logs-integration"></a>Integração de logs de Azure Monitor
Você pode ler esses eventos de syslog em uma ferramenta de monitoramento, como logs de Azure Monitor. Você pode criar um espaço de trabalho Log Analytics usando o Azure Marketplace usando estas [instruções]. (.. /Azure-monitor/Learn/Quick-Create-Workspace.MD) você também precisa adicionar o agente de Log Analytics ao seu cluster para coletar e enviar esses dados para o espaço de trabalho. Esse é o mesmo agente usado para coletar contadores de desempenho. 

1. Navegue até a folha `Advanced Settings`

    ![Workspace Settings](media/service-fabric-diagnostics-oms-syslog/workspace-settings.png)

2. Clique `Data`
3. Clique `Syslog`
4. Configure o Local0 como o recurso a ser acompanhado. Você pode adicionar outro recurso se o tiver alterado em fabricSettings

    ![Configurar o Syslog](media/service-fabric-diagnostics-oms-syslog/syslog-configure.png)
5. Vá até o Gerenciador de consultas clicando em `Logs` no menu do recurso do espaço de trabalho para iniciar a consulta

    ![Logs de espaço de trabalho](media/service-fabric-diagnostics-oms-syslog/workspace-logs.png)
6. Você pode consultar a tabela `Syslog` procurando por `ServiceFabric` como o ProcessName. A consulta abaixo é um exemplo de como analisar o JSON no evento e exibir seu conteúdo

```kusto
    Syslog | where ProcessName == "ServiceFabric" | extend $payload = parse_json(SyslogMessage) | project $payload
```

![Consulta de syslog](media/service-fabric-diagnostics-oms-syslog/syslog-query.png)

O exemplo acima é de um evento NodeDown. Você pode exibir a lista completa de eventos [aqui](service-fabric-diagnostics-event-generation-operational.md).

## <a name="next-steps"></a>Passos seguintes
* [Implementar o agente do Log Analytics](service-fabric-diagnostics-oms-agent.md) para os nós para recolher contadores de desempenho e recolher estatísticas de docker e registos para os seus contentores
* Familiarize-se com os recursos de [pesquisa de logs e consulta](../log-analytics/log-analytics-log-searches.md) oferecidos como parte dos logs de Azure monitor
* [Usar o designer de exibição para criar exibições personalizadas em logs de Azure Monitor](../log-analytics/log-analytics-view-designer.md)
* Referência sobre como [Azure monitor a integração de logs com o syslog](../log-analytics/log-analytics-data-sources-syslog.md).
