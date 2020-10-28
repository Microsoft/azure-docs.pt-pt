---
title: Monitorize um novo cluster Azure Kubernetes Service (AKS) Microsoft Docs
description: Saiba como permitir a monitorização de um novo cluster do Serviço Azure Kubernetes (AKS) com o Azure Monitor para a subscrição de contentores.
ms.topic: conceptual
ms.date: 04/25/2019
ms.custom: devx-track-terraform, devx-track-azurecli
ms.openlocfilehash: 19c4a88cee8776136593b041e94dd14c7c9c28d6
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92735089"
---
# <a name="enable-monitoring-of-a-new-azure-kubernetes-service-aks-cluster"></a>Permitir a monitorização de um novo cluster Azure Kubernetes Service (AKS)

Este artigo descreve como configurar o Azure Monitor para contentores para monitorizar o cluster gerido de Kubernetes hospedado no [Serviço Azure Kubernetes](../../aks/index.yml) que se prepara para implementar na sua subscrição.

Pode ativar a monitorização de um cluster AKS utilizando um dos métodos suportados:

* CLI do Azure
* Terraform

## <a name="enable-using-azure-cli"></a>Ativar através do CLI do Azure

Para permitir a monitorização de um novo cluster AKS criado com O Azure CLI, siga o passo no artigo de arranque rápido na secção [Criar cluster AKS](../../aks/kubernetes-walkthrough.md#create-aks-cluster).  

>[!NOTE]
>Se optar por utilizar o Azure CLI, primeiro tem de instalar e utilizar o CLI localmente. Deve estar a executar a versão Azure CLI 2.0.74 ou posterior. Para identificar a sua versão, corra `az --version` . Se necessitar de instalar ou atualizar o Azure CLI, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli). Se tiver instalado a versão de extensão CLI de pré-visualização aks 0.4.12 ou superior, remova quaisquer alterações que tenha feito para permitir uma extensão de pré-visualização, uma vez que pode sobrepor o comportamento padrão do Azure CLI uma vez que as funcionalidades de Pré-visualização AKS não estão disponíveis na cloudmnet do Azure US Governmnet.

## <a name="enable-using-terraform"></a>Ativar a utilização do Terraform

Se estiver [a implementar um novo cluster AKS utilizando o Terraform,](/azure/developer/terraform/create-k8s-cluster-with-tf-and-aks)especifique os argumentos necessários no perfil [para criar um espaço de trabalho Log Analytics](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_workspace.html) se não optar por especificar um existente. 

>[!NOTE]
>Se optar por utilizar o Terraform, deve estar a executar a versão 1.17.0 ou superior do Fornecedor Terraform Azure RM.

Para adicionar o Azure Monitor para recipientes no espaço de trabalho, consulte [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html) e complete o perfil incluindo o [**addon_profile**](https://www.terraform.io/docs/providers/azurerm/r/kubernetes_cluster.html#addon_profile) e especifique **oms_agent** . 

Depois de ter ativado a monitorização e todas as tarefas de configuração concluídas com sucesso, pode monitorizar o desempenho do seu cluster de duas formas:

* Diretamente no cluster AKS selecionando **Health** no painel esquerdo.
* Selecionando o azulejo do **recipiente** monitor na página de cluster AKS para o cluster selecionado. No Monitor Azure, no painel esquerdo, selecione **Health** . 

  ![Opções para selecionar O Monitor Azure para contentores em AKS](./media/container-insights-onboard/kubernetes-select-monitoring-01.png)

Depois de ter ativado a monitorização, pode demorar cerca de 15 minutos até poder ver as métricas de saúde para o cluster. 

## <a name="verify-agent-and-solution-deployment"></a>Verificar a implementação do agente e da solução
Com a versão *06072018* ou posterior, pode verificar se tanto o agente como a solução foram implementados com sucesso. Com versões anteriores do agente, só pode verificar a implementação do agente.

### <a name="agent-version-06072018-or-later"></a>Agente versão 06072018 ou mais tarde
Executar o seguinte comando para verificar se o agente é implantado com sucesso. 

```
kubectl get ds omsagent --namespace=kube-system
```

A saída deve assemelhar-se ao seguinte, o que indica que foi corretamente implantado:

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

Para verificar a implantação da solução, executar o seguinte comando:

```
kubectl get deployment omsagent-rs -n=kube-system
```

A saída deve assemelhar-se ao seguinte, o que indica que foi corretamente implantado:

```
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>Versão de agente antes de 06072018

Para verificar se a versão do agente Log Analytics lançada antes *do 06072018* é devidamente implantada, execute o seguinte comando:  

```
kubectl get ds omsagent --namespace=kube-system
```

A saída deve assemelhar-se ao seguinte, o que indica que foi corretamente implantado:  

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-configuration-with-cli"></a>Ver configuração com CLI
Utilize o `aks show` comando para obter detalhes como é a solução ativada ou não, o que é o log Analytics workspace resourceID, e detalhes sumários sobre o cluster.  

```azurecli
az aks show -g <resourceGroupofAKSCluster> -n <nameofAksCluster>
```

Após alguns minutos, o comando completa e devolve informações formatadas com JSON sobre solução.  Os resultados do comando devem mostrar o perfil de acompanhamento e assemelhar-se à seguinte saída de exemplo:

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

* Se sentir problemas ao tentar embarcar na solução, reveja o [guia de resolução de problemas](container-insights-troubleshoot.md)

* Com a monitorização habilitada a recolher a saúde e a utilização de recursos do seu cluster AKS e cargas de trabalho que os executam, aprenda [a utilizar o](container-insights-analyze.md) Azure Monitor para recipientes.

