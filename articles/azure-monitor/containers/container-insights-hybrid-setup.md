---
title: Configure agrupamentos híbridos Kubernetes com insights de contentores | Microsoft Docs
description: Este artigo descreve como pode configurar insights de contentores para monitorizar os clusters kubernetes alojados em Azure Stack ou outro ambiente.
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: d2692b4a634d60ef62339f68277591d711260712
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101711251"
---
# <a name="configure-hybrid-kubernetes-clusters-with-container-insights"></a>Configure agrupamentos híbridos Kubernetes com insights de contentores

Os insights de contentores proporcionam uma rica experiência de monitorização para o Serviço Azure Kubernetes (AKS) e [O Motor AKS em Azure,](https://github.com/Azure/aks-engine)que é um cluster kubernetes auto-gerido hospedado em Azure. Este artigo descreve como permitir a monitorização dos clusters Kubernetes alojados fora de Azure e obter uma experiência de monitorização semelhante.

## <a name="supported-configurations"></a>Configurações suportadas

As seguintes configurações são oficialmente suportadas com insights do Contentor. Se tiver uma versão diferente de Kubernetes e versões do sistema operativo, por favor envie um e-mail para askcoin@microsoft.com .

- Ambientes:

    - Kubernetes no local
    - Motor AKS em Azure e Azure Stack. Para mais informações, consulte [o motor AKS em Azure Stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview)
    - [Versão OpenShift](https://docs.openshift.com/container-platform/4.3/welcome/index.html) 4 e superior, no local ou outros ambientes em nuvem.

- As versões de Kubernetes e a política de suporte são as mesmas que as versões [suportadas pela AKS.](../../aks/supported-kubernetes-versions.md)

- Os seguintes tempos de funcionação do contentor são suportados: Estivador, Moby e CRI compatívels com tempos de execução tais como CRI-O e ContainerD.

- Linux OS lançados para os nós de mestre e trabalhador apoiados são: Ubuntu (18.04 LTS e 16.04 LTS) e Red Hat Enterprise Linux CoreOS 43.81.

- Controlo de acesso suportado: Kubernetes RBAC e não-RBAC

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que tem o seguinte:

- Uma [área de trabalho do Log Analytics](../logs/design-logs-deployment.md).

    Os insights de contentores suportam um espaço de trabalho Log Analytics nas regiões listadas nos Produtos Azure [por região.](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor) Para criar o seu próprio espaço de trabalho, pode ser criado através do [Azure Resource Manager,](../logs/resource-manager-workspace.md)através do [PowerShell,](../logs/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)ou no [portal Azure.](../logs/quick-create-workspace.md)

    >[!NOTE]
    >Não é suportada a monitorização de múltiplos clusters com o mesmo nome de cluster para o mesmo espaço de trabalho Log Analytics. Os nomes dos agrupamentos devem ser únicos.
    >

- É membro da **função de contribuinte Log Analytics** para permitir a monitorização do contentor. Para obter mais informações sobre como controlar o acesso a um espaço de trabalho do Log Analytics, consulte [Gerir o acesso ao espaço de trabalho e registar dados.](../logs/manage-access.md)

- Para visualizar os dados de monitorização, é necessário ter o papel de [*leitor do Log Analytics*](../logs/manage-access.md#manage-access-using-azure-permissions) no espaço de trabalho Do Log Analytics, configurado com insights do Contentor.

- [Cliente HELM](https://helm.sh/docs/using_helm/) a bordo do gráfico de insights do contentor para o cluster kubernetes especificado.

- São necessárias as seguintes informações de configuração de procuração e firewall para que a versão contentorizada do agente Log Analytics para o Linux comunique com o Azure Monitor:

    |Recursos do Agente|Portas |
    |------|---------|
    |*.ods.opinsights.azure.com |Porto 443 |
    |*.oms.opinsights.azure.com |Porto 443 |
    |*.dc.services.visualstudio.com |Porto 443 |

- O agente contentorizado requer `cAdvisor secure port: 10250` kubelet's ou ser aberto em todos os nós do cluster para recolher `unsecure port :10255` métricas de desempenho. Recomendamos que configure `secure port: 10250` no cAdvisor do Kubelet se ainda não estiver configurado.

- O agente contentor exige que sejam especificadas no contentor as seguintes variáveis ambientais, a fim de comunicar com o serviço API da Kubernetes dentro do cluster para recolher dados de inventário - `KUBERNETES_SERVICE_HOST` e `KUBERNETES_PORT_443_TCP_PORT` .

>[!IMPORTANT]
>A versão de agente mínimo suportada para monitorizar os clusters híbridos de Kubernetes é ciprod10182019 ou mais tarde.

## <a name="enable-monitoring"></a>Ativar monitorização

Permitir insights de contentores para o cluster híbrido Kubernetes consiste em executar os seguintes passos em ordem.

1. Configure o seu espaço de trabalho Log Analytics com a solução Container Insights.   

2. Ativar o gráfico DE INSIGHTS DE Contentores HELM com log analytics espaço de trabalho.

Para obter informações adicionais sobre soluções de monitorização no Azure Monitor consulte [aqui](../../azure-monitor/insights/solutions.md).

### <a name="how-to-add-the-azure-monitor-containers-solution"></a>Como adicionar a solução Azure Monitor Containers

Pode implementar a solução com o modelo de Gestor de Recursos Azure fornecido utilizando o cmdlet Azure PowerShell `New-AzResourceGroupDeployment` ou com o Azure CLI.

Se não estiver familiarizado com o conceito de implantação de recursos utilizando um modelo, consulte:

- [Implementar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)

- [Implementar recursos com modelos de Gestor de Recursos e o CLI Azure](../../azure-resource-manager/templates/deploy-cli.md)

Se optar por utilizar o Azure CLI, primeiro tem de instalar e utilizar o CLI localmente. Deve estar a executar a versão Azure CLI 2.0.59 ou posterior. Para identificar a sua versão, corra `az --version` . Se necessitar de instalar ou atualizar o Azure CLI, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli).

Este método inclui dois modelos JSON. Um modelo especifica a configuração para permitir a monitorização, e o outro contém valores de parâmetros que configura para especificar o seguinte:

- **workspaceResourceId** - o ID completo do seu espaço de trabalho Log Analytics.
- **workspaceRegion** - a região em que o espaço de trabalho é criado, que também é referido como **Localização** nas propriedades do espaço de trabalho ao visualizar a partir do portal Azure.

Para primeiro identificar o ID completo do seu espaço de trabalho Log Analytics necessário para o valor do `workspaceResourceId` parâmetro nocontainerSolutionParams.js **no** ficheiro, execute os seguintes passos e, em seguida, execute o comando PowerShell cmdlet ou Azure CLI para adicionar a solução.

1. Listar todas as subscrições a que tem acesso utilizando o seguinte comando:

    ```azurecli
    az account list --all -o table
    ```

    A saída assemelha-se-á ao seguinte:

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   0fb60ef2-03cc-4290-b595-e71108e8f4ce  Enabled  True
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

    Na saída, encontre o nome do espaço de trabalho e, em seguida, copie o ID completo do recurso daquele espaço de trabalho Log Analytics sob o **ID** de campo .

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

5. Guarde este ficheiro à medida que containerSolution.jsnuma pasta local.

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

7. Editar os valores para **workspaceResourceId** utilizando o valor copiado no passo 3, e para o **espaço de trabalho Aregiona** o valor da **Região** depois de executar o show de espaço de trabalho de [log-analytics do](/cli/azure/monitor/log-analytics/workspace#az-monitor-log-analytics-workspace-list&preserve-view=true)comando Azure CLI monitor.

8. Guarde este ficheiro à medida que containerSolutionParams.jsnuma pasta local.

9. Está pronto para implementar este modelo.

   - Para implementar com a Azure PowerShell, utilize os seguintes comandos na pasta que contém o modelo:

       ```powershell
       # configure and login to the cloud of Log Analytics workspace.Specify the corresponding cloud environment of your workspace to below command.
       Connect-AzureRmAccount -Environment <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       ```

       ```powershell
       # set the context of the subscription of Log Analytics workspace
       Set-AzureRmContext -SubscriptionId <subscription Id of Log Analytics workspace>
       ```

       ```powershell
       # execute deployment command to add Container Insights solution to the specified Log Analytics workspace
       New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <resource group of Log Analytics workspace> -TemplateFile .\containerSolution.json -TemplateParameterFile .\containerSolutionParams.json
       ```

       A alteração de configuração pode demorar alguns minutos a ser concluída. Quando estiver concluída, é exibida uma mensagem semelhante à seguinte e inclui o resultado:

       ```powershell
       provisioningState       : Succeeded
       ```

   - Para implementar com o Azure CLI, executar os seguintes comandos:

       ```azurecli
       az login
       az account set --name <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       az login
       az account set --subscription "Subscription Name"
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       az deployment group create --resource-group <resource group of log analytics workspace> --name <deployment name> --template-file  ./containerSolution.json --parameters @./containerSolutionParams.json
       ```

       A alteração de configuração pode demorar alguns minutos a ser concluída. Quando estiver concluída, é exibida uma mensagem semelhante à seguinte e inclui o resultado:

       ```azurecli
       provisioningState       : Succeeded
       ```

       Depois de ter ativado a monitorização, pode demorar cerca de 15 minutos até poder ver as métricas de saúde para o cluster.

## <a name="install-the-helm-chart"></a>Instale o gráfico HELM

Nesta secção instale o agente contentorizado para informações sobre o recipiente. Antes de prosseguir, é necessário identificar o ID do espaço de trabalho necessário para o `omsagent.secret.wsid` parâmetro e a chave primária necessária para o `omsagent.secret.key` parâmetro. Pode identificar esta informação executando os seguintes passos e, em seguida, executar os comandos para instalar o agente utilizando o gráfico HELM.

1. Executar o seguinte comando para identificar o ID do espaço de trabalho:

    `az monitor log-analytics workspace list --resource-group <resourceGroupName>`

    Na saída, encontre o nome do espaço de trabalho sob o **nome** de campo e, em seguida, copie o ID do espaço de trabalho desse log analytics sob o **cliente de campoID**.

2. Executar o seguinte comando para identificar a chave primária para o espaço de trabalho:

    `az monitor log-analytics workspace get-shared-keys --resource-group <resourceGroupName> --workspace-name <logAnalyticsWorkspaceName>`

    Na saída, encontre a chave primária sob o campo **primarySharedKey** e, em seguida, copie o valor.

>[!NOTE]
>Os seguintes comandos são aplicáveis apenas para a versão Helm 2. A utilização do `--name` parâmetro não é aplicável com a versão Helm 3. 

>[!NOTE]
>Se o seu cluster Kubernetes comunicar através de um servidor proxy, configuure o parâmetro `omsagent.proxy` com o URL do servidor proxy. Se o cluster não comunicar através de um servidor proxy, então não precisa de especificar este parâmetro. Para mais informações, consulte [o ponto final de proxy Configure mais](#configure-proxy-endpoint) tarde neste artigo.

3. Adicione o repositório de gráficos Azure à sua lista local executando o seguinte comando:

    ```
    helm repo add microsoft https://microsoft.github.io/charts/repo
    ````

4. Instale o gráfico executando o seguinte comando:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<my_prod_cluster> microsoft/azuremonitor-containers
    ```

    Se o espaço de trabalho Log Analytics estiver no Azure China 21Vianet, executar o seguinte comando:

    ```
    $ helm install --name myrelease-1 \
     --set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

    Se o espaço de trabalho Log Analytics estiver no Governo dos EUA, executar o seguinte comando:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

### <a name="enable-the-helm-chart-using-the-api-model"></a>Ativar o gráfico Helm utilizando o Modelo API

Pode especificar um addon no ficheiro json de especificação de cluster do motor AKS, também referido como Modelo API. Neste addon, forneça a versão codificada base64 de `WorkspaceGUID` e do espaço de trabalho Log Analytics onde os `WorkspaceKey` dados de monitorização recolhidos são armazenados. Pode encontrar os `WorkspaceGUID` `WorkspaceKey` passos 1 e 2 na secção anterior.

As definições de API suportadas para o cluster Azure Stack Hub podem ser encontradas neste exemplo - [kubernetes-container-monitoring_existing_workspace_id_and_key.jsem](https://github.com/Azure/aks-engine/blob/master/examples/addons/container-monitoring/kubernetes-container-monitoring_existing_workspace_id_and_key.json). Especificamente, encontre a propriedade **addons** em **kubernetesConfig:**

```json
"orchestratorType": "Kubernetes",
       "kubernetesConfig": {
         "addons": [
           {
             "name": "container-monitoring",
             "enabled": true,
             "config": {
               "workspaceGuid": "<Azure Log Analytics Workspace Id in Base-64 encoded>",
               "workspaceKey": "<Azure Log Analytics Workspace Key in Base-64 encoded>"
             }
           }
         ]
       }
```

## <a name="configure-agent-data-collection"></a>Recolha de dados de agente de configuração

Olhando com a versão 1.0.0, as definições de recolha de dados do agente são controladas a partir do ConfigMap. Consulte [aqui](container-insights-agent-config.md)a documentação sobre as definições de recolha de dados do agente.

Depois de ter implementado com sucesso o gráfico, pode rever os dados do seu cluster híbrido Kubernetes em insights de Contentores a partir do portal Azure.  

>[!NOTE]
>A latência da ingestão está a cerca de 5 a 10 minutos do agente para se comprometer no espaço de trabalho Azure Log Analytics. Estado do cluster mostra o valor **Nenhum dado** ou **Desconhecido** até que todos os dados de monitorização necessários estão disponíveis no Azure Monitor.

## <a name="configure-proxy-endpoint"></a>Configure ponto final de procuração

Começando pela versão 2.7.1 do gráfico, o gráfico irá suportar especificar o ponto final de procuração com o parâmetro da `omsagent.proxy` tabela. Isto permite-lhe comunicar através do seu servidor proxy. A comunicação entre o agente de insights do Contentor e o Azure Monitor pode ser um servidor de procuração HTTP ou HTTPS, e a autenticação anónima e básica (nome de utilizador/palavra-passe) são suportadas.

O valor de configuração proxy tem a seguinte sintaxe: `[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
>Se o seu servidor proxy não necessitar de autenticação, ainda precisa de especificar um nome de utilizador/palavra-passe psuedo. Este pode ser qualquer nome de utilizador ou senha.

|Propriedade| Descrição |
|--------|-------------|
|Protocolo | http ou https |
|utilizador | Nome de utilizador opcional para autenticação por procuração |
|palavra-passe | Senha opcional para autenticação por procuração |
|proxyhost | Endereço ou FQDN do servidor proxy |
|porta | Número de porta opcional para o servidor proxy |

Por exemplo: `omsagent.proxy=http://user01:password@proxy01.contoso.com:8080`

Se especificar o protocolo como **http,** os pedidos HTTP são criados utilizando a ligação segura SSL/TLS. O seu servidor proxy deve suportar os protocolos SSL/TLS.

## <a name="troubleshooting"></a>Resolução de problemas

Se encontrar um erro ao tentar ativar a monitorização do seu cluster híbrido Kubernetes, copie o script PowerShell [TroubleshootError_nonAzureK8s.ps1](https://aka.ms/troubleshoot-non-azure-k8s) e guarde-o numa pasta no seu computador. Este script é fornecido para ajudar a detetar e corrigir os problemas encontrados. As questões que se destinam a detetar e tentar corrigir são as seguintes:

- O espaço de trabalho especificado log analytics é válido
- O espaço de trabalho Log Analytics está configurado com a solução de insights do Recipiente. Caso contrário, configuure o espaço de trabalho.
- As cápsulas de réplica omsAgent estão a funcionar
- As cápsulas omsAgent daemonset estão a funcionar
- O serviço de saúde da OmsAgent está em funcionamento
- O ID do espaço de trabalho Log Analytics e a chave configurada no agente contentorizado coincidem com o espaço de trabalho com o que o Insight está configurado.
- Valide todos os nós de trabalhadores Linux têm `kubernetes.io/role=agent` etiqueta para agendar o vagem rs. Se não existir, adicione.
- Valide `cAdvisor secure port:10250` ou seja aberto em todos os nós do `unsecure port: 10255` cluster.

Para executar com a Azure PowerShell, utilize os seguintes comandos na pasta que contém o script:

```powershell
.\TroubleshootError_nonAzureK8s.ps1 - azureLogAnalyticsWorkspaceResourceId </subscriptions/<subscriptionId>/resourceGroups/<resourcegroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName> -kubeConfig <kubeConfigFile> -clusterContextInKubeconfig <clusterContext>
```

## <a name="next-steps"></a>Passos seguintes

Com a monitorização habilitada a recolher a saúde e a utilização de recursos do seu cluster híbrido Kubernetes e cargas de trabalho em execução sobre eles, [aprenda a usar](container-insights-analyze.md) insights de Contentores.