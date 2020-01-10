---
title: Configurar clusters do Azure Red Hat OpenShift com Azure Monitor para contêineres | Microsoft Docs
description: Este artigo descreve como você pode configurar Azure Monitor para contêineres para monitorar clusters kubernetes hospedados no Azure Red Hat OpenShift.
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: 6922cb7b143989ba329df972a06825629c4c5020
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75405578"
---
# <a name="configure-azure-red-hat-openshift-clusters-with-azure-monitor-for-containers"></a>Configurar clusters do Azure Red Hat OpenShift com Azure Monitor para contêineres

Azure Monitor para contêineres fornece experiência de monitoramento avançada para os clusters do AKS (serviço kubernetes do Azure) e do mecanismo do AKS. Este artigo descreve como habilitar o monitoramento de clusters kubernetes hospedados no [Red Hat OpenShift do Azure](../../openshift/intro-openshift.md) para obter uma experiência de monitoramento semelhante.

>[!NOTE]
>O suporte para o Azure Red Hat OpenShift é um recurso em visualização pública no momento.
>

Azure Monitor para contêineres podem ser habilitados para novas ou uma ou mais implantações existentes do Azure Red Hat OpenShift usando os seguintes métodos com suporte:

- Para um cluster existente do portal do Azure ou usando o modelo de Azure Resource Manager
- Para um novo cluster usando Azure Resource Manager modelo 

## <a name="supported-and-unsupported-features"></a>Recursos com e sem suporte

Azure Monitor para contêineres dá suporte ao monitoramento do Azure Red Hat OpenShift conforme descrito no artigo de [visão geral](container-insights-overview.md) , com exceção dos seguintes recursos:

- Dados dinâmicos
- Recorte de métricas de Prometheus
- [Coletar métricas](container-insights-update-metrics.md) de nós de cluster e pods e armazená-los no banco de dados de métricas de Azure monitor
- Recurso de integridade

## <a name="prerequisites"></a>Pré-requisitos

- Para habilitar e acessar os recursos no Azure Monitor para contêineres, no mínimo você precisa ser um membro da função *colaborador* do Azure na assinatura do Azure e um membro da [*log Analytics função colaborador*](../platform/manage-access.md#manage-access-using-azure-permissions) do espaço de trabalho log Analytics configurado com Azure monitor para contêineres.

- Para exibir os dados de monitoramento, você é um membro da permissão da função [*leitor de log Analytics*](../platform/manage-access.md#manage-access-using-azure-permissions) com o espaço de trabalho log Analytics configurado com Azure monitor para contêineres.

## <a name="enable-for-a-new-cluster-using-an-azure-resource-manager-template"></a>Habilitar para um novo cluster usando um modelo de Azure Resource Manager

Execute as etapas a seguir para implantar um cluster do Azure Red Hat OpenShift com monitoramento habilitado. Antes de continuar, examine o tutorial [criar um cluster do Azure Red Hat OpenShift](../../openshift/tutorial-create-cluster.md#prerequisites) para entender as dependências que você precisa configurar para que seu ambiente seja configurado corretamente.

Esse método inclui dois modelos JSON. Um modelo especifica a configuração para implantar o cluster com monitoramento habilitado e o outro contém valores de parâmetro que você configura para especificar o seguinte:

- A ID de recurso de cluster do Azure Red Hat OpenShift. 

- O grupo de recursos no qual o cluster é implantado.

- [Azure Active Directory ID de locatário](../../openshift/howto-create-tenant.md#create-a-new-azure-ad-tenant) observada depois de executar as etapas para criar uma ou uma já criada.

- [Azure Active Directory ID do aplicativo cliente](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-app-registration) anotada depois de executar as etapas para criar uma ou uma já criada.

- [Azure Active Directory segredo do cliente](../../openshift/howto-aad-app-configuration.md#create-a-client-secret) observado depois de executar as etapas para criar uma ou uma já criada.

- [Grupo de segurança do Azure ad](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-security-group) anotado depois de executar as etapas para criar uma ou uma já criada.

- ID de recurso de um espaço de trabalho de Log Analytics existente.

- O número de nós mestres a serem criados no cluster.

- O número de nós de computação no perfil do pool de agentes.

- O número de nós de infraestrutura no perfil do pool de agentes. 

Se não estiver familiarizado com o conceito de implementar recursos com um modelo, consulte:

- [Implementar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)

- [Implementar recursos com modelos do Resource Manager e a CLI do Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Se optar por utilizar a CLI do Azure, tem primeiro de instalar e utilizar a CLI localmente. Você deve estar executando o CLI do Azure versão 2.0.65 ou posterior. Para identificar a versão, execute `az --version`. Se precisar de instalar ou atualizar a CLI do Azure, veja [instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

O espaço de trabalho do Log Analytics deve ser criado antes de habilitar o monitoramento usando Azure PowerShell ou a CLI. Para criar a área de trabalho, pode configurá-lo por meio [do Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md), da funcionalidade [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json), ou no [portal do Azure](../../azure-monitor/learn/quick-create-workspace.md).

1. Baixe e salve em uma pasta local, o modelo de Azure Resource Manager e o arquivo de parâmetro, para criar um cluster com o complemento de monitoramento usando os seguintes comandos:

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoring.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoringParam.json` 

2. Iniciar sessão no Azure 

    ```azurecli
    az login    
    ```
    
    Se você tiver acesso a várias assinaturas, execute `az account set -s {subscription ID}` substituindo `{subscription ID}` pela assinatura que você deseja usar.
 
3. Crie um grupo de recursos para o cluster se você ainda não tiver um. Para obter uma lista de regiões do Azure que dão suporte a OpenShift no Azure, consulte [regiões com suporte](../../openshift/supported-resources.md#azure-regions). 

    ```azurecli
    az group create -g <clusterResourceGroup> -l <location> 
    ```

4. Edite o arquivo de parâmetro JSON **newClusterWithMonitoringParam. JSON** e atualize os seguintes valores:

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

5. A etapa a seguir implanta o cluster com monitoramento habilitado usando o CLI do Azure. 

    ```azurecli
    az group deployment create --resource-group <ClusterResourceGroupName> --template-file ./newClusterWithMonitoring.json --parameters @./newClusterWithMonitoringParam.json 
    ```
 
    A saída é semelhante ao seguinte:

    ```azurecli
    provisioningState       : Succeeded
    ```

## <a name="enable-for-an-existing-cluster"></a>Habilitar para um cluster existente

Execute as etapas a seguir para habilitar o monitoramento de um cluster do Azure Red Hat OpenShift implantado no Azure. Você pode fazer isso na portal do Azure ou usando os modelos fornecidos.

### <a name="from-the-azure-portal"></a>No portal do Azure
 
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

2. No menu portal do Azure ou na home page do, selecione **Azure monitor**. Sob o **Insights** secção, selecione **contentores**. 

3. Sobre o **Monitor - contentores** página, selecione **clusters não monitorizados**.

4. Na lista de clusters não monitorados, localize o cluster na lista e clique em **habilitar**. Você pode identificar os resultados na lista procurando o valor **toa** sob o **tipo de cluster**de coluna.

5. Sobre o **integração para o Azure Monitor para contentores** página, se tiver um existentes do Log Analytics área de trabalho na mesma subscrição que o cluster, selecione-o na lista pendente.  
    A lista seleciona o espaço de trabalho padrão e o local no qual o cluster é implantado na assinatura. 

    ![Habilitar o monitoramento de clusters não monitorados](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >Se quiser criar uma nova área de trabalho do Log Analytics para armazenar os dados de monitorização do cluster, siga as instruções em [criar uma área de trabalho do Log Analytics](../../azure-monitor/learn/quick-create-workspace.md). Certifique-se de criar o espaço de trabalho na mesma assinatura em que o cluster do RedHat OpenShift é implantado. 
 
Depois de ativar a monitorização, poderá demorar cerca de 15 minutos antes de poder visualizar as métricas de estado de funcionamento para o cluster. 

### <a name="enable-using-an-azure-resource-manager-template"></a>Habilitar usando um modelo de Azure Resource Manager

Esse método inclui dois modelos JSON. Um modelo especifica a configuração para ativar a monitorização e a outra contém valores de parâmetros que pode configurar para especificar o seguinte:

- A ID de recurso de cluster do Azure RedHat OpenShift. 

- O grupo de recursos no qual o cluster é implantado.

- Uma área de trabalho do Log Analytics.

Se não estiver familiarizado com o conceito de implementar recursos com um modelo, consulte:

- [Implementar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)

- [Implementar recursos com modelos do Resource Manager e a CLI do Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Se optar por utilizar a CLI do Azure, tem primeiro de instalar e utilizar a CLI localmente. Você deve estar executando o CLI do Azure versão 2.0.65 ou posterior. Para identificar a versão, execute `az --version`. Se precisar de instalar ou atualizar a CLI do Azure, veja [instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

O espaço de trabalho do Log Analytics deve ser criado antes de habilitar o monitoramento usando Azure PowerShell ou a CLI. Para criar a área de trabalho, pode configurá-lo por meio [do Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md), da funcionalidade [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json), ou no [portal do Azure](../../azure-monitor/learn/quick-create-workspace.md).

1. Baixe o modelo e o arquivo de parâmetro para atualizar o cluster com o complemento de monitoramento usando os seguintes comandos:

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_existing_cluster/existingClusterOnboarding.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_existing_cluster/existingClusterParam.json` 

2. Iniciar sessão no Azure 

    ```azurecli
    az login    
    ```

    Se você tiver acesso a várias assinaturas, execute `az account set -s {subscription ID}` substituindo `{subscription ID}` pela assinatura que você deseja usar.

3. Especifique a assinatura do cluster do Azure RedHat OpenShift.

    ```azurecli
    az account set --subscription "Subscription Name"  
    ```

4. Execute o seguinte comando para identificar o local do cluster e a ID do recurso:

    ```azurecli
    az openshift show -g <clusterResourceGroup> -n <clusterName> 
    ```

5. Edite o arquivo de parâmetro JSON **existingClusterParam. JSON** e atualize os valores *araResourceId* e *araResoruceLocation*. O valor para **workspaceResourceId** é a ID de recurso completo da sua área de trabalho do Log Analytics, que inclui o nome de área de trabalho. 

6. Para implantar com o CLI do Azure, execute os seguintes comandos: 

    ```azurecli
    az group deployment create --resource-group <ClusterResourceGroupName> --template-file ./ExistingClusterOnboarding.json --parameters @./existingClusterParam.json 
    ```

    A saída é semelhante ao seguinte:

    ```azurecli
    provisioningState       : Succeeded
    ```

## <a name="next-steps"></a>Passos seguintes

- Com o monitoramento habilitado para coletar a utilização de recursos e de integridade do cluster do RedHat OpenShift e das cargas de trabalho em execução neles, saiba [como usar](container-insights-analyze.md) Azure monitor para contêineres.

- Para saber como parar de monitorar o cluster com Azure Monitor para contêineres, consulte [como parar de monitorar o cluster do Azure Red Hat OpenShift](container-insights-optout-openshift.md).
