---
title: Como atualizar o Azure Monitor para contentores para métricas Microsoft Docs
description: Este artigo descreve como atualiza o Azure Monitor para contentores para ativar a funcionalidade de métricas personalizadas que suporta a exploração e alerta em métricas agregadas.
ms.topic: conceptual
ms.date: 06/01/2020
ms.openlocfilehash: d299fc5e6b0c41188fac1fa19bb66387263c12e9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84298266"
---
# <a name="how-to-update-azure-monitor-for-containers-to-enable-metrics"></a>Como atualizar o Azure Monitor para contentores para ativar métricas

O Azure Monitor para contentores está a introduzir suporte para recolher métricas dos aglomerados de aglomerados Azure Kubernetes (AKS) e escrevendo-os para a loja de métricas Azure Monitor. Esta alteração destina-se a fornecer uma melhor oportunidade ao apresentar cálculos agregados (Avg, Count, Max, Min, Sum) em gráficos de desempenho, suporte a gráficos de desempenho em dashboards do portal Azure e alertas métricos de suporte.

>[!NOTE]
>Esta funcionalidade não suporta atualmente clusters Azure Red Hat OpenShift.
>

As seguintes métricas são ativadas como parte desta característica:

| Espaço de nome métrico | Metric | Descrição |
|------------------|--------|-------------|
| insights.container/nós | cpuUsageMillicores, cpuUsagePercentage, memoryRssBytes, memoryRssPercentage, memoryWorkingSetBytes, memoryWorkingSetPercentage, nodesCount | Estas são métricas *de nó* e incluem *hospedeiro* como uma dimensão, e eles também incluem o<br> nome do nó como valor para a dimensão do *hospedeiro.* |
| insights.contentor/pods | podCount | Estas são métricas *de pod* e incluem as seguintes dimensões - ControllerName, Kubernetes namespace, nome, fase. |

A atualização do cluster para suportar estas novas capacidades pode ser realizada a partir do portal Azure PowerShell ou com o Azure CLI. Com a Azure PowerShell e o CLI, pode ativar este por cluster ou para todos os clusters da sua subscrição. Novas implementações de AKS incluirão automaticamente esta alteração de configuração e capacidades.

Qualquer processo atribui a função **de Editor de Métricas de Monitorização** ao principal de serviço do cluster ou ao MSI atribuído ao Utilizador para o addon de monitorização para que os dados recolhidos pelo agente possam ser publicados no recurso de clusters. Monitoring Metrics Publisher tem permissão apenas para empurrar métricas para o recurso, não pode alterar nenhum estado, atualizar o recurso ou ler quaisquer dados. Para obter mais informações sobre o papel, consulte [a função de Editor de Métricas de Monitorização](../../role-based-access-control/built-in-roles.md#monitoring-metrics-publisher).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, confirme o seguinte:

* As métricas personalizadas só estão disponíveis num subconjunto de regiões de Azure. Está aqui documentada [uma](../platform/metrics-custom-overview.md#supported-regions)lista de regiões apoiadas.
* Você é membro da função **[Proprietário](../../role-based-access-control/built-in-roles.md#owner)** no recurso cluster AKS para permitir a recolha de métricas de desempenho personalizadas de nó e pod. 

Se optar por utilizar o Azure CLI, primeiro tem de instalar e utilizar o CLI localmente. Deve estar a executar a versão Azure CLI 2.0.59 ou posterior. Para identificar a sua versão, corra `az --version` . Se necessitar de instalar ou atualizar o Azure CLI, consulte [instalar o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). 

## <a name="upgrade-a-cluster-from-the-azure-portal"></a>Atualize um cluster a partir do portal Azure

Para os aglomerados AKS existentes monitorizados pelo Azure Monitor para recipientes, depois de selecionar o cluster para visualizar a sua saúde a partir da vista multi-cluster no Azure Monitor ou diretamente do cluster selecionando **Insights** a partir do painel da esquerda, você deve ver uma banner no topo do portal.

![Banner de cluster AKS de upgrade no portal Azure](./media/container-insights-update-metrics/portal-banner-enable-01.png)

O ClickIng **Enable** iniciará o processo para atualizar o cluster. Este processo pode demorar vários segundos a terminar, e pode acompanhar o seu progresso no menu de Notificações.

## <a name="upgrade-all-clusters-using-bash-in-azure-command-shell"></a>Atualize todos os clusters usando Bash in Azure Command Shell

Execute os seguintes passos para atualizar todos os clusters da sua subscrição utilizando a Bash in Azure Command Shell.

1. Executar o seguinte comando utilizando o Azure CLI.  Editar o valor para **subscriçãoId** usando o valor da página **de visão geral AKS** para o cluster AKS.

    ```azurecli
    az login
    az account set --subscription "Subscription Name"
    curl -sL https://aka.ms/ci-md-onboard-atscale | bash -s subscriptionId   
    ```

    A alteração de configuração pode demorar alguns segundos a ser concluída. Quando estiver concluída, é exibida uma mensagem semelhante à seguinte e inclui o resultado:

    ```azurecli
    completed role assignments for all AKS clusters in subscription: <subscriptionId>
    ```

## <a name="upgrade-per-cluster-using-azure-cli"></a>Upgrade por cluster usando Azure CLI

Execute os seguintes passos para atualizar um cluster específico na sua subscrição utilizando o Azure CLI.

1. Executar o seguinte comando utilizando o Azure CLI. Editar os valores para **subscriçãoId**, **resourceGroupName**e **clusterName** utilizando os valores na página **AKS Overview** para o cluster AKS.  Para obter o valor do **clienteIdOfSPN,** é devolvido quando executar o comando `az aks show` como mostrado no exemplo abaixo.

    ```azurecli
    az login
    az account set --subscription "<subscriptionName>"
    az aks show -g <resourceGroupName> -n <clusterName> 
    az role assignment create --assignee <clientIdOfSPN> --scope <clusterResourceId> --role "Monitoring Metrics Publisher" 
    ```

    Para obter o valor para **clienteIdOfSPNOrMsi,** pode executar o comando `az aks show` como mostrado no exemplo abaixo. Se o **objeto ServicePrincipalProfile** tiver um valor *clienteid* válido, pode usá-lo. Caso contrário, se estiver definido para *MSI,* você precisa passar no clienteid de `addonProfiles.omsagent.identity.clientId` .

    ```azurecli
    az login
    az account set --subscription "<subscriptionName>"
    az aks show -g <resourceGroupName> -n <clusterName> 
    az role assignment create --assignee <clientIdOfSPNOrMsi> --scope <clusterResourceId> --role "Monitoring Metrics Publisher"
    ```

## <a name="upgrade-all-clusters-using-azure-powershell"></a>Atualizar todos os clusters usando Azure PowerShell

Execute os seguintes passos para atualizar todos os clusters da sua subscrição utilizando a Azure PowerShell.

1. [Descarregue](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/docs/aks/mdmonboarding/mdm_onboarding_atscale.ps1) o script **mdm_onboarding_atscale.ps1** e guarde-o para uma pasta local do nosso repo GitHub.
2. Executar o seguinte comando utilizando o Azure PowerShell.  Editar o valor para **subscriçãoId** usando o valor da página **de visão geral AKS** para o cluster AKS.

    ```powershell
    .\mdm_onboarding_atscale.ps1 subscriptionId
    ```
    A alteração de configuração pode demorar alguns segundos a ser concluída. Quando estiver concluída, é exibida uma mensagem semelhante à seguinte e inclui o resultado:

    ```powershell
    Completed adding role assignment for the aks clusters in subscriptionId :<subscriptionId>
    ```

## <a name="upgrade-per-cluster-using-azure-powershell"></a>Upgrade por cluster usando Azure PowerShell

Execute os seguintes passos para atualizar um cluster específico utilizando a Azure PowerShell.

1. [Descarregue](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/docs/aks/mdmonboarding/mdm_onboarding.ps1) o script **mdm_onboarding.ps1** e guarde-o para uma pasta local do nosso repo GitHub.

2. Executar o seguinte comando utilizando o Azure PowerShell. Editar os valores para **subscriçãoId**, **resourceGroupName**e **clusterName** utilizando os valores na página **AKS Overview** para o cluster AKS.

    ```powershell
    .\mdm_onboarding.ps1 subscriptionId <subscriptionId> resourceGroupName <resourceGroupName> clusterName <clusterName>
    ```

    A alteração de configuração pode demorar alguns segundos a ser concluída. Quando estiver concluída, é exibida uma mensagem semelhante à seguinte e inclui o resultado:

    ```powershell
    Successfully added Monitoring Metrics Publisher role assignment to cluster : <clusterName>
    ```

## <a name="verify-update"></a>Verificar atualização

Depois de iniciar a atualização utilizando um dos métodos descritos anteriormente, pode utilizar o explorador de métricas Azure Monitor e verificar a partir do **espaço de nome métrico** que os insights estão **listados.** Se for, isto indica que pode ir em frente e começar a configurar [alertas métricos](../platform/alerts-metric.md) ou fixar as suas fichas em [dashboards](../../azure-portal/azure-portal-dashboards.md).  
