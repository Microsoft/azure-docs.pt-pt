---
title: Monitorar um novo cluster do AKS (serviço kubernetes do Azure) | Microsoft Docs
description: Saiba como habilitar o monitoramento para um novo cluster do AKS (serviço kubernetes do Azure) com Azure Monitor para assinatura de contêineres.
ms.topic: conceptual
ms.date: 04/25/2019
ms.openlocfilehash: c731826f2780c45358730f9ce20d6a6151f6f259
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75405438"
---
# <a name="enable-monitoring-of-a-new-azure-kubernetes-service-aks-cluster"></a>Habilitar o monitoramento de um novo cluster do AKS (serviço kubernetes do Azure)

Este artigo descreve como configurar Azure Monitor para contêineres para monitorar o cluster kubernetes gerenciado hospedado no [serviço kubernetes do Azure](https://docs.microsoft.com/azure/aks/) que você está preparando para implantar em sua assinatura.

Você pode habilitar o monitoramento de um cluster AKS usando um dos métodos com suporte:

* CLI do Azure
* Terraform

## <a name="enable-using-azure-cli"></a>Ativar a CLI do Azure

Para ativar a monitorização de um novo cluster do AKS criado com a CLI do Azure, siga o passo no artigo guia de introdução na seção [cluster do AKS criar](../../aks/kubernetes-walkthrough.md#create-aks-cluster).  

>[!NOTE]
>Se optar por utilizar a CLI do Azure, tem primeiro de instalar e utilizar a CLI localmente. Você deve estar executando o CLI do Azure versão 2.0.74 ou posterior. Para identificar a versão, execute `az --version`. Se precisar de instalar ou atualizar a CLI do Azure, veja [instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). Se você tiver instalado a extensão da CLI AKs-preview 0.4.12 ou superior, remova as alterações feitas para habilitar uma extensão de visualização, pois ela pode substituir o comportamento de CLI do Azure padrão, pois os recursos de visualização de AKS não estão disponíveis no Azure US governamental Cloud.

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

## <a name="next-steps"></a>Passos seguintes

* Se ocorrerem problemas ao tentar carregar a solução, reveja o [guia de resolução de problemas](container-insights-troubleshoot.md)

* Com o monitoramento habilitado para coletar a utilização de recursos e de integridade do cluster AKS e das cargas de trabalho em execução neles, saiba [como usar](container-insights-analyze.md) Azure monitor para contêineres.
