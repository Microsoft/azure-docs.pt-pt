---
title: Criando alertas de métrica para logs no Azure Monitor
description: Tutorial sobre a criação de alertas de métrica quase em tempo real em dados populares do log Analytics.
author: yanivlavi
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: yalavi
ms.subservice: alerts
ms.openlocfilehash: d6b65b76138cb180ab105631ebc0f19b7d38f206
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226526"
---
# <a name="create-metric-alerts-for-logs-in-azure-monitor"></a>Criar alertas de métrica para logs no Azure Monitor

## <a name="overview"></a>Descrição geral

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Monitor dá suporte ao [tipo de alerta de métrica](../../azure-monitor/platform/alerts-metric-near-real-time.md) que tem benefícios em relação aos [alertas clássicos](../../azure-monitor/platform/alerts-classic-portal.md). As métricas estão disponíveis para [uma grande lista de serviços do Azure](../../azure-monitor/platform/metrics-supported.md). Este artigo explica o uso de um subconjunto (ou seja) para `Microsoft.OperationalInsights/workspaces`de recursos.

Você pode usar alertas de métrica em logs populares de Log Analytics extraídos como métricas como parte das métricas de logs, incluindo recursos no Azure ou localmente. As soluções de Log Analytics com suporte estão listadas abaixo:

- [Contadores de desempenho](../../azure-monitor/platform/data-sources-performance-counters.md) para computadores Windows & Linux
- [Registros de pulsação para Integridade do Agente](../../azure-monitor/insights/solution-agenthealth.md)
- [Atualizar](../../automation/automation-update-management.md) registros de gerenciamento
- Logs de [dados de eventos](../../azure-monitor/platform/data-sources-windows-events.md)

Há muitos benefícios para usar **alertas de métricas para logs** em [alertas de log](../../azure-monitor/platform/alerts-log.md) baseados em consulta no Azure; alguns deles estão listados abaixo:

- Os alertas de métrica oferecem capacidade de monitoramento quase em tempo real e alertas de métrica para logs bifurca dados da fonte de log para garantir o mesmo.
- Os alertas de métricas são notificados apenas uma vez quando o alerta é disparado e uma vez quando o alerta é resolvido; em oposição aos alertas de log, que são sem estado e continuam sendo acionados em cada intervalo se a condição de alerta for atendida.
- Os alertas de métrica para o log fornecem várias dimensões, permitindo a filtragem de valores específicos, como computadores, tipo de sistema operacional, etc. mais simples; sem a necessidade de consulta escrevendo na análise.

> [!NOTE]
> Métrica e/ou dimensão específica serão mostradas somente se os dados para ele existirem no período escolhido. Essas métricas estão disponíveis para clientes com espaços de trabalho do Azure Log Analytics.

## <a name="metrics-and-dimensions-supported-for-logs"></a>Métricas e dimensões com suporte para logs

 Os alertas de métricas dão suporte a alertas para métricas que usam dimensões. Você pode usar dimensões para filtrar sua métrica para o nível certo. A lista completa de métricas com suporte para logs de [log Analytics espaços de trabalho](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces) está listada; entre soluções com suporte.

> [!NOTE]
> Para exibir as métricas com suporte para serem extraídas do espaço de trabalho Log Analytics por meio [de métricas de Azure monitor](../../azure-monitor/platform/metrics-charts.md); um alerta de métrica para o log deve ser criado para a métrica mencionada. As dimensões escolhidas no alerta de métrica para logs – só aparecerão para exploração por meio de métricas de Azure Monitor.

## <a name="creating-metric-alert-for-log-analytics"></a>Criando alerta de métrica para Log Analytics

Os dados de métrica de logs populares são canalizados antes de serem processados em Log Analytics, em métricas de Azure Monitor. Isso permite que os usuários aproveitem os recursos da plataforma de métrica, bem como o alerta de métrica, incluindo a presença de alertas com frequência de até 1 minuto.
Listados abaixo estão os meios de criar um alerta de métrica para logs.

## <a name="prerequisites-for-metric-alert-for-logs"></a>Pré-requisitos para o alerta de métrica para logs

Antes que a métrica dos logs coletados nos dados do Log Analytics funcione, o seguinte deve ser configurado e disponível:

1. **Espaço de trabalho do Active log Analytics**: um espaço de trabalho válido e ativo do log Analytics deve estar presente. Para obter mais informações, consulte [criar um log Analytics espaço de trabalho no portal do Azure](../../azure-monitor/learn/quick-create-workspace.md).
2. **Agente está configurado para log Analytics espaço de trabalho**: o agente precisa ser configurado para VMs do Azure (e/ou) VMS locais para enviar dados para o espaço de trabalho log Analytics usado na etapa anterior. Para obter mais informações, consulte [visão geral do agente de log Analytics](../../azure-monitor/platform/agents-overview.md).
3. As **soluções log Analytics com suporte estão instaladas**: a solução de log Analytics deve ser configurada e enviar dados para log Analytics soluções com suporte do espaço de trabalho são [contadores de desempenho para Windows & Linux](../../azure-monitor/platform/data-sources-performance-counters.md), [registros de pulsação para integridade do agente](../../azure-monitor/insights/solution-agenthealth.md), [Gerenciamento de atualizações](../../automation/automation-update-management.md)e [dados de eventos](../../azure-monitor/platform/data-sources-windows-events.md).
4. **Log Analytics soluções configuradas para enviar logs**: a solução log Analytics deve ter os logs/dados necessários correspondentes às [métricas com suporte para espaços de trabalho log Analytics](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces) habilitados. Por exemplo, para%, o contador de *memória disponível* deve ser configurado na solução de [contadores de desempenho](../../azure-monitor/platform/data-sources-performance-counters.md) primeiro.

## <a name="configuring-metric-alert-for-logs"></a>Configurando o alerta de métrica para logs

 Os alertas de métrica podem ser criados e gerenciados usando o portal do Azure, modelos do Resource Manager, API REST, PowerShell e CLI do Azure. Como os alertas de métrica para logs, é uma variante de alertas de métrica-depois que os pré-requisitos são concluídos, o alerta de métrica para logs pode ser criado para o espaço de trabalho Log Analytics especificado. Todas as características e funcionalidades dos [alertas de métrica](../../azure-monitor/platform/alerts-metric-near-real-time.md) também serão aplicáveis a alertas de métricas para logs. incluindo o esquema de carga, os limites de cota aplicáveis e o preço cobrado.

Para obter detalhes e exemplos passo a passo – consulte [criando e gerenciando alertas de métrica](https://aka.ms/createmetricalert). Especificamente, para alertas de métricas de logs-siga as instruções para gerenciar alertas de métrica e verifique o seguinte:

- O destino para o alerta de métrica é um *espaço de trabalho log Analytics* válido
- O sinal escolhido para o alerta de métrica para o *espaço de trabalho log Analytics* selecionado é do tipo **métrica**
- Filtrar por condições ou recursos específicos usando filtros de dimensão; as métricas para logs são multidimensionais
- Ao configurar a *lógica de sinal*, um único alerta pode ser criado para abranger vários valores de dimensão (como computador)
- Se **não** estiver usando portal do Azure para criar um alerta de métrica para o *espaço de trabalho log Analytics*selecionado; em seguida, o usuário deve primeiro criar manualmente uma regra explícita para converter dados de log em uma métrica usando [regras de consulta Azure monitor agendadas](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules).

> [!NOTE]
> Ao criar um alerta de métrica para Log Analytics espaço de trabalho por meio de portal do Azure regra correspondente para converter dados de log em métrica por meio de [regras de consulta de Azure monitor,](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) é automaticamente criada em segundo plano, *sem a necessidade de intervenção ou ação do usuário*. Para o alerta de métrica para a criação de logs usando meios diferentes de portal do Azure, consulte [modelo de recurso para alertas de métricas para logs](#resource-template-for-metric-alerts-for-logs) na seção exemplo de como criar um log baseado em ScheduledQueryRule para regra de conversão de métrica antes da criação de alerta de medição. não haverá dados para o alerta de métrica nos logs criados.

## <a name="resource-template-for-metric-alerts-for-logs"></a>Modelo de recurso para alertas de métrica para logs

Como mencionado anteriormente, o processo de criação de alertas de métricas de logs é dois pinos:

1. Criar uma regra para extrair métricas de logs com suporte usando a API do scheduledQueryRule
2. Criar um alerta de métrica para a métrica extraída do log (em etapa 1) e Log Analytics espaço de trabalho como um recurso de destino

### <a name="metric-alerts-for-logs-with-static-threshold"></a>Alertas de métrica para logs com limite estático

Para obter o mesmo, é possível usar o modelo de Azure Resource Manager de exemplo abaixo – em que a criação de um alerta de métrica de limite estático depende da criação bem-sucedida da regra para extrair métricas de logs via scheduledQueryRule.

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
                "description": "Full Resource ID of the resource emitting the metric that will be used for the comparison. For example /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroups/ResourceGroupName/providers/Microsoft.compute/virtualMachines/VM_xyz"
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

Digamos que o JSON acima seja salvo como metricfromLogsAlertStatic. JSON. em seguida, ele pode ser acoplado a um arquivo JSON de parâmetro para a criação baseada em modelo de recurso. Um arquivo JSON de parâmetro de exemplo é listado abaixo:

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

Supondo que o arquivo de parâmetro acima seja salvo como metricfromLogsAlertStatic. Parameters. JSON; em seguida, é possível criar um alerta de métrica para logs usando o [modelo de recurso para criação em portal do Azure](../../azure-resource-manager/resource-group-template-deploy-portal.md).

Como alternativa, também é possível usar o comando do Azure PowerShell abaixo:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile metricfromLogsAlertStatic.json TemplateParameterFile metricfromLogsAlertStatic.parameters.json
```

Ou use o modelo de recurso de implantação usando CLI do Azure:

```CLI
az group deployment create --resource-group myRG --template-file metricfromLogsAlertStatic.json --parameters @metricfromLogsAlertStatic.parameters.json
```

### <a name="metric-alerts-for-logs-with-dynamic-thresholds"></a>Alertas de métrica para logs com limites dinâmicos

Para obter o mesmo, é possível usar o modelo de Azure Resource Manager de exemplo abaixo – em que a criação de um alerta de métrica de limites dinâmicos depende da criação bem-sucedida da regra para extrair métricas de logs via scheduledQueryRule.

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
                "description": "Full Resource ID of the resource emitting the metric that will be used for the comparison. For example /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroups/ResourceGroupName/providers/Microsoft.compute/virtualMachines/VM_xyz"
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

Digamos que o JSON acima seja salvo como metricfromLogsAlertDynamic. JSON. em seguida, ele pode ser acoplado a um arquivo JSON de parâmetro para a criação baseada em modelo de recurso. Um arquivo JSON de parâmetro de exemplo é listado abaixo:

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

Supondo que o arquivo de parâmetro acima seja salvo como metricfromLogsAlertDynamic. Parameters. JSON; em seguida, é possível criar um alerta de métrica para logs usando o [modelo de recurso para criação em portal do Azure](../../azure-resource-manager/resource-group-template-deploy-portal.md).

Como alternativa, também é possível usar o comando do Azure PowerShell abaixo:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile metricfromLogsAlertDynamic.json TemplateParameterFile metricfromLogsAlertDynamic.parameters.json
```

Ou use o modelo de recurso de implantação usando CLI do Azure:

```CLI
az group deployment create --resource-group myRG --template-file metricfromLogsAlertDynamic.json --parameters @metricfromLogsAlertDynamic.parameters.json
```

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre os [alertas de métrica](alerts-metric.md).
- Saiba mais sobre os [alertas de log no Azure](../../azure-monitor/platform/alerts-unified-log.md).
- Saiba mais sobre [alertas no Azure](alerts-overview.md).
