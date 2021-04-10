---
title: Monitorize e diagnostice recipientes windows
description: Neste tutorial, configurar registos do Azure Monitor para monitorização e diagnóstico de recipientes Windows no Azure Service Fabric.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: b7689d6e259055137a8d1d3c61552790ab9f28d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100588236"
---
# <a name="tutorial-monitor-windows-containers-on-service-fabric-using-azure-monitor-logs"></a>Tutorial: Monitorize recipientes windows em tecido de serviço usando registos do Monitor Azure

Esta é a terceira parte de um tutorial, e acompanha-o através da configuração de registos do Azure Monitor para monitorizar os seus contentores Windows orquestrados no Service Fabric.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configurar registos do Monitor Azure para o seu cluster de tecido de serviço
> * Utilizar uma área de trabalho do Log Analytics para ver e consultar os registos de contentores e nós
> * Configurar o agente do Log Analytics para recolher métricas de contentores e nós

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, tem de:

* Tem um cluster no Azure ou [criar um com este tutorial](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* [Implementar uma aplicação contentorizada no mesmo](service-fabric-host-app-in-a-container.md)

## <a name="setting-up-azure-monitor-logs-with-your-cluster-in-the-resource-manager-template"></a>Configurar registos do Monitor Azure com o seu cluster no modelo de Gestor de Recursos

Caso tenha utilizado o [modelo disponibilizado](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) na primeira parte deste tutorial, o mesmo deveria incluir as adições seguintes aos modelos do Azure Resource Manager genéricos do Service Fabric. Caso tenha um aglomerado próprio que pretenda configurar para monitorizar contentores com registos do Azure Monitor:

* Faça as alterações seguintes ao modelo do Resource Manager.
* Implemente-o com o PowerShell para atualizar o cluster mediante a [implementação do modelo](./service-fabric-cluster-creation-via-arm.md). O Azure Resource Manager apercebe-se de que o recurso existe, pelo que o implementa como uma atualização.

### <a name="adding-azure-monitor-logs-to-your-cluster-template"></a>Adicionar registos do Monitor Azure ao seu modelo de cluster

Faça as alterações seguintes ao *template.json*:

1. Adicione a localização e o nome da área de trabalho do Log Analytics à secção *parameters*:

    ```json
    "omsWorkspacename": {
      "type": "string",
      "defaultValue": "[toLower(concat('sf',uniqueString(resourceGroup().id)))]",
      "metadata": {
        "description": "Name of your Log Analytics Workspace"
      }
    },
    "omsRegion": {
      "type": "string",
      "defaultValue": "East US",
      "allowedValues": [
        "West Europe",
        "East US",
        "Southeast Asia"
      ],
      "metadata": {
        "description": "Specify the Azure Region for your Log Analytics workspace"
      }
    }
    ```

    Para alterar um dos dois valores utilizados, adicione os mesmos parâmetros ao *template.parameters.json* e altere os valores utilizados aí.

2. Adicione o nome da solução e a solução a *variables*:

    ```json
    "omsSolutionName": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "omsSolution": "ServiceFabric"
    ```

3. Adicione o Microsoft Monitoring Agent como uma extensão da máquina virtual. Encontre o recurso de escala de máquina virtual: *recursos*  >  *"apiversion": "[variáveis('vmssApiVersion')]]"*. Sob as *propriedades*  >  *virtualMachineProfile*  >  *extensõesProfile*  >  *extensões*, adicione a seguinte descrição de extensão sob a extensão *ServiceFabricNode:* 
    
    ```json
    {
        "name": "[concat(variables('vmNodeType0Name'),'OMS')]",
        "properties": {
            "publisher": "Microsoft.EnterpriseCloud.Monitoring",
            "type": "MicrosoftMonitoringAgent",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "workspaceId": "[reference(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')), '2015-11-01-preview').customerId]"
            },
            "protectedSettings": {
                "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')),'2015-11-01-preview').primarySharedKey]"
            }
        }
    },
    ```

4. Adicione a área de trabalho do Log Analytics como recurso individual. Em *resources*, a seguir ao recurso de conjuntos de dimensionamento de máquinas virtuais, adicione o seguinte:

    ```json
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[parameters('omsWorkspacename')]",
        "type": "Microsoft.OperationalInsights/workspaces",
        "properties": {
            "sku": {
                "name": "Free"
            }
        },
        "resources": [
            {
                "apiVersion": "2015-11-01-preview",
                "name": "[concat(variables('applicationDiagnosticsStorageAccountName'),parameters('omsWorkspacename'))]",
                "type": "storageinsightconfigs",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]",
                    "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
                ],
                "properties": {
                    "containers": [ ],
                    "tables": [
                        "WADServiceFabric*EventTable",
                        "WADWindowsEventLogsTable",
                        "WADETWEventTable"
                    ],
                    "storageAccount": {
                        "id": "[resourceId('Microsoft.Storage/storageaccounts/', variables('applicationDiagnosticsStorageAccountName'))]",
                        "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('applicationDiagnosticsStorageAccountName')),'2015-06-15').key1]"
                    }
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "name": "System",
                "type": "datasources",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
                ],
                "kind": "WindowsEvent",
                "properties": {
                    "eventLogName": "System",
                    "eventTypes": [
                        {
                            "eventType": "Error"
                        },
                        {
                            "eventType": "Warning"
                        },
                        {
                            "eventType": "Information"
                        }
                    ]
                }
            }
        ]
    },
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[variables('omsSolutionName')]",
        "type": "Microsoft.OperationsManagement/solutions",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('OMSWorkspacename'))]"
        ],
        "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        },
        "plan": {
            "name": "[variables('omsSolutionName')]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('omsSolution'))]",
            "promotionCode": ""
        }
    },
    ```

Está disponível [aqui](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/d2ffa318581fc23ac7f1b0ab2b52db1a0d7b4ba7/5-VM-Windows-OMS-UnSecure/sfclusteroms.json) um modelo de exemplo (utilizado na primeira parte do tutorial) que tem todas estas alterações e que pode consultar sempre que necessário. Estas alterações adicionarão uma área de trabalho do Log Analytics ao seu grupo de recursos. A área de trabalho será configurada para recolher eventos da plataforma do Service Fabric a partir das tabelas de armazenamento configuradas com o agente [Diagnóstico do Microsoft Azure](service-fabric-diagnostics-event-aggregation-wad.md). O agente do Log Analytics (Microsoft Monitoring Agent) também foi adicionado a cada nó do seu cluster como extensão de máquina virtual, o que significa que, à medida que dimensiona o cluster, o agente é configurado automaticamente em cada máquina e ligado à mesma área de trabalho.

Implemente o modelo com as alterações novas para atualizar o seu cluster atual. Deverá consultar os recursos de análise de registo no seu grupo de recursos uma vez concluído. Quando o cluster estiver pronto, implemente a aplicação contentorizada no mesmo. No próximo passo, vamos configurar a monitorização dos contentores.

## <a name="add-the-container-monitoring-solution-to-your-log-analytics-workspace"></a>Adicionar a solução de Monitorização de Contentores à sua área de trabalho do Log Analytics

Para configurar a solução de Contentores na sua área de trabalho, procure *Solução de Monitorização de Contentores* e crie um recurso Contentores (na categoria Monitorização + Gestão).

![Adicionar a solução de Contentores](./media/service-fabric-tutorial-monitoring-wincontainers/containers-solution.png)

Quando solicitado para o *espaço de trabalho Log Analytics,* selecione o espaço de trabalho criado no seu grupo de recursos e selecione **Criar**. Isto adiciona uma *solução de monitorização de contentores* ao seu espaço de trabalho, iniciando o agente Log Analytics implantado pelo modelo para começar a recolher registos e estatísticas de estivadores.

Navegue de volta para o *grupo de recursos*, onde deverá ver agora a solução de monitorização acabada de adicionar. Se selecionar, a página de aterragem deve mostrar-lhe o número de imagens de contentores que tem em execução.

*Note que fizemos cinco instâncias do recipiente fabrikam [da segunda parte](service-fabric-host-app-in-a-container.md) do tutorial*

![Página de destino da solução de contentor](./media/service-fabric-tutorial-monitoring-wincontainers/solution-landing.png)

A seleção da **Solução monitor de contentores** irá levá-lo a um dashboard mais detalhado, que lhe permite percorrer vários painéis, bem como executar consultas em registos do Monitor Azure.

Uma vez que o agente recolhe registos do Docker, mostra *stdout* e *stderr* como predefinição. Se deslocar horizontalmente, verá o inventário de imagem do contentor, o estado, as métricas e as consultas de amostras que poderá executar para obter dados mais úteis.

![Dashboard da solução de Contentores](./media/service-fabric-tutorial-monitoring-wincontainers/container-metrics.png)

Clicar em qualquer um destes painéis irá levá-lo à consulta Kusto que está a gerar o valor apresentado. Mude a consulta *\** para ver todos os diferentes tipos de registos que estão a ser recolhidos. Aqui, pode consultar ou filtrar por desempenho do contentor ou por registos ou ver eventos da plataforma do Service Fabric. Os agentes também estão constantemente a emitir um heartbeat de cada nó e que pode ver para confirmar que ainda estão a ser recolhidos dados das suas máquinas, caso a configuração do seu cluster se altere.

![Consulta do contentor](./media/service-fabric-tutorial-monitoring-wincontainers/query-sample.png)

## <a name="configure-log-analytics-agent-to-pick-up-performance-counters"></a>Configurar o agente do Log Analytics para recolher contadores de desempenho

Outro benefício da utilização do agente Log Analytics é a capacidade de alterar os contadores de desempenho que pretende captar através da experiência UI de análise de registo, em vez de ter de configurar o agente de diagnóstico Azure e fazer uma atualização baseada no modelo do Gestor de Recursos de cada vez. Para isso, selecione no **espaço de trabalho OMS** na página de aterragem da sua solução de Monitorização de Contentores (ou Tecido de Serviço).

Desta forma, é encaminhado para a área de trabalho do Log Analytics, onde pode ver as suas soluções, criar dashboards personalizados e configurar o agente do Log Analytics. 
* Selecione **Definições Avançadas** para abrir o menu Definições Avançadas.
* Selecione Servidores Windows **de fontes**  >  **conectadas** para verificar se tem *5 computadores Windows ligados*.
* Selecione   >  **contadores de desempenho do Windows de dados** para procurar e adicione novos contadores de desempenho. Aqui verá uma lista de recomendações dos registos do Azure Monitor para balcões de desempenho que pode recolher, bem como a opção de procurar outros contadores. Verifique se os contadores **Processador(_Total)\% de Tempo do Processador** e **Memória(*)\MBytes Disponíveis** estão a ser recolhidos.

**Atualize** a Solução de Monitorização de Contadores passados alguns minutos e, depois, deverá começar a receber dados de *Desempenho do Computador*. Isto ajuda a compreender de que forma é que os seus recursos estão a ser atualizados. Também pode utilizar estas métricas para tomar decisões adequadas relativamente ao dimensionamento do seu cluster ou para confirmar se um cluster está a balancear a sua carga conforme esperado.

*Nota: certifique-se de que os seus filtros de hora estão definidos corretamente para poder consumir estas métricas.*

![Contadores de desempenho 2](./media/service-fabric-tutorial-monitoring-wincontainers/perf-counters2.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Configurar registos do Monitor Azure para o seu cluster de tecido de serviço
> * Utilizar uma área de trabalho do Log Analytics para ver e consultar os registos de contentores e nós
> * Configurar o agente do Log Analytics para recolher métricas de contentores e nós

Agora que tem uma monitorização configurada para a sua aplicação contentorizada, tente:

* Configurar registos do Monitor Azure para um cluster Linux, seguindo passos semelhantes a este tutorial. Veja [este modelo](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeType-Secure-OMS) para fazer alterações ao seu modelo do Resource Manager.
* Configurar registos do Azure Monitor para [configurar alertas automatizados](../azure-monitor/alerts/alerts-overview.md) para ajudar na deteção e diagnóstico.
* Explorar a lista de [contadores de desempenho recomendados](service-fabric-diagnostics-event-generation-perf.md) do Service Fabric a configurar para os seus clusters.
* Familiarize-se com as funcionalidades [de pesquisa e consulta](../azure-monitor/logs/log-query-overview.md) de registo oferecidas como parte dos registos do Azure Monitor.
