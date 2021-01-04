---
title: Monitorar um cluster do Serviço Azure Kubernetes (AKS) implantado Microsoft Docs
description: Saiba como permitir a monitorização de um cluster Azure Kubernetes Service (AKS) com o Azure Monitor para contentores já implantados na sua subscrição.
ms.topic: conceptual
ms.date: 09/12/2019
ms.custom: devx-track-terraform, devx-track-azurecli
ms.openlocfilehash: 547c22e4d82aa728009a2fdb42f2c3b481b7a625
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/19/2020
ms.locfileid: "97695661"
---
# <a name="enable-monitoring-of-azure-kubernetes-service-aks-cluster-already-deployed"></a>Permitir a monitorização do cluster do Serviço Azure Kubernetes (AKS) já implantado

Este artigo descreve como configurar o Azure Monitor para contentores para monitorizar o cluster gerido kubernetes hospedado no [Serviço Azure Kubernetes](../../aks/index.yml) que já foram implantados na sua subscrição.

Pode ativar a monitorização de um cluster AKS que já foi implantado utilizando um dos métodos suportados:

* CLI do Azure
* Terraform
* [Do Monitor Azure](#enable-from-azure-monitor-in-the-portal) ou [diretamente do cluster AKS](#enable-directly-from-aks-cluster-in-the-portal) no portal Azure
* Com o [modelo de Gestor de Recursos Azure fornecido](#enable-using-an-azure-resource-manager-template) utilizando o cmdlet Azure PowerShell ou com O `New-AzResourceGroupDeployment` Azure CLI.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="enable-using-azure-cli"></a>Ativar através do CLI do Azure

O passo seguinte permite a monitorização do seu cluster AKS utilizando o Azure CLI. Neste exemplo, não é obrigado a pré-criar ou especificar um espaço de trabalho existente. Este comando simplifica o processo para si, criando um espaço de trabalho predefinido no grupo de recursos predefinidos da subscrição do cluster AKS se já não existir na região.  O espaço de trabalho padrão criado assemelha-se ao formato *defaultWorkspace- \<GUID> - \<Region>*.

```azurecli
az aks enable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG
```

A saída assemelha-se-á ao seguinte:

```output
provisioningState       : Succeeded
```

### <a name="integrate-with-an-existing-workspace"></a>Integre-se com um espaço de trabalho existente

Se preferir integrar-se com um espaço de trabalho existente, execute os seguintes passos para primeiro identificar o ID completo do seu espaço de trabalho Log Analytics necessário para o `--workspace-resource-id` parâmetro e, em seguida, executar o comando para ativar o add-on de monitorização contra o espaço de trabalho especificado.

1. Listar todas as subscrições a que tem acesso utilizando o seguinte comando:

    ```azurecli
    az account list --all -o table
    ```

    A saída assemelha-se-á ao seguinte:

    ```output
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   68627f8c-91fO-4905-z48q-b032a81f8vy0  Enabled  True
    ```

    Copie o valor para **SubscriçãoId**.

2. Mude para a subscrição que hospeda o espaço de trabalho Log Analytics utilizando o seguinte comando:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. O exemplo a seguir mostra a lista de espaços de trabalho nas suas subscrições no formato JSON predefinido.

    ```azurecli
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    Na saída, encontre o nome do espaço de trabalho e, em seguida, copie o ID completo do espaço de trabalho Log Analytics sob o **id** de campo .

4. Executar o seguinte comando para ativar o addon de monitorização, substituindo o valor do `--workspace-resource-id` parâmetro. O valor das cordas deve estar dentro das cotações duplas:

    ```azurecli
    az aks enable-addons -a monitoring -n ExistingManagedCluster -g ExistingManagedClusterRG --workspace-resource-id "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>"
    ```

    A saída assemelha-se-á ao seguinte:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="enable-using-terraform"></a>Ativar a utilização do Terraform

1. Adicione o perfil de **complemento de oms_agent** ao [recurso azurerm_kubernetes_cluster](https://www.terraform.io/docs/providers/azurerm/d/kubernetes_cluster.html#addon_profile) existente

   ```
   addon_profile {
    oms_agent {
      enabled                    = true
      log_analytics_workspace_id = "${azurerm_log_analytics_workspace.test.id}"
     }
   }
   ```

2. Adicione o [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html) seguindo os passos na documentação Terraform.

## <a name="enable-from-azure-monitor-in-the-portal"></a>Ativar a partir do Monitor Azure no portal

Para permitir a monitorização do seu cluster AKS no portal Azure a partir do Azure Monitor, faça o seguinte:

1. No portal Azure, selecione **Monitor**.

2. Selecione **recipientes** da lista.

3. Na página **Monitor - recipientes,** selecione **clusters não monitorizados**.

4. A partir da lista de aglomerados não monitorizados, encontre o recipiente na lista e clique em **Enable**.

5. Na página **"Onboarding to Azure Monitor" para contentores,** se tiver um espaço de trabalho log analytics existente na mesma subscrição que o cluster, selecione-o da lista de espera.
    A lista pré-seleciona o espaço de trabalho predefinido e a localização para a qual o contentor AKS é implantado na subscrição.

    ![Permitir a monitorização dos insights do contentor AKS](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >Se pretender criar um novo espaço de trabalho log Analytics para armazenar os dados de monitorização do cluster, siga as instruções no Criar um espaço de [trabalho Log Analytics](../learn/quick-create-workspace.md). Certifique-se de criar o espaço de trabalho na mesma subscrição para a qual o contentor AKS está implantado.

Depois de ter ativado a monitorização, pode demorar cerca de 15 minutos até poder ver as métricas de saúde para o cluster.

## <a name="enable-directly-from-aks-cluster-in-the-portal"></a>Ativar diretamente a partir do cluster AKS no portal

Para ativar a monitorização diretamente de um dos seus clusters AKS no portal Azure, faça o seguinte:

1. No portal Azure, selecione **Todos os serviços**.

2. Na lista de recursos, comece a escrever **contentores.**  Os filtros da lista com base na sua entrada.

3. Selecione **serviços Kubernetes**.
    
4. Na lista de serviços da Kubernetes, selecione um serviço.

5. Na página geral do serviço Kubernetes, selecione **Monitoring - Insights**.

6. Na página **"Onboarding to Azure Monitor" para contentores,** se tiver um espaço de trabalho log analytics existente na mesma subscrição que o cluster, selecione-o na lista de espera.
    A lista pré-seleciona o espaço de trabalho predefinido e a localização para a qual o contentor AKS é implantado na subscrição.

    ![Permitir a monitorização da saúde dos contentores AKS](./media/container-insights-onboard/kubernetes-onboard-brownfield-02.png)

    >[!NOTE]
    >Se pretender criar um novo espaço de trabalho log Analytics para armazenar os dados de monitorização do cluster, siga as instruções no Criar um espaço de [trabalho Log Analytics](../learn/quick-create-workspace.md). Certifique-se de criar o espaço de trabalho na mesma subscrição para a qual o contentor AKS está implantado.

Depois de ter ativado a monitorização, pode demorar cerca de 15 minutos até poder visualizar os dados operacionais do cluster.

## <a name="enable-using-an-azure-resource-manager-template"></a>Ative a utilização de um modelo de Gestor de Recursos Azure

Este método inclui dois modelos JSON. Um modelo especifica a configuração para permitir a monitorização, e o outro contém valores de parâmetros que configura para especificar o seguinte:

* Identificação do recurso do contentor AKS.
* O grupo de recursos em que o cluster está implantado.

>[!NOTE]
>O modelo precisa de ser implantado no mesmo grupo de recursos que o cluster.
>

O espaço de trabalho Log Analytics tem de ser criado antes de permitir a monitorização utilizando o Azure PowerShell ou o CLI. Para criar o espaço de trabalho, pode criá-lo através [do Azure Resource Manager,](../samples/resource-manager-workspace.md)através do [PowerShell,](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)ou no [portal Azure](../learn/quick-create-workspace.md).

Se não estiver familiarizado com o conceito de implantação de recursos utilizando um modelo, consulte:

* [Implementar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)

* [Implementar recursos com modelos de Gestor de Recursos e o CLI Azure](../../azure-resource-manager/templates/deploy-cli.md)

Se optar por utilizar o Azure CLI, primeiro tem de instalar e utilizar o CLI localmente. Deve estar a executar a versão Azure CLI 2.0.59 ou posterior. Para identificar a sua versão, corra `az --version` . Se necessitar de instalar ou atualizar o Azure CLI, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli).

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

2. Guarde este ficheiro à medida **queexistingClusterOnboarding.jsnuma** pasta local.

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

4. Editar os **valores para aksResourceId** e **aksResourceLocation** utilizando os valores na página de visão geral da **AKS** para o cluster AKS. O valor para **o espaço de trabalhoResourceId** é o ID de recursos completo do seu espaço de trabalho Log Analytics, que inclui o nome do espaço de trabalho.

    Editar os **valores para aksResourceTagValues** para corresponder aos valores de etiqueta existentes especificados para o cluster AKS.

5. Guarde este ficheiro à medida **queexistingClusterParam.jsnuma** pasta local.

6. Está pronto para implementar este modelo.

   * Para implementar com a Azure PowerShell, utilize os seguintes comandos na pasta que contém o modelo:

       ```powershell
       New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
       ```

       A alteração de configuração pode demorar alguns minutos a ser concluída. Quando estiver concluída, é exibida uma mensagem semelhante à seguinte e inclui o resultado:

       ```output
       provisioningState       : Succeeded
       ```

   * Para implementar com o Azure CLI, executar os seguintes comandos:

       ```azurecli
       az login
       az account set --subscription "Subscription Name"
       az deployment group create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
       ```

       A alteração de configuração pode demorar alguns minutos a ser concluída. Quando estiver concluída, é exibida uma mensagem semelhante à seguinte e inclui o resultado:

       ```output
       provisioningState       : Succeeded
       ```

       Depois de ter ativado a monitorização, pode demorar cerca de 15 minutos até poder ver as métricas de saúde para o cluster.

## <a name="verify-agent-and-solution-deployment"></a>Verificar a implementação do agente e da solução

Com a versão *06072018* ou posterior, pode verificar se tanto o agente como a solução foram implementados com sucesso. Com versões anteriores do agente, só pode verificar a implementação do agente.

### <a name="agent-version-06072018-or-later"></a>Agente versão 06072018 ou mais tarde

Executar o seguinte comando para verificar se o agente é implantado com sucesso.

```
kubectl get ds omsagent --namespace=kube-system
```

A saída deve assemelhar-se ao seguinte, o que indica que foi corretamente implantado:

```output
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```

Se existirem nós do Windows Server no cluster, pode executar o seguinte comando para verificar se o agente é implantado com sucesso.

```
kubectl get ds omsagent-win --namespace=kube-system
```

A saída deve assemelhar-se ao seguinte, o que indica que foi corretamente implantado:

```output
User@aksuser:~$ kubectl get ds omsagent-win --namespace=kube-system
NAME                   DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                   AGE
omsagent-win           2         2         2         2            2           beta.kubernetes.io/os=windows   1d
```

Para verificar a implantação da solução, executar o seguinte comando:

```
kubectl get deployment omsagent-rs -n=kube-system
```

A saída deve assemelhar-se ao seguinte, o que indica que foi corretamente implantado:

```output
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>Versão de agente antes de 06072018

Para verificar se a versão do agente Log Analytics lançada antes *do 06072018* é devidamente implantada, execute o seguinte comando:

```
kubectl get ds omsagent --namespace=kube-system
```

A saída deve assemelhar-se ao seguinte, o que indica que foi corretamente implantado:

```output
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```

## <a name="view-configuration-with-cli"></a>Ver configuração com CLI

Utilize o `aks show` comando para obter detalhes como é a solução ativada ou não, o que é o log Analytics workspace resourceID, e detalhes sumários sobre o cluster.

```azurecli
az aks show -g <resourceGroupofAKSCluster> -n <nameofAksCluster>
```

Após alguns minutos, o comando completa e devolve informações formatadas com JSON sobre solução.  Os resultados do comando devem mostrar o perfil de acompanhamento e assemelhar-se à seguinte saída de exemplo:

```output
"addonProfiles": {
    "omsagent": {
      "config": {
        "logAnalyticsWorkspaceResourceID": "/subscriptions/<WorkspaceSubscription>/resourceGroups/<DefaultWorkspaceRG>/providers/Microsoft.OperationalInsights/workspaces/<defaultWorkspaceName>"
      },
      "enabled": true
    }
  }
```

## <a name="next-steps"></a>Passos seguintes

* Se sentir problemas ao tentar embarcar na solução, reveja o [guia de resolução de problemas](container-insights-troubleshoot.md)

* Com a monitorização habilitada a recolher a saúde e a utilização de recursos do seu cluster AKS e cargas de trabalho que os executam, aprenda [a utilizar o](container-insights-analyze.md) Azure Monitor para recipientes.
