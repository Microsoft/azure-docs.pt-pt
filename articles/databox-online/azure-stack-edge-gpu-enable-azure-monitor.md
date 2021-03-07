---
title: Ativar o Monitor Azure no dispositivo GPU Azure Stack Edge Pro
description: Descreve como ativar o Azure Monitor num dispositivo GPU Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: c2f66895fccd14dcffd8c5570f1d5f46933090aa
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102439192"
---
# <a name="enable-azure-monitor-on-your-azure-stack-edge-pro-gpu-device"></a>Ativar o Monitor Azure no seu dispositivo GPU Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

A monitorização de recipientes no seu dispositivo GPU Azure Stack Edge Pro é fundamental, especialmente quando está a executar várias aplicações de computação. O Azure Monitor permite-lhe recolher registos de contentores e métricas de memória e processador do cluster Kubernetes em execução no seu dispositivo.

Este artigo descreve os passos necessários para ativar o Azure Monitor no seu dispositivo e recolher registos de contentores no espaço de trabalho do Log Analytics. A loja de métricas Azure Monitor não é suportada atualmente com o seu dispositivo GPU Azure Stack Edge Pro. 


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, vai precisar de:

- Um dispositivo Azure Stack Edge Pro. Certifique-se de que o dispositivo está ativado de acordo com os passos do [Tutorial: Ative o seu dispositivo](azure-stack-edge-gpu-deploy-activate.md).
- Completou o passo **de computação Configure** de acordo com o [Tutorial: Conigure computo no seu dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-configure-compute.md) no seu dispositivo. O seu dispositivo deve ter um recurso IoT Hub associado, um dispositivo IoT e um dispositivo IoT Edge.


## <a name="create-log-analytics-workspace"></a>Criar uma área de trabalho do Log Analytics

Tome os seguintes passos para criar um espaço de trabalho de análise de log. Um espaço de trabalho de análise de registo é uma unidade de armazenamento lógica onde os dados de registo são recolhidos e armazenados.

1. No portal Azure, selecione **+ Crie um recurso** e procure por Log Analytics **Workspace** e, em seguida, selecione **Criar**. 
1. No **espaço de trabalho Create Log Analytics,** configufique as seguintes definições. Aceite o restante como padrão.

    1. No separador **Básicos,** forneça a subscrição, grupo de recursos, nome e região para o espaço de trabalho. 

        ![Separador básico para log analytics espaço de trabalho](media/azure-stack-edge-gpu-enable-azure-monitor/create-log-analytics-workspace-basics-1.png)  

    1. No **separador de nível de preços,** aceite o **plano de pagamento** padrão.

        ![Separador de preços para log analytics espaço de trabalho](media/azure-stack-edge-gpu-enable-azure-monitor/create-log-analytics-workspace-pricing-1.png) 

    1. No **separador 'Rever + Criar',** rever as informações para o seu espaço de trabalho e selecionar **Criar.**

        ![Comentário + Criar para log analytics espaço de trabalho](media/azure-stack-edge-gpu-enable-azure-monitor/create-log-analytics-workspace-review-create-1.png)

Para mais informações, consulte os passos detalhados na [Criação de um espaço de trabalho Log Analytics através do portal Azure.](../azure-monitor/logs/quick-create-workspace.md)



## <a name="enable-container-insights"></a>Permitir insights de contentores

Tome os seguintes passos para permitir informações sobre o recipiente no seu espaço de trabalho. 

1. Siga os passos detalhados na [solução Como adicionar a solução de Recipientes monitores Azure](../azure-monitor/containers/container-insights-hybrid-setup.md#how-to-add-the-azure-monitor-containers-solution). Utilize o seguinte ficheiro de `containerSolution.json` modelo:

    ```yml
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceResourceId": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace Resource ID"
            }
        },
        "workspaceRegion": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace region"
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "name": "[Concat('ContainerInsights', '-',  uniqueString(parameters('workspaceResourceId')))]",
            "apiVersion": "2017-05-10",
            "subscriptionId": "[split(parameters('workspaceResourceId'),'/')[2]]",
            "resourceGroup": "[split(parameters('workspaceResourceId'),'/')[4]]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "apiVersion": "2015-11-01-preview",
                            "type": "Microsoft.OperationsManagement/solutions",
                            "location": "[parameters('workspaceRegion')]",
                            "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                            "properties": {
                                "workspaceResourceId": "[parameters('workspaceResourceId')]"
                            },
                            "plan": {
                                "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                                "product": "[Concat('OMSGallery/', 'ContainerInsights')]",
                                "promotionCode": "",
                                "publisher": "Microsoft"
                            }
                        }
                    ]
                },
                "parameters": {}
            }
            }
        ]
    }
    ```

1. Obtenha a identificação e a localização do recurso. Aceda a `Your Log Analytics workspace > General > Properties`. Copiar as seguintes informações:

    - **ID de recurso**, que é o ID de recurso Azure totalmente qualificado do espaço de trabalho Azure Log Analytics. 
    - **localização**, que é a região de Azure.

    ![Propriedades do espaço de trabalho Log Analytics](media/azure-stack-edge-gpu-enable-azure-monitor/log-analytics-workspace-properties-1.png) 

1. Utilize o seguinte ficheiro de `containerSolutionParams.json` parâmetros. `workspaceResourceId`Substitua-o pelo ID do recurso e `workspaceRegion` pela localização copiada no passo anterior.

    ```yaml
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        "workspaceResourceId": {
            "value": "/subscriptions/fa68082f-8ff7-4a25-95c7-ce9da541242f/resourcegroups/myaserg/providers/microsoft.operationalinsights/workspaces/myaseloganalyticsws"
        },
        "workspaceRegion": {
        "value": "westus"
        }
        }
    }
    ```
    
    Aqui está uma saída de amostra de um espaço de trabalho Log Analytics com Insights de Contentores habilitados:
    
    ```powershell
    Requesting a Cloud Shell.Succeeded.
    Connecting terminal...
    MOTD: Switch to Bash from PowerShell: bash
    VERBOSE: Authenticating to Azure ...
    VERBOSE: Building your Azure drive ...
    
    PS /home/alpa> az account set -s fa68082f-8ff7-4a25-95c7-ce9da541242f
    PS /home/alpa> ls
    clouddrive  containerSolution.json
    PS /home/alpa> ls
    clouddrive  containerSolution.json  containerSolutionParams.json
    PS /home/alpa> az deployment group create --resource-group myaserg --name Testdeployment1 --template-file containerSolution.json --parameters containerSolutionParams.json
    {- Finished ..
        "id": "/subscriptions/fa68082f-8ff7-4a25-95c7-ce9da541242f/resourceGroups/myaserg/providers/Microsoft.Resources/deployments/Testdeployment1",
        "location": null,
        "name": "Testdeployment1",
        "properties": {
        "correlationId": "3a9045fe-2de0-428c-b17b-057508a8c575",
        "debugSetting": null,
        "dependencies": [],
        "duration": "PT11.1588316S",
        "error": null,
        "mode": "Incremental",
        "onErrorDeployment": null,
        "outputResources": [
            {
            "id": "/subscriptions/fa68082f-8ff7-4a25-95c7-ce9da541242f/resourceGroups/myaserg/providers/Microsoft.OperationsManagement/solutions/ContainerInsights(myaseloganalyticsws)",
            "resourceGroup": "myaserg"
            }
        ],
        "outputs": null,
        "parameters": {
            "workspaceRegion": {
            "type": "String",
            "value": "westus"
            },
            "workspaceResourceId": {
            "type": "String",
            "value": "/subscriptions/fa68082f-8ff7-4a25-95c7-ce9da541242f/resourcegroups/myaserg/providers/microsoft.operationalinsights/workspaces/myaseloganalyticsws"
            }
        },
        "parametersLink": null,
        "providers": [
            {
            "id": null,
            "namespace": "Microsoft.Resources",
            "registrationPolicy": null,
            "registrationState": null,
            "resourceTypes": [
                {
                "aliases": null,
                "apiProfiles": null,
                "apiVersions": null,
                "capabilities": null,
                "defaultApiVersion": null,
                "locations": [
                    null
                ],
                "properties": null,
                "resourceType": "deployments"
                }
            ]
            }
        ],
        "provisioningState": "Succeeded",
        "templateHash": "10500027184662969395",
        "templateLink": null,
        "timestamp": "2020-11-06T22:09:56.908983+00:00",
        "validatedResources": null
        },
        "resourceGroup": "myaserg",
        "tags": null,
        "type": "Microsoft.Resources/deployments"
    }
    PS /home/alpa>
    ```

## <a name="configure-azure-monitor-on-your-device"></a>Configure Monitor Azure no seu dispositivo

1. Vá ao recém-criado Recurso De Análise de Log e copie a **chave de ID** e **Primário do** Espaço de Trabalho (chave do espaço de trabalho).

    ![Gestão de agentes no espaço de trabalho Log Analytics](media/azure-stack-edge-gpu-enable-azure-monitor/log-analytics-workspace-agents-management-1.png)

    Guarde esta informação pois a utilizará num passo posterior.

1. [Ligue-se à interface PowerShell do dispositivo](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface). 
    
1. Utilize a chave workspace de análise de registo e espaço de trabalho com o seguinte cmdlet:

    `Set-HcsKubernetesAzureMonitorConfiguration -WorkspaceId <> -WorkspaceKey <>`

1. Depois de ativar o Monitor Azure, deverá ver registos no espaço de trabalho do Log Analytics. Para visualizar o estado do cluster Kubernetes implantado no seu dispositivo, aceda ao **Azure Monitor > Insights > Containers**. Para a opção ambiente, selecione **All**. 

    ![Métricas no espaço de trabalho Log Analytics](media/azure-stack-edge-gpu-enable-azure-monitor/log-analytics-workspace-metrics-1.png)

## <a name="next-steps"></a>Passos seguintes

- Saiba como monitorizar as [cargas de trabalho da Kubernetes através do Painel kubernetes.](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md)
- Saiba como gerir notificações de [alerta de eventos do dispositivo](azure-stack-edge-gpu-manage-device-event-alert-notifications.md). 