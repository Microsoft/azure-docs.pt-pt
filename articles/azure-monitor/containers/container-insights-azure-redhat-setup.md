---
title: Configure Azure Red Hat OpenShift v3.x com Monitor Azure para recipientes | Microsoft Docs
description: Este artigo descreve como configurar a monitorização de um cluster Kubernetes com o Azure Monitor hospedado na versão 3 e superior do Azure Red Hat OpenShift.
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: f21a338a06d4a0947e2623854d828c720fb2d4bb
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100620121"
---
# <a name="configure-azure-red-hat-openshift-v3-with-azure-monitor-for-containers"></a>Configure Azure Red Hat OpenShift v3 com Monitor Azure para contentores

>[!IMPORTANT]
> Azure Red Hat OpenShift 3.11 será retirado em junho de 2022.
>
> A partir de outubro de 2020 já não poderá criar novos clusters 3.11.
> Os agrupamentos de 3,11 existentes continuarão a funcionar até junho de 2022, mas deixarão de ser apoiados após essa data.
>
> Siga este guia para [criar um cluster Azure Red Hat OpenShift 4](../../openshift/tutorial-create-cluster.md).
> Se tiver questões específicas, [contacte-nos.](mailto:aro-feedback@microsoft.com)

O Azure Monitor para contentores proporciona uma experiência de monitorização rica para os clusters Azure Kubernetes Service (AKS) e AKS Engine. Este artigo descreve como permitir a monitorização dos clusters Kubernetes alojados na versão 3 do [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) e na versão mais recente suportada da versão 3, para obter uma experiência de monitorização semelhante.

>[!NOTE]
>Suporte para Azure Red Hat OpenShift é uma funcionalidade em pré-visualização pública neste momento.
>

O Monitor Azure para contentores pode ser ativado para novas ou mais implementações existentes do Azure Red Hat OpenShift utilizando os seguintes métodos suportados:

- Para um cluster existente a partir do portal Azure ou usando o modelo de Gestor de Recursos Azure.
- Para um novo cluster usando o modelo Azure Resource Manager, ou enquanto cria um novo cluster usando o [Azure CLI](/cli/azure/openshift#az-openshift-create).

## <a name="supported-and-unsupported-features"></a>Funcionalidades suportadas e não apoiadas

O Azure Monitor para contentores suporta a monitorização do Azure Red Hat OpenShift, conforme descrito no artigo de [visão geral,](container-insights-overview.md) com exceção das seguintes características:

- Dados ao Vivo (pré-visualização)
- [Recolher métricas](container-insights-update-metrics.md) de nódoas e cápsulas de cluster e guardá-las na base de dados de métricas do Monitor Azure

## <a name="prerequisites"></a>Pré-requisitos

- Uma [área de trabalho do Log Analytics](../platform/design-logs-deployment.md).

    O Azure Monitor para contentores suporta um espaço de trabalho Log Analytics nas regiões listadas nos Produtos Azure [por região.](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor) Para criar o seu próprio espaço de trabalho, pode ser criado através do [Azure Resource Manager,](../samples/resource-manager-workspace.md)através do [PowerShell,](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)ou no [portal Azure.](../learn/quick-create-workspace.md)

- Para ativar e aceder às funcionalidades no Azure Monitor para contentores, é necessário ser membro da função *Azure Contributor* na subscrição Azure, e membro da função de Contribuinte [*Log Analytics*](../platform/manage-access.md#manage-access-using-azure-permissions) do espaço de trabalho Log Analytics configurado com O Monitor Azure para contentores.

- Para visualizar os dados de monitorização, é membro da permissão de função do [*leitor log Analytics*](../platform/manage-access.md#manage-access-using-azure-permissions) com o espaço de trabalho Log Analytics configurado com o Azure Monitor para contentores.

## <a name="identify-your-log-analytics-workspace-id"></a>Identifique o seu ID do espaço de trabalho Log Analytics

 Para se integrar com um espaço de trabalho log analytics existente, comece por identificar o ID completo do seu espaço de trabalho Log Analytics. O ID de recursos do espaço de trabalho é necessário para o parâmetro `workspaceResourceId` quando ativa a monitorização utilizando o método do gestor de recursos Azure.

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

## <a name="enable-for-a-new-cluster-using-an-azure-resource-manager-template"></a>Ativar um novo cluster usando um modelo de Gestor de Recursos Azure

Execute os seguintes passos para implantar um cluster Azure Red Hat OpenShift com monitorização ativada. Antes de prosseguir, reveja o tutorial [Crie um cluster Azure Red Hat OpenShift](../../openshift/tutorial-create-cluster.md) para entender as dependências que precisa de configurar para que o seu ambiente seja configurado corretamente.

Este método inclui dois modelos JSON. Um modelo especifica a configuração para implantar o cluster com a monitorização ativada, e o outro contém valores de parâmetros que configura para especificar o seguinte:

- O ID de recurso de cluster OpenShift do chapéu vermelho azul.

- O grupo de recursos em que o cluster é implantado.

- [O ID do inquilino do Azure Ative Directory](../../openshift/howto-create-tenant.md#create-a-new-azure-ad-tenant) observou depois de executar os passos para criar um ou um já criado.

- [ID de aplicação do cliente do Azure Ative Directory](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-app-registration) anotado após a execução dos passos para criar um ou um já criado.

- [Azure Ative Directory Client secret](../../openshift/howto-aad-app-configuration.md#create-a-client-secret) observou após a execução dos passos para criar um ou um já criado.

- [O grupo de segurança AZure AD](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-security-group) observou depois de executar os passos para criar um ou um já criado.

- ID de recursos de um espaço de trabalho log analytics existente. Consulte [identifique o seu ID do espaço de trabalho Log Analytics](#identify-your-log-analytics-workspace-id) para aprender a obter esta informação.

- O número de nós mestres para criar no cluster.

- O número de nós computacional no perfil de piscina do agente.

- O número de nós de infraestrutura no perfil da piscina do agente.

Se não estiver familiarizado com o conceito de implantação de recursos utilizando um modelo, consulte:

- [Implementar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)

- [Implementar recursos com modelos de Gestor de Recursos e o CLI Azure](../../azure-resource-manager/templates/deploy-cli.md)

Se optar por utilizar o Azure CLI, primeiro tem de instalar e utilizar o CLI localmente. Deve estar a executar a versão Azure CLI 2.0.65 ou posterior. Para identificar a sua versão, corra `az --version` . Se necessitar de instalar ou atualizar o Azure CLI, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli).

1. Faça o download e guarde para uma pasta local, o modelo do Gestor de Recursos Azure e o ficheiro de parâmetros, para criar um cluster com o add-on de monitorização utilizando os seguintes comandos:

    `curl -LO https://raw.githubusercontent.com/microsoft/Docker-Provider/ci_dev/scripts/onboarding/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoring.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/Docker-Provider/ci_dev/scripts/onboarding/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoringParam.json`

2. Iniciar sessão no Azure

    ```azurecli
    az login
    ```

    Se tiver acesso a várias subscrições, faça `az account set -s {subscription ID}` a substituição `{subscription ID}` pela subscrição que pretende utilizar.

3. Crie um grupo de recursos para o seu cluster se ainda não tiver um. Para obter uma lista de regiões de Azure que suportam o OpenShift on Azure, consulte [Regiões Apoiadas.](../../openshift/supported-resources.md#azure-regions)

    ```azurecli
    az group create -g <clusterResourceGroup> -l <location>
    ```

4. Editar o ficheiro de parâmetro JSON **newClusterWithMonitoringParam.js** ligado e atualizar os seguintes valores:

    - *localização*
    - *clusterName*
    - *aadTenantId*
    - *aadClientId*
    - *aadClientSecret*
    - *aadCustomerAdminGroupId*
    - *workspaceResourceId*
    - *masterNodeCount*
    - *computeNodeCount*
    - *infraNodeCount*

5. O passo seguinte implanta o cluster com a monitorização ativada através do CLI Azure.

    ```azurecli
    az deployment group create --resource-group <ClusterResourceGroupName> --template-file ./newClusterWithMonitoring.json --parameters @./newClusterWithMonitoringParam.json
    ```

    A saída assemelha-se ao seguinte:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="enable-for-an-existing-cluster"></a>Ativar um cluster existente

Execute os seguintes passos para permitir a monitorização de um cluster Azure Red Hat OpenShift implantado em Azure. Pode fazê-lo a partir do portal Azure ou utilizando os modelos fornecidos.

### <a name="from-the-azure-portal"></a>No portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. No menu do portal Azure ou na página Inicial, selecione **Azure Monitor**. Na secção **Insights,** selecione **Containers**.

3. Na página **Monitor - contentores,** selecione **clusters não monitorizados**.

4. A partir da lista de clusters não monitorizados, encontre o cluster na lista e clique em **Enable**. Pode identificar os resultados da lista procurando o valor **ARO** sob a coluna **CLUSTER TYPE**.

5. Na página **"Onboarding to Azure Monitor" para contentores,** se tiver um espaço de trabalho log analytics existente na mesma subscrição que o cluster, selecione-o da lista de espera.  
    A lista pré-seleciona o espaço de trabalho predefinido e a localização para a qual o cluster é implantado na subscrição.

    ![Permitir a monitorização de clusters não monitorizados](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >Se pretender criar um novo espaço de trabalho log Analytics para armazenar os dados de monitorização do cluster, siga as instruções no Criar um espaço de [trabalho Log Analytics](../learn/quick-create-workspace.md). Certifique-se de criar o espaço de trabalho na mesma subscrição para a qual o cluster RedHat OpenShift está implantado.

Depois de ter ativado a monitorização, pode demorar cerca de 15 minutos até poder ver as métricas de saúde para o cluster.

### <a name="enable-using-an-azure-resource-manager-template"></a>Ative a utilização de um modelo de Gestor de Recursos Azure

Este método inclui dois modelos JSON. Um modelo especifica a configuração para permitir a monitorização, e o outro contém valores de parâmetros que configura para especificar o seguinte:

- O ID de recurso do cluster OpenShift Azure RedHat.

- O grupo de recursos em que o cluster é implantado.

- Uma área de trabalho do Log Analytics. Consulte [identifique o seu ID do espaço de trabalho Log Analytics](#identify-your-log-analytics-workspace-id) para aprender a obter esta informação.

Se não estiver familiarizado com o conceito de implantação de recursos utilizando um modelo, consulte:

- [Implementar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)

- [Implementar recursos com modelos de Gestor de Recursos e o CLI Azure](../../azure-resource-manager/templates/deploy-cli.md)

Se optar por utilizar o Azure CLI, primeiro tem de instalar e utilizar o CLI localmente. Deve estar a executar a versão Azure CLI 2.0.65 ou posterior. Para identificar a sua versão, corra `az --version` . Se necessitar de instalar ou atualizar o Azure CLI, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli).

1. Descarregue o modelo e o ficheiro de parâmetros para atualizar o seu cluster com o add-on de monitorização utilizando os seguintes comandos:

    `curl -LO https://raw.githubusercontent.com/microsoft/Docker-Provider/ci_dev/scripts/onboarding/aro/enable_monitoring_to_existing_cluster/existingClusterOnboarding.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/Docker-Provider/ci_dev/scripts/onboarding/aro/enable_monitoring_to_existing_cluster/existingClusterParam.json`

2. Iniciar sessão no Azure

    ```azurecli
    az login
    ```

    Se tiver acesso a várias subscrições, faça `az account set -s {subscription ID}` a substituição `{subscription ID}` pela subscrição que pretende utilizar.

3. Especifique a subscrição do cluster Azure RedHat OpenShift.

    ```azurecli
    az account set --subscription "Subscription Name"  
    ```

4. Executar o seguinte comando para identificar a localização do cluster e identificação de recursos:

    ```azurecli
    az openshift show -g <clusterResourceGroup> -n <clusterName>
    ```

5. Editar o ficheiro de parâmetro JSON **existingClusterParam.js** ligado e atualizar os valores *aroResourceId* e *aroResourceLocation*. O valor para **o espaço de trabalhoResourceId** é o ID de recursos completo do seu espaço de trabalho Log Analytics, que inclui o nome do espaço de trabalho.

6. Para implementar com o Azure CLI, executar os seguintes comandos:

    ```azurecli
    az deployment group create --resource-group <ClusterResourceGroupName> --template-file ./ExistingClusterOnboarding.json --parameters @./existingClusterParam.json
    ```

    A saída assemelha-se ao seguinte:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="next-steps"></a>Passos seguintes

- Com a monitorização habilitada a recolher a saúde e a utilização de recursos do seu cluster RedHat OpenShift e cargas de trabalho em execução, aprenda [a utilizar o](container-insights-analyze.md) Azure Monitor para recipientes.

- Por predefinição, o agente contentor recolhe os registos de contentores stdout/stderr de todos os recipientes que estão a funcionar em todos os espaços de nome, exceto o sistema kube. Para configurar a recolha de registos de contentores específica para espaços de nome ou espaços de nome específicos, reveja a [configuração do agente Do Contentor Insights](container-insights-agent-config.md) para configurar as definições de recolha de dados desejadas no seu ficheiro de configurações ConfigMap.

- Para raspar e analisar as métricas prometheus do seu cluster, [reveja métricas configure Prometheus raspando](container-insights-prometheus-integration.md)

- Para aprender a parar de monitorizar o seu cluster com o Azure Monitor para obter recipientes, consulte [Como Parar de monitorizar o seu aglomerado de chapéus abertos Azure Red Hat](./container-insights-optout-openshift-v3.md).
