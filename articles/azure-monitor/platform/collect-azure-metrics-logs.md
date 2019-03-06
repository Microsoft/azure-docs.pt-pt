---
title: Recolher registos do serviço do Azure e as métricas para o Log Analytics | Documentos da Microsoft
description: Configure diagnósticos em recursos do Azure para escrever registos e métricas para o Log Analytics.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 84105740-3697-4109-bc59-2452c1131bfe
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/12/2017
ms.author: magoedte
ms.openlocfilehash: ae4e4487b3d9df4b2cf756174f0a56e721af91c4
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57406915"
---
# <a name="collect-azure-service-logs-and-metrics-for-use-in-log-analytics"></a>Recolher registos de serviço do Azure e as métricas de utilização do Log Analytics

Existem quatro formas diferentes de recolha de registos e métricas para os serviços do Azure:

1. Direcionar o diagnóstico do Azure para o Log Analytics (*diagnóstico* na tabela a seguir)
2. Diagnóstico do Azure para o armazenamento do Azure para o Log Analytics (*armazenamento* na tabela a seguir)
3. Conectores para serviços do Azure (*conectores* na tabela a seguir)
4. Scripts para recolher e, em seguida, postar dados para o Log Analytics (em branco na tabela a seguir e para os serviços que não estão listados)


| Serviço                 | Tipo de Recurso                           | Registos        | Métricas     | Solução |
| --- | --- | --- | --- | --- |
| Gateways de aplicação    | Microsoft.Network/applicationGateways   | Diagnóstico | Diagnóstico | [Análise do Gateway de aplicação do Azure](../../azure-monitor/insights/azure-networking-analytics.md#azure-application-gateway-analytics-solution-in-log-analytics) |
| O Application insights    |                                         | Conector   | Conector   | [Conector do Application Insights](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/) (pré-visualização) |
| Contas de Automatização     | Microsoft.Automation/AutomationAccounts | Diagnóstico |             | [Mais informações](../../automation/automation-manage-send-joblogs-log-analytics.md)|
| Contas de Batch          | Microsoft.Batch/batchAccounts           | Diagnóstico | Diagnóstico | |
| Serviços cloud clássico  |                                         | Armazenamento     |             | [Mais informações](azure-storage-iis-table.md) |
| Serviços cognitivos      | Microsoft.CognitiveServices/accounts    |             | Diagnóstico | |
| Data Lake analytics     | Microsoft.DataLakeAnalytics/accounts    | Diagnóstico |             | |
| Arquivo do Data Lake         | Microsoft.DataLakeStore/accounts        | Diagnóstico |             | |
| Espaço de nomes do hub de eventos     | Microsoft.EventHub/namespaces           | Diagnóstico | Diagnóstico | |
| Hubs IoT                | Microsoft.Devices/IotHubs               |             | Diagnóstico | |
| Cofre de Chaves               | Microsoft.KeyVault/vaults               | Diagnóstico |             | [Análise do Cofre de chaves](../../azure-monitor/insights/azure-key-vault.md) |
| Balanceador de Carga          | Microsoft.Network/loadBalancers         | Diagnóstico |             |  |
| Aplicações Lógicas              | Microsoft.Logic/workflows <br> Microsoft.Logic/integrationAccounts | Diagnóstico | Diagnóstico | |
| Grupos de Segurança de Rede | Microsoft.Network/networksecuritygroups | Diagnóstico |             | [Análise do grupo de segurança de rede do Azure](../../azure-monitor/insights/azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-log-analytics) |
| Cofres de recuperação         | Microsoft.RecoveryServices/vaults       |             |             | [Análise (pré-visualização) de serviços de recuperação do Azure](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
| Procurar serviços         | Microsoft.Search/searchServices         | Diagnóstico | Diagnóstico | |
| Espaço de nomes do Service Bus   | Microsoft.ServiceBus/namespaces         | Diagnóstico | Diagnóstico | [Análise do Service Bus (pré-visualização)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
| Service Fabric          |                                         | Armazenamento     |             | [Análise do Service Fabric (pré-visualização)](../../service-fabric/service-fabric-diagnostics-oms-setup.md) |
| SQL (v12)               | Microsoft.Sql/servers/databases <br> Microsoft.Sql/servers/elasticPools |             | Diagnóstico | [Análise SQL do Azure (pré-visualização)](../../azure-monitor/insights/azure-sql.md) |
| Armazenamento                 |                                         |             | Script      | [Análise de armazenamento do Azure (pré-visualização)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution) |
| Virtual Machines        | Microsoft.Compute/virtualMachines       | Extensão   | Extensão <br> Diagnóstico  | |
| Conjuntos de dimensionamento de máquinas virtuais | Microsoft.Compute/virtualMachines <br> Microsoft.Compute/virtualMachineScaleSets/virtualMachines |             | Diagnóstico | |
| Farms de servidores Web        | Microsoft.Web/serverfarms               |             | Diagnóstico | |
| Web Sites               | Microsoft.Web/sites <br> Microsoft.Web/sites/slots |             | Diagnóstico | [Análise de aplicações Web do Azure (pré-visualização)](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-web-apps-analytics) |


> [!NOTE]
> Para monitorizar máquinas virtuais do Azure (Linux e Windows), recomendamos que instale o [extensão de VM do Log Analytics](../../azure-monitor/learn/quick-collect-azurevm.md). O agente fornece informações recolhidas a partir de suas máquinas virtuais. Também pode utilizar a extensão para conjuntos de dimensionamento de Máquina Virtual.
>
>

## <a name="azure-diagnostics-direct-to-log-analytics"></a>Direcionar o diagnóstico do Azure para o Log Analytics
Muitos recursos do Azure podem escrever os registos de diagnóstico e métricas de diretamente para o Log Analytics e esta é a maneira preferencial de recolha de dados para análise. Ao utilizar o diagnóstico do Azure, a dados são escritos imediatamente ao Log Analytics e não é necessário primeiro escrever os dados para o armazenamento.

Recursos do Azure que suportam [o Azure monitor](../../azure-monitor/overview.md) pode enviar os registos e métricas diretamente ao Log Analytics.

> [!NOTE]
> Atualmente, o envio de métricas multidimensionais para o Log Analytics através das definições de diagnóstico não é suportado. As métricas com dimensões são exportadas como métricas dimensionais simples e agregadas em valores de dimensões.
>
> *Por exemplo*: A métrica "Mensagens recebidas" num Hub de eventos pode ser explorada e representada um por nível de fila. No entanto, se for exportada através das definições de diagnóstico que a métrica é representada como todas as mensagens de entrada em todos os coloca na fila de eventos Hub.
>
>

* Para obter os detalhes das métricas disponíveis, consulte [suportado métricas com o Azure Monitor](../../azure-monitor/platform/metrics-supported.md).
* Para obter os detalhes dos registos disponíveis, consulte [suportada serviços e o esquema para os registos de diagnóstico](../../azure-monitor/platform/diagnostic-logs-schema.md).

### <a name="enable-diagnostics-with-powershell"></a>Ativar diagnósticos com o PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Tem de Novembro de 2016 (v2.3.0) ou versão posterior do [do Azure PowerShell](/powershell/azure/overview).

O exemplo de PowerShell seguinte mostra como usar [Set-AzDiagnosticSetting](/powershell/module/Az.Monitor/Set-AzDiagnosticSetting) para ativar o diagnóstico num grupo de segurança de rede. A mesma abordagem funciona para todos os recursos suportados - definir `$resourceId` para o id de recurso do recurso que pretende ativar o diagnóstico para.

```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO"

Set-AzDiagnosticSetting -ResourceId $ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="enable-diagnostics-with-resource-manager-templates"></a>Ativar o diagnóstico com modelos do Resource Manager

Para ativar o diagnóstico num recurso quando é criado, e que tenha o diagnóstico enviados para a área de trabalho do Log Analytics pode utilizar um modelo semelhante à abaixo. Este exemplo destina-se uma conta de automatização, mas funciona para todos os tipos de recurso suportados.

```json
        {
            "type": "Microsoft.Automation/automationAccounts/providers/diagnosticSettings",
            "name": "[concat(parameters('omsAutomationAccountName'), '/', 'Microsoft.Insights/service')]",
            "apiVersion": "2015-07-01",
            "dependsOn": [
                "[concat('Microsoft.Automation/automationAccounts/', parameters('omsAutomationAccountName'))]",
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspaceName'))]"
            ],
            "properties": {
                "workspaceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('omsWorkspaceName'))]",
                "logs": [
                    {
                        "category": "JobLogs",
                        "enabled": true
                    },
                    {
                        "category": "JobStreams",
                        "enabled": true
                    }
                ]
            }
        }
```

[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="azure-diagnostics-to-storage-then-to-log-analytics"></a>Diagnóstico do Azure para o armazenamento, em seguida, para o Log Analytics

Para recolher registos de dentro de alguns recursos, é possível enviar os registos para o armazenamento do Azure e, em seguida, configurar o Log Analytics para ler os registos de armazenamento.

O log Analytics pode utilizar esta abordagem para recolher diagnósticos de armazenamento do Azure para os registos e os seguintes recursos:

| Recurso | Registos |
| --- | --- |
| Service Fabric |ETWEvent <br> Eventos operacionais <br> Eventos de Reliable Actor <br> Eventos de Reliable Services |
| Virtual Machines |Linux Syslog <br> Eventos do Windows <br> Registo do IIS <br> ETWEvent do Windows |
| Funções da Web <br> Funções de trabalho |Linux Syslog <br> Eventos do Windows <br> Registo do IIS <br> ETWEvent do Windows |

> [!NOTE]
> São cobradas taxas normais de dados do Azure para armazenamento e transações quando enviar diagnósticos para uma conta de armazenamento e para quando o Log Analytics lê os dados da sua conta de armazenamento.
>
>

Ver [utilizar o armazenamento de BLOBs para o IIS e a tabela de armazenamento para eventos](azure-storage-iis-table.md) para saber mais sobre como o Log Analytics pode recolher estes registos.

## <a name="connectors-for-azure-services"></a>Conectores para serviços do Azure

Existe um conector do Application Insights, que permite que os dados recolhidos pelo Application Insights para serem enviados para o Log Analytics.

Saiba mais sobre o [conector do Application Insights](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/).

## <a name="scripts-to-collect-and-post-data-to-log-analytics"></a>Scripts para recolher e publicar dados no Log Analytics

Para serviços do Azure que não fornecem uma forma direta de enviar os registos e métricas para o Log Analytics pode utilizar um script de automatização do Azure para recolher os registos e métricas. O script, em seguida, pode enviar os dados para o Log Analytics utilizando o [API do recoletor de dados](../../azure-monitor/platform/data-collector-api.md)

A galeria do modelo do Azure tem [exemplos de como utilizar a automatização do Azure](https://azure.microsoft.com/resources/templates/?term=OMS) para recolher dados de serviços e enviá-la para o Log Analytics.

## <a name="next-steps"></a>Passos Seguintes

* [Utilizar o armazenamento de BLOBs para o IIS e a tabela de armazenamento para eventos](azure-storage-iis-table.md) para ler os registos para esse diagnóstico de escrita para armazenamento de tabelas ou de registos do IIS escritos para armazenamento de BLOBs de serviços do Azure.
* [Ativar soluções](../../azure-monitor/insights/solutions.md) para fornecer informações sobre os dados.
* [Utilizar consultas de pesquisa](../../azure-monitor/log-query/log-query-overview.md) para analisar os dados.
