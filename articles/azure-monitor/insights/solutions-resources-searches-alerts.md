---
title: Buscas guardadas em soluções de gestão Microsoft Docs
description: As soluções de gestão normalmente incluem consultas de registo guardadas para analisar os dados recolhidos pela solução. Este artigo descreve como definir log Analytics guardou pesquisas num modelo de Gestor de Recursos.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/29/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 61fc64e140af091b5ff3f631398daf901557791b
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77663033"
---
# <a name="adding-log-analytics-saved-searches-and-alerts-to-management-solution-preview"></a>Adicionar Log Analytics guardou pesquisas e alertas para a solução de gestão (Pré-visualização)

> [!IMPORTANT]
> Como [anunciado anteriormente](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/), log analytics workspace(s) criado após 1 de junho de *2019* - será capaz de gerir regras de alerta usando **apenas** o Azure scheduledQueryRules [REST API,](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/)Modelo de Gestor de [Recursos Azure](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-azure-resource-template) e [Cmdlet PowerShell.](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-powershell) Os clientes podem facilmente [mudar os seus meios preferidos de gestão de regras de alerta](../../azure-monitor/platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api) para espaços de trabalho mais antigos para alavancar o Programado Azure MonitorQueryRules como padrão e obter muitos novos [benefícios](../../azure-monitor/platform/alerts-log-api-switch.md#benefits-of-switching-to-new-azure-api) como a capacidade de usar cmdlets nativos powerShell, o aumento do tempo de retrospetiva nas regras, a criação de regras em grupo de recursos separados ou subscrição e muito mais.

> [!NOTE]
> Esta é uma documentação preliminar para a criação de soluções de gestão que estão atualmente em pré-visualização. Qualquer esquema descrito abaixo está sujeito a alterações.

[As soluções](solutions.md) de gestão normalmente incluem [pesquisas guardadas](../../azure-monitor/log-query/log-query-overview.md) no Log Analytics para analisar os dados recolhidos pela solução. Podem também definir [alertas](../../azure-monitor/platform/alerts-overview.md) para notificar o utilizador ou tomar automaticamente medidas em resposta a um problema crítico. Este artigo descreve como definir log Analytics guardou pesquisas e alertas num modelo de [Gestão](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) de Recursos para que possam ser incluídos em soluções de [gestão](solutions-creating.md).

> [!NOTE]
> As amostras deste artigo utilizam parâmetros e variáveis que são necessários ou comuns a soluções de gestão e descritas no [Design e constroem uma solução de gestão em Azure](solutions-creating.md)

## <a name="prerequisites"></a>Pré-requisitos
Este artigo assume que já está familiarizado com a forma de [criar uma solução](solutions-creating.md) de gestão e a estrutura de um modelo de Gestor de [Recursos](../../azure-resource-manager/templates/template-syntax.md) e ficheiro de solução.


## <a name="log-analytics-workspace"></a>Área de trabalho do Log Analytics
Todos os recursos em Log Analytics estão contidos num [espaço de trabalho.](../../azure-monitor/platform/manage-access.md) Tal como descrito na [conta log Analytics workspace e automação,](solutions.md#log-analytics-workspace-and-automation-account)o espaço de trabalho não está incluído na solução de gestão, mas deve existir antes de a solução ser instalada. Se não estiver disponível, a instalação da solução falha.

O nome do espaço de trabalho está em nome de cada recurso Log Analytics. Isto é feito na solução com o parâmetro espaço de **trabalho** como no exemplo seguinte de um recurso SavedSearch.

    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearchId'))]"

## <a name="log-analytics-api-version"></a>Versão API de Log Analytics
Todos os recursos do Log Analytics definidos num modelo de Gestor de Recursos têm uma **apiVersão** de propriedade que define a versão da API que o recurso deve usar.

A tabela seguinte lista a versão API para o recurso utilizado neste exemplo.

| Tipo de recurso | Versão API | Consulta |
|:---|:---|:---|
| savedSearches | Antevisão 2017-03-15 | Evento &#124; onde EventLevelName == "Error"  |


## <a name="saved-searches"></a>Pesquisas Guardadas
Inclua [pesquisas guardadas](../../azure-monitor/log-query/log-query-overview.md) numa solução para permitir que os utilizadores questionem os dados recolhidos pela sua solução. Pesquisas guardadas aparecem em **buscas guardadas** no portal Azure. É também necessária uma busca guardada para cada alerta.

Os recursos de [pesquisa guardados no Log Analytics](../../azure-monitor/log-query/log-query-overview.md) têm um tipo de `Microsoft.OperationalInsights/workspaces/savedSearches` e têm a seguinte estrutura. Isto inclui variáveis e parâmetros comuns para que possa copiar e colar este código no seu ficheiro de solução e alterar os nomes dos parâmetros.

    {
        "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name)]",
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches",
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "dependsOn": [
        ],
        "tags": { },
        "properties": {
            "etag": "*",
            "query": "[variables('SavedSearch').Query]",
            "displayName": "[variables('SavedSearch').DisplayName]",
            "category": "[variables('SavedSearch').Category]"
        }
    }

Cada propriedade de uma pesquisa guardada é descrita na tabela seguinte.

| Propriedade | Descrição |
|:--- |:--- |
| categoria | A categoria para a pesquisa guardada.  Quaisquer pesquisas guardadas na mesma solução muitas vezes partilharão uma única categoria para que estejam agrumoradas na consola. |
| DisplayName | Nome a exibir para a pesquisa guardada no portal. |
| consulta | Consulta para correr. |

> [!NOTE]
> Você pode precisar usar caracteres de fuga na consulta se incluir caracteres que podem ser interpretados como JSON. Por exemplo, se a sua consulta fosse **AzureActivity  OperationName:"Microsoft.Compute/virtualMachines/write",** deve ser escrito no ficheiro de solução como **AzureActivity  Nome de funcionamento:/\"Microsoft.Compute/virtualMachines/write\"** .

## <a name="alerts"></a>Alertas
Os [alertas De Log Azure](../../azure-monitor/platform/alerts-unified-log.md) são criados pelas regras do Alerta Azure que executam consultas de registo especificadas em intervalos regulares. Se os resultados da consulta corresponderem aos critérios especificados, é criado um registo de alerta e uma ou mais ações são executadas utilizando [grupos](../../azure-monitor/platform/action-groups.md)de ação .

Para os utilizadores que estendem os alertas para o Azure, as ações agora são controladas em grupos de ação do Azure. Quando um espaço de trabalho e os seus alertas são estendidos ao Azure, pode recuperar ou adicionar ações utilizando o Modelo de Gestor de [Recursos Azure](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).
As regras de alerta na solução de gestão do legado são compostas dos seguintes três recursos diferentes.

- **Busca guardada.** Define a pesquisa de registo que é executada. Várias regras de alerta podem partilhar uma única pesquisa guardada.
- **Horário.** Define a frequência com que a pesquisa de registo é executada. Cada regra de alerta tem um e apenas um horário.
- **Ação de alerta.** Cada regra de alerta tem um recurso de grupo de ação ou recurso de ação (legado) com um tipo de **Alerta** que define os detalhes do alerta, tais como os critérios para quando um registo de alerta é criado e a gravidade do alerta. [O](../../azure-monitor/platform/action-groups.md) recurso do grupo de ação pode ter uma lista de ações configuradas a tomar quando o alerta é disparado - como chamada de voz, SMS, e-mail, webhook, ferramenta ITSM, livro de execução de automação, app lógica, etc.

Os recursos de pesquisa guardados são descritos acima. Os outros recursos são descritos abaixo.

### <a name="schedule-resource"></a>Recurso de agenda

Uma pesquisa guardada pode ter um ou mais horários com cada horário representando uma regra de alerta separada. O horário define a frequência com que a pesquisa é executada e o intervalo de tempo sobre o qual os dados são recuperados. Os recursos de agendação têm um tipo de `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/` e têm a seguinte estrutura. Isto inclui variáveis e parâmetros comuns para que possa copiar e colar este código no seu ficheiro de solução e alterar os nomes dos parâmetros.

    {
        "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name, '/', variables('Schedule').Name)]",
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/",
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('SavedSearch').Name)]"
        ],
        "properties": {
            "etag": "*",
            "interval": "[variables('Schedule').Interval]",
            "queryTimeSpan": "[variables('Schedule').TimeSpan]",
            "enabled": "[variables('Schedule').Enabled]"
        }
    }
As propriedades para recursos de horário são descritas na tabela seguinte.

| Nome do elemento | Necessário | Descrição |
|:--|:--|:--|
| enabled       | Sim | Especifica se o alerta está ativado quando é criado. |
| intervalo      | Sim | Quantas vezes a consulta corre em minutos. |
| queryTimeSpan | Sim | Duração do tempo em minutos para avaliar resultados. |

O recurso de agenda deve depender da pesquisa guardada para que seja criado antes do horário.
> [!NOTE]
> O nome da agenda deve ser único num determinado espaço de trabalho; dois horários não podem ter o mesmo ID mesmo que estejam associados a diferentes pesquisas guardadas. Também o nome para todas as pesquisas, horários e ações criadas com a API log analytics devem estar em minúsculas.

### <a name="actions"></a>Ações
Uma agenda pode ter várias ações. Uma ação pode definir um ou mais processos para efetuar como enviar um email ou iniciar um runbook ou ele pode definir um limiar que determina quando os resultados de uma pesquisa corresponderem a critérios. Algumas ações definir ambos, para que os processos são executados quando o limiar for cumprido.
As ações podem ser definidas usando recursos ou recursos de ação [grupo de ação].

Existem dois tipos de recurso de ação especificados pela propriedade **Tipo.** Um horário requer uma ação **de Alerta,** que define os detalhes da regra de alerta e que ações são tomadas quando um alerta é criado. Os recursos de ação têm um tipo de `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions`.

As ações de alerta têm a seguinte estrutura. Isto inclui variáveis e parâmetros comuns para que possa copiar e colar este código no seu ficheiro de solução e alterar os nomes dos parâmetros.

```json
{
    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name, '/', variables('Schedule').Name, '/', variables('Alert').Name)]",
    "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
    "apiVersion": "[variables('LogAnalyticsApiVersion')]",
    "dependsOn": [
        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('SavedSearch').Name, '/schedules/', variables('Schedule').Name)]"
    ],
    "properties": {
        "etag": "*",
        "type": "Alert",
        "name": "[variables('Alert').Name]",
        "description": "[variables('Alert').Description]",
        "severity": "[variables('Alert').Severity]",
        "threshold": {
            "operator": "[variables('Alert').Threshold.Operator]",
            "value": "[variables('Alert').Threshold.Value]",
            "metricsTrigger": {
                "triggerCondition": "[variables('Alert').Threshold.Trigger.Condition]",
                "operator": "[variables('Alert').Trigger.Operator]",
                "value": "[variables('Alert').Trigger.Value]"
            },
        },
        "AzNsNotification": {
            "GroupIds": "[variables('MyAlert').AzNsNotification.GroupIds]",
            "CustomEmailSubject": "[variables('MyAlert').AzNsNotification.CustomEmailSubject]",
            "CustomWebhookPayload": "[variables('MyAlert').AzNsNotification.CustomWebhookPayload]"
        }
    }
}
```

As propriedades para os recursos de ação de alerta são descritas nas seguintes tabelas.

| Nome do elemento | Necessário | Descrição |
|:--|:--|:--|
| `type` | Sim | Tipo de ação.  Este é **alerta** para ações de alerta. |
| `name` | Sim | Nome a apresentar para o alerta.  Este é o nome que está exposto na consola para a regra de alerta. |
| `description` | Não | Descrição opcional do alerta. |
| `severity` | Sim | Gravidade do registo de alerta dos seguintes valores:<br><br> **crítico**<br>**aviso**<br>**informativo**

#### <a name="threshold"></a>Limiar
Esta secção é necessária. Define as propriedades para o limiar de alerta.

| Nome do elemento | Necessário | Descrição |
|:--|:--|:--|
| `Operator` | Sim | Operador para a comparação dos seguintes valores:<br><br>**gt = maior do que<br>lt = menos do que** |
| `Value` | Sim | O valor para comparar os resultados. |

##### <a name="metricstrigger"></a>MetricsTrigger
Esta secção é opcional. Inclua-o para um alerta de medição métrica.

| Nome do elemento | Necessário | Descrição |
|:--|:--|:--|
| `TriggerCondition` | Sim | Especifica se o limiar é para o número total de infrações ou infrações consecutivas dos seguintes valores:<br><br>**Total<br>Consecutiva** |
| `Operator` | Sim | Operador para a comparação dos seguintes valores:<br><br>**gt = maior do que<br>lt = menos do que** |
| `Value` | Sim | Número de vezes que os critérios devem ser cumpridos para desencadear o alerta. |


#### <a name="throttling"></a>Limitação
Esta secção é opcional. Inclua esta secção se quiser suprimir alertas da mesma regra durante algum tempo após a criação de um alerta.

| Nome do elemento | Necessário | Descrição |
|:--|:--|:--|
| DurationInMinutes | Sim, se o elemento throttling incluído | Número de minutos para suprimir alertas após a criação de um da mesma regra de alerta. |

#### <a name="azure-action-group"></a>Grupo de ação Azure
Todos os alertas no Azure, utilize o grupo de ação como o mecanismo predefinido para a manipulação de ações. Com o grupo de ação, pode especificar as suas ações de uma vez e, em seguida, associar o grupo de ação para múltiplos alertas - em todo o Azure. Sem a necessidade, repetidamente declarar as mesmas ações repetidamente. Os grupos de ação suportam várias ações - incluindo e-mail, SMS, chamada de voz, a ligação ITSM, Runbook de automatização, Webhook URI e muito mais.

Para o utilizador que tiver expandido o seus alertas no Azure - uma agenda já deve ter os detalhes do grupo de ação transmitidos juntamente com o limiar, para poder criar um alerta. Os detalhes do e-mail, URLs Webhook, detalhes da Automatização do Livro de Executa e outras Ações, precisam de ser definidos ao lado de um Grupo de Ação primeiro antes de criar um alerta; pode criar [o Grupo de Ação do Monitor Azure](../../azure-monitor/platform/action-groups.md) no Portal ou utilizar o Grupo de Ação - Modelo de [Recurso](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

| Nome do elemento | Necessário | Descrição |
|:--|:--|:--|
| AzNsNotification | Sim | O iD de recursos do grupo de ação Azure deve ser associado ao alerta para tomar as medidas necessárias quando os critérios de alerta são cumpridos. |
| CustomEmailSubject | Não | Linha de assunto personalizado do correio enviado para todos os endereços especificados em grupo de ação associado. |
| CustomWebhookPayload | Não | Carga útil personalizada a ser enviada para todos os pontos finais do webhook definidos em grupo de ação associado. O formato depende do que o webhook espera e deve ser um JSON serializado válido. |

## <a name="sample"></a>Sample

Segue-se uma amostra de uma solução que inclui os seguintes recursos:

- Pesquisa guardada
- Agenda
- Grupo de ação

A amostra utiliza variáveis de parâmetros de [solução padrão]( solutions-solution-file.md#parameters) que normalmente seriam usadas numa solução em oposição aos valores de codificação de dificuldade nas definições de recursos.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "Description": "Name of Log Analytics workspace"
            }
        },
        "workspaceregionId": {
            "type": "string",
            "metadata": {
                "Description": "Region of Log Analytics workspace"
            }
        },
        "actiongroup": {
            "type": "string",
            "metadata": {
                "Description": "List of action groups for alert actions separated by semicolon"
            }
        }
    },
    "variables": {
        "SolutionName": "MySolution",
        "SolutionVersion": "1.0",
        "SolutionPublisher": "Contoso",
        "ProductName": "SampleSolution",
        "LogAnalyticsApiVersion-Search": "2017-03-15-preview",
        "LogAnalyticsApiVersion-Solution": "2015-11-01-preview",
        "MySearch": {
            "displayName": "Error records by hour",
            "query": "MyRecord_CL | summarize AggregatedValue = avg(Rating_d) by Instance_s, bin(TimeGenerated, 60m)",
            "category": "Samples",
            "name": "Samples-Count of data"
        },
        "MyAlert": {
            "Name": "[toLower(concat('myalert-',uniqueString(resourceGroup().id, deployment().name)))]",
            "DisplayName": "My alert rule",
            "Description": "Sample alert. Fires when 3 error records found over hour interval.",
            "Severity": "critical",
            "ThresholdOperator": "gt",
            "ThresholdValue": 3,
            "Schedule": {
                "Name": "[toLower(concat('myschedule-',uniqueString(resourceGroup().id, deployment().name)))]",
                "Interval": 15,
                "TimeSpan": 60
            },
            "MetricsTrigger": {
                "TriggerCondition": "Consecutive",
                "Operator": "gt",
                "Value": 3
            },
            "ThrottleMinutes": 60,
            "AzNsNotification": {
                "GroupIds": [
                    "[parameters('actiongroup')]"
                ],
                "CustomEmailSubject": "Sample alert"
            }
        }
    },
    "resources": [
        {
            "name": "[concat(variables('SolutionName'), '[' ,parameters('workspacename'), ']')]",
            "location": "[parameters('workspaceRegionId')]",
            "tags": { },
            "type": "Microsoft.OperationsManagement/solutions",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Solution')]",
            "dependsOn": [
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches', parameters('workspacename'), variables('MySearch').Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Name)]"
            ],
            "properties": {
                "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspacename'))]",
                "referencedResources": [
                ],
                "containedResources": [
                    "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches', parameters('workspacename'), variables('MySearch').Name)]",
                    "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name)]",
                    "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Name)]"
                ]
            },
            "plan": {
                "name": "[concat(variables('SolutionName'), '[' ,parameters('workspaceName'), ']')]",
                "Version": "[variables('SolutionVersion')]",
                "product": "[variables('ProductName')]",
                "publisher": "[variables('SolutionPublisher')]",
                "promotionCode": ""
            }
        },
        {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Search')]",
            "dependsOn": [ ],
            "tags": { },
            "properties": {
                "etag": "*",
                "query": "[variables('MySearch').query]",
                "displayName": "[variables('MySearch').displayName]",
                "category": "[variables('MySearch').category]"
            }
        },
        {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/', variables('MyAlert').Schedule.Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Search')]",
            "dependsOn": [
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('MySearch').Name)]"
            ],
            "properties": {
                "etag": "*",
                "interval": "[variables('MyAlert').Schedule.Interval]",
                "queryTimeSpan": "[variables('MyAlert').Schedule.TimeSpan]",
                "enabled": true
            }
        },
        {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/', variables('MyAlert').Schedule.Name, '/', variables('MyAlert').Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Search')]",
            "dependsOn": [
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('MySearch').Name, '/schedules/', variables('MyAlert').Schedule.Name)]"
            ],
            "properties": {
                "etag": "*",
                "Type": "Alert",
                "Name": "[variables('MyAlert').DisplayName]",
                "Description": "[variables('MyAlert').Description]",
                "Severity": "[variables('MyAlert').Severity]",
                "Threshold": {
                    "Operator": "[variables('MyAlert').ThresholdOperator]",
                    "Value": "[variables('MyAlert').ThresholdValue]",
                    "MetricsTrigger": {
                        "TriggerCondition": "[variables('MyAlert').MetricsTrigger.TriggerCondition]",
                        "Operator": "[variables('MyAlert').MetricsTrigger.Operator]",
                        "Value": "[variables('MyAlert').MetricsTrigger.Value]"
                    }
                },
                "Throttling": {
                    "DurationInMinutes": "[variables('MyAlert').ThrottleMinutes]"
                },
                "AzNsNotification": {
                    "GroupIds": "[variables('MyAlert').AzNsNotification.GroupIds]",
                    "CustomEmailSubject": "[variables('MyAlert').AzNsNotification.CustomEmailSubject]"
                }
            }
        }
    ]
}
```

O ficheiro de parâmetro seguinte fornece valores de amostras para esta solução.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspacename": {
            "value": "myWorkspace"
        },
        "accountName": {
            "value": "myAccount"
        },
        "workspaceregionId": {
            "value": "East US"
        },
        "regionId": {
            "value": "East US 2"
        },
        "pricingTier": {
            "value": "Free"
        },
        "actiongroup": {
            "value": "/subscriptions/3b540246-808d-4331-99aa-917b808a9166/resourcegroups/myTestGroup/providers/microsoft.insights/actiongroups/sample"
        }
    }
}
```

## <a name="next-steps"></a>Passos seguintes
* [Adicione pontos de vista](solutions-resources-views.md) à sua solução de gestão.
* [Adicione livros de execução automation e outros recursos](solutions-resources-automation.md) à sua solução de gestão.
