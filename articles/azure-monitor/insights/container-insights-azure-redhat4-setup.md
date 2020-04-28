---
title: Configure Chapéu Vermelho Azure OpenShift v4.x com Monitor Azure para contentores Microsoft Docs
description: Este artigo descreve como configurar a monitorização de um cluster Kubernetes com o Azure Monitor hospedado na versão 4 do Chapéu Vermelho Azure 4 e superior.
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: 4b827524845874dabaabe535163d99c408f77a60
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82196299"
---
# <a name="configure-azure-red-hat-openshift-v4x-with-azure-monitor-for-containers"></a>Configure Chapéu Vermelho Azure OpenShift v4.x com Monitor Azure para recipientes

O Azure Monitor para contentores proporciona uma rica experiência de monitorização para os clusters Azure Kubernetes Service (AKS) e AKS Engine. Este artigo descreve como permitir a monitorização dos clusters Kubernetes hospedados na versão 4.x do [Chapéu Vermelho Azure](../../openshift/intro-openshift.md) 4.x para alcançar uma experiência de monitorização semelhante.

>[!NOTE]
>O suporte para o Azure Red Hat OpenShift é uma característica na pré-estreia pública neste momento.
>

O Monitor Azure para recipientes pode ser ativado para uma ou mais implementações existentes do Azure Red Hat OpenShift v4.x utilizando os seguintes métodos suportados:

- Para um cluster existente utilizando o script de bash fornecido e funcionando no [Azure CLI](https://docs.microsoft.com/cli/azure/openshift?view=azure-cli-latest#az-openshift-create).

## <a name="supported-and-unsupported-features"></a>Características suportadas e não suportadas

O Monitor Azure para contentores suporta a monitorização do Azure Red Hat OpenShift v4.x, conforme descrito no artigo [overview,](container-insights-overview.md) com exceção das seguintes características:

- Dados ao Vivo (pré-visualização)
- [Recolher métricas](container-insights-update-metrics.md) de nós de cluster e cápsulas e armazená-las na base de dados de métricas do Monitor Azure

## <a name="prerequisites"></a>Pré-requisitos

- Versão Azure CLI 2.0.72 ou superior

- [Leme 3](https://helm.sh/docs/intro/install/) Ferramenta CLI

- [Versão bash 4](https://www.gnu.org/software/bash/)

- Ferramenta de linha de comando [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

- Para ativar e aceder às funcionalidades do Monitor Azure para contentores, no mínimo é necessário ser membro do papel do *Colaborador* Azure na subscrição do Azure, e um membro do papel de Colaborador de [*Log Analytics*](../platform/manage-access.md#manage-access-using-azure-permissions) do espaço de trabalho Log Analytics configurado com o Monitor Azure para contentores.

- Para visualizar os dados de monitorização, é membro da permissão de função de [*leitor de Log Analytics*](../platform/manage-access.md#manage-access-using-azure-permissions) com o espaço de trabalho log Analytics configurado com o Monitor Azure para recipientes.

## <a name="enable-for-an-existing-cluster"></a>Ativar para um cluster existente

Execute os seguintes passos para permitir a monitorização de uma versão 4 do Chapéu Vermelho Azure e um cluster superior implantado em Azure utilizando o script de bash fornecido.

1. Iniciar sessão no Azure

    ```azurecli
    az login
    ```

2. Descarregue e guarde o script para uma pasta local que configura o seu cluster com o complemento de monitorização utilizando os seguintes comandos:

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aroV4/onboarding_azuremonitor_for_containers.sh.`

3. Para identificar o **contexto kube** do `oc login` seu cluster, depois de `kubectl config current-context` ter sucesso no seu cluster, execute o comando e copie o valor.

### <a name="integrate-with-an-existing-workspace"></a>Integrar com um espaço de trabalho existente

O passo seguinte permite a monitorização do seu cluster utilizando o script de bash que descarregou anteriormente. Para integrar com um espaço de trabalho existente no Log Analytics, execute os seguintes passos `workspaceResourceId` para identificar primeiro o id completo do seu espaço de trabalho Log Analytics necessário para o parâmetro e, em seguida, executar o comando para ativar o complemento de monitorização contra o espaço de trabalho especificado. Se não tiver um espaço de trabalho para especificar, pode saltar para o passo 5 e deixar o script criar um novo espaço de trabalho para si.

1. Enumerar todas as subscrições a que tem acesso utilizando o seguinte comando:

    ```azurecli
    az account list --all -o table
    ```

    A saída assemelhar-se-á ao seguinte:

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

4. Executar o seguinte comando para ativar a `workspaceResourceId` monitorização, substituindo o valor para o parâmetro: 

    `bash onboarding_azuremonitor_for_containers.sh <kube-context> <azureAroV4ResourceId> <LogAnayticsWorkspaceResourceId>`

    Exemplo:

    `bash onboarding_azuremonitor_for_containers.sh MyK8sTestCluster /subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/test-aro-v4-rg/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/test-aro-v4  /subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourcegroups/test-la-workspace-rg/providers/microsoft.operationalinsights/workspaces/test-la-workspace`

Depois de ter ativado a monitorização, pode demorar cerca de 15 minutos até ver as métricas de saúde para o cluster.

### <a name="integrate-with-default-workspace"></a>Integrar com espaço de trabalho padrão

O passo seguinte permite a monitorização do seu cluster Azure Red Hat OpenShift v4.x utilizando o script de bash que descarregou. Neste exemplo, não é obrigado a criar ou especificar um espaço de trabalho existente. Este comando simplifica o processo para si criando um espaço de trabalho padrão no grupo de recursos padrão da subscrição do cluster se não existir já na região. O espaço de trabalho predefinido criado assemelha-se ao formato de *DefaultWorkspace-\<GUID\<>- Região>*.  

    `bash onboarding_azuremonitor_for_containers.sh <kube-context> <azureAroV4ResourceId>`

    For example:

    `bash onboarding_azuremonitor_for_containers.sh MyK8sTestCluster /subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/test-aro-v4-rg/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/test-aro-v4`

Depois de ter ativado a monitorização, pode demorar cerca de 15 minutos até ver as métricas de saúde para o cluster.

### <a name="from-the-azure-portal"></a>No portal do Azure

A vista de vários clusters no Monitor Azure para recipientes destaca os seus clusters OpenShift do Chapéu Vermelho Azure que não têm monitorização ativada sob o separador de **clusters não monitorizados.** A opção **Ativa** ao lado do seu cluster não inicia o embarque na monitorização a partir do portal. É redirecionado para este artigo para permitir a monitorização manual seguindo os passos anteriores neste artigo.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. No menu do portal Azure ou na página Inicial, **selecione Azure Monitor**. Na secção **Insights,** selecione **Recipientes**.

3. No Monitor - página de **recipientes,** selecione **clusters não monitorizados**.

4. A partir da lista de clusters não monitorizados, encontre o cluster na lista e clique em **Ativar**. Pode identificar os resultados da lista procurando o valor **ARO** sob a coluna **CLUSTER TYPE**. Depois de clicar em **Ativar,** é redirecionado para este artigo.

## <a name="next-steps"></a>Passos seguintes

- Com a monitorização habilitada a recolher a utilização da saúde e dos recursos do seu cluster RedHat OpenShift 4.x e as cargas de trabalho que estão a decorrer sobre eles, aprenda [a utilizar o](container-insights-analyze.md) Monitor Azure para recipientes.

- Por predefinição, o agente contentorizado recolhe os registos de contentores stdout/stderr de todos os recipientes que estão em funcionamento em todos os espaços de nome, exceto o sistema kube. Para configurar a recolha de registos de contentores específica para espaços de nome ou nomes específicos, reveja a [configuração do agente Container Insights](container-insights-agent-config.md) para configurar as definições de recolha de dados desejadas no ficheiro de configurações do ConfigMap.

- Para raspar e analisar as métricas de Prometeu do seu cluster, reveja as [métricas do Configure Prometheus](container-insights-prometheus-integration.md)

- Para aprender a parar de monitorizar o seu cluster com o Monitor Azure para obter recipientes, consulte [Como Parar de monitorizar o seu cluster OpenShift](container-insights-optout-openshift.md)do seu chapéu vermelho azul .
