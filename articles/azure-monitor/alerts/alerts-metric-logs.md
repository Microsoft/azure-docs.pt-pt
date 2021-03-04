---
title: Criação de alertas métricos para registos no Monitor Azure
description: Tutorial sobre a criação de alertas métricos em tempo real em dados populares de análise de registo.
author: harelbr
ms.author: harelbr
ms.topic: conceptual
ms.date: 02/14/2021
ms.openlocfilehash: bb9bad1668340182083101ad879ee13e0ca3ea77
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102038276"
---
# <a name="create-metric-alerts-for-logs-in-azure-monitor"></a>Criar alertas métricos para registos no Monitor Azure

## <a name="overview"></a>Descrição Geral

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Pode utilizar alertas métricos em registos populares do Log Analytics extraídos como métricas como parte de Métricas de Registos, incluindo recursos em Azure ou no local. As soluções suportadas do Log Analytics estão listadas abaixo:

- [Contadores de desempenho](./../agents/data-sources-performance-counters.md) para máquinas Windows & Linux
- [Registos de batimentos cardíacos para a Agente Saúde](../insights/solution-agenthealth.md)
- Atualizar registos [de gestão](../../automation/update-management/overview.md)
- [Registos](./../agents/data-sources-windows-events.md) de dados do evento

Existem muitos benefícios para a utilização **de alertas métricos para registos** em consulta com base em [Alertas de Registo](./alerts-log.md) em Azure; alguns deles estão listados abaixo:

- Os Alertas Métricos oferecem capacidade de monitorização em tempo real e alertas métricos para os dados de registos da fonte de registo para garantir o mesmo.
- Os alertas métricos são imponentes - notificando apenas uma vez quando o alerta é disparado e uma vez quando o alerta é resolvido; em oposição aos alertas de Log, que são apátridas e continuam a disparar em todos os intervalos se a condição de alerta for cumprida.
- Os alertas métricos para o Log fornecem múltiplas dimensões, permitindo a filtragem a valores específicos como Computadores, Tipo DE SO, etc. mais simples; sem a necessidade de penhorar consulta em análise.

> [!NOTE]
> A métrica específica e/ou a dimensão só serão mostradas se os dados aparecerem no período escolhido. Estas métricas estão disponíveis para clientes com espaços de trabalho Azure Log Analytics.

## <a name="metrics-and-dimensions-supported-for-logs"></a>Métricas e dimensões suportadas para troncos

 Métrica alerta o suporte para métricas que usam dimensões. Pode utilizar as dimensões para filtrar a sua métrica para o nível certo. Está listada a lista completa de métricas suportadas por Registos de [Log Analytics;](../essentials/metrics-supported.md#microsoftoperationalinsightsworkspaces) através de soluções apoiadas.

> [!NOTE]
> Para visualizar uma métrica suportada extraída de um espaço de trabalho Log Analytics via [Azure Monitor - Métricas,](../essentials/metrics-charts.md)deve ser criado um alerta métrico para o log nessa métrica específica. As dimensões escolhidas no alerta métrico para registos - só aparecerão para exploração através do Azure Monitor - Métricas.

## <a name="creating-metric-alert-for-log-analytics"></a>Criação de alerta métrico para Log Analytics

Os dados métricos dos registos populares são canalizados antes de serem processados no Log Analytics, em Azure Monitor - Métricas. Isto permite que os utilizadores aproveitem as capacidades da plataforma Métrica, bem como o alerta métrico - incluindo ter alertas com frequência até 1 minuto.
Listados abaixo estão os meios de elaborar um alerta métrico para registos.

## <a name="prerequisites-for-metric-alert-for-logs"></a>Pré-requisitos para alerta métrico para registos

Antes da métrica de registos recolhidos nos trabalhos de dados do Log Analytics, deve ser configurado e disponível:

1. **Ative Log Analytics Workspace**: Um espaço de trabalho válido e ativo do Log Analytics deve estar presente. Para obter mais informações, consulte [Criar um espaço de trabalho de Log Analytics no portal Azure.](../logs/quick-create-workspace.md)
2. **O agente está configurado para o Log Analytics Workspace**: O agente precisa de ser configurado para VMs (e/ou) VMs no local para enviar dados para o espaço de trabalho log analytics utilizado em passo anterior. Para obter mais informações, consulte [Log Analytics - Visão geral do agente](./../agents/agents-overview.md).
3. **As Soluções de Análise de Log Suportadas estão instaladas**: A solução Log Analytics deve ser configurada e enviar dados para o espaço de trabalho do Log Analytics - as soluções suportadas são [contadores de desempenho para Windows & Linux](./../agents/data-sources-performance-counters.md), [Registos de Batimentos Cardíacos para A Saúde do Agente,](../insights/solution-agenthealth.md) [Gestão de Atualização](../../automation/update-management/overview.md)e [Dados do Evento](./../agents/data-sources-windows-events.md).
4. **Registar soluções de Analytics configuradas para enviar registos**: A solução Log Analytics deve ter os registos/dados necessários correspondentes às [métricas suportadas para espaços de trabalho do Log Analytics](../essentials/metrics-supported.md#microsoftoperationalinsightsworkspaces) ativados. Por exemplo, para % do contador *de memória disponível* deve ser configurado primeiro na solução [de contadores de desempenho.](./../agents/data-sources-performance-counters.md)

## <a name="configuring-metric-alert-for-logs"></a>Configurar alerta métrico para registos

 Os alertas métricos podem ser criados e geridos utilizando o portal Azure, Modelos de Gestor de Recursos, API REST, PowerShell e Azure CLI. Uma vez que os Alertas Métricos para Registos, é uma variante de alertas métricos - uma vez que os pré-requisitos são feitos, o alerta métrico para registos pode ser criado para o espaço de trabalho especificado Log Analytics. Todas as características e funcionalidades dos [alertas métricos](./alerts-metric-near-real-time.md) serão aplicáveis aos alertas métricos para registos, bem como; incluindo esquema de carga útil, limites de quota aplicáveis e preço cobrado.

Para obter detalhes e amostras passo a passo - consulte [a criação e gestão de alertas métricos](./alerts-metric.md). Especificamente, para Alertas Métricos para Registos - siga as instruções para gerir os alertas métricos e certifique-se de:

- O alvo para alerta métrico é um espaço de trabalho válido *do Log Analytics*
- Sinal escolhido para alerta métrico para o espaço de trabalho do *Log Analytics* selecionado é do tipo **Métrico**
- Filtrar para condições específicas ou recursos utilizando filtros de dimensão; métricas para troncos são multidimensionais
- Ao configurar a Lógica de *Sinal,* pode ser criado um único alerta para abranger múltiplos valores de dimensão (como o Computador)
- Se **não** utilizar o portal Azure para criar um alerta métrico para *o espaço de trabalho* do Log Analytics selecionado; em seguida, o utilizador deve primeiro criar uma regra explícita para converter dados de registo numa métrica utilizando [O Monitor Azure - Regras de Consulta Programadas](/rest/api/monitor/scheduledqueryrules).

> [!NOTE]
> Ao criar um alerta métrico para o log analytics espaço de trabalho através do portal Azure - regra correspondente para converter dados de registo em métrica via [Azure Monitor - As Regras de Consulta Programadas](/rest/api/monitor/scheduledqueryrules) são automaticamente criadas em segundo plano, *sem necessidade de qualquer intervenção ou ação do utilizador*. Para alerta métrico para criação de registos utilizando meios que não o portal Azure, consulte [o Modelo de Recurso para Alertas Métricos para](#resource-template-for-metric-alerts-for-logs) a secção de Registos em meios de amostragem de criação de um registo baseado em Placas de Marcação para regra de conversão métrica antes da criação de alerta métrico - caso contrário, não haverá dados para o alerta métrico nos registos criados.

## <a name="resource-template-for-metric-alerts-for-logs"></a>Modelo de recursos para alertas métricos para registos

Como indicado anteriormente, o processo de criação de alertas métricos a partir de troncos é de duas vertentes:

1. Criar uma regra para extrair métricas de troncos suportados usando a API de Tesouro
2. Crie um alerta métrico para métrica extraída do log (no passo 1) e log Analytics espaço de trabalho como recurso alvo

### <a name="metric-alerts-for-logs-with-static-threshold"></a>Alertas métricos para Registos com limiar estático

Para obter o mesmo, pode-se utilizar a amostra Azure Resource Manager Modelo abaixo - onde a criação de um alerta métrico estático depende da criação bem sucedida da regra para extrair métricas de registos através do agendadoQueryRule.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the rule to convert log to metric"
            }
        },
        "convertRuleDescription": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Description for log converted to metric"
            }
        },
        "convertRuleRegion": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the region used by workspace"
            }
        },
        "convertRuleStatus": {
            "type": "string",
            "defaultValue": "true",
            "metadata": {
                "description": "Specifies whether the log conversion rule is enabled"
            }
        },
        "convertRuleMetric": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric once extraction done from logs."
            }
        },
        "alertName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "resourceId": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Full Resource ID of the resource emitting the metric that will be used for the comparison. For example: /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroups/ResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
            }
        },
        "metricName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric used in the comparison to activate the alert."
            }
        },
        "operator": {
            "type": "string",
            "defaultValue": "GreaterThan",
            "allowedValues": [
                "Equals",
                "NotEquals",
                "GreaterThan",
                "GreaterThanOrEqual",
                "LessThan",
                "LessThanOrEqual"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "threshold": {
            "type": "string",
            "defaultValue": "0",
            "metadata": {
                "description": "The threshold value at which the alert is activated."
            }
        },
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between five minutes and one day. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT1M",
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": {
        "convertRuleTag": "hidden-link:/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName",
        "convertRuleSourceWorkspace": {
            "SourceId": "/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        }
    },
    "resources": [
        {
            "name": "[parameters('convertRuleName')]",
            "type": "Microsoft.Insights/scheduledQueryRules",
            "apiVersion": "2018-04-16",
            "location": "[parameters('convertRuleRegion')]",
            "tags": {
                "[variables('convertRuleTag')]": "Resource"
            },
            "properties": {
                "description": "[parameters('convertRuleDescription')]",
                "enabled": "[parameters('convertRuleStatus')]",
                "source": {
                    "dataSourceId": "[variables('convertRuleSourceWorkspace').SourceId]"
                },
                "action": {
                    "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.LogToMetricAction",
                    "criteria": [{
                            "metricName": "[parameters('convertRuleMetric')]",
                            "dimensions": []
                        }
                    ]
                }
            }
        },
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "dependsOn":["[resourceId('Microsoft.Insights/scheduledQueryRules',parameters('convertRuleName'))]"],
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('resourceId')]"],
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.SingleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "dimensions":[],
                            "operator": "[parameters('operator')]",
                            "threshold" : "[parameters('threshold')]",
                            "timeAggregation": "[parameters('timeAggregation')]"
                        }
                    ]
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"
                    }
                ]
            }
        }
    ]
}
```

Digamos que o JSON acima é guardado à medida que metricfromLogsAlertStatic.js- então pode ser associado a um ficheiro JSON de parâmetro para criação baseada em modelo de recurso. Um ficheiro JSON do parâmetro da amostra é listado abaixo:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "value": "TestLogtoMetricRule" 
        },
        "convertRuleDescription": {
            "value": "Test rule to extract metrics from logs via template"
        },
        "convertRuleRegion": {
            "value": "West Central US"
        },
        "convertRuleStatus": {
            "value": "true"
        },
        "convertRuleMetric": {
            "value": "Average_% Idle Time"
        },
        "alertName": {
            "value": "TestMetricAlertonLog"
        },
        "alertDescription": {
            "value": "New multi-dimensional metric alert created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "resourceId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        },
        "metricName":{
            "value": "Average_% Idle Time"
        },
        "operator": {
            "value": "GreaterThan"
        },
        "threshold":{
            "value": "1"
        },
        "timeAggregation":{
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/microsoft.insights/actionGroups/actionGroupName"
        }
    }
}
```

Assumindo que o ficheiro de parâmetro acima é guardado à medida que metricfromLogsAlertStatic.parameters.jsligado; em seguida, pode-se criar alerta métrico para registos usando [o Modelo de Recursos para a criação no portal Azure](../../azure-resource-manager/templates/deploy-portal.md).

Em alternativa, também se pode utilizar o comando Azure PowerShell abaixo:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile metricfromLogsAlertStatic.json TemplateParameterFile metricfromLogsAlertStatic.parameters.json
```

Ou use o modelo de recursos de implementação usando O Azure CLI:

```azurecli
az deployment group create --resource-group myRG --template-file metricfromLogsAlertStatic.json --parameters @metricfromLogsAlertStatic.parameters.json
```

### <a name="metric-alerts-for-logs-with-dynamic-thresholds"></a>Alertas métricos para Registos com Limiares Dinâmicos

Para obter o mesmo, pode-se utilizar a amostra Azure Resource Manager Modelo abaixo - onde a criação de um alerta métrico Dynamic Thresholds depende da criação bem-sucedida da regra para extrair métricas de registos através do agendadoQueryRule.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the rule to convert log to metric"
            }
        },
        "convertRuleDescription": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Description for log converted to metric"
            }
        },
        "convertRuleRegion": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the region used by workspace"
            }
        },
        "convertRuleStatus": {
            "type": "string",
            "defaultValue": "true",
            "metadata": {
                "description": "Specifies whether the log conversion rule is enabled"
            }
        },
        "convertRuleMetric": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric once extraction done from logs."
            }
        },
        "alertName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "resourceId": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Full Resource ID of the resource emitting the metric that will be used for the comparison. For example: /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroups/ResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
            }
        },
        "metricName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric used in the comparison to activate the alert."
            }
        },
        "operator": {
            "type": "string",
            "defaultValue": "GreaterOrLessThan",
            "allowedValues": [
                "GreaterThan",
                "LessThan",
                "GreaterOrLessThan"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "alertSensitivity": {
            "type": "string",
            "defaultValue": "Medium",
            "allowedValues": [
                "High",
                "Medium",
                "Low"
            ],
            "metadata": {
                "description": "Tunes how 'noisy' the Dynamic Thresholds alerts will be: 'High' will result in more alerts while 'Low' will result in fewer alerts."
            }
        },
        "numberOfEvaluationPeriods": {
            "type": "string",
            "defaultValue": "4",
            "metadata": {
                "description": "The number of periods to check in the alert evaluation."
            }
        },
        "minFailingPeriodsToAlert": {
            "type": "string",
            "defaultValue": "3",
            "metadata": {
                "description": "The number of unhealthy periods to alert on (must be lower or equal to numberOfEvaluationPeriods)."
            }
        },
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between five minutes and one day. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT1M",
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": {
        "convertRuleTag": "hidden-link:/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName",
        "convertRuleSourceWorkspace": {
            "SourceId": "/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        }
    },
    "resources": [
        {
            "name": "[parameters('convertRuleName')]",
            "type": "Microsoft.Insights/scheduledQueryRules",
            "apiVersion": "2018-04-16",
            "location": "[parameters('convertRuleRegion')]",
            "tags": {
                "[variables('convertRuleTag')]": "Resource"
            },
            "properties": {
                "description": "[parameters('convertRuleDescription')]",
                "enabled": "[parameters('convertRuleStatus')]",
                "source": {
                    "dataSourceId": "[variables('convertRuleSourceWorkspace').SourceId]"
                },
                "action": {
                    "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.LogToMetricAction",
                    "criteria": [{
                            "metricName": "[parameters('convertRuleMetric')]",
                            "dimensions": []
                        }
                    ]
                }
            }
        },
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "dependsOn":["[resourceId('Microsoft.Insights/scheduledQueryRules',parameters('convertRuleName'))]"],
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('resourceId')]"],
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.MultipleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "criterionType": "DynamicThresholdCriterion",
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "dimensions":[],
                            "operator": "[parameters('operator')]",
                            "alertSensitivity": "[parameters('alertSensitivity')]",
                            "failingPeriods": {
                                "numberOfEvaluationPeriods": "[parameters('numberOfEvaluationPeriods')]",
                                "minFailingPeriodsToAlert": "[parameters('minFailingPeriodsToAlert')]"
                            },
                            "timeAggregation": "[parameters('timeAggregation')]"
                        }
                    ]
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"
                    }
                ]
            }
        }
    ]
}
```

Digamos que o JSON acima é guardado à medida que metricfromLogsAlertDynamic.js- então pode ser associado a um ficheiro JSON de parâmetro para criação baseada em modelo de recurso. Um ficheiro JSON do parâmetro da amostra é listado abaixo:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "value": "TestLogtoMetricRule"
        },
        "convertRuleDescription": {
            "value": "Test rule to extract metrics from logs via template"
        },
        "convertRuleRegion": {
            "value": "West Central US"
        },
        "convertRuleStatus": {
            "value": "true"
        },
        "convertRuleMetric": {
            "value": "Average_% Idle Time"
        },
        "alertName": {
            "value": "TestMetricAlertonLog"
        },
        "alertDescription": {
            "value": "New multi-dimensional metric alert created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "resourceId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        },
        "metricName":{
            "value": "Average_% Idle Time"
        },
        "operator": {
            "value": "GreaterOrLessThan"
          },
          "alertSensitivity": {
              "value": "Medium"
          },
          "numberOfEvaluationPeriods": {
              "value": "4"
          },
          "minFailingPeriodsToAlert": {
              "value": "3"
          },
        "timeAggregation":{
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/microsoft.insights/actionGroups/actionGroupName"
        }
    }
}
```

Assumindo que o ficheiro de parâmetro acima é guardado à medida que metricfromLogsAlertDynamic.parameters.jsligado; em seguida, pode-se criar alerta métrico para registos usando [o Modelo de Recursos para a criação no portal Azure](../../azure-resource-manager/templates/deploy-portal.md).

Em alternativa, também se pode utilizar o comando Azure PowerShell abaixo:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile metricfromLogsAlertDynamic.json TemplateParameterFile metricfromLogsAlertDynamic.parameters.json
```

Ou use o modelo de recursos de implementação usando O Azure CLI:

```azurecli
az deployment group create --resource-group myRG --template-file metricfromLogsAlertDynamic.json --parameters @metricfromLogsAlertDynamic.parameters.json
```

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre os [alertas métricos.](../alerts/alerts-metric.md)
- Saiba mais sobre [os alertas de registo em Azure](./alerts-unified-log.md).
- Conheça os [alertas em Azure.](./alerts-overview.md)