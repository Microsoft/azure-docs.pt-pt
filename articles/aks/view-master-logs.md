---
title: Exibir logs do controlador do AKS (serviço de kubernetes do Azure)
description: Saiba como habilitar e exibir os logs do nó mestre kubernetes no serviço kubernetes do Azure (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 01/03/2019
ms.author: mlearned
ms.openlocfilehash: dc72a8d448a189918def35da0250d83c81da7fa0
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812808"
---
# <a name="enable-and-review-kubernetes-master-node-logs-in-azure-kubernetes-service-aks"></a>Habilitar e examinar os logs do nó mestre do kubernetes no serviço kubernetes do Azure (AKS)

Com o AKS (serviço kubernetes do Azure), os componentes mestres, como *Kube-apiserver* e *Kube-Controller-Manager* , são fornecidos como um serviço gerenciado. Você cria e gerencia os nós que executam o tempo de execução de *kubelet* e contêiner e implanta seus aplicativos por meio do servidor de API kubernetes gerenciado. Para ajudar a solucionar problemas de aplicativos e serviços, talvez seja necessário exibir os logs gerados por esses componentes mestres. Este artigo mostra como usar os logs de Azure Monitor para habilitar e consultar os logs dos componentes mestres do kubernetes.

## <a name="before-you-begin"></a>Antes de começar

Este artigo requer um cluster AKS existente em execução na sua conta do Azure. Se você ainda não tiver um cluster AKS, crie um usando o [CLI do Azure][cli-quickstart] ou [portal do Azure][portal-quickstart]. Azure Monitor logs funciona com clusters AKS e não habilitados para RBAC.

## <a name="enable-diagnostics-logs"></a>Habilitar logs de diagnóstico

Para ajudar a coletar e examinar dados de várias fontes, os logs de Azure Monitor fornecem uma linguagem de consulta e um mecanismo de análise que fornece informações ao seu ambiente. Um espaço de trabalho é usado para agrupar e analisar os dados e pode ser integrado a outros serviços do Azure, como o Application Insights e a central de segurança. Para usar uma plataforma diferente para analisar os logs, você pode optar por enviar logs de diagnóstico para uma conta de armazenamento do Azure ou Hub de eventos. Para obter mais informações, consulte [o que são Azure monitor logs?][log-analytics-overview].

Os logs de Azure Monitor são habilitados e gerenciados no portal do Azure. Para habilitar a coleta de log para os componentes mestres do kubernetes no cluster do AKS, abra o portal do Azure em um navegador da Web e conclua as seguintes etapas:

1. Selecione o grupo de recursos para o cluster AKS, como *MyResource*Group. Não selecione o grupo de recursos que contém os recursos de cluster AKS individuais, como *MC_myResourceGroup_myAKSCluster_eastus*.
1. No lado esquerdo, escolha **configurações de diagnóstico**.
1. Selecione o cluster AKS, como *myAKSCluster*, e escolha **Adicionar configuração de diagnóstico**.
1. Insira um nome, como *myAKSClusterLogs*, e selecione a opção para **Enviar para log Analytics**.
1. Selecione um espaço de trabalho existente ou crie um novo. Se você criar um espaço de trabalho, forneça um nome de espaço de trabalho, um grupo de recursos e um local.
1. Na lista de logs disponíveis, selecione os logs que você deseja habilitar. Os logs comuns incluem *Kube-apiserver*, *Kube-Controller-Manager*e *Kube-Scheduler*. Você pode habilitar logs adicionais, como *Kube-Audit* e *cluster-* AutoScaler. Você pode retornar e alterar os logs coletados quando Log Analytics espaços de trabalho estiverem habilitados.
1. Quando estiver pronto, selecione **salvar** para habilitar a coleta dos logs selecionados.

A captura de tela do portal de exemplo a seguir mostra a janela *configurações de diagnóstico* :

![Habilitar Log Analytics espaço de trabalho para logs de Azure Monitor do cluster AKS](media/view-master-logs/enable-oms-log-analytics.png)

## <a name="schedule-a-test-pod-on-the-aks-cluster"></a>Agendar um pod de teste no cluster AKS

Para gerar alguns logs, crie um novo pod no cluster AKS. O exemplo de manifesto YAML a seguir pode ser usado para criar uma instância NGINX básica. Crie um arquivo chamado `nginx.yaml` em um editor de sua escolha e cole o seguinte conteúdo:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: mypod
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    ports:
    - containerPort: 80
```

Crie o Pod com o comando [kubectl Create][kubectl-create] e especifique o arquivo YAML, conforme mostrado no exemplo a seguir:

```
$ kubectl create -f nginx.yaml

pod/nginx created
```

## <a name="view-collected-logs"></a>Exibir logs coletados

Pode levar alguns minutos para que os logs de diagnóstico sejam habilitados e apareçam no espaço de trabalho Log Analytics. No portal do Azure, selecione o grupo de recursos para seu espaço de trabalho do Log Analytics, como MyResource Group, em seguida, escolha o recurso do log Analytics, como *myAKSLogs*.

![Selecione o espaço de trabalho Log Analytics para o cluster AKS](media/view-master-logs/select-log-analytics-workspace.png)

No lado esquerdo, escolha **logs**. Para exibir o *Kube-apiserver*, insira a seguinte consulta na caixa de texto:

```
AzureDiagnostics
| where Category == "kube-apiserver"
| project log_s
```

Muitos logs são provavelmente retornados para o servidor de API. Para reduzir a consulta para exibir os logs sobre o Pod NGINX criado na etapa anterior, adicione uma instrução *Where* adicional para pesquisar *pods/Nginx* , conforme mostrado na seguinte consulta de exemplo:

```
AzureDiagnostics
| where Category == "kube-apiserver"
| where log_s contains "pods/nginx"
| project log_s
```

Os logs específicos para seu Pod NGINX são exibidos, conforme mostrado no seguinte exemplo de captura de tela:

![Resultados da consulta do log Analytics para o Pod NGINX de exemplo](media/view-master-logs/log-analytics-query-results.png)

Para exibir logs adicionais, você pode atualizar a consulta para o nome da *categoria* para *Kube-Controller-Manager* ou *Kube-Scheduler*, dependendo de quais logs adicionais você habilitar. Instruções *Where* adicionais podem ser usadas para refinar os eventos que você está procurando.

Para obter mais informações sobre como consultar e filtrar os dados de log, consulte [Exibir ou analisar dados coletados com a pesquisa de logs do log Analytics][analyze-log-analytics].

## <a name="log-event-schema"></a>Esquema de evento de log

Para ajudar a analisar os dados de log, a tabela a seguir detalha o esquema usado para cada evento:

| Nome do campo               | Descrição |
|--------------------------|-------------|
| *resourceId*             | Recurso do Azure que produziu o log |
| *tempo*                   | Carimbo de data/hora de quando o log foi carregado |
| *category*               | Nome do contêiner/componente que gera o log |
| *operationName*          | Sempre *Microsoft. ContainerService/managedClusters/diagnosticLogs/Read* |
| *properties.log*         | Texto completo do log do componente |
| *properties.stream*      | *stderr* ou *stdout* |
| *properties.pod*         | Nome do Pod do qual o log veio |
| *properties.containerID* | ID do contêiner do Docker do qual este log veio |

## <a name="log-roles"></a>Funções de log

| Role                     | Descrição |
|--------------------------|-------------|
| *aksService*             | O nome de exibição no log de auditoria para a operação do plano de controle (do hcpService) |
| *masterclient*           | O nome de exibição no log de auditoria para MasterClientCertificate, o certificado obtido de AZ AKs Get-Credentials |
| *nodeclient*             | O nome de exibição para ClientCertificate, que é usado por nós de agente |

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu a habilitar e a examinar os logs dos componentes mestres do kubernetes no cluster do AKS. Para monitorar e solucionar problemas adicionais, você também pode [exibir os logs do Kubelet][kubelet-logs] e [habilitar o acesso ao nó SSH][aks-ssh].

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create

<!-- LINKS - internal -->
[cli-quickstart]: kubernetes-walkthrough.md
[portal-quickstart]: kubernetes-walkthrough-portal.md
[log-analytics-overview]: ../log-analytics/log-analytics-overview.md
[analyze-log-analytics]: ../azure-monitor/learn/tutorial-viewdata.md
[kubelet-logs]: kubelet-logs.md
[aks-ssh]: ssh.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
