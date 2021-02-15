---
title: Configure Azure Red Hat OpenShift v4.x com Monitor Azure para recipientes | Microsoft Docs
description: Este artigo descreve como configurar a monitorização para um cluster Kubernetes com o Azure Monitor que está hospedado na versão 4 ou mais tarde do Azure Red Hat OpenShift.
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: e6668ac22c6c0f53c7511cfb76bf50c5474f3a76
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/15/2021
ms.locfileid: "100521633"
---
# <a name="configure-azure-red-hat-openshift-v4x-with-azure-monitor-for-containers"></a>Configure Azure Red Hat OpenShift v4.x com Monitor Azure para recipientes

O Azure Monitor para contentores proporciona uma rica experiência de monitorização para os clusters de motores Azure Kubernetes (AKS) e AKS. Este artigo descreve como obter uma experiência de monitorização semelhante, permitindo a monitorização de clusters Kubernetes que são hospedados na versão 4.x [do Azure Red Hat OpenShift.](../../openshift/intro-openshift.md)

>[!NOTE]
>Suporte para Azure Red Hat OpenShift é uma funcionalidade em pré-visualização pública neste momento.
>

Pode ativar o Azure Monitor para contentores para uma ou mais implementações existentes do Azure Red Hat OpenShift v4.x utilizando os métodos suportados descritos neste artigo.

Para um cluster existente, execute este [script Bash no Azure CLI](/cli/azure/openshift#az-openshift-create&preserve-view=true).

## <a name="supported-and-unsupported-features"></a>Funcionalidades suportadas e não apoiadas

O Monitor Azure para contentores suporta a monitorização do Azure Red Hat OpenShift v4.x, conforme descrito no [Azure Monitor para a visão geral dos contentores,](container-insights-overview.md)com exceção das seguintes características:

- Dados ao Vivo (pré-visualização)
- [Recolher métricas](container-insights-update-metrics.md) de nódoas e cápsulas de cluster e guardá-las na base de dados de métricas do Monitor Azure

## <a name="prerequisites"></a>Pré-requisitos

- A versão Azure CLI 2.0.72 ou mais tarde  

- A ferramenta [Helm 3](https://helm.sh/docs/intro/install/) CLI

- [Bash versão 4](https://www.gnu.org/software/bash/)

- A ferramenta de linha de comando [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

- Uma [área de trabalho do Log Analytics](../platform/design-logs-deployment.md).

    O Azure Monitor para contentores suporta um espaço de trabalho Log Analytics nas regiões listadas nos Produtos Azure [por região.](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor) Para criar o seu próprio espaço de trabalho, pode ser criado através do [Azure Resource Manager,](../samples/resource-manager-workspace.md)através do [PowerShell,](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)ou no [portal Azure.](../learn/quick-create-workspace.md)

- Para ativar e aceder às funcionalidades no Azure Monitor para contentores, é necessário ter, no mínimo, um papel *de Azure Contribute* na subscrição do Azure e uma função [*de Contribuinte Log Analytics*](../platform/manage-access.md#manage-access-using-azure-permissions) no espaço de trabalho Log Analytics, configurado com monitor Azure para contentores.

- Para visualizar os dados de monitorização, é necessário ter o papel de [*leitor do Log Analytics*](../platform/manage-access.md#manage-access-using-azure-permissions) no espaço de trabalho Do Log Analytics, configurado com o Azure Monitor para contentores.

## <a name="enable-monitoring-for-an-existing-cluster"></a>Permitir a monitorização de um cluster existente

Para permitir a monitorização de um Azure Red Hat OpenShift versão 4 ou posterior cluster que é implantado em Azure usando o script bash fornecido, faça o seguinte:

1. Inicie sessão no Azure ao executar o seguinte comando:

    ```azurecli
    az login
    ```

1. Faça o download e guarde para uma pasta local o script que configura o seu cluster com o add-in de monitorização executando o seguinte comando:

    `curl -o enable-monitoring.sh -L https://aka.ms/enable-monitoring-bash-script`

1. Para identificar o *kubeContext* do seu cluster, executar os seguintes comandos

    ```
    adminUserName=$(az aro list-credentials -g $clusterResourceGroup -n $clusterName --query 'kubeadminUsername' -o tsv)
    adminPassword=$(az aro list-credentials -g $clusterResourceGroup -n $clusterName --query 'kubeadminPassword' -o tsv)
    apiServer=$(az aro show -g $clusterResourceGroup -n $clusterName --query apiserverProfile.url -o tsv)
    oc login $apiServer -u $adminUserName -p $adminPassword
    # openshift project name for azure monitor for containers
    openshiftProjectName="azure-monitor-for-containers"
    oc new-project $openshiftProjectName
    # get the kube config context
    kubeContext=$(oc config current-context)
    ```

1. Copie o valor para utilização posterior.

### <a name="integrate-with-an-existing-workspace"></a>Integre-se com um espaço de trabalho existente

Nesta secção, você pode ativar a monitorização do seu cluster usando o script Bash que descarregou anteriormente. Para se integrar com um espaço de trabalho log analytics existente, comece por identificar o ID completo do seu espaço de trabalho Log Analytics que é necessário para o `logAnalyticsWorkspaceResourceId` parâmetro e, em seguida, executar o comando para ativar o add-in de monitorização contra o espaço de trabalho especificado.

Se não tiver um espaço de trabalho para especificar, pode saltar para a Secção De Integração com a secção [de espaço de trabalho predefinido](#integrate-with-the-default-workspace) e deixar o script criar um novo espaço de trabalho para si.

1. Liste todas as subscrições a que tem acesso executando o seguinte comando:

    ```azurecli
    az account list --all -o table
    ```

    A saída será parecida com as seguintes:

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   0fb60ef2-03cc-4290-b595-e71108e8f4ce  Enabled  True
    ```

1. Copie o valor para **SubscriçãoId**.

1. Mude para a subscrição que acolhe o espaço de trabalho Log Analytics executando o seguinte comando:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

1. Mostrar a lista de espaços de trabalho nas suas subscrições no formato JSON predefinido, executando o seguinte comando:

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

1. Na saída, encontre o nome do espaço de trabalho e, em seguida, copie o ID completo do recurso daquele espaço de trabalho Log Analytics sob o **ID** de campo .

1. Para ativar a monitorização, executar o seguinte comando. Substitua os valores do `azureAroV4ClusterResourceId` `logAnalyticsWorkspaceResourceId` , e `kubeContext` parâmetros.

    ```bash
    export azureAroV4ClusterResourceId=“/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/<clusterName>”
    export logAnalyticsWorkspaceResourceId=“/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>”
    export kubeContext="<kubeContext name of your ARO v4 cluster>"  
    ```

    Aqui está o comando que deve executar depois de ter povoado as 3 variáveis com comandos de Exportação:

    `bash enable-monitoring.sh --resource-id $azureAroV4ClusterResourceId --kube-context $kubeContext --workspace-id $logAnalyticsWorkspaceResourceId`

Depois de ter ativado a monitorização, pode demorar cerca de 15 minutos até poder ver as métricas de saúde do cluster.

### <a name="integrate-with-the-default-workspace"></a>Integre-se com o espaço de trabalho predefinido

Nesta secção, você pode ativar a monitorização para o seu cluster Azure Red Hat OpenShift v4.x usando o script Bash que você descarregou.

Neste exemplo, não é obrigado a pré-criar ou especificar um espaço de trabalho existente. Este comando simplifica o processo para si, criando um espaço de trabalho predefinido no grupo de recursos predefinidos da subscrição do cluster, caso já não exista na região.

O espaço de trabalho predefinido que é criado está no formato *defaultWorkspace- \<GUID> - \<Region>*.  

Substitua os valores para os `azureAroV4ClusterResourceId` `kubeContext` parâmetros e parâmetros.

```bash
export azureAroV4ClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/<clusterName>"
export kubeContext="<kubeContext name of your ARO v4 cluster>"
```

Por exemplo:

`bash enable-monitoring.sh --resource-id $azureAroV4ClusterResourceId --kube-context $kubeContext`

Depois de ter ativado a monitorização, pode demorar cerca de 15 minutos até poder ver as métricas de saúde para o cluster.

### <a name="enable-monitoring-from-the-azure-portal"></a>Permitir a monitorização a partir do portal Azure

A vista multi-cluster no Azure Monitor para contentores destaca os seus clusters Azure Red Hat OpenShift que não têm monitorização ativada no **separador clusters não monitorizados.** A opção **Enable** ao lado do seu cluster não inicia o embarque de monitorização a partir do portal. Você é redirecionado para este artigo para permitir a monitorização manualmente seguindo os passos que foram delineados anteriormente neste artigo.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No painel esquerdo ou na página inicial, selecione **Azure Monitor**.

1. Na secção **Insights,** selecione **Containers**.

1. Na página **Monitor - recipientes,** selecione **clusters não monitorizados**.

1. Na lista de clusters não monitorizados, selecione o cluster e, em seguida, selecione **Enable**.

    Pode identificar os resultados da lista procurando o valor **ARO** na coluna **Cluster Type.** Depois de selecionar **Enable,** é redirecionado para este artigo.

## <a name="next-steps"></a>Passos seguintes

- Agora que permitiu a monitorização para recolher a saúde e a utilização de recursos do seu cluster RedHat OpenShift 4.x e das cargas de trabalho que estão a funcionar sobre eles, aprenda [a usar](container-insights-analyze.md) o Azure Monitor para recipientes.

- Por predefinição, o agente contentor recolhe os registos de contentores *de stdout* e *stderr* de todos os recipientes que estão a funcionar em todos os espaços de nome, exceto o sistema kube. Para configurar uma recolha de registos de contentores específica para um determinado espaço de nome ou espaço de nome, reveja a [configuração](container-insights-agent-config.md) do agente Do Contentor Insights para configurar as definições de recolha de dados que deseja para o seu ficheiro de configuração *ConfigMap.*

- Para raspar e analisar as métricas de Prometeu do seu cluster, [reveja as métricas Configure Prometheus raspando](container-insights-prometheus-integration.md).

- Para aprender a parar de monitorizar o seu cluster utilizando o Azure Monitor para recipientes, consulte [Como parar de monitorizar o seu cluster Azure Red Hat OpenShift](./container-insights-optout-openshift-v3.md).
