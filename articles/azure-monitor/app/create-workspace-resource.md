---
title: Criar um novo recurso baseado no espaço de trabalho do Azure Monitor Insights Insights Microsoft Docs
description: Conheça os passos necessários para permitir os novos recursos baseados no espaço de trabalho do Azure Monitor Application Insights.
author: mrbullwinkle
ms.author: mbullwin
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 65c2220bd3e9ea93e562b256e84796aed9046d00
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83211411"
---
# <a name="workspace-based-application-insights-resources-preview"></a>Recursos de Insights de Aplicação baseados no espaço de trabalho (pré-visualização)

Os recursos baseados no espaço de trabalho suportam a integração completa entre os Insights de Aplicação e o Log Analytics. Agora pode optar por enviar a sua telemetria De Insights de Aplicação para um espaço de trabalho comum no Log Analytics, que lhe permite o acesso total a todas as funcionalidades do Log Analytics, mantendo os registos de aplicações, infraestruturas e plataformas num único local consolidado.

Isto também permite um controlo comum de acesso baseado em funções (RBAC) em todos os seus recursos, e elimina a necessidade de consultas cruzadas/espaço de trabalho.

> [!NOTE]
> A ingestão e retenção de dados para os recursos de Insights de Aplicação baseados no espaço de trabalho são faturados através do espaço de trabalho Log Analytics onde os dados estão localizados. [Saiba mais]( https://docs.microsoft.com/azure/azure-monitor/app/pricing#workspace-based-application-insights) sobre a faturação dos recursos de Insights de Aplicação baseados no espaço de trabalho.

Para testar a nova experiência, inscreva-se no [portal Azure](https://portal.azure.com)e crie um recurso Devisão de Aplicações:

![Recurso de Insights de Aplicação baseado no espaço de trabalho](./media/create-workspace-resource/create-workspace-based.png)

Se ainda não tiver um espaço de trabalho de Log Analytics existente, [consulte a documentação de criação do espaço de trabalho Log Analytics.](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)

Para a pré-visualização pública, os recursos baseados no espaço de **trabalho estão atualmente limitados a West US 2, East US e South Central US.**

Assim que o seu recurso for criado, verá a informação correspondente do espaço de trabalho no painel **de visão geral:**

![Nome da Área de Trabalho](./media/create-workspace-resource/workspace-name.png)

Clicar no texto de link azul irá levá-lo ao espaço de trabalho associado log Analytics onde você pode aproveitar o novo ambiente de consulta de espaço de trabalho unificado.

> [!NOTE]
> Ainda fornecemos compatibilidade total para o seu aplicativo Insights consultas de recursos clássicos, livros de trabalho e alertas baseados em log dentro da experiência Application Insights. Para consultar/ver contra a [nova estrutura/esquema](apm-tables.md) de mesa baseado no espaço de trabalho, tem de navegar primeiro para o seu espaço de trabalho Log Analytics. Durante a pré-visualização, a seleção de **Registos** a partir dos paineles de Insights de Aplicação dar-lhe-á acesso à experiência de consulta clássica de Insights de Aplicação.

## <a name="copy-the-connection-string"></a>Copiar a cadeia de ligação

A [cadeia de ligação]() identifica o recurso com o que pretende associar os seus dados de telemetria. Também permite modificar os pontos finais que o seu recurso utilizará como destino para a sua telemetria. Terá de copiar a cadeia de ligação e adicioná-la ao código da sua aplicação ou a uma variável ambiental.

![Clique e copie a chave de instrumentação](./media/create-new-resource/instrumentation-key.png)

## <a name="monitoring-configuration"></a>Configuração de monitorização

Uma vez criado um recurso de Insights de Aplicação baseado no espaço de trabalho, a monitorização de configuração é relativamente simples.

### <a name="code-based-application-monitoring"></a>Monitorização de aplicações baseada em código

Para a monitorização da aplicação baseada em códigos, basta instalar o SDK de Insights de Aplicação apropriado e apontá-lo para a chave de instrumentação ou cadeia de ligação ao seu recurso recém-criado.  

Para documentação detalhada sobre a criação de um SDK de insights de aplicação para monitorização baseada em códigos, consulte a documentação específica de linguagem/enquadramento:

- [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)
- [núcleo ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)
- [Tarefas de fundo & aplicações modernas de consola (.NET/.NET Core)](https://docs.microsoft.com/azure/azure-monitor/app/worker-service)
- [Aplicações clássicas de consola (.NET)](https://docs.microsoft.com/azure/azure-monitor/app/console) 
- [Java](https://docs.microsoft.com/azure/azure-monitor/app/java-get-started?tabs=maven)
- [JavaScript](https://docs.microsoft.com/azure/azure-monitor/app/javascript)
- [Node.js](https://docs.microsoft.com/azure/azure-monitor/app/nodejs)
- [Python](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python)

### <a name="codeless-monitoring-and-visual-studio-resource-creation"></a>Monitorização sem código e criação de recursos do Estúdio Visual

Para uma monitorização sem código de serviços como o Azure Functions e o Azure App Services, também terá de criar primeiro o seu recurso de Insights de Aplicação baseado no espaço de trabalho e, em seguida, apontar para esse recurso durante a fase de configuração de monitorização.

Embora estes serviços ofereçam a opção de criar um novo recurso Application Insights dentro do seu próprio processo de criação de recursos, os recursos criados através destas opções de UI estão atualmente restritos à experiência clássica de Insights de Aplicação.

O mesmo se aplica à experiência de criação de recursos da Application Insights no Visual Studio para ASP.NET e ASP.NET Core. Deve selecionar um recurso baseado no espaço de trabalho existente a partir do Visual Studio monitoring UI. A seleção criar novos recursos dentro do Visual Studio irá limitá-lo a criar um recurso clássico da Application Insights.

## <a name="creating-a-resource-automatically"></a>Criar um recurso automaticamente

### <a name="azure-cli"></a>CLI do Azure

Para aceder aos comandos de pré-visualização Insights Azure CLI, primeiro é necessário executar:

```azurecli
 az extension add -n application-insights
```

Se não executar o `az extension add` comando, verá uma mensagem de erro que diz:`az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

Agora pode executar o seguinte para criar o seu recurso Application Insights:

```azurecli
az monitor app-insights component create --app
                                         --location
                                         --resource-group
                                         [--application-type]
                                         [--ingestion-access {Disabled, Enabled}]
                                         [--kind]
                                         [--only-show-errors]
                                         [--query-access {Disabled, Enabled}]
                                         [--tags]
                                         [--workspace]
```

#### <a name="example"></a>Exemplo

```azurecli
az monitor app-insights component create --app demoApp --location eastus --kind web -g my_resource_group --workspace "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/test1234/providers/microsoft.operationalinsights/workspaces/test1234555"
```

Para obter a documentação completa do Azure CLI para este comando, consulte a [documentação Azure CLI](https://docs.microsoft.com/cli/azure/ext/application-insights/monitor/app-insights/component?view=azure-cli-latest#ext-application-insights-az-monitor-app-insights-component-create).

### <a name="azure-powershell"></a>Azure PowerShell

O `New-AzApplicationInsights` comando PowerShell não suporta atualmente a criação de um recurso de Insights de Aplicação baseado no espaço de trabalho. Para criar um recurso baseado no espaço de trabalho com o PowerShell, pode utilizar os modelos do Gestor de Recursos Azure abaixo e implantar com o PowerShell.

### <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager

#### <a name="template-file"></a>Ficheiro de modelo

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "string"
        },
        "type": {
            "type": "string"
        },
        "regionId": {
            "type": "string"
        },
        "tagsArray": {
            "type": "object"
        },
        "requestSource": {
            "type": "string"
        },
        "workspaceResourceId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "microsoft.insights/components",
            "location": "[parameters('regionId')]",
            "tags": "[parameters('tagsArray')]",
            "apiVersion": "2020-02-02-preview",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Application_Type": "[parameters('type')]",
                "Flow_Type": "Redfield",
                "Request_Source": "[parameters('requestSource')]",
                "WorkspaceResourceId": "[parameters('workspaceResourceId')]"
            }
        }
    ]
}
```

#### <a name="parameters-file"></a>Arquivo de parâmetros

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "type": {
            "value": "web"
        },
        "name": {
            "value": "customresourcename"
        },
        "regionId": {
            "value": "eastus"
        },
        "tagsArray": {
            "value": {}
        },
        "requestSource": {
            "value": "Custom"
        },
        "workspaceResourceId": {
            "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my_resource_group/providers/microsoft.operationalinsights/workspaces/myworkspacename"
        }
    }
}

```

## <a name="modifying-the-associated-workspace"></a>Modificação do espaço de trabalho associado

Uma vez criado um recurso de Insights de Aplicação baseado no espaço de trabalho, pode modificar o espaço de trabalho associado do Log Analytics Workspace.

A partir do painel de recursos Da Aplicação Insights, **selecione Properties**  >  **Change Workspace**Log Analytics  >  **Workspaces**

## <a name="next-steps"></a>Passos seguintes

* [Explorar métricas](../../azure-monitor/platform/metrics-charts.md)
* [Escrever consultas da Análise](../../azure-monitor/app/analytics.md)

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[metrics]: ../../azure-monitor/platform/metrics-charts.md
[start]: ../../azure-monitor/app/app-insights-overview.md
