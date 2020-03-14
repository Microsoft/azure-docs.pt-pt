---
title: Monitorize um novo cluster do Serviço Azure Kubernetes (AKS) Microsoft Docs
description: Saiba como permitir a monitorização de um novo cluster do Serviço Azure Kubernetes (AKS) com o Monitor Azure para a subscrição de contentores.
ms.topic: conceptual
ms.date: 04/25/2019
ms.openlocfilehash: c731826f2780c45358730f9ce20d6a6151f6f259
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79275441"
---
# <a name="enable-monitoring-of-a-new-azure-kubernetes-service-aks-cluster"></a>Permitir a monitorização de um novo cluster do Serviço Azure Kubernetes (AKS)

Este artigo descreve como configurar o Azure Monitor para contentores para monitorizar o cluster Kubernetes gerido hospedado no [Serviço Azure Kubernetes](https://docs.microsoft.com/azure/aks/) que se prepara para implementar na sua subscrição.

Pode ativar a monitorização de um cluster AKS utilizando um dos métodos suportados:

* CLI do Azure
* Terraform

## <a name="enable-using-azure-cli"></a>Ativar a CLI do Azure

Para permitir a monitorização de um novo cluster AKS criado com o Azure CLI, siga o passo no artigo de arranque rápido sob a secção [Criar o cluster AKS](../../aks/kubernetes-walkthrough.md#create-aks-cluster).  

>[!NOTE]
>Se optar por utilizar a CLI do Azure, tem primeiro de instalar e utilizar a CLI localmente. Deve estar a executar a versão Azure CLI 2.0.74 ou mais tarde. Para identificar a sua versão, execute `az --version`. Se precisar de instalar ou atualizar o Azure CLI, consulte [Instalar o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). Se tiver instalado a versão de extensão CLI de pré-visualização de aks 0.4.12 ou superior, remova quaisquer alterações que tenha feito para permitir uma extensão de pré-visualização, uma vez que pode anular o comportamento cli do Azure padrão, uma vez que as funcionalidades de pré-visualização AKS não estão disponíveis na nuvem Azure US Governmnet.

## <a name="enable-using-terraform"></a>Ativar com o Terraform

Se estiver [a implementar um novo cluster AKS utilizando terrafora,](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md)especifice os argumentos necessários no perfil para criar um espaço de trabalho log [Analytics](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_workspace.html) se não optar por especificar um existente. 

>[!NOTE]
>Se optar por utilizar o Terraform, tem de executar o fornecedor de RM do Terraform do Azure versão 1.17.0 ou superior.

Para adicionar o Monitor Azure para os contentores ao espaço de trabalho, consulte [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html) e complete o [**perfil,** ](https://www.terraform.io/docs/providers/azurerm/r/kubernetes_cluster.html#addon_profile) incluindo a addon_profile e especifique **oms_agent**. 

Depois de ativar a monitorização e todas as tarefas de configuração são concluídas com êxito, pode monitorizar o desempenho do seu cluster em qualquer uma das seguintes formas:

* Diretamente no cluster AKS selecionando **a Saúde** no painel esquerdo.
* Selecionando o azulejo de **insights do Monitor Container** na página do cluster AKS para o cluster selecionado. No Monitor Azure, no painel esquerdo, selecione **Health**. 

  ![Opções para selecionar o Monitor do Azure para contentores no AKS](./media/container-insights-onboard/kubernetes-select-monitoring-01.png)

Depois de ativar a monitorização, poderá demorar cerca de 15 minutos antes de poder visualizar as métricas de estado de funcionamento para o cluster. 

## <a name="verify-agent-and-solution-deployment"></a>Verificar a implementação de agente e solução
Com a versão do agente *06072018* ou posterior, pode verificar se tanto o agente como a solução foram implementados com sucesso. Com as versões anteriores do agente, pode verificar apenas a implementação de agente.

### <a name="agent-version-06072018-or-later"></a>Versão do agente 06072018 ou posterior
Execute o seguinte comando para verificar que o agente é implementado com êxito. 

```
kubectl get ds omsagent --namespace=kube-system
```

A saída deve assemelhar-se o seguinte, que indica que foi implementado devidamente:

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

Para verificar a implementação da solução, execute o seguinte comando:

```
kubectl get deployment omsagent-rs -n=kube-system
```

A saída deve assemelhar-se o seguinte, que indica que foi implementado devidamente:

```
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>Versão de agente anteriores ao 06072018

Para verificar se a versão do agente Log Analytics lançada antes de *06072018* está corretamente implantada, execute o seguinte comando:  

```
kubectl get ds omsagent --namespace=kube-system
```

A saída deve assemelhar-se o seguinte, que indica que foi implementado devidamente:  

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-configuration-with-cli"></a>Ver configuração com a CLI
Utilize o comando `aks show` para obter detalhes como a solução ativada ou não, qual é o Serviço de Recursos do espaço de trabalho Log Analytics e detalhes sumários sobre o cluster.  

```azurecli
az aks show -g <resourceGroupofAKSCluster> -n <nameofAksCluster>
```

Após alguns minutos, o comando é concluído e devolve o formato JSON informações sobre a solução.  Os resultados do comando deve mostrar o perfil de complemento de monitorização e é semelhante a saída de exemplo seguinte:

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

* Se tiver problemas ao tentar embarcar na solução, reveja o guia de resolução de [problemas](container-insights-troubleshoot.md)

* Com a monitorização habilitada a recolher a utilização da saúde e dos recursos do seu cluster AKS e as cargas de trabalho que os utilizam, aprenda [a utilizar o](container-insights-analyze.md) Monitor Azure para contentores.
