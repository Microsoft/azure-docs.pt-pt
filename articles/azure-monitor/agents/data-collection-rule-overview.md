---
title: Regras de recolha de dados no Monitor Azure (pré-visualização)
description: Visão geral das regras de recolha de dados (DCRs) no Azure Monitor, incluindo o seu conteúdo e estrutura e como pode criar e trabalhar com eles.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/19/2021
ms.openlocfilehash: a52d6dca423565e7b5e4b6ac059bcf21b637c87c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104586340"
---
# <a name="data-collection-rules-in-azure-monitor-preview"></a>Regras de recolha de dados no Azure Monitor (pré-visualização)
As Regras de Recolha de Dados (DCR) definem os dados que entram no Azure Monitor e especificam para onde esses dados devem ser enviados ou armazenados. Este artigo fornece uma visão geral das regras de recolha de dados, incluindo o seu conteúdo e estrutura e como pode criar e trabalhar com eles.

## <a name="input-sources"></a>Fontes de entrada
As regras de recolha de dados suportam atualmente as seguintes fontes de entrada:

- Máquina virtual Azure com o agente Azure Monitor. Consulte [a recolha de dados configurar para o agente Azure Monitor (pré-visualização)](../agents/data-collection-rule-azure-monitor-agent.md).



## <a name="components-of-a-data-collection-rule"></a>Componentes de uma regra de recolha de dados
Uma regra de recolha de dados inclui os seguintes componentes.

| Componente | Descrição |
|:---|:---|
| Origens de dados | Fonte única de monitorização de dados com o seu próprio formato e método de exposição dos seus dados. Exemplos de uma fonte de dados incluem registo de eventos do Windows, contadores de desempenho e syslog. Cada fonte de dados corresponde a um determinado tipo de fonte de dados, conforme descrito abaixo. |
| Fluxos | Cabo único que descreve um conjunto de fontes de dados que serão transformadas e schematizadas como um tipo. Cada fonte de dados requer um ou mais fluxos, e um fluxo pode ser usado por várias fontes de dados. Todas as fontes de dados num fluxo partilham um esquema comum. Utilize vários fluxos, por exemplo, quando pretender enviar uma determinada fonte de dados para várias tabelas no mesmo espaço de trabalho do Log Analytics. |
| Destinos | Conjunto de destinos para onde os dados devem ser enviados. Exemplos incluem log analytics espaço de trabalho, métricas de monitor Azure e Hubs de Eventos Azure. | 
| Fluxos de dados | Definição dos fluxos a que os destinos devem ser enviados. | 

O diagrama seguinte mostra os componentes de uma regra de recolha de dados e a sua relação

[![Diagrama de DCR](media/data-collection-rule-overview/data-collection-rule-components.png)](media/data-collection-rule-overview/data-collection-rule-components.png#lightbox)

### <a name="data-source-types"></a>Tipos de origem de dados
Cada fonte de dados tem um tipo de fonte de dados. Cada tipo define um conjunto único de propriedades que devem ser especificadas para cada fonte de dados. Os tipos de fonte de dados atualmente disponíveis são apresentados na tabela seguinte.

| Tipo de fonte de dados | Description | 
|:---|:---|
| extensão | Fonte de dados baseada em extensão VM |
| performanceCounters | Contadores de desempenho tanto para Windows como Linux |
| syslog | Eventos Syslog em Linux |
| windowsEventLogs | Registo de eventos do Windows |


## <a name="limits"></a>Limites
Para limites aplicáveis a cada regra de recolha de [dados, consulte os limites de serviço do Azure Monitor](../service-limits.md#data-collection-rules).


## <a name="create-a-dcr"></a>Criar um DCR
Atualmente pode utilizar qualquer um dos seguintes métodos para criar um DCR:

- [Utilize o portal Azure](../agents/data-collection-rule-azure-monitor-agent.md) para criar uma regra de recolha de dados e associá-la a uma ou mais máquinas virtuais.
- Editar diretamente a regra de recolha de dados em JSON e [submeter-se através da API REST](/rest/api/monitor/datacollectionrules).
- Criar DCR e associações com [a Azure CLI](https://github.com/Azure/azure-cli-extensions/blob/master/src/monitor-control-service/README.md).
- Crie DCR e associações com a Azure PowerShell.
  - [Get-AzDataCollectionrule](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Get-AzDataCollectionRule.md)
  - [Novo-AzDataCollectionrule](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/New-AzDataCollectionRule.md)
  - [Set-AzDataCollectionrule](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Set-AzDataCollectionRule.md)
  - [Atualização-AzDataCollectionrule](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Update-AzDataCollectionRule.md)
  - [Remover-AzDataCollectionrule](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Remove-AzDataCollectionRule.md)
  - [Get-AzDataCollectionRuleAssociation](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Get-AzDataCollectionRuleAssociation.md)
  - [Nova-AzDataCollectionRuleAssociation](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/New-AzDataCollectionRuleAssociation.md)
  - [Remover-AzDataCollectionRuleAssociation](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Remove-AzDataCollectionRuleAssociation.md)

## <a name="sample-data-collection-rule"></a>Regra de recolha de dados de amostra
A regra de recolha de dados da amostra abaixo é para máquinas virtuais com o agente da Azure Management e tem os seguintes detalhes:

- Dados de desempenho
  - Recolhe contadores específicos de processador, memória, disco lógico e disco físico a cada 15 segundos e carrega a cada minuto.
  - Recolhe contadores de processos específicos a cada 30 segundos e carrega a cada 5 minutos.
- Eventos do Windows
  - Recolhe eventos de segurança do Windows e faz uploads a cada minuto.
  - Recolhe a aplicação do Windows e os eventos do sistema e faz uploads a cada 5 minutos.
- Syslog
  - Recolhe eventos de Debug, Critical e Emergência das instalações da Cron.
  - Recolhe eventos de alerta, crítico e emergência das instalações do syslog.
- Destinos
  - Envia todos os dados para um espaço de trabalho Log Analytics chamado centralWorkspace.

> [!NOTE]
> Para obter uma explicação dos XPaths que são usados para especificar a recolha de eventos nas regras de recolha de dados, consulte [a recolha de dados Limite com consultas personalizadas de XPath](data-collection-rule-azure-monitor-agent.md#limit-data-collection-with-custom-xpath-queries)


```json
{
    "location": "eastus",
    "properties": {
      "dataSources": {
        "performanceCounters": [
          {
            "name": "cloudTeamCoreCounters",
            "streams": [
              "Microsoft-Perf"
            ],
            "scheduledTransferPeriod": "PT1M",
            "samplingFrequencyInSeconds": 15,
            "counterSpecifiers": [
              "\\Processor(_Total)\\% Processor Time",
              "\\Memory\\Committed Bytes",
              "\\LogicalDisk(_Total)\\Free Megabytes",
              "\\PhysicalDisk(_Total)\\Avg. Disk Queue Length"
            ]
          },
          {
            "name": "appTeamExtraCounters",
            "streams": [
              "Microsoft-Perf"
            ],
            "scheduledTransferPeriod": "PT5M",
            "samplingFrequencyInSeconds": 30,
            "counterSpecifiers": [
              "\\Process(_Total)\\Thread Count"
            ]
          }
        ],
        "windowsEventLogs": [
          {
            "name": "cloudSecurityTeamEvents",
            "streams": [
              "Microsoft-Event"
            ],
            "scheduledTransferPeriod": "PT1M",
            "xPathQueries": [
              "Security!*"
            ]
          },
          {
            "name": "appTeam1AppEvents",
            "streams": [
              "Microsoft-Event"
            ],
            "scheduledTransferPeriod": "PT5M",
            "xPathQueries": [
              "System!*[System[(Level = 1 or Level = 2 or Level = 3)]]",
              "Application!*[System[(Level = 1 or Level = 2 or Level = 3)]]"
            ]
          }
        ],
        "syslog": [
          {
            "name": "cronSyslog",
            "streams": [
              "Microsoft-Syslog"
            ],
            "facilityNames": [
              "cron"
            ],
            "logLevels": [
              "Debug",
              "Critical",
              "Emergency"
            ]
          },
          {
            "name": "syslogBase",
            "streams": [
              "Microsoft-Syslog"
            ],
            "facilityNames": [
              "syslog"
            ],
            "logLevels": [
              "Alert",
              "Critical",
              "Emergency"
            ]
          }
        ]
      },
      "destinations": {
        "logAnalytics": [
          {
            "workspaceResourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.OperationalInsights/workspaces/my-workspace",
            "name": "centralWorkspace"
          }
        ]
      },
      "dataFlows": [
        {
          "streams": [
            "Microsoft-Perf",
            "Microsoft-Syslog",
            "Microsoft-Event"
          ],
          "destinations": [
            "centralWorkspace"
          ]
        }
      ]
    }
  }
```


## <a name="next-steps"></a>Passos seguintes

- [Crie uma regra de recolha de dados](data-collection-rule-azure-monitor-agent.md) e uma associação a partir de uma máquina virtual utilizando o agente Azure Monitor.
