---
title: Criação de alertas métricos para registos no Monitor Azure
description: Tutorial sobre a criação de alertas métricos em tempo quase real sobre dados populares de análise de registo.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 09/17/2018
ms.subservice: alerts
ms.openlocfilehash: 9f61c62bc4960cb8e5bff31f84bd73a14240b613
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78250717"
---
# <a name="create-metric-alerts-for-logs-in-azure-monitor"></a>Criar alertas métricos para registos no Monitor Azure

## <a name="overview"></a>Descrição geral

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

O Azure Monitor suporta o tipo de [alerta métrico](../../azure-monitor/platform/alerts-metric-near-real-time.md) que tem benefícios sobre os [alertas clássicos.](../../azure-monitor/platform/alerts-classic-portal.md) As métricas estão disponíveis para [uma grande lista de serviços Azure.](../../azure-monitor/platform/metrics-supported.md) Este artigo explica o uso de um subconjunto (isto é) para recursos - `Microsoft.OperationalInsights/workspaces`.

Pode utilizar alertas métricos em registos populares de Log Analytics extraídos como métricas como parte de Métricas de Registos, incluindo recursos em Azure ou no local. As soluções de Log Analytics suportadas estão listadas abaixo:

- [Contadores de desempenho](../../azure-monitor/platform/data-sources-performance-counters.md) para máquinas Windows & Linux
- [Registos de batimentos cardíacos para agente de saúde](../../azure-monitor/insights/solution-agenthealth.md)
- [Registos de gestão de atualizações](../../automation/automation-update-management.md)
- Registos de dados do [evento](../../azure-monitor/platform/data-sources-windows-events.md)

Existem muitos benefícios para a utilização **de Alertas Métricos para Registos** sobre alertas de [registo baseados](../../azure-monitor/platform/alerts-log.md) em consulta em Azure; alguns deles estão listados abaixo:

- Os Alertas Métricos oferecem capacidade de monitorização em tempo quase real e alertas métricos para os dados de garfos de logs de origem para garantir o mesmo.
- Os Alertas Métricos são audais - apenas notificando uma vez quando o alerta é disparado e uma vez quando o alerta é resolvido; em oposição aos alertas de Registo, que são apátridas e continuam a disparar a cada intervalo se a condição de alerta for satisfeita.
- Os alertas métricos para o Registo proporcionam múltiplas dimensões, permitindo a filtragem a valores específicos como Computadores, Tipo OS, etc. mais simples; sem a necessidade de escrever consulta em análise.

> [!NOTE]
> A métrica e/ou dimensão específicasó serão mostradas se existirem dados para o mesmo no período escolhido. Estas métricas estão disponíveis para clientes com espaços de trabalho Azure Log Analytics.

## <a name="metrics-and-dimensions-supported-for-logs"></a>Métricas e dimensões suportadas para registos

 Alertas métricos suportam alertamento para métricas que usam dimensões. Pode utilizar dimensões para filtrar a sua métrica para o nível certo. A lista completa de métricas suportadas para Registos de espaços de [trabalho de Log Analytics](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces) está listada; através de soluções apoiadas.

> [!NOTE]
> Para ver métricas suportadas para ser extraído do espaço de trabalho Log Analytics via [Monitor Azure - Métricas;](../../azure-monitor/platform/metrics-charts.md) deve ser criado um alerta métrico para o log para a referida métrica. As dimensões escolhidas em Alerta Métrico para registos - só aparecerão para exploração via Monitor Azure - Métricas.

## <a name="creating-metric-alert-for-log-analytics"></a>Criação de alerta métrico para Log Analytics

Os dados métricos de registos populares são canalizados antes de ser processado no Log Analytics, no Monitor Azure - Métricas. Isto permite que os utilizadores aproveitem as capacidades da plataforma Métrica, bem como o alerta métrico - incluindo ter alertas com frequência tão baixa como 1 minuto.
Abaixo estão os meios de elaboração de um alerta métrico para os registos.

## <a name="prerequisites-for-metric-alert-for-logs"></a>Pré-requisitos para alerta métrico para registos

Antes de a Métrica de Registos recolhida nos dados do Log Analytics funcionar, devem ser configurados e disponíveis os seguintes dados:

1. Espaço de **trabalho ative Log Analytics Workspace**: Deve estar presente um espaço de trabalho válido e ativo do Log Analytics. Para mais informações, consulte Criar um espaço de trabalho de [Log Analytics no portal Azure](../../azure-monitor/learn/quick-create-workspace.md).
2. **O agente está configurado para log analytics workspace**: O agente precisa de ser configurado para VMs Azure (e/ou) vMs no local para enviar dados para o Log Analytics Workspace usado em etapas anteriores. Para mais informações, consulte [Log Analytics - Agent Overview](../../azure-monitor/platform/agents-overview.md).
3. **As soluções de Log Analytics suportadas estão instaladas**: A solução Log Analytics deve ser configurada e enviar dados para o espaço de trabalho do Log Analytics - as soluções suportadas são contadores de [desempenho para Windows & Linux,](../../azure-monitor/platform/data-sources-performance-counters.md) [registos heartbeat para a Saúde do Agente,](../../azure-monitor/insights/solution-agenthealth.md) [gestão](../../automation/automation-update-management.md)de atualizações e dados do [Evento.](../../azure-monitor/platform/data-sources-windows-events.md)
4. **Soluções de Log Analytics configuradas para enviar registos**: A solução Log Analytics deve ter os registos/dados [necessários correspondentes às métricas suportadas para espaços](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces) de trabalho de Log Analytics ativados. Por exemplo, para o contador *de memória disponível %* deve ser configurado em primeiro lugar na solução de contadores de [desempenho.](../../azure-monitor/platform/data-sources-performance-counters.md)

## <a name="configuring-metric-alert-for-logs"></a>Configuração de alerta métrico para registos

 Os alertas métricos podem ser criados e geridos através do portal Azure, Modelos de Gestor de Recursos, Rest API, PowerShell e Azure CLI. Uma vez que os Alertas Métricos para Registos, é uma variante de alertas métricos - uma vez que os pré-requisitos são feitos, o alerta métrico para os registos pode ser criado para o espaço de trabalho especificado log Analytics. Todas as características e funcionalidades dos [alertas métricos](../../azure-monitor/platform/alerts-metric-near-real-time.md) serão aplicáveis também a alertas métricos para registos; incluindo esquema de carga útil, limites de quota aplicáveis e preço faturado.

Para obter detalhes passo a passo e amostras - consulte [a criação e gestão de alertas métricos](https://aka.ms/createmetricalert). Especificamente, para Alertas Métricos para Registos - siga as instruções para gerir alertas métricos e certifique-se do seguinte:

- O alvo para alerta métrico é um espaço de trabalho válido log *Analytics*
- Sinal escolhido para alerta métrico para espaço de trabalho selecionado log *Analytics* é do tipo **Métrico**
- Filtrar para condições específicas ou recurso utilizando filtros de dimensão; métricas para registos são multidimensionais
- Ao configurar a Lógica do *Sinal,* pode ser criado um único alerta para abranger vários valores de dimensão (como computador)
- Se **não** utilizar o portal Azure para criar um alerta métrico para o espaço de trabalho selecionado do *Log Analytics;* em seguida, o utilizador deve criar manualmente uma regra explícita para converter dados de registo numa métrica utilizando [o Monitor Azure - Regras de Consulta Agendadas](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules).

> [!NOTE]
> Ao criar um alerta métrico para o espaço de trabalho log Analytics via portal Azure - regra correspondente para converter dados de registo em métricavia [Monitor Azure - Regras](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) de consulta agendadas são automaticamente criadas em segundo plano, *sem necessidade de qualquer intervenção ou ação do utilizador*. Para alerta métrico para a criação de registos utilizando outros meios que não o portal Azure, consulte o [Modelo de Recurso para Alertas Métricos para a](#resource-template-for-metric-alerts-for-logs) secção de Registos na secção de amostras de criação de um registo baseado em ScheduledQueryRule para a regra de conversão métrica antes da criação de alerta métrico - caso contrário não haverá dados para o alerta métrico sobre os registos criados.

## <a name="resource-template-for-metric-alerts-for-logs"></a>Modelo de recurso para alertas métricos para registos

Como referido anteriormente, o processo de criação de alertas métricos a partir de registos é de dois empinados:

1. Criar uma regra para extrair métricas de registos suportados utilizando a API agendada
2. Criar um alerta métrico para métrica extraída do log (no passo1) e log Analytics espaço de trabalho como recurso-alvo

### <a name="metric-alerts-for-logs-with-static-threshold"></a>Alertas métricos para registos com limiar estático

Para conseguir o mesmo, pode-se utilizar a amostra Modelo de Gestor de Recursos Azure abaixo - onde a criação de um alerta métrico de limiar estático depende da criação bem-sucedida da regra para extrair métricas de registos através do ScheduledQueryRule.

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

Diga que o JSON acima é guardado como metricfromLogsAlertStatic.json - então pode ser associado com um ficheiro JSON parâmetro para criação baseada em modelo de recurso. Um ficheiro JSON do parâmetro de amostra está listado abaixo:

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

Assumindo que o ficheiro de parâmetro acima é guardado como metricfromLogsAlertStatic.parameters.json; em seguida, pode-se criar um alerta métrico para os registos usando o Modelo de [Recurso para a criação no portal Azure](../../azure-resource-manager/templates/deploy-portal.md).

Alternativamente, pode-se utilizar o comando Azure Powershell abaixo também:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile metricfromLogsAlertStatic.json TemplateParameterFile metricfromLogsAlertStatic.parameters.json
```

Ou utilize o modelo de recurso de implantação utilizando o AZURE CLI:

```azurecli
az group deployment create --resource-group myRG --template-file metricfromLogsAlertStatic.json --parameters @metricfromLogsAlertStatic.parameters.json
```

### <a name="metric-alerts-for-logs-with-dynamic-thresholds"></a>Alertas métricos para logs com limiares dinâmicos

Para conseguir o mesmo, pode-se utilizar a amostra Modelo de Gestor de Recursos Azure abaixo - onde a criação de um alerta métrico Dynamic Thresholds depende da criação bem-sucedida da regra de extração de métricas de registos através do scheduledQueryRule.

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

Diga que o JSON acima é guardado como metricfromLogsAlertDynamic.json - então pode ser associado com um ficheiro JSON parâmetro para criação baseada em modelo de recurso. Um ficheiro JSON do parâmetro de amostra está listado abaixo:

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

Assumindo que o ficheiro de parâmetro acima é guardado como metricfromLogsAlertDynamic.parameters.json; em seguida, pode-se criar um alerta métrico para os registos usando o Modelo de [Recurso para a criação no portal Azure](../../azure-resource-manager/templates/deploy-portal.md).

Alternativamente, pode-se utilizar o comando Azure Powershell abaixo também:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile metricfromLogsAlertDynamic.json TemplateParameterFile metricfromLogsAlertDynamic.parameters.json
```

Ou utilize o modelo de recurso de implantação utilizando o AZURE CLI:

```azurecli
az group deployment create --resource-group myRG --template-file metricfromLogsAlertDynamic.json --parameters @metricfromLogsAlertDynamic.parameters.json
```

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre os [alertas métricos.](alerts-metric.md)
- Saiba mais sobre alertas de [log em Azure.](../../azure-monitor/platform/alerts-unified-log.md)
- Saiba mais sobre [alertas em Azure.](alerts-overview.md)
