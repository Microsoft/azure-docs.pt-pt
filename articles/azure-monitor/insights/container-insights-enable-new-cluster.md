---
title: Monitorar um novo cluster do AKS (serviço kubernetes do Azure) | Microsoft Docs
description: Saiba como habilitar o monitoramento para um novo cluster do AKS (serviço kubernetes do Azure) com Azure Monitor para assinatura de contêineres.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/25/2019
ms.author: magoedte
ms.openlocfilehash: cff0286e944414d70cffd801620159ffef3db1a5
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389818"
---
# <a name="enable-monitoring-of-a-new-azure-kubernetes-service-aks-cluster"></a>Habilitar o monitoramento de um novo cluster do AKS (serviço kubernetes do Azure)

Este artigo descreve como configurar Azure Monitor para contêineres para monitorar o cluster kubernetes gerenciado hospedado no [serviço kubernetes do Azure](https://docs.microsoft.com/azure/aks/) que você está preparando para implantar em sua assinatura.

Você pode habilitar o monitoramento de um cluster AKS usando um dos métodos com suporte:

* CLI do Azure
* Terraform

## <a name="enable-using-azure-cli"></a>Habilitar usando CLI do Azure

Para habilitar o monitoramento de um novo cluster AKS criado com CLI do Azure, siga a etapa no artigo de início rápido na seção [criar cluster AKs](../../aks/kubernetes-walkthrough.md#create-aks-cluster).  

>[!NOTE]
>Se você optar por usar o CLI do Azure, primeiro será necessário instalar e usar a CLI localmente. Você deve estar executando o CLI do Azure versão 2.0.74 ou posterior. Para identificar sua versão, execute `az --version`. Se você precisar instalar ou atualizar o CLI do Azure, consulte [instalar o CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). Se você tiver instalado a extensão da CLI AKs-preview 0.4.12 ou superior, remova as alterações feitas para habilitar uma extensão de visualização, pois ela pode substituir o comportamento de CLI do Azure padrão, pois os recursos de visualização de AKS não estão disponíveis no Azure US governamental Cloud.

## <a name="enable-using-terraform"></a>Habilitar usando Terraform

Se você estiver [implantando um novo cluster AKs usando o Terraform](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md), especifique os argumentos necessários no perfil [para criar um log Analytics espaço de trabalho](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_workspace.html) se você não optar por especificar um existente. 

>[!NOTE]
>Se você optar por usar o Terraform, deverá estar executando o provedor do Terraform Azure RM versão 1.17.0 ou superior.

Para adicionar Azure Monitor para contêineres ao espaço de trabalho, consulte [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html) e conclua o perfil, incluindo o [**addon_profile**](https://www.terraform.io/docs/providers/azurerm/r/kubernetes_cluster.html#addon_profile) e especifique **oms_agent**. 

Depois de habilitar o monitoramento e todas as tarefas de configuração serem concluídas com êxito, você poderá monitorar o desempenho do cluster de uma das duas maneiras:

* Diretamente no cluster AKS selecionando **integridade** no painel esquerdo.
* Selecionando o bloco de **informações do contêiner do monitor** na página do cluster AKs para o cluster selecionado. Em Azure Monitor, no painel esquerdo, selecione **integridade**. 

  ![Opções para selecionar Azure Monitor para contêineres no AKS](./media/container-insights-onboard/kubernetes-select-monitoring-01.png)

Depois de habilitar o monitoramento, pode levar cerca de 15 minutos para que você possa exibir as métricas de integridade do cluster. 

## <a name="verify-agent-and-solution-deployment"></a>Verificar a implantação do agente e da solução
Com o Agent versão *06072018* ou posterior, você pode verificar se o agente e a solução foram implantados com êxito. Com versões anteriores do agente, você pode verificar apenas a implantação do agente.

### <a name="agent-version-06072018-or-later"></a>Versão do agente 06072018 ou posterior
Execute o comando a seguir para verificar se o agente foi implantado com êxito. 

```
kubectl get ds omsagent --namespace=kube-system
```

A saída deve se parecer com o seguinte, que indica que foi implantada corretamente:

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

Para verificar a implantação da solução, execute o seguinte comando:

```
kubectl get deployment omsagent-rs -n=kube-system
```

A saída deve se parecer com o seguinte, que indica que foi implantada corretamente:

```
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>Versão do agente anterior a 06072018

Para verificar se a versão do agente de Log Analytics liberada antes de *06072018* é implantada corretamente, execute o seguinte comando:  

```
kubectl get ds omsagent --namespace=kube-system
```

A saída deve se parecer com o seguinte, que indica que foi implantada corretamente:  

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-configuration-with-cli"></a>Exibir a configuração com a CLI
Use o comando `aks show` para obter detalhes, como a solução está habilitada ou não, qual é a ResourceId do espaço de trabalho Log Analytics e detalhes resumidos sobre o cluster.  

```azurecli
az aks show -g <resourceGroupofAKSCluster> -n <nameofAksCluster>
```

Após alguns minutos, o comando é concluído e retorna informações formatadas em JSON sobre a solução.  Os resultados do comando devem mostrar o perfil do complemento de monitoramento e se assemelham ao seguinte exemplo de saída:

```
"addonProfiles": {
    "omsagent": {
      "config": {
        "logAnalyticsWorkspaceResourceID": "/subscriptions/<WorkspaceSubscription>/resourceGroups/<DefaultWorkspaceRG>/providers/Microsoft.OperationalInsights/workspaces/<defaultWorkspaceName>"
      },
      "enabled": true
    }
  }
```

## <a name="next-steps"></a>Passos seguintes

* Se você tiver problemas ao tentar carregar a solução, examine o [Guia de solução de problemas](container-insights-troubleshoot.md)

* Com o monitoramento habilitado para capturar métricas de integridade para os nós de cluster AKS e pods, essas métricas de integridade estão disponíveis no portal do Azure. Para saber como usar Azure Monitor para contêineres, consulte [exibir a integridade do serviço kubernetes do Azure](container-insights-analyze.md).
