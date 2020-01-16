---
title: Configurar clusters kubernetes híbridos com Azure Monitor para contêineres | Microsoft Docs
description: Este artigo descreve como você pode configurar Azure Monitor para contêineres para monitorar clusters kubernetes hospedados em Azure Stack ou em outro ambiente.
ms.topic: conceptual
ms.date: 12/04/2019
ms.openlocfilehash: d6218550f4b5a3a59b4addc69b19ff11e282d45a
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75977747"
---
# <a name="configure-hybrid-kubernetes-clusters-with-azure-monitor-for-containers"></a>Configurar clusters kubernetes híbridos com Azure Monitor para contêineres

Azure Monitor para contêineres fornece experiência de monitoramento avançada para os clusters do AKS (serviço kubernetes do Azure) e do mecanismo do AKS hospedados no Azure. Este artigo descreve como habilitar o monitoramento de clusters kubernetes hospedados fora do Azure e obter uma experiência de monitoramento semelhante.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que tem o seguinte:

* Uma área de trabalho do Log Analytics.

    Azure Monitor para contêineres dá suporte a um espaço de trabalho Log Analytics nas regiões listadas em produtos do Azure [por região](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor). Para criar seu próprio espaço de trabalho, ele pode ser criado por meio de [Azure Resource Manager](../platform/template-workspace-configuration.md), por meio do [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)ou no [portal do Azure](../learn/quick-create-workspace.md).

    >[!NOTE]
    >Não há suporte para a habilitação do monitoramento de vários clusters com o mesmo nome de cluster para o mesmo espaço de trabalho Log Analytics. Os nomes de cluster devem ser exclusivos.
    >

* Você é membro da **função colaborador de log Analytics** para habilitar o monitoramento de contêiner. Para obter mais informações sobre como controlar o acesso a um espaço de trabalho do Log Analytics, consulte [gerenciar o acesso ao espaço de trabalho e aos dados de log](../platform/manage-access.md)

* [Helm cliente](https://helm.sh/docs/using_helm/) para carregar o Azure monitor para o gráfico de contêineres para o cluster kubernetes especificado.

* As seguintes informações de configuração de proxy e firewall são necessárias para que a versão em contêiner do agente de Log Analytics para Linux se comunique com Azure Monitor:

    |Recursos do Agente|Portas |
    |------|---------|   
    |*.ods.opinsights.azure.com |Porta 443 |  
    |*.oms.opinsights.azure.com |Porta 443 |  
    |*.blob.core.windows.net |Porta 443 |  
    |*. dc.services.visualstudio.com |Porta 443 |

* O agente em contêiner requer que `cAdvisor port: 10255` seja aberto em todos os nós no cluster para coletar métricas de desempenho.

* O agente em contêiner requer que as seguintes variáveis de ambiente sejam especificadas no contêiner para se comunicar com o serviço de API kubernetes no cluster para coletar dados de inventário-`KUBERNETES_SERVICE_HOST` e `KUBERNETES_PORT_443_TCP_PORT`.

>[!IMPORTANT]
>A versão mínima do agente com suporte para monitorar clusters híbridos do kubernetes é ciprod10182019 ou posterior.

## <a name="supported-configurations"></a>Configurações suportadas

O seguinte é oficialmente suportado com Azure Monitor para contêineres.

- Ambientes: kubernetes local, AKS Engine no Azure e Azure Stack. Para obter mais informações, consulte [AKs Engine on Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908).
- As versões do kubernetes e da política de suporte são as mesmas que as versões do [AKs com suporte](../../aks/supported-kubernetes-versions.md).
- Tempo de execução do contêiner: Docker e Moby
- Versão do sistema operacional Linux para nós mestres e trabalhados: Ubuntu (18, 4 LTS e 16, 4 LTS)
- Suporte ao controle de acesso: kubernetes RBAC e non-RBAC

## <a name="enable-monitoring"></a>Ativar monitorização

A habilitação de Azure Monitor para contêineres para o cluster híbrido kubernetes consiste em executar as etapas a seguir na ordem.

1. Configure seu espaço de trabalho Log Analytics com a solução de informações de contêiner.

2. Habilite a Azure Monitor para contêineres HELM gráfico com Log Analytics espaço de trabalho.

### <a name="how-to-add-the-azure-monitor-containers-solution"></a>Como adicionar a solução de contêineres de Azure Monitor

Você pode implantar a solução com o modelo de Azure Resource Manager fornecido usando o cmdlet Azure PowerShell `New-AzResourceGroupDeployment` ou com CLI do Azure.

Se não estiver familiarizado com o conceito de implementar recursos com um modelo, consulte:

* [Implementar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)

* [Implementar recursos com modelos do Resource Manager e a CLI do Azure](../../azure-resource-manager/templates/deploy-cli.md)

Se optar por utilizar a CLI do Azure, tem primeiro de instalar e utilizar a CLI localmente. Você deve estar executando o CLI do Azure versão 2.0.59 ou posterior. Para identificar a versão, execute `az --version`. Se precisar de instalar ou atualizar a CLI do Azure, veja [instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

Esse método inclui dois modelos JSON. Um modelo especifica a configuração para ativar a monitorização e a outra contém valores de parâmetros que pode configurar para especificar o seguinte:

- **workspaceResourceId** -a ID de recurso completa do seu espaço de trabalho log Analytics.
- **workspaceRegion** -a região em que o espaço de trabalho é criado, que também é conhecido como **local** nas propriedades do espaço de trabalho ao exibir da portal do Azure.

Para identificar primeiro a ID de recurso completo do seu espaço de trabalho de Log Analytics necessário para o valor do parâmetro `workspaceResourceId` no arquivo **containerSolutionParams. JSON** , execute as etapas a seguir e execute o cmdlet do PowerShell ou o comando CLI do Azure para adicionar a solução.

1. Liste todas as assinaturas às quais você tem acesso usando o seguinte comando:

    ```azurecli
    az account list --all -o table
    ```

    A saída terá a seguinte aparência:

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   68627f8c-91fO-4905-z48q-b032a81f8vy0  Enabled  True
    ```

    Copie o valor de **SubscriptionId**.

2. Alterne para a assinatura que hospeda o espaço de trabalho Log Analytics usando o seguinte comando:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. O exemplo a seguir exibe a lista de espaços de trabalho em suas assinaturas no formato JSON padrão.

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    Na saída, localize o nome do espaço de trabalho e copie a ID de recurso completo do espaço de trabalho Log Analytics sob a **ID**do campo.

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

5. Salve esse arquivo como containerSolution. JSON em uma pasta local.

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

7. Edite os valores de **workspaceResourceId** usando o valor que você copiou na etapa 3 e, para **workspaceRegion** , copie o valor da **região** depois de executar o comando de CLI do Azure [AZ monitor log-Analytics Workspace show](https://docs.microsoft.com/cli/azure/monitor/log-analytics/workspace?view=azure-cli-latest#az-monitor-log-analytics-workspace-list).

8. Salve esse arquivo como containerSolutionParams. JSON em uma pasta local.

9. Está pronto para implementar este modelo.

   * Para implantar com Azure PowerShell, use os seguintes comandos na pasta que contém o modelo:

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

   * Para implantar com o CLI do Azure, execute os seguintes comandos:

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

## <a name="install-the-chart"></a>Instalar o gráfico

Para habilitar o gráfico HELM, faça o seguinte:

1. Adicione o repositório de gráficos do Azure à lista local executando o seguinte comando:

    ```
    helm repo add incubator https://kubernetes-charts-incubator.storage.googleapis.com/
    ````

2. Instale o gráfico executando o seguinte comando:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<my_prod_cluster> incubator/azuremonitor-containers
    ```

    Se o espaço de trabalho Log Analytics estiver no Azure China, execute o seguinte comando:

    ```
    $ helm install --name myrelease-1 \
     --set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

    Se o espaço de trabalho Log Analytics estiver no governo dos EUA do Azure, execute o seguinte comando:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

## <a name="configure-agent-data-collection"></a>Configurar coleta de dados do agente

Em estrela com a versão 1.0.0 do gráfico, as configurações de coleta de dados do agente são controladas por meio do ConfigMap. Consulte a documentação sobre as configurações de coleta de dados do agente [aqui](container-insights-agent-config.md).

Depois de ter implantado com êxito o gráfico, você pode examinar os dados para o cluster kubernetes híbrido em Azure Monitor para contêineres do portal do Azure.  

>[!NOTE]
>A latência de ingestão é de cerca de cinco a dez minutos do agente para ser confirmada no espaço de trabalho Log Analytics do Azure. O status do cluster mostra o valor **sem dados** ou **desconhecido** até que todos os dados de monitoramento necessários estejam disponíveis no Azure monitor.

## <a name="troubleshooting"></a>Resolução de problemas

Se você encontrar um erro ao tentar habilitar o monitoramento para o cluster kubernetes híbrido, copie o script do PowerShell [TroubleshootError_nonAzureK8s. ps1](https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/Troubleshoot/TroubleshootError_nonAzureK8s.ps1) e salve-o em uma pasta no computador. Esse script é fornecido para ajudar a detectar e corrigir os problemas encontrados. Os problemas projetados para detectar e tentar a correção do são os seguintes:

* O espaço de trabalho de Log Analytics especificado é válido
* O espaço de trabalho Log Analytics é configurado com a Azure Monitor para a solução de contêineres. Caso contrário, configure o espaço de trabalho.
* O Pod do OmsAgent réplicaset está em execução
* O Pod OmsAgent daemonset está em execução
* O serviço de integridade do OmsAgent está em execução
* A ID e a chave do espaço de trabalho Log Analytics configuradas no agente em contêiner correspondem ao espaço de trabalho com o qual a percepção está configurada.
* Valide se todos os nós de trabalho do Linux têm `kubernetes.io/role=agent` rótulo para agendar o Pod RS. Se ele não existir, adicione-o.
* Validar `cAdvisor port: 10255` é aberto em todos os nós no cluster.

Para executar com Azure PowerShell, use os seguintes comandos na pasta que contém o script:

```powershell
.\TroubleshootError_nonAzureK8s.ps1 - azureLogAnalyticsWorkspaceResourceId </subscriptions/<subscriptionId>/resourceGroups/<resourcegroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName> -kubeConfig <kubeConfigFile>
```

## <a name="next-steps"></a>Passos seguintes

Com o monitoramento habilitado para coletar a utilização de recursos e de integridade do seu cluster kubernetes híbrido e cargas de trabalho em execução neles, saiba [como usar](container-insights-analyze.md) Azure monitor para contêineres.
