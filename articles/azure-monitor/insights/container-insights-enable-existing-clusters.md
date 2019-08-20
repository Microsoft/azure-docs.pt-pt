---
title: Monitorar um cluster do AKS (serviço kubernetes do Azure) implantado | Microsoft Docs
description: Saiba como habilitar o monitoramento de um cluster AKS (serviço kubernetes do Azure) com Azure Monitor para contêineres já implantados em sua assinatura.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/19/2019
ms.author: magoedte
ms.openlocfilehash: 650729269370bfcd6608b82fc14c3306da1ed222
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624440"
---
# <a name="enable-monitoring-of-azure-kubernetes-service-aks-cluster-already-deployed"></a>Habilitar o monitoramento do cluster AKS (serviço kubernetes do Azure) já implantado

Este artigo descreve como configurar Azure Monitor para contêineres para monitorar o cluster kubernetes gerenciado hospedado no [serviço kubernetes do Azure](https://docs.microsoft.com/azure/aks/) que já foi implantado em sua assinatura.

Você pode habilitar o monitoramento de um cluster AKS que já está implantado usando um dos métodos com suporte:

* CLI do Azure
* Terraform
* [De Azure monitor](#enable-from-azure-monitor-in-the-portal) ou [diretamente do cluster AKs](#enable-directly-from-aks-cluster-in-the-portal) no portal do Azure 
* Com o [modelo de Azure Resource Manager fornecido](#enable-using-an-azure-resource-manager-template) usando o cmdlet `New-AzResourceGroupDeployment` Azure PowerShell ou com CLI do Azure. 

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com). 

## <a name="enable-using-azure-cli"></a>Ativar a CLI do Azure

O passo seguinte permite o monitoramento do cluster do AKS com a CLI do Azure. Neste exemplo, não tem de criar por ou especificar uma área de trabalho existente. Este comando simplifica o processo para si através da criação de uma área de trabalho padrão no grupo de recursos predefinido da subscrição de cluster do AKS, se já não existir na região.  O espaço de trabalho padrão criado assemelha-se ao formato de *defaultworkspace\<-\<GUID de >-region >* .  

```azurecli
az aks enable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG  
```

A saída terá a seguinte aparência:

```azurecli
provisioningState       : Succeeded
```

Se teria, em vez disso, integrar com uma área de trabalho existente, utilize o seguinte comando para especificar essa área de trabalho.

```azurecli
az aks enable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG --workspace-resource-id <ExistingWorkspaceResourceID> 
```

A saída terá a seguinte aparência:

```azurecli
provisioningState       : Succeeded
```

## <a name="enable-using-terraform"></a>Ativar com o Terraform

1. Adicionar a **oms_agent** perfil de suplemento para o existente [azurerm_kubernetes_cluster recursos](https://www.terraform.io/docs/providers/azurerm/d/kubernetes_cluster.html#addon_profile)

   ```
   addon_profile {
    oms_agent {
      enabled                    = true
      log_analytics_workspace_id = "${azurerm_log_analytics_workspace.test.id}"
     }
   }
   ```

2. Adicionar a [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html) seguindo os passos na documentação do Terraform.

## <a name="enable-from-azure-monitor-in-the-portal"></a>Habilitar de Azure Monitor no portal 

Para ativar a monitorização do seu cluster do AKS no portal do Azure do Azure Monitor, faça o seguinte:

1. No portal do Azure, selecione **Monitor**. 

2. Selecione **contentores** da lista.

3. Sobre o **Monitor - contentores** página, selecione **clusters não monitorizados**.

4. Na lista de clusters não monitorizado, encontrar o contentor na lista e clique em **ativar**.   

5. Sobre o **integração para o Azure Monitor para contentores** página, se tiver um existentes do Log Analytics área de trabalho na mesma subscrição que o cluster, selecione-o na lista pendente.  
    A lista preselects a área de trabalho predefinida e a localização que o contentor do AKS é implementado na subscrição. 

    ![Ativar a monitorização de informações de contentor do AKS](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >Se quiser criar uma nova área de trabalho do Log Analytics para armazenar os dados de monitorização do cluster, siga as instruções em [criar uma área de trabalho do Log Analytics](../../azure-monitor/learn/quick-create-workspace.md). Certifique-se de que criar a área de trabalho na mesma subscrição que está implementado o contentor do AKS. 
 
Depois de ativar a monitorização, poderá demorar cerca de 15 minutos antes de poder visualizar as métricas de estado de funcionamento para o cluster. 

## <a name="enable-directly-from-aks-cluster-in-the-portal"></a>Habilitar diretamente do cluster AKS no portal

Para habilitar o monitoramento diretamente de um dos seus clusters AKS no portal do Azure, faça o seguinte:

1. No portal do Azure, selecione **Todos os serviços**. 

2. Na lista de recursos, comece a escrever **contentores**.  Os filtros de lista com base na sua entrada. 

3. Selecione **serviços de Kubernetes**.  

    ![A ligação de serviços do Kubernetes](./media/container-insights-onboard/portal-search-containers-01.png)

4. Na lista de contentores, selecione um contentor.

5. Na página de descrição geral do contentor, selecione **contentores de Monitor**.  

6. Sobre o **integração para o Azure Monitor para contentores** página, se tiver um existentes do Log Analytics área de trabalho na mesma subscrição que o cluster, selecione-o na lista pendente.  
    A lista preselects a área de trabalho predefinida e a localização que o contentor do AKS é implementado na subscrição. 

    ![Ativar a monitorização de estado de funcionamento de contentor do AKS](./media/container-insights-onboard/kubernetes-onboard-brownfield-02.png)

    >[!NOTE]
    >Se quiser criar uma nova área de trabalho do Log Analytics para armazenar os dados de monitorização do cluster, siga as instruções em [criar uma área de trabalho do Log Analytics](../../azure-monitor/learn/quick-create-workspace.md). Certifique-se de que criar a área de trabalho na mesma subscrição que está implementado o contentor do AKS. 
 
Depois de ativar a monitorização, poderá demorar cerca de 15 minutos antes de poder visualizar dados operacionais para o cluster. 

## <a name="enable-using-an-azure-resource-manager-template"></a>Habilitar usando um modelo de Azure Resource Manager

Esse método inclui dois modelos JSON. Um modelo especifica a configuração para ativar a monitorização e a outra contém valores de parâmetros que pode configurar para especificar o seguinte:

* O ID de recurso de contentor do AKS. 
* O grupo de recursos que o cluster é implementado no.

>[!NOTE]
>O modelo precisa ser implantado no mesmo grupo de recursos do cluster.
>

O espaço de trabalho do Log Analytics deve ser criado antes de habilitar o monitoramento usando Azure PowerShell ou a CLI. Para criar a área de trabalho, pode configurá-lo por meio [do Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md), da funcionalidade [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json), ou no [portal do Azure](../../azure-monitor/learn/quick-create-workspace.md).

Se não estiver familiarizado com o conceito de implementar recursos com um modelo, consulte:

* [Implementar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)

* [Implementar recursos com modelos do Resource Manager e a CLI do Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Se optar por utilizar a CLI do Azure, tem primeiro de instalar e utilizar a CLI localmente. Você deve estar executando o CLI do Azure versão 2.0.59 ou posterior. Para identificar a versão, execute `az --version`. Se precisar de instalar ou atualizar a CLI do Azure, veja [instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

### <a name="create-and-execute-a-template"></a>Criar e executar um modelo

1. Copie e cole a seguinte sintaxe JSON no seu ficheiro:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
          "type": "string",
          "metadata": {
            "description": "AKS Cluster Resource ID"
          }
        },
        "aksResourceLocation": {
          "type": "string",
          "metadata": {
            "description": "Location of the AKS resource e.g. \"East US\""
          }
        },
        "aksResourceTagValues": {
          "type": "object",
          "metadata": {
            "description": "Existing all tags on AKS Cluster Resource"
          }
        },
        "workspaceResourceId": {
          "type": "string",
          "metadata": {
            "description": "Azure Monitor Log Analytics Resource ID"
          }
        }
      },
      "resources": [
        {
          "name": "[split(parameters('aksResourceId'),'/')[8]]",
          "type": "Microsoft.ContainerService/managedClusters",
          "location": "[parameters('aksResourceLocation')]",
          "tags": "[parameters('aksResourceTagValues')]",
          "apiVersion": "2018-03-31",
          "properties": {
            "mode": "Incremental",
            "id": "[parameters('aksResourceId')]",
            "addonProfiles": {
              "omsagent": {
                "enabled": true,
                "config": {
                  "logAnalyticsWorkspaceResourceID": "[parameters('workspaceResourceId')]"
                }
              }
            }
          }
        }
      ]
    }
    ```

2. Guarde este ficheiro como **existingClusterOnboarding.json** para uma pasta local.

3. Cole a seguinte sintaxe JSON no seu ficheiro:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
          "value": "/subscriptions/<SubscriptionId>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
        },
        "aksResourceLocation": {
          "value": "<aksClusterLocation>"
        },
        "workspaceResourceId": {
          "value": "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>"
        },
        "aksResourceTagValues": {
          "value": {
            "<existing-tag-name1>": "<existing-tag-value1>",
            "<existing-tag-name2>": "<existing-tag-value2>",
            "<existing-tag-nameN>": "<existing-tag-valueN>"
          }
        }
      }
    }
    ```

4. Edite os valores de **aksResourceId** e **aksResourceLocation** usando os valores na página de **visão geral do AKS** para o cluster AKs. O valor para **workspaceResourceId** é a ID de recurso completo da sua área de trabalho do Log Analytics, que inclui o nome de área de trabalho. 

    Edite os valores de **aksResourceTagValues** para corresponder aos valores de marca existentes especificados para o cluster AKs.

5. Guarde este ficheiro como **existingClusterParam.json** para uma pasta local.

6. Está pronto para implementar este modelo. 

   * Para implantar com Azure PowerShell, use os seguintes comandos na pasta que contém o modelo:

       ```powershell
       New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
       ```
       
       A alteração de configuração pode demorar alguns minutos a concluir. Quando for concluído, será apresentada uma mensagem que é semelhante ao seguinte e inclui o resultado:

       ```powershell
       provisioningState       : Succeeded
       ```

   * Para implantar com o CLI do Azure, execute os seguintes comandos:
    
       ```azurecli
       az login
       az account set --subscription "Subscription Name"
       az group deployment create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
       ```

       A alteração de configuração pode demorar alguns minutos a concluir. Quando for concluído, será apresentada uma mensagem que é semelhante ao seguinte e inclui o resultado:

       ```azurecli
       provisioningState       : Succeeded
       ```
     
       Depois de ativar a monitorização, poderá demorar cerca de 15 minutos antes de poder visualizar as métricas de estado de funcionamento para o cluster. 

## <a name="verify-agent-and-solution-deployment"></a>Verificar a implementação de agente e solução

Com a versão do agente *06072018* ou posterior, pode verificar que o agente e a solução foram implementados com êxito. Com as versões anteriores do agente, pode verificar apenas a implementação de agente.

### <a name="agent-version-06072018-or-later"></a>Versão do agente 06072018 ou posterior

Execute o seguinte comando para verificar que o agente é implementado com êxito. 

```
kubectl get ds omsagent --namespace=kube-system
```

A saída deve assemelhar-se o seguinte, que indica que foi implementado devidamente:

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

Para verificar a implementação da solução, execute o seguinte comando:

```
kubectl get deployment omsagent-rs -n=kube-system
```

A saída deve assemelhar-se o seguinte, que indica que foi implementado devidamente:

```
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>Versão de agente anteriores ao 06072018

Para verificar que a versão do agente do Log Analytics lançado antes *06072018* está implementado corretamente, execute o seguinte comando:  

```
kubectl get ds omsagent --namespace=kube-system
```

A saída deve assemelhar-se o seguinte, que indica que foi implementado devidamente:  

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-configuration-with-cli"></a>Ver configuração com a CLI

Utilize o `aks show` comando para obter os detalhes desse tipo, tal como está a solução ativada ou não, o que é o resourceID de área de trabalho do Log Analytics e detalhes de resumo sobre o cluster.  

```azurecli
az aks show -g <resourceGroupofAKSCluster> -n <nameofAksCluster>
```

Após alguns minutos, o comando é concluído e devolve o formato JSON informações sobre a solução.  Os resultados do comando deve mostrar o perfil de complemento de monitorização e é semelhante a saída de exemplo seguinte:

```
"addonProfiles": {
    "omsagent": {
      "config": {
        "logAnalyticsWorkspaceResourceID": "/subscriptions/<WorkspaceSubscription>/resourceGroups/<DefaultWorkspaceRG>/providers/Microsoft.OperationalInsights/workspaces/<defaultWorkspaceName>"
      },
      "enabled": true
    }
  }
```

## <a name="next-steps"></a>Passos Seguintes

* Se ocorrerem problemas ao tentar carregar a solução, reveja o [guia de resolução de problemas](container-insights-troubleshoot.md)

* Com a monitorização ativada para capturar métricas de estado de funcionamento para os nós de cluster do AKS e os pods, estas métricas de estado de funcionamento estão disponíveis no portal do Azure. Para saber como utilizar o Azure Monitor para contentores, veja [estado de funcionamento do serviço de Kubernetes do Azure de modo de exibição](container-insights-analyze.md).
