---
title: Configure clusters Híbridos Kubernetes com Monitor Azure para contentores Microsoft Docs
description: Este artigo descreve como pode configurar o Monitor Azure para os contentores monitorizarem os clusters Kubernetes alojados em Azure Stack ou noutro ambiente.
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: 6d03716b988b1139e01d41120f48ea9a9bf34be1
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198059"
---
# <a name="configure-hybrid-kubernetes-clusters-with-azure-monitor-for-containers"></a>Configure aglomerados kubernetes híbridos com Monitor Azure para contentores

O Azure Monitor para contentores proporciona uma rica experiência de monitorização para o Serviço Azure Kubernetes (AKS) e [aks engine em Azure,](https://github.com/Azure/aks-engine)que é um cluster Kubernetes autogerido hospedado no Azure. Este artigo descreve como permitir a monitorização dos clusters Kubernetes alojados fora de Azure e obter uma experiência de monitorização semelhante.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que tem o seguinte:

* Uma área de trabalho do Log Analytics.

    O Azure Monitor para contentores suporta um espaço de trabalho log Analytics nas regiões listadas em Produtos Azure [por região.](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor) Para criar o seu próprio espaço de trabalho, pode ser criado através do [Azure Resource Manager](../platform/template-workspace-configuration.md), através do [PowerShell,](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)ou no [portal Azure.](../learn/quick-create-workspace.md)

    >[!NOTE]
    >Permitir a monitorização de vários clusters com o mesmo nome de cluster para o mesmo espaço de trabalho log Analytics não é suportado. Os nomes do agrupamento devem ser únicos.
    >

* É membro da **função** de colaborador do Log Analytics para permitir a monitorização dos contentores. Para obter mais informações sobre como controlar o acesso a um espaço de trabalho do Log Analytics, consulte [Gerir o acesso ao espaço](../platform/manage-access.md) de trabalho e registar dados

* [Cliente HELM](https://helm.sh/docs/using_helm/) a bordo do gráfico do Monitor Azure para contentores para o cluster Kubernetes especificado.

* São necessárias as seguintes informações de proxy e de configuração de firewall para a versão contentorizada do agente Log Analytics para o Linux comunicar com o Monitor Azure:

    |Recursos do Agente|Portas |
    |------|---------|   
    |*.ods.opinsights.azure.com |Porta 443 |  
    |*.oms.opinsights.azure.com |Porta 443 |  
    |*.blob.core.windows.net |Porta 443 |  
    |*.dc.services.visualstudio.com |Porta 443 |

* O agente contentorizado exige que a `cAdvisor secure port: 10250` ou `unsecure port :10255` de Kubelet seja aberta em todos os nós do cluster para recolher métricas de desempenho. Recomendamos que configure `secure port: 10250` no cAdvisor do Kubelet se ainda não estiver configurado.

* O agente contentorizado exige que sejam especificadas as seguintes variáveis ambientais no recipiente, a fim de comunicar com o serviço Kubernetes API dentro do cluster para recolher dados de inventário - `KUBERNETES_SERVICE_HOST` e `KUBERNETES_PORT_443_TCP_PORT`.

>[!IMPORTANT]
>A versão de agente mínimo suportada para monitorizar os clusters híbridos kubernetes é ciprod10182019 ou mais tarde.

## <a name="supported-configurations"></a>Configurações suportadas

O seguinte é oficialmente suportado com o Monitor Azure para contentores.

- Ambientes: Kubernetes no local, motor AKS em Azure e Azure Stack. Para mais informações, consulte o [AkS Engine em Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908).
- As versões de Kubernetes e política de suporte são as mesmas que as versões de [AKS suportadas.](../../aks/supported-kubernetes-versions.md)
- Tempo de execução do contentor: Docker e Moby
- Lançamento do Linux OS para mestre e nós trabalhados: Ubuntu (18.04 LTS e 16.04 LTS)
- Controlo de acesso suportado: Kubernetes RBAC e não RBAC

## <a name="enable-monitoring"></a>Ativar monitorização

A habilitação do Monitor Azure para os recipientes para o cluster híbrido Kubernetes consiste em executar os seguintes passos por ordem.

1. Configure o seu espaço de trabalho Log Analytics com a solução Container Insights.

2. Ative o Monitor Azure para recipientes gráfico HELM com log Analytics workspace.

### <a name="how-to-add-the-azure-monitor-containers-solution"></a>Como adicionar a solução de contentores do Monitor Azure

Pode implantar a solução com o modelo de Gestor de Recursos Azure fornecido utilizando o cmdlet Azure PowerShell `New-AzResourceGroupDeployment` ou com o Azure CLI.

Se não estiver familiarizado com o conceito de implementar recursos com um modelo, consulte:

* [Implementar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)

* [Implementar recursos com modelos de Gestor de Recursos e o Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Se optar por utilizar a CLI do Azure, tem primeiro de instalar e utilizar a CLI localmente. Deve estar a executar a versão Azure CLI 2.0.59 ou mais tarde. Para identificar a sua versão, execute `az --version`. Se precisar de instalar ou atualizar o Azure CLI, consulte [Instalar o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

Esse método inclui dois modelos JSON. Um modelo especifica a configuração para ativar a monitorização e a outra contém valores de parâmetros que pode configurar para especificar o seguinte:

- **workspaceResourceId** - o ID de recursos completos do seu espaço de trabalho Log Analytics.
- **workspaceRegion** - a região onde o espaço de trabalho é criado, que também é referido como **Localização** nas propriedades do espaço de trabalho ao visualizar a partir do portal Azure.

Para identificar primeiro o id completo do seu espaço de trabalho Log Analytics necessário para o valor do parâmetro `workspaceResourceId` no ficheiro **contentorSolutionParams.json,** execute os seguintes passos e, em seguida, execute o comando PowerShell cmdlet ou Azure CLI para adicionar a solução.

1. Enumerar todas as subscrições a que tem acesso utilizando o seguinte comando:

    ```azurecli
    az account list --all -o table
    ```

    A saída terá a seguinte aparência:

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   68627f8c-91fO-4905-z48q-b032a81f8vy0  Enabled  True
    ```

    Copiar o valor para **SubscriptionId**.

2. Mude para a subscrição que acolhe o espaço de trabalho log analytics utilizando o seguinte comando:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. O exemplo seguinte apresenta a lista de espaços de trabalho nas suas subscrições no formato Padrão JSON.

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    Na saída, encontre o nome do espaço de trabalho e, em seguida, copie o id completo do recurso desse espaço de trabalho log Analytics sob o **ID**de campo .

4. Copie e cole a seguinte sintaxe JSON no seu ficheiro:

    ```json
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

5. Guarde este ficheiro como recipienteSolution.json para uma pasta local.

6. Cole a seguinte sintaxe JSON no seu ficheiro:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "workspaceResourceId": {
          "value": "<workspaceResourceId>"
      },
      "workspaceRegion": {
        "value": "<workspaceRegion>"
      }
     }
    }
    ```

7. Editar os valores para **workspaceResourceId** utilizando o valor copiado no passo 3, e para **workspaceRegion** copiar o valor **região** após executar o show de [log-analytics](https://docs.microsoft.com/cli/azure/monitor/log-analytics/workspace?view=azure-cli-latest#az-monitor-log-analytics-workspace-list)de comando Azure CLI .

8. Guarde este ficheiro como recipienteSolutionParams.json para uma pasta local.

9. Está pronto para implementar este modelo.

   * Para implantar com o Azure PowerShell, utilize os seguintes comandos na pasta que contém o modelo:

       ```powershell
       # configure and login to the cloud of log analytics workspace.Specify the corresponding cloud environment of your workspace to below command.
       Connect-AzureRmAccount -Environment <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       ```

       ```powershell
       # set the context of the subscription of Log Analytics workspace
       Set-AzureRmContext -SubscriptionId <subscription Id of log analytics workspace>
       ```

       ```powershell
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <resource group of log analytics workspace> -TemplateFile .\containerSolution.json -TemplateParameterFile .\containerSolutionParams.json
       ```

       A alteração de configuração pode demorar alguns minutos a concluir. Quando for concluído, será apresentada uma mensagem que é semelhante ao seguinte e inclui o resultado:

       ```powershell
       provisioningState       : Succeeded
       ```

   * Para implantar com o Azure CLI, execute os seguintes comandos:

       ```azurecli
       az login
       az account set --name <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       az login
       az account set --subscription "Subscription Name"
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       az group deployment create --resource-group <resource group of log analytics workspace> --template-file ./containerSolution.json --parameters @./containerSolutionParams.json
       ```

       A alteração de configuração pode demorar alguns minutos a concluir. Quando for concluído, será apresentada uma mensagem que é semelhante ao seguinte e inclui o resultado:

       ```azurecli
       provisioningState       : Succeeded
       ```

       Depois de ativar a monitorização, poderá demorar cerca de 15 minutos antes de poder visualizar as métricas de estado de funcionamento para o cluster.

## <a name="install-the-chart"></a>Instale o gráfico

Para ativar o gráfico HELM, faça o seguinte:

1. Adicione o repositório de gráficos Azure à sua lista local executando o seguinte comando:

    ```
    helm repo add incubator https://kubernetes-charts-incubator.storage.googleapis.com/
    ````

2. Instale o gráfico executando o seguinte comando:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<my_prod_cluster> incubator/azuremonitor-containers
    ```

    Se o espaço de trabalho do Log Analytics estiver na China Azure, execute o seguinte comando:

    ```
    $ helm install --name myrelease-1 \
     --set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

    Se o espaço de trabalho do Log Analytics estiver no Governo dos EUA, execute o seguinte comando:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

## <a name="configure-agent-data-collection"></a>Configure a recolha de dados do agente

Olhando para a versão 1.0.0 do gráfico, as definições de recolha de dados do agente são controladas a partir do ConfigMap. Consulte a documentação sobre as definições de recolha de dados do agente [aqui](container-insights-agent-config.md).

Depois de ter implementado com sucesso o gráfico, pode rever os dados do seu cluster híbrido Kubernetes no Azure Monitor para contentores do portal Azure.  

>[!NOTE]
>A latência de ingestão está a cerca de 5 a 10 minutos do agente para se comprometer no espaço de trabalho azure Log Analytics. O estado do cluster mostra o valor **Nenhum dado** ou **desconhecido** até que todos os dados de monitorização necessários estão disponíveis no Monitor Azure.

## <a name="troubleshooting"></a>Resolução de problemas

Se encontrar um erro ao tentar ativar a monitorização do seu cluster Híbrido Kubernetes, copie o script PowerShell [TroubleshootError_nonAzureK8s.ps1](https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/Troubleshoot/TroubleshootError_nonAzureK8s.ps1) e guarde-o para uma pasta no seu computador. Este script é fornecido para ajudar a detetar e corrigir os problemas encontrados. As questões que foi concebida para detetar e tentar corrigir são as seguintes:

* O espaço de trabalho especificado log Analytics é válido
* O espaço de trabalho Log Analytics está configurado com a solução Azure Monitor para contentores. Caso contrário, configure o espaço de trabalho.
* As cápsulas de replicação OmsAgent estão em execução
* As cápsulas damsAgent daemonset estão em execução
* O serviço de saúde OmsAgent está em execução
* O ID do espaço de trabalho Log Analytics e a chave configurada no agente contentorizado combinam com o espaço de trabalho com o insight configurado.
* Valide todos os nós de trabalhador linux têm `kubernetes.io/role=agent` etiqueta para agendar rs pod. Se não existir, adicione.
* Valide `cAdvisor secure port:10250` ou `unsecure port: 10255` é aberto em todos os nós do cluster.

Para executar com o Azure PowerShell, utilize os seguintes comandos na pasta que contém o script:

```powershell
.\TroubleshootError_nonAzureK8s.ps1 - azureLogAnalyticsWorkspaceResourceId </subscriptions/<subscriptionId>/resourceGroups/<resourcegroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName> -kubeConfig <kubeConfigFile> -clusterContextInKubeconfig <clusterContext>
```

## <a name="next-steps"></a>Passos seguintes

Com a monitorização habilitada a recolher a utilização de recursos e saúde do seu cluster híbrido Kubernetes e as cargas de trabalho que os utilizam, aprenda [a utilizar o](container-insights-analyze.md) Monitor Azure para contentores.
