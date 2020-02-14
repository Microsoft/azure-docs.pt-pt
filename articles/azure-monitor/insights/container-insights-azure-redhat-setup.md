---
title: Configure Azure Red Hat OpenShift clusters com Monitor Azure para contentores Microsoft Docs
description: Este artigo descreve como configurar a monitorização de um cluster Kubernetes com o Azure Monitor hospedado no Azure Red Hat OpenShift.
ms.topic: conceptual
ms.date: 02/12/2020
ms.openlocfilehash: 215835c04a1877ccdb6454c4c3902332b9dc1ab2
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190081"
---
# <a name="configure-azure-red-hat-openshift-clusters-with-azure-monitor-for-containers"></a>Configure Azure Red Hat OpenShift clusters com Monitor Azure para recipientes

O Azure Monitor para contentores proporciona uma rica experiência de monitorização para os clusters Azure Kubernetes Service (AKS) e AKS Engine. Este artigo descreve como permitir a monitorização dos clusters Kubernetes hospedados no [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) para obter uma experiência de monitorização semelhante.

>[!NOTE]
>O suporte para o Azure Red Hat OpenShift é uma característica na pré-estreia pública neste momento.
>

O Monitor Azure para contentores pode ser ativado para novas ou mais implementações existentes do Azure Red Hat OpenShift utilizando os seguintes métodos suportados:

- Para um cluster existente a partir do portal Azure ou utilizando o modelo do Gestor de Recursos Azure.
- Para um novo cluster utilizando o modelo do Gestor de Recursos Azure, ou ao mesmo tempo criar um novo cluster utilizando o [Azure CLI](https://docs.microsoft.com/cli/azure/openshift?view=azure-cli-latest#az-openshift-create).

## <a name="supported-and-unsupported-features"></a>Características suportadas e não suportadas

O Monitor Azure para contentores suporta a monitorização do Azure Red Hat OpenShift, conforme descrito no artigo [overview,](container-insights-overview.md) com exceção das seguintes características:

- Dados ao Vivo (pré-visualização)
- [Recolher métricas](container-insights-update-metrics.md) de nós de cluster e cápsulas e armazená-las na base de dados de métricas do Monitor Azure

## <a name="prerequisites"></a>Pré-requisitos

- Para ativar e aceder às funcionalidades do Monitor Azure para contentores, no mínimo é necessário ser membro do papel do *Colaborador* Azure na subscrição do Azure, e um membro do papel de Colaborador de [*Log Analytics*](../platform/manage-access.md#manage-access-using-azure-permissions) do espaço de trabalho Log Analytics configurado com o Monitor Azure para contentores.

- Para visualizar os dados de monitorização, é membro da permissão de função de [*leitor de Log Analytics*](../platform/manage-access.md#manage-access-using-azure-permissions) com o espaço de trabalho log Analytics configurado com o Monitor Azure para recipientes.

## <a name="enable-for-a-new-cluster-using-an-azure-resource-manager-template"></a>Ativar para um novo cluster usando um modelo de Gestor de Recursos Azure

Execute os seguintes passos para implantar um cluster OpenShift do Chapéu Vermelho Azure com monitorização ativada. Antes de prosseguir, reveja o tutorial [Criar um cluster OpenShift](../../openshift/tutorial-create-cluster.md#prerequisites) de chapéu vermelho azure para entender as dependências que precisa para configurar para que o seu ambiente seja configurado corretamente.

Esse método inclui dois modelos JSON. Um modelo especifica a configuração para implantar o cluster com monitorização ativada, e o outro contém valores de parâmetros que configura para especificar o seguinte:

- O id de recurso de cluster OpenShift do Chapéu Vermelho Azure.

- O grupo de recursos em que o cluster está implantado.

- Id de inquilino do [Azure Ative Directory](../../openshift/howto-create-tenant.md#create-a-new-azure-ad-tenant) observou depois de executar os passos para criar um ou um já criado.

- Id de aplicação de cliente do [Azure Ative Directory](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-app-registration) anotado após a realização dos passos para criar um ou um já criado.

- [Azure Ative Directory Client secret](../../openshift/howto-aad-app-configuration.md#create-a-client-secret) notado após realizar os passos para criar um ou um já criado.

- O grupo de [segurança Azure AD](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-security-group) observou depois de realizar os passos para criar um ou um já criado.

- Identificação de recursos de um espaço de trabalho existente no Log Analytics.

- O número de nós mestres para criar no aglomerado.

- O número de nós computacionais no perfil da piscina do agente.

- O número de nós de infraestrutura no perfil da piscina do agente.

Se não estiver familiarizado com o conceito de implementar recursos com um modelo, consulte:

- [Implementar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)

- [Implementar recursos com modelos de Gestor de Recursos e o Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Se optar por utilizar a CLI do Azure, tem primeiro de instalar e utilizar a CLI localmente. Deve estar a executar a versão Azure CLI 2.0.65 ou mais tarde. Para identificar a sua versão, execute `az --version`. Se precisar de instalar ou atualizar o Azure CLI, consulte [Instalar o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

O espaço de trabalho Log Analytics tem de ser criado antes de ativar a monitorização utilizando o Azure PowerShell ou o CLI. Para criar o espaço de trabalho, pode instalá-lo através do [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md), através do [PowerShell,](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)ou no [portal Azure](../../azure-monitor/learn/quick-create-workspace.md).

1. Descarregue e guarde para uma pasta local, o modelo de gestor de recursos azure e o ficheiro parâmetro, para criar um cluster com o addon de monitorização utilizando os seguintes comandos:

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoring.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoringParam.json`

2. Iniciar sessão no Azure

    ```azurecli
    az login    
    ```

    Se tiver acesso a várias subscrições, faça `az account set -s {subscription ID}` substituir `{subscription ID}` pela subscrição que pretende utilizar.

3. Crie um grupo de recursos para o seu cluster se ainda não tiver um. Para uma lista das regiões de Azure que suporta o OpenShift em Azure, consulte [Regiões Apoiadas.](../../openshift/supported-resources.md#azure-regions)

    ```azurecli
    az group create -g <clusterResourceGroup> -l <location>
    ```

4. Editar o ficheiro de parâmetroJSON **novoClusterWithMonitoringParam.json** e atualizar os seguintes valores:

    - *localização*
    - *clusterName*
    - *aadTenantId*
    - *aadClientId*
    - *aadClientSecret*
    - *aadCustomerAdminGroupId*
    - *espaço de trabalhoResourceId*
    - *masterNodeCount*
    - *cálculoNodeCount*
    - *infraNodeCount*

5. O passo seguinte implanta o cluster com monitorização ativada pela utilização do ClI Azure.

    ```azurecli
    az group deployment create --resource-group <ClusterResourceGroupName> --template-file ./newClusterWithMonitoring.json --parameters @./newClusterWithMonitoringParam.json
    ```

    A saída assemelha-se ao seguinte:

    ```azurecli
    provisioningState       : Succeeded
    ```

## <a name="enable-for-an-existing-cluster"></a>Ativar para um cluster existente

Execute os seguintes passos para permitir a monitorização de um cluster OpenShift do Chapéu Vermelho Azure implantado em Azure. Pode fazê-lo a partir do portal Azure ou utilizando os modelos fornecidos.

### <a name="from-the-azure-portal"></a>No portal do Azure

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

2. No menu do portal Azure ou na página Inicial, **selecione Azure Monitor**. Na secção **Insights,** selecione **Recipientes**.

3. No Monitor - página de **recipientes,** selecione **clusters não monitorizados**.

4. A partir da lista de clusters não monitorizados, encontre o cluster na lista e clique em **Ativar**. Pode identificar os resultados da lista procurando o valor **ARO** sob a coluna **CLUSTER TYPE**.

5. No **Onboarding para O Monitor Azure para a** página de contentores, se tiver um espaço de trabalho log analytics existente na mesma subscrição que o cluster, selecione-o a partir da lista de drop-down.  
    A lista pré-seleciona o espaço de trabalho padrão e a localização para a a que o cluster é implantado na subscrição.

    ![Permitir a monitorização de clusters não monitorizados](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >Se pretender criar um novo espaço de trabalho do Log Analytics para armazenar os dados de monitorização do cluster, siga as instruções em [Create a Log Analytics workspace](../../azure-monitor/learn/quick-create-workspace.md). Certifique-se de criar o espaço de trabalho na mesma subscrição para a qual o cluster RedHat OpenShift está implantado.

Depois de ativar a monitorização, poderá demorar cerca de 15 minutos antes de poder visualizar as métricas de estado de funcionamento para o cluster.

### <a name="enable-using-an-azure-resource-manager-template"></a>Ativar usando um modelo de Gestor de Recursos Azure

Esse método inclui dois modelos JSON. Um modelo especifica a configuração para ativar a monitorização e a outra contém valores de parâmetros que pode configurar para especificar o seguinte:

- O Azure RedHat OpenShift cluster resource ID.

- O grupo de recursos em que o cluster está implantado.

- Uma área de trabalho do Log Analytics.

Se não estiver familiarizado com o conceito de implementar recursos com um modelo, consulte:

- [Implementar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)

- [Implementar recursos com modelos de Gestor de Recursos e o Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Se optar por utilizar a CLI do Azure, tem primeiro de instalar e utilizar a CLI localmente. Deve estar a executar a versão Azure CLI 2.0.65 ou mais tarde. Para identificar a sua versão, execute `az --version`. Se precisar de instalar ou atualizar o Azure CLI, consulte [Instalar o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

O espaço de trabalho Log Analytics tem de ser criado antes de ativar a monitorização utilizando o Azure PowerShell ou o CLI. Para criar o espaço de trabalho, pode instalá-lo através do [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md), através do [PowerShell,](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)ou no [portal Azure](../../azure-monitor/learn/quick-create-workspace.md).

1. Descarregue o modelo e o ficheiro parâmetro para atualizar o seu cluster com o complemento de monitorização utilizando os seguintes comandos:

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_existing_cluster/existingClusterOnboarding.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_existing_cluster/existingClusterParam.json`

2. Iniciar sessão no Azure

    ```azurecli
    az login    
    ```

    Se tiver acesso a várias subscrições, faça `az account set -s {subscription ID}` substituir `{subscription ID}` pela subscrição que pretende utilizar.

3. Especifique a subscrição do cluster Azure RedHat OpenShift.

    ```azurecli
    az account set --subscription "Subscription Name"  
    ```

4. Executar o seguinte comando para identificar a localização do cluster e id de recursos:

    ```azurecli
    az openshift show -g <clusterResourceGroup> -n <clusterName>
    ```

5. Editar o ficheiro de parâmetroJSON **existenteClusterParam.json** e atualizar os valores *araResourceId* e *araResoruceLocation*. O valor para o espaço de **trabalhoResourceId** é o id completo de recursos do seu espaço de trabalho Log Analytics, que inclui o nome do espaço de trabalho.

6. Para implantar com o Azure CLI, execute os seguintes comandos:

    ```azurecli
    az group deployment create --resource-group <ClusterResourceGroupName> --template-file ./ExistingClusterOnboarding.json --parameters @./existingClusterParam.json
    ```

    A saída assemelha-se ao seguinte:

    ```azurecli
    provisioningState       : Succeeded
    ```

## <a name="next-steps"></a>Passos seguintes

- Com a monitorização habilitada a recolher a utilização da saúde e dos recursos do seu cluster RedHat OpenShift e as cargas de trabalho que os executam, aprenda [a utilizar o](container-insights-analyze.md) Monitor Azure para contentores.

- Para aprender a parar de monitorizar o seu cluster com o Monitor Azure para obter recipientes, consulte [Como Parar de monitorizar o seu cluster OpenShift](container-insights-optout-openshift.md)do seu chapéu vermelho azul .
