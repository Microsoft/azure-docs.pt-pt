---
title: Arco Azure a bordo com Monitor Azure para contentores (Pré-visualização)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Arco Azure a bordo com Monitor Azure para contentores
keywords: Kubernetes, Arc, Azure, K8s, contentores
ms.openlocfilehash: 3e1ea96a9241211b25a4e5b356723290fa647412
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680745"
---
# <a name="onboard-azure-monitor-for-containers-with-arc-preview"></a>Monitor Azure a bordo para recipientes com Arco (Pré-visualização)

A bordo do [Monitor Azure, os contentores habilitados](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview) para o Arco Azure ativaram o cluster ou o cluster kubernetes.

## <a name="before-you-begin"></a>Antes de começar

* [Versões Kubernetes](https://docs.microsoft.com/azure/aks/supported-kubernetes-versions)
* Linux distros para os nós do cluster (master & operário) – Ubuntu (18.04 LTS e 16.04 LTS)
* Autorização de função RBAC contribuidor mínima na subscrição do Azure Arc permitiu o cluster Kubernetes
* Id de recurso azure totalmente qualificado do Arco Azure permitiu o cluster Kubernetes
* Contexto kubeconfig do cluster Kubernetes
* O agente de monitorização requer cAdvisor no Kubelet está em execução em qualquer porta segura: 10250 ou porta não segura: 10255 em todos os nós para puxar as métricas perf   
* Recomenda-se configurar a porta cAdvisor kubelet para proteger a porta:10250.
* O Agente de Monitorização requer que as seguintes portas e domínios de saída enviem os dados de monitorização para o backend do Monitor Azure (se bloqueados por procuração/firewall)
    -  *.ods.opinsights.azure.com 443
    -  *.oms.opinsights.azure.com 443
    -  *.blob.core.windows.net 443
    -  dc.services.visualstudio.com 443

## <a name="onboarding"></a>Inclusão

### <a name="using-helm-chart"></a>Usando gráfico HELM

### <a name="option-1-using-powershell--script"></a>Opção 1: Utilização do script PowerShell

1. Descarregue o script Onboarding

    ```console
    curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/haiku/onboarding_azuremonitor_for_containers.ps1
     ```

2. Instale o [núcleo PowerShell](https://docs.microsoft.com/PowerShell/scripting/install/installing-PowerShell?view=PowerShell-6) na sua máquina de dev para executar o script de embarque powerShell.

3. Iniciar sessão no Azure

    ```console
    az login --use-device-code
    ```

4. Execute abaixo o script com o seu cluster Azure Arc K8s Cluster ResourceId e contexto do cluster kubernetes

    ```console
    .\onboarding_azuremonitor_for_containers.ps1 -azureArcClusterResourceId <resourcedIdOfAzureArcCluster> -kubeContext <kube-context>

    For Example ..
    .\onboarding_azuremonitor_for_containers.ps1 -azureArcClusterResourceId /subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1 -kubeContext MyK8sTestCluster
     ```

### <a name="option-2-using-bash-script"></a>Opção 2: Utilização do Roteiro bash

> **Dica:** O guião usa características de bash 4, por isso certifique-se de que a sua festa está atualizada. Pode verificar a sua versão atual com `bash --version` .

1. Descarregue o script Onboarding

    ```console
    curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/haiku/onboarding_azuremonitor_for_containers.sh
     ```

2. Execute abaixo o script com o seu cluster Azure Arc K8s Cluster ResourceId e contexto do cluster kubernetes

    ```console
    bash onboarding_azuremonitor_for_containers.sh <resourcedIdOfAzureArcCluster>  <kube-context>

    For Example:
    bash onboarding_azuremonitor_for_containers.sh /subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1 MyK8sTestCluster

     ```

## <a name="configure-agent-data-collection"></a>Configure a recolha de dados do agente

Por padrão, o agente não recolhe troncos de contentores em espaço de nome do sistema kube.
Consulte https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-agent-config o agente configurado com as definições de recolha de dados desejadas.

## <a name="configure-scraping-of-prometheus-metrics"></a>Configurar a raspagem das métricas de Prometeu

O Monitor Azure para recipientes raspa as métricas prometheus e ingerir para o backend do Monitor Azure.
Consulte https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-prometheus-integration as instruções sobre como configurar a raspagem de Prometeu.

## <a name="user-interface"></a>Interface de utilizador

Navegue https://aka.ms/azmon-containers-azurearc para ver o Cluster Onboarded.

## <a name="disable-monitoring"></a>Monitorização de desativação

Se quiser desativar a monitorização por alguma razão, pode simplesmente eliminar o gráfico DO Helm do Monitor Azure para deixar de recolher e ingerir dados de monitorização para o Azure Monitor para obter backend de contentores.

    ```console
    helm del azmon-containers-release-1
    ```

## <a name="next-steps"></a>Passos seguintes

* [Use a Política Azure para governar a configuração do cluster](./use-azure-policy.md)

