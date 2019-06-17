---
title: Monitorizar um novo cluster do Azure Kubernetes Service (AKS) | Documentos da Microsoft
description: Saiba como ativar a monitorização para um novo cluster do Azure Kubernetes Service (AKS) com o Azure Monitor para a subscrição de contentores.
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
ms.openlocfilehash: d73ab2d5cca4f20f954a0b0e972111d3f395c3c8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65077534"
---
# <a name="enable-monitoring-of-a-new-azure-kubernetes-service-aks-cluster"></a>Ativar a monitorização de um novo cluster do Azure Kubernetes Service (AKS)

Este artigo descreve como configurar o Azure Monitor para contentores para monitorizar o cluster de Kubernetes gerido alojado no [do Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/) que estiver a preparar para implementar na sua subscrição.

Pode ativar a monitorização de um cluster do AKS utilizando um dos métodos suportados:

* CLI do Azure
* Terraform

## <a name="enable-using-azure-cli"></a>Ativar a CLI do Azure

Para ativar a monitorização de um novo cluster do AKS criado com a CLI do Azure, siga o passo no artigo guia de introdução na seção [cluster do AKS criar](../../aks/kubernetes-walkthrough.md#create-aks-cluster).  

>[!NOTE]
>Se optar por utilizar a CLI do Azure, tem primeiro de instalar e utilizar a CLI localmente. Tem de executar a CLI do Azure versão 2.0.59 ou posterior. Para identificar a versão, execute `az --version`. Se precisar de instalar ou atualizar a CLI do Azure, veja [instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 
>

## <a name="enable-using-terraform"></a>Ativar com o Terraform

Se estiver [implementar um novo cluster do AKS com o Terraform](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md), especificar os argumentos necessários no perfil [para criar uma área de trabalho do Log Analytics](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_workspace.html) se optou por não especificar um já existente. 

>[!NOTE]
>Se optar por utilizar o Terraform, tem de executar o fornecedor de RM do Terraform do Azure versão 1.17.0 ou superior.

Para adicionar o Azure Monitor para contentores para a área de trabalho, consulte [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html) e conclua o perfil, incluindo o [ **addon_profile** ](https://www.terraform.io/docs/providers/azurerm/r/kubernetes_cluster.html#addon_profile) e especifique **oms_agent**. 

Depois de ativar a monitorização e todas as tarefas de configuração são concluídas com êxito, pode monitorizar o desempenho do seu cluster em qualquer uma das seguintes formas:

* Diretamente no cluster do AKS, selecionando **estado de funcionamento** no painel esquerdo.
* Ao selecionar o **informações de contentor do Monitor** mosaico na página de cluster do AKS para o cluster selecionado. No Azure Monitor, no painel esquerdo, selecione **estado de funcionamento**. 

  ![Opções para selecionar o Monitor do Azure para contentores no AKS](./media/container-insights-onboard/kubernetes-select-monitoring-01.png)

Depois de ativar a monitorização, poderá demorar cerca de 15 minutos antes de poder visualizar as métricas de estado de funcionamento para o cluster. 

## <a name="verify-agent-and-solution-deployment"></a>Verificar a implementação de agente e solução
Com a versão do agente *06072018* ou posterior, pode verificar que o agente e a solução foram implementados com êxito. Com as versões anteriores do agente, pode verificar apenas a implementação de agente.

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

Para verificar que a versão do agente do Log Analytics lançado antes *06072018* está implementado corretamente, execute o seguinte comando:  

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
Utilize o `aks show` comando para obter os detalhes desse tipo, tal como está a solução ativada ou não, o que é o resourceID de área de trabalho do Log Analytics e detalhes de resumo sobre o cluster.  

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

## <a name="next-steps"></a>Passos Seguintes

* Se ocorrerem problemas ao tentar carregar a solução, reveja o [guia de resolução de problemas](container-insights-troubleshoot.md)

* Com a monitorização ativada para capturar métricas de estado de funcionamento para os nós de cluster do AKS e os pods, estas métricas de estado de funcionamento estão disponíveis no portal do Azure. Para saber como utilizar o Azure Monitor para contentores, veja [estado de funcionamento do serviço de Kubernetes do Azure de modo de exibição](container-insights-analyze.md).
