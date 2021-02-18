---
title: Como atualizar o Azure Monitor para recipientes para métricas | Microsoft Docs
description: Este artigo descreve como atualiza o Azure Monitor para contentores para ativar a funcionalidade de métricas personalizadas que suporta a exploração e alerta em métricas agregadas.
ms.topic: conceptual
ms.date: 10/09/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 37c19cd074e9ce1985d5d0e82137d8603913d4bd
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100615301"
---
# <a name="how-to-update-azure-monitor-for-containers-to-enable-metrics"></a>Como atualizar o Azure Monitor para contentores para ativar métricas

O Azure Monitor para contentores está a introduzir suporte para a recolha de métricas dos Serviços Azure Kubernetes (AKS) e do Azure Arc que permitiu que os aglomerados de Kubernetes e as escrevendo para a loja de métricas do Azure Monitor. Esta alteração destina-se a fornecer uma melhor oportunidade ao apresentar cálculos agregados (Avg, Count, Max, Min, Sum) em gráficos de desempenho, suporte a gráficos de desempenho em dashboards do portal Azure e alertas métricos de suporte.

>[!NOTE]
>Esta funcionalidade não suporta atualmente clusters Azure Red Hat OpenShift.
>

As seguintes métricas são ativadas como parte desta característica:

| Espaço de nome métrico | Metric | Descrição |
|------------------|--------|-------------|
| Insights.container/nós | cpuUsageMillicores, cpuUsagePercentage, memoryRssBytes, memoryRssPercentage, memoryWorkingSetBytes, memoryWorkingSetPercentage, nodesCount, diskUsedPercentage, | Como *métricas de nó,* incluem *hospedeiro* como uma dimensão. Eles também incluem o<br> nome do nó como valor para a dimensão do *hospedeiro.* |
| Insights.container/pods | podCount, completeJobsCount, restartContainerCount, oomKilledContainerCount, podReadyPercentage | Como métricas *de pod,* incluem as seguintes dimensões - ControllerName, Kubernetes namespace, nome, fase. |
| Insights.contentor/contentores | cpuExceededPercentage, memoryRssExceededPercentage, memoryWorkingSetExceedpercentage | |
| Insights.container/persistentevolumes | pvUsageExceededPercentage | |

Para suportar estas novas capacidades, um novo agente contentorizado está incluído no lançamento, versão **microsoft/oms:ciprod05262020** para AKS e versão **microsoft/oms:ciprod09252020** para Azure Arc habilitado clusters Kubernetes. As novas implementações de AKS incluem automaticamente esta alteração de configuração e capacidades. A atualização do seu cluster para suportar esta funcionalidade pode ser realizada a partir do portal Azure PowerShell ou com o Azure CLI. Com Azure PowerShell e CLI. Pode ativar este por cluster ou para todos os clusters da sua subscrição.

Qualquer processo atribui a função **de Editor de Métricas de Monitorização** ao principal de serviço do cluster ou ao MSI atribuído ao Utilizador para o addon de monitorização para que os dados recolhidos pelo agente possam ser publicados no recurso de clusters. Monitoring Metrics Publisher tem permissão apenas para empurrar métricas para o recurso, não pode alterar nenhum estado, atualizar o recurso ou ler quaisquer dados. Para obter mais informações sobre o papel, consulte [o papel de Editor de Métricas de Monitorização](../../role-based-access-control/built-in-roles.md#monitoring-metrics-publisher). O requisito de função do Monitoring Metrics Publisher não é aplicável aos clusters de Kubernetes habilitados pelo Azure Arc.

> [!IMPORTANT]
> A atualização não é necessária para clusters Kubernetes ativados pelo Azure Arc, uma vez que já terão a versão de agente mínima exigida.

## <a name="prerequisites"></a>Pré-requisitos

Antes de atualizar o seu cluster, confirme o seguinte:

* As métricas personalizadas só estão disponíveis num subconjunto de regiões de Azure. Está aqui documentada [uma](../essentials/metrics-custom-overview.md#supported-regions)lista de regiões apoiadas.

* Você é membro da função **[Proprietário](../../role-based-access-control/built-in-roles.md#owner)** no recurso cluster AKS para permitir a recolha de métricas de desempenho personalizadas de nó e pod. Esta exigência não se aplica aos agrupamentos de Kubernetes habilitados pelo Arco Azure.

Se optar por utilizar o Azure CLI, primeiro tem de instalar e utilizar o CLI localmente. Deve estar a executar a versão Azure CLI 2.0.59 ou posterior. Para identificar a sua versão, corra `az --version` . Se necessitar de instalar ou atualizar o Azure CLI, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli).

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

1. Executar o seguinte comando utilizando o Azure CLI. Editar os valores para **subscriçãoId**, **resourceGroupName** e **clusterName** utilizando os valores na página **AKS Overview** para o cluster AKS.  Para obter o valor do **clienteIdOfSPN,** é devolvido quando executar o comando `az aks show` como mostrado no exemplo abaixo.

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

2. Executar o seguinte comando utilizando o Azure PowerShell. Editar os valores para **subscriçãoId**, **resourceGroupName** e **clusterName** utilizando os valores na página **AKS Overview** para o cluster AKS.

    ```powershell
    .\mdm_onboarding.ps1 subscriptionId <subscriptionId> resourceGroupName <resourceGroupName> clusterName <clusterName>
    ```

    A alteração de configuração pode demorar alguns segundos a ser concluída. Quando estiver concluída, é exibida uma mensagem semelhante à seguinte e inclui o resultado:

    ```powershell
    Successfully added Monitoring Metrics Publisher role assignment to cluster : <clusterName>
    ```

## <a name="verify-update"></a>Verificar atualização

Depois de iniciar a atualização utilizando um dos métodos descritos anteriormente, pode utilizar o explorador de métricas Azure Monitor e verificar a partir do **espaço de nome métrico** que os insights estão **listados.** Se for, pode ir em frente e começar a configurar [alertas métricos](../alerts/alerts-metric.md) ou fixar as suas [fichas em dashboards](../../azure-portal/azure-portal-dashboards.md).  
