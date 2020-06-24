---
title: Configure Azure Red Hat OpenShift v4.x com Monitor Azure para contentores / Microsoft Docs
description: Este artigo descreve como configurar a monitorização de um cluster Kubernetes com o Azure Monitor hospedado na versão 4 e superior do Azure Red Hat OpenShift.
ms.topic: conceptual
ms.date: 06/15/2020
ms.openlocfilehash: 872d842f02e19313940dfeba5258feb7d3799547
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2020
ms.locfileid: "84888448"
---
# <a name="configure-azure-red-hat-openshift-v4x-with-azure-monitor-for-containers"></a>Configure Azure Red Hat OpenShift v4.x com Monitor Azure para recipientes

O Azure Monitor para contentores proporciona uma experiência de monitorização rica para os clusters Azure Kubernetes Service (AKS) e AKS Engine. Este artigo descreve como permitir a monitorização dos clusters Kubernetes alojados na versão 4.x [do Azure Red Hat OpenShift](../../openshift/intro-openshift.md) para obter uma experiência de monitorização semelhante.

>[!NOTE]
>Suporte para Azure Red Hat OpenShift é uma funcionalidade em pré-visualização pública neste momento.
>

O Monitor Azure para contentores pode ser ativado para uma ou mais implementações existentes do Azure Red Hat OpenShift v4.x utilizando os seguintes métodos suportados:

- Para um cluster existente utilizando o roteiro de bash fornecido e correndo no [Azure CLI](https://docs.microsoft.com/cli/azure/openshift?view=azure-cli-latest#az-openshift-create).

## <a name="supported-and-unsupported-features"></a>Funcionalidades suportadas e não apoiadas

O Azure Monitor para contentores suporta a monitorização do Azure Red Hat OpenShift v4.x, conforme descrito no artigo do [Overview,](container-insights-overview.md) com exceção das seguintes características:

- Dados ao Vivo (pré-visualização)
- [Recolher métricas](container-insights-update-metrics.md) de nódoas e cápsulas de cluster e guardá-las na base de dados de métricas do Monitor Azure

## <a name="prerequisites"></a>Pré-requisitos

- Versão Azure CLI 2.0.72 ou maior

- [Leme 3](https://helm.sh/docs/intro/install/) Ferramenta CLI

- [Bash versão 4](https://www.gnu.org/software/bash/)

- Ferramenta de linha de comando [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

- Para ativar e aceder às funcionalidades no Azure Monitor para contentores, é necessário ser membro da função *Azure Contributor* na subscrição Azure, e membro da função de Contribuinte [*Log Analytics*](../platform/manage-access.md#manage-access-using-azure-permissions) do espaço de trabalho Log Analytics configurado com O Monitor Azure para contentores.

- Para visualizar os dados de monitorização, é membro da permissão de função do [*leitor log Analytics*](../platform/manage-access.md#manage-access-using-azure-permissions) com o espaço de trabalho Log Analytics configurado com o Azure Monitor para contentores.

## <a name="enable-for-an-existing-cluster"></a>Ativar um cluster existente

Execute os seguintes passos para permitir a monitorização de uma versão 4 do Chapéu Vermelho Azure e um cluster superior implantado em Azure utilizando o script de bash fornecido.

1. Iniciar sessão no Azure

    ```azurecli
    az login
    ```

2. Faça o download e guarde o script para uma pasta local que configura o seu cluster com o add-on de monitorização utilizando os seguintes comandos:

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aroV4/onboarding_azuremonitor_for_containers.sh.`

3. Para identificar o **contexto kube** do seu cluster, depois de ter sido bem sucedido `oc login` no seu cluster, executar o comando e copiar `kubectl config current-context` o valor.

### <a name="integrate-with-an-existing-workspace"></a>Integre-se com um espaço de trabalho existente

O passo seguinte permite monitorizar o seu cluster utilizando o script de bash que descarregou anteriormente. Para se integrar com um espaço de trabalho log analytics existente, execute os seguintes passos para primeiro identificar o ID completo do seu espaço de trabalho Log Analytics necessário para o `workspaceResourceId` parâmetro e, em seguida, executar o comando para ativar o add-on de monitorização contra o espaço de trabalho especificado. Se não tiver um espaço de trabalho para especificar, pode saltar para a secção [Integrar-se com espaço de trabalho predefinido](#integrate-with-default-workspace) e deixar o script criar um novo espaço de trabalho para si.

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

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    Na saída, encontre o nome do espaço de trabalho e, em seguida, copie o ID completo do recurso daquele espaço de trabalho Log Analytics sob o **ID**de campo .

4. Executar o seguinte comando para permitir a monitorização, substituindo o valor para os `workspaceResourceId` parâmetros e `azureAroV4ResourceIdparameter` parâmetros: 

    `bash onboarding_azuremonitor_for_containers.sh <kube-context> <azureAroV4ResourceId> <workspaceResourceId>`

    Exemplo:

    `bash onboarding_azuremonitor_for_containers.sh MyK8sTestCluster /subscriptions/0fb60ef2-03cc-4290-b595-e71108e8f4ce/resourceGroups/test-aro-v4-rg/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/test-aro-v4 /subscriptions/0fb60ef2-03cc-4290-b595-e71108e8f4ce/resourcegroups/test-la-workspace-rg/providers/microsoft.operationalinsights/workspaces/test-la-workspace`

Depois de ter ativado a monitorização, pode demorar cerca de 15 minutos até poder ver as métricas de saúde para o cluster.

### <a name="integrate-with-default-workspace"></a>Integre-se com espaço de trabalho predefinido

O passo seguinte permite monitorizar o seu cluster Azure Red Hat OpenShift v4.x utilizando o script de bash que descarregou. Neste exemplo, não é obrigado a criar ou especificar um espaço de trabalho existente. Este comando simplifica o processo para si, criando um espaço de trabalho predefinido no grupo de recursos predefinidos da subscrição do cluster se já não existir na região. O espaço de trabalho padrão criado assemelha-se ao formato *defaultWorkspace- \<GUID> - \<Region> *.  

`bash onboarding_azuremonitor_for_containers.sh <kube-context> <azureAroV4ResourceId>`

Por exemplo:

`bash onboarding_azuremonitor_for_containers.sh MyK8sTestCluster /subscriptions/0fb60ef2-03cc-4290-b595-e71108e8f4ce/resourceGroups/test-aro-v4-rg/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/test-aro-v4`

Depois de ter ativado a monitorização, pode demorar cerca de 15 minutos até poder ver as métricas de saúde para o cluster.

### <a name="from-the-azure-portal"></a>No portal do Azure

A vista multi-cluster no Azure Monitor para contentores destaca os seus clusters Azure Red Hat OpenShift que não têm monitorização ativada no **separador clusters não monitorizados.** A opção **Enable** ao lado do seu cluster não inicia o embarque de monitorização a partir do portal. Você é redirecionado para este artigo para permitir manualmente a monitorização seguindo os passos anteriores neste artigo.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. No menu do portal Azure ou na página Inicial, selecione **Azure Monitor**. Na secção **Insights,** selecione **Containers**.

3. Na página **Monitor - contentores,** selecione **clusters não monitorizados**.

4. A partir da lista de clusters não monitorizados, encontre o cluster na lista e clique em **Enable**. Pode identificar os resultados da lista procurando o valor **ARO** sob a coluna **CLUSTER TYPE**. Depois de clicar em **Enable,** é redirecionado para este artigo.

## <a name="next-steps"></a>Passos seguintes

- Com a monitorização ativada para recolher a saúde e a utilização de recursos do seu cluster RedHat OpenShift 4.x e cargas de trabalho em execução, aprenda [a utilizar](container-insights-analyze.md) o Azure Monitor para recipientes.

- Por predefinição, o agente contentor recolhe os registos de contentores stdout/stderr de todos os recipientes que estão a funcionar em todos os espaços de nome, exceto o sistema kube. Para configurar a recolha de registos de contentores específica para espaços de nome ou espaços de nome específicos, reveja a [configuração do agente Do Contentor Insights](container-insights-agent-config.md) para configurar as definições de recolha de dados desejadas no seu ficheiro de configurações ConfigMap.

- Para raspar e analisar as métricas prometheus do seu cluster, [reveja métricas configure Prometheus raspando](container-insights-prometheus-integration.md)

- Para aprender a parar de monitorizar o seu cluster com o Azure Monitor para obter recipientes, consulte [Como Parar de monitorizar o seu aglomerado de chapéus abertos Azure Red Hat](container-insights-optout-openshift.md).
