---
title: Monitor Azure Arc permitiu clusters kubernetes
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Recolher métricas e troncos de Azure Arc habilitados agrupamentos Kubernetes usando Azure Monitor
ms.openlocfilehash: 0a983f6d7032310d02d35e713482de942bfbfd70
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443855"
---
# <a name="azure-monitor-container-insights-for-azure-arc-enabled-kubernetes-clusters"></a>Azure Monitor Container Insights for Azure Arc enabled clusters Kubernetes

[A Azure Monitor Container Insights](container-insights-overview.md) proporciona uma rica experiência de monitorização para aglomerados de Kubernetes ativados pelo Arco Azure.

[!INCLUDE [preview features note](../../azure-arc/kubernetes/includes/preview/preview-callout.md)]

## <a name="supported-configurations"></a>Configurações suportadas

- A Azure Monitor Container Insights suporta a monitorização do Arco Azure ativado kubernetes (pré-visualização) conforme descrito no artigo [do Overview,](container-insights-overview.md) exceto a função de dados ao vivo (pré-visualização). Além disso, os utilizadores não são obrigados a ter permissões [do Proprietário](../../role-based-access-control/built-in-roles.md#owner) para [ativar métricas](container-insights-update-metrics.md)
- `Docker`, `Moby` e cri compatível com os tempos de funcionação do contentor `CRI-O` e . `containerd` .
- São suportados procuração de saída sem autenticação e procuração de saída com autenticação básica. O representante de saída que espera certificados fidedignos não é atualmente suportado.

## <a name="prerequisites"></a>Pré-requisitos

- Encontrou os pré-requisitos listados sob a [documentação genérica](../../azure-arc/kubernetes/extensions.md#prerequisites)de extensões de cluster.
- Um espaço de trabalho Log Analytics: Azure Monitor Container Insights suporta um espaço de trabalho Log Analytics nas regiões listadas nos produtos Azure [por página](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor). Pode criar o seu próprio espaço de trabalho através [do Azure Resource Manager,](../logs/resource-manager-workspace.md) [PowerShell](../logs/powershell-sample-create-workspace.md)ou [portal Azure.](../logs/quick-create-workspace.md)
- Precisa de ter a atribuição de funções [do Contribuinte](../../role-based-access-control/built-in-roles.md#contributor) na subscrição Azure contendo o recurso Azure Arc ativado por Kubernetes. Se o espaço de trabalho Log Analytics estiver numa subscrição diferente, então a atribuição de funções [de Colaborador do Log Analytics](../logs/manage-access.md#manage-access-using-azure-permissions) é necessária no espaço de trabalho do Log Analytics.
- Para visualizar os dados de monitorização, precisa de ter a atribuição de funções [do Log Analytics Reader](../logs/manage-access.md#manage-access-using-azure-permissions) no espaço de trabalho do Log Analytics.
- Os seguintes pontos finais devem ser habilitados para o acesso de saída, para além dos mencionados ao [ligar um cluster Kubernetes ao Arco de Azure.](../../azure-arc/kubernetes/quickstart-connect-cluster.md#meet-network-requirements)

    | Ponto final | Porta |
    |----------|------|
    | `*.ods.opinsights.azure.com` | 443 |
    | `*.oms.opinsights.azure.com` | 443 |
    | `dc.services.visualstudio.com` | 443 |
    | `*.monitoring.azure.com` | 443 |
    | `login.microsoftonline.com` | 443 |

    Se o seu recurso Kubernetes ativado pelo Arco estiver no ambiente do Governo dos EUA, os seguintes pontos finais devem ser ativados para acesso de saída:

    | Ponto final | Porta |
    |----------|------|
    | `*.ods.opinsights.azure.us` | 443 |
    | `*.oms.opinsights.azure.us` | 443 |
    | `dc.services.visualstudio.com` | 443 |
    

- Se já tinha implantado informações sobre o recipiente do Monitor Azure neste cluster utilizando scripts sem extensões de cluster, siga as instruções [aqui](container-insights-optout-hybrid.md) listadas para eliminar este gráfico Helm. Pode então continuar a criar uma instância de extensão de cluster para insights de recipientes do monitor Azure.

    >[!NOTE]
    > A versão baseada no script de implantação de Insights de Recipientes monitores Azure (pré-visualização) está a ser substituída pela forma de extensão de [extensão](../../azure-arc/kubernetes/extensions.md) de cluster de implantação. O Azure Monitor implantado anteriormente através do script só é suportado até junho de 2021 e, assim, é aconselhável migrar para a forma de extensão de cluster de implantação o mais cedo possível.

### <a name="identify-workspace-resource-id"></a>Identificar iD de recursos de espaço de trabalho

Executar os seguintes comandos para localizar o identificador completo do Gestor de Recursos Azure do espaço de trabalho Log Analytics. 

1. Listar todas as subscrições a que tem acesso utilizando o seguinte comando:

    ```azurecli
    az account list --all -o table
    ```

2. Mude para a subscrição que hospeda o espaço de trabalho Log Analytics utilizando o seguinte comando:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. O exemplo a seguir mostra a lista de espaços de trabalho nas suas subscrições no formato JSON predefinido.

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    Na saída, encontre o nome de interesse do espaço de trabalho. O `id` campo disso representa o identificador de recursos Azure Do espaço de trabalho Log Analytics.

    >[!TIP]
    > Isto `id` também pode ser encontrado na lâmina *geral* do espaço de trabalho Log Analytics através do portal Azure.

## <a name="create-extension-instance-using-azure-cli"></a>Criar instância de extensão usando O Azure CLI

### <a name="option-1---with-default-values"></a>Opção 1 - Com valores predefinidos

Esta opção utiliza os seguintes predefinidos:

- Cria ou utiliza o espaço de trabalho de análise de registo predefinido existente correspondente à região do cluster
- A atualização automática está ativada para a extensão do cluster do Azure Monitor

```console
az k8s-extension create --name azuremonitor-containers --cluster-name <cluster-name> --resource-group <resource-group> --cluster-type connectedClusters --extension-type Microsoft.AzureMonitor.Containers
```

### <a name="option-2---with-existing-azure-log-analytics-workspace"></a>Opção 2 - Com o espaço de trabalho Azure Log Analytics

Pode utilizar um espaço de trabalho existente no Azure Log Analytics em qualquer subscrição na qual tenha *Colaborador* ou uma atribuição de função mais permissiva.

```console
az k8s-extension create --name azuremonitor-containers --cluster-name <cluster-name> --resource-group <resource-group> --cluster-type connectedClusters --extension-type Microsoft.AzureMonitor.Containers --configuration-settings logAnalyticsWorkspaceResourceID=<armResourceIdOfExistingWorkspace>
```

### <a name="option-3---with-advanced-configuration"></a>Opção 3 - Com configuração avançada

Se pretender ajustar os pedidos e limites de recursos predefinidos, pode utilizar as definições de configurações avançadas:

```console
az k8s-extension create --name azuremonitor-containers --cluster-name <cluster-name> --resource-group <resource-group> --cluster-type connectedClusters --extension-type Microsoft.AzureMonitor.Containers --configuration-settings  omsagent.resources.daemonset.limits.cpu=150m omsagent.resources.daemonset.limits.memory=600Mi omsagent.resources.deployment.limits.cpu=1 omsagent.resources.deployment.limits.memory=750Mi
```

Check-out da [secção de pedidos de recursos e limites da tabela Helm](https://github.com/helm/charts/blob/master/incubator/azuremonitor-containers/values.yaml) para as definições de configuração disponíveis.

### <a name="option-4---on-azure-stack-edge"></a>Opção 4 - Na Borda da Pilha Azure

Se o aglomerado de Kubernetes ativado pelo Arco Azure estiver no Azure Stack Edge, então é necessário utilizar um caminho de montagem `/home/data/docker` personalizado.

```console
az k8s-extension create --name azuremonitor-containers --cluster-name <cluster-name> --resource-group <resource-group> --cluster-type connectedClusters --extension-type Microsoft.AzureMonitor.Containers --configuration-settings omsagent.logsettings.custommountpath=/home/data/docker
```

>[!NOTE]
> Se estiver a especificar explicitamente a versão da extensão a instalar no comando criar, certifique-se de que a versão especificada é >= 2.8.2.

## <a name="create-extension-instance-using-azure-portal"></a>Criar instância de extensão usando o portal Azure

>[!IMPORTANT]
>  Se estiver a implementar o Azure Monitor num cluster Kubernetes em execução em cima do Azure Stack Edge, então a opção Azure CLI precisa de ser seguida em vez da opção do portal Azure, uma vez que é necessário definir um caminho de montagem personalizado para estes clusters.    

### <a name="onboarding-from-the-azure-arc-enabled-kubernetes-resource-blade"></a>A bordo do Arco Azure permitiu a lâmina de recurso kubernetes

1. No portal Azure, selecione o cluster Kubernetes ativado pelo Arco que deseja monitorizar.

2. Selecione o item 'Insights (pré-visualização)' na secção 'Monitorização' da lâmina de recurso.

3. Na página de bordo, selecione o botão 'Configure Azure Monitor'

4. Pode agora escolher o [espaço de trabalho do Log Analytics](../logs/quick-create-workspace.md) para enviar as suas métricas e registar dados para.

5. Selecione o botão 'Configurar' para implantar a extensão do cluster Azure Monitor Insights.

### <a name="onboarding-from-azure-monitor-blade"></a>Embarque a partir da lâmina do Monitor Azure

1. No portal Azure, navegue na lâmina 'Monitor' e selecione a opção 'Recipientes' no menu 'Insights'.

2. Selecione o separador 'Clusters não monitorizados' para visualizar o Arco Azure ativado pelos clusters kubernetes para os quais pode ativar a monitorização.

3. Clique no link 'Activar' ao lado do cluster para o qual deseja ativar a monitorização.

4. Escolha o espaço de trabalho Log Analytics e selecione o botão 'Configurar' para continuar.

## <a name="create-extension-instance-using-azure-resource-manager"></a>Criar instância de extensão usando O Gestor de Recursos Azure

1. Descarregue o modelo e o parâmetro do Gestor de Recursos Azure:

    ```console
    curl -L https://aka.ms/arc-k8s-azmon-extension-arm-template -o arc-k8s-azmon-extension-arm-template.json
    curl -L https://aka.ms/arc-k8s-azmon-extension-arm-template-params -o  arc-k8s-azmon-extension-arm-template-params.json
    ```

2. Atualizar os valores dos parâmetros em arc-k8s-azmon-extension-arm-template-params.jsno ficheiro. Para a nuvem pública Azure, `opinsights.azure.com` precisa ser usado como o valor do espaço de trabalhoDomain.

3. Implemente o modelo para criar a extensão de Insights de Contentores do Monitor Azure 

    ```console
    az login
    az account set --subscription "Subscription Name"
    az deployment group create --resource-group <resource-group> --template-file ./arc-k8s-azmon-extension-arm-template.json --parameters @./arc-k8s-azmon-extension-arm-template-params.json
    ```

## <a name="delete-extension-instance"></a>Eliminar instância de extensão

O seguinte comando apenas elimina a instância de extensão, mas não elimina o espaço de trabalho Log Analytics. Os dados dentro do recurso Log Analytics são deixados intactos.

```bash
az k8s-extension delete --name azuremonitor-containers --cluster-type connectedClusters --cluster-name <cluster-name> --resource-group <resource-group>
```

## <a name="disconnected-cluster"></a>Aglomerado desligado
Se o seu cluster estiver desligado do Azure durante > 48 horas, então o Azure Resource Graph não terá informações sobre o seu cluster. Como resultado, a lâmina Insights pode apresentar informações incorretas sobre o seu estado de cluster.

## <a name="next-steps"></a>Passos seguintes

- Com a monitorização habilitada a recolher a saúde e a utilização de recursos do seu cluster Kubernetes ativado pelo Arco e cargas de trabalho em execução, aprenda [a utilizar](container-insights-analyze.md) insights de Contentores.

- Por predefinição, o agente contentor recolhe os registos de contentores stdout/stderr de todos os recipientes que estão a funcionar em todos os espaços de nome, exceto o sistema kube. Para configurar a recolha de registos de contentores específica para espaços de nome ou espaços de nome específicos, reveja a [configuração do agente Do Contentor Insights](container-insights-agent-config.md) para configurar as definições de recolha de dados desejadas no seu ficheiro de configurações ConfigMap.

- Para raspar e analisar as métricas prometheus do seu cluster, [reveja métricas configure Prometheus raspando](container-insights-prometheus-integration.md)
