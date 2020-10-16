---
title: Criar um novo recurso baseado no espaço de trabalho do Azure Monitor Insights Microsoft Docs
description: Conheça os passos necessários para permitir os novos recursos baseados no espaço de trabalho do Azure Monitor Application Insights.
ms.topic: conceptual
ms.date: 10/06/2020
ms.openlocfilehash: 9b91e5065729bee6af3f8018c36930e132eb1a15
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/12/2020
ms.locfileid: "91945418"
---
# <a name="workspace-based-application-insights-resources"></a>Recursos de Insights de Aplicação baseados no espaço de trabalho

Os recursos baseados no espaço de trabalho suportam a integração total entre o Application Insights e o Log Analytics. Pode agora optar por enviar a telemetria Application Insights para um espaço de trabalho comum do Log Analytics, que lhe permite ter acesso total a todas as funcionalidades do Log Analytics, mantendo os registos de aplicações, infraestruturas e plataformas num único local consolidado.

Isto também permite que o Controle de Acesso Role-Based comum (RBAC) através dos seus recursos, e elimina a necessidade de consultas cruzadas/espaço de trabalho.

> [!NOTE]
> A ingestão de dados e a retenção de recursos de Insights de Aplicação baseados no espaço de trabalho são faturados através do espaço de trabalho Log Analytics onde os dados estão localizados. [Saiba mais]( ./pricing.md#workspace-based-application-insights) sobre a faturação dos recursos de Aplicações Insights baseados no espaço de trabalho.

## <a name="new-capabilities"></a>Novas funcionalidades

O Workspace Application Insights permite-lhe tirar partido das mais recentes capacidades do Azure Monitor e do Log Analytics, incluindo:

* [As Chaves Geridas pelo Cliente (CMK)](../platform/customer-managed-keys.md) fornecem encriptação em repouso para os seus dados com chaves de encriptação às quais só tem acesso.
* [O Azure Private Link](../platform/private-link-security.md) permite-lhe ligar de forma segura os serviços Azure PaaS à sua rede virtual utilizando pontos finais privados.
* [Bring Your Own Storage (BYOS) for Profiler and Snapshot Debugger](./profiler-bring-your-own-storage.md) dá-lhe total controlo sobre a política de encriptação em repouso, a política de gestão vitalícia e o acesso à rede para todos os dados associados ao Profiler Application Insights e ao Snapshot Debugger. 
* [Os níveis de Reserva de Capacidade](../platform/manage-cost-storage.md#pricing-model) permitem-lhe economizar até 25% em comparação com o preço Pay-As-You-Go. 
* Ingestão de dados mais rápida via Log Analytics streaming ingestão.

## <a name="create-workspace-based-resource"></a>Criar recursos baseados no espaço de trabalho

Inscreva-se no [portal Azure](https://portal.azure.com)e crie um recurso Application Insights:

![Recurso de Insights de Aplicação baseado em espaço de trabalho](./media/create-workspace-resource/create-workspace-based.png)

Se ainda não tiver um espaço de trabalho log analytics existente, [consulte a documentação de criação do espaço de trabalho Log Analytics](../learn/quick-create-workspace.md).

**Os recursos baseados no espaço de trabalho estão atualmente disponíveis em todas as regiões comerciais e no Governo de Azure**

Uma vez criado o seu recurso, verá as informações correspondentes do espaço de trabalho no **painel de visão** geral:

![Nome da Área de Trabalho](./media/create-workspace-resource/workspace-name.png)

Clicar no texto de ligação azul irá levá-lo ao espaço de trabalho associado do Log Analytics onde poderá tirar partido do novo ambiente de consulta de espaço de trabalho unificado.

> [!NOTE]
> Ainda fornecemos compatibilidade total para trás para as suas consultas clássicas de recursos, livros de trabalho e alertas baseados em registos dentro da experiência Application Insights. Para consultar/ver contra a [nova estrutura/esquema de mesa baseada no espaço de trabalho,](apm-tables.md) tem primeiro de navegar para o seu espaço de trabalho Log Analytics. A seleção de **Registos (Analytics)** a partir dos painéis de Insights de Aplicação irá dar-lhe acesso à experiência clássica de consulta de Insights de Aplicação.

## <a name="copy-the-connection-string"></a>Copiar a cadeia de ligação

A [cadeia de ligação](./sdk-connection-string.md?tabs=net) identifica o recurso com o que pretende associar os seus dados de telemetria. Também permite modificar os pontos finais que o seu recurso utilizará como destino para a sua telemetria. Terá de copiar o fio de ligação e adicioná-lo ao código da sua aplicação ou a uma variável ambiental.

## <a name="monitoring-configuration"></a>Configuração de monitorização

Uma vez criado um recurso de Insights de Aplicação baseado no espaço de trabalho, a monitorização configurada é relativamente simples.

### <a name="code-based-application-monitoring"></a>Monitorização de aplicações baseadas em código

Para uma monitorização de aplicações baseada em código, basta instalar o SDK de Insights de Aplicação apropriado e apontá-lo para a chave de instrumentação ou cadeia de ligação ao seu recurso recém-criado.  

Para documentação detalhada sobre a criação de um SDK de Insights de Aplicação para monitorização baseada em código, consulte a documentação específica da linguagem/quadro:

- [ASP.NET](./asp-net.md)
- [Núcleo ASP.NET ](./asp-net-core.md)
- [Tarefas de fundo & aplicações de consola modernas (.NET/.NET Core)](./worker-service.md)
- [Aplicações clássicas de consola (.NET)](./console.md) 
- [Rio Java ](./java-get-started.md?tabs=maven)
- [JavaScript](./javascript.md)
- [Node.js](./nodejs.md)
- [Python](./opencensus-python.md)

### <a name="codeless-monitoring-and-visual-studio-resource-creation"></a>Monitorização sem código e criação de recursos do Estúdio Visual

Para uma monitorização codificada de serviços como Azure Functions e Azure App Services, também terá de criar primeiro o seu recurso de Insights de Aplicação baseado no espaço de trabalho e, em seguida, apontar para esse recurso durante a fase de configuração de monitorização.

Embora estes serviços ofereçam a opção de criar um novo recurso Application Insights dentro do seu próprio processo de criação de recursos, os recursos criados através destas opções de UI estão atualmente restritos à experiência clássica de Application Insights.

O mesmo se aplica à experiência de criação de recursos Application Insights no Visual Studio para ASP.NET e ASP.NET Core. Tem de selecionar um recurso baseado no espaço de trabalho existente a partir do UI de monitorização do Estúdio Visual. Selecionar criar um novo recurso a partir do Estúdio Visual irá limitá-lo a criar um recurso clássico de Application Insights.

## <a name="creating-a-resource-automatically"></a>Criar um recurso automaticamente

### <a name="azure-cli"></a>CLI do Azure

Para aceder aos comandos de Pré-visualização Do CLI do Application Insights, primeiro tem de executar:

```azurecli
 az extension add -n application-insights
```

Se não executar o `az extension add` comando, verá uma mensagem de erro que diz: `az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

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

Para obter a documentação completa do Azure CLI para este comando, consulte a documentação do [Azure CLI](/cli/azure/ext/application-insights/monitor/app-insights/component?view=azure-cli-latest#ext-application-insights-az-monitor-app-insights-component-create).

### <a name="azure-powershell"></a>Azure PowerShell

O `New-AzApplicationInsights` comando PowerShell não suporta atualmente a criação de um recurso de Insights de Aplicação baseado no espaço de trabalho. Para criar um recurso baseado no espaço de trabalho com o PowerShell, pode utilizar os modelos Azure Resource Manager abaixo e implementar com o PowerShell.

### <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager

#### <a name="template-file"></a>Arquivo de modelo

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

Uma vez criado um recurso de Insights de Aplicação baseado no espaço de trabalho, pode modificar o espaço de trabalho associado do Log Analytics.

A partir do painel de recursos do Application Insights, selecione **Propriedades**  >  **Change Workspace**  >  **Analytics Workspace Workspace**

## <a name="export-telemetry"></a>Telemetria de exportação

A funcionalidade de exportação contínua do legado não é suportada por recursos baseados no espaço de trabalho. Em vez disso, selecione **Definições de Diagnóstico**  >  **adicionar definição** de diagnóstico a partir do seu recurso Application Insights. Pode selecionar todas as tabelas ou um subconjunto de tabelas para arquivar numa conta de armazenamento ou para transmitir para um Azure Event Hub.

> [!NOTE]
> Atualmente, não existem encargos adicionais para a exportação de telemetria. As informações sobre os preços desta funcionalidade estarão disponíveis na página de preços do [Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).  Antes do início da faturação, serão enviadas notificações. Caso opte por continuar a utilizar <feature name> após o período de pré-aviso, será cobrado à taxa aplicável. 
 

## <a name="next-steps"></a>Próximos passos

* [Explorar métricas](../platform/metrics-charts.md)
* [Escrever consultas da Análise](../log-query/log-query-overview.md)