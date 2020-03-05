---
title: (DEPRECIADO) Tutorial do Serviço de Contentores Azure - Monitor Kubernetes
description: Tutorial do Azure Container Service - Monitorizar o Kubernetes com o Log Analytics
author: iainfoulds
ms.service: container-service
ms.topic: tutorial
ms.date: 04/05/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 84c2438a8c25b1b64f46e12923212812beac687d
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273329"
---
# <a name="deprecated-monitor-a-kubernetes-cluster-with-log-analytics"></a>(DEPRECIADO) Monitorize um cluster Kubernetes com Log Analytics

> [!TIP]
> Para a versão atualizada este tutorial que utiliza o Serviço Azure Kubernetes, consulte o Monitor Azure para obter uma visão geral dos [recipientes (Pré-visualização).](../../azure-monitor/insights/container-insights-overview.md)

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

É fundamental monitorizar o cluster e os contentores do Kubernetes, especialmente ao gerir um cluster de produção em escala, com várias aplicações.

Pode tirar partido das várias soluções de monitorização do Kubernetes, da Microsoft ou de outros fornecedores. Neste tutorial, vai monitorizar o seu cluster do Kubernetes com a solução Contentores do [Log Analytics](../../operations-management-suite/operations-management-suite-overview.md), a solução da Microsoft para gestão de TI com base na cloud. (A solução Contentores está em pré-visualização.)

Este tutorial, a parte sete de sete, abrange as seguintes tarefas:

> [!div class="checklist"]
> * Definições Obter Área de Trabalho do Log Analytics
> * Configurar os agentes do Log Analytics em nós do Kubernetes
> * Aceder a informações de monitorização no portal do Log Analytics ou no portal do Azure

## <a name="before-you-begin"></a>Antes de começar

Nos tutoriais anteriores, foi compactada uma aplicação em imagens de contentores, carregadas estas imagens para o Azure Container Registry e criado um cluster de Kubernetes.

Se ainda não concluiu estes passos e pretende acompanhar, regresse ao [Tutorial 1 – Criar imagens de contentor](./container-service-tutorial-kubernetes-prepare-app.md).

## <a name="get-workspace-settings"></a>Obter definições da Área de Trabalho

Quando puder aceder ao [portal do Log Analytics](https://mms.microsoft.com), aceda a **Definições** > **Origens Ligadas** > **Servidores Linux**. Aqui, pode encontrar o *ID da Área de Trabalho* e uma *Chave da Área de Trabalho* primária ou secundária. Tome nota destes valores, pois precisará deles para configurar os agentes do Log Analytics no cluster.

## <a name="create-kubernetes-secret"></a>Criar segredo do Kubernetes

Armazene as definições da área de trabalho do Log Analytics no segredo do Kubernetes denominado `omsagent-secret` com o comando [kubectl create secret][kubectl-create-secret]. Atualize `WORKSPACE_ID` com o seu ID da área de trabalho do Log Analytics e `WORKSPACE_KEY` com a chave da área de trabalho.

```console
kubectl create secret generic omsagent-secret --from-literal=WSID=WORKSPACE_ID --from-literal=KEY=WORKSPACE_KEY
```

## <a name="set-up-log-analytics-agents"></a>Configurar os agentes do Log Analytics

O ficheiro de manifesto do Kubernetes seguinte pode ser utilizado para configurar agentes de monitorização do contentor num cluster de Kubernetes. Cria um [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) do Kubernetes, que executa um único pod idêntico em cada nó de cluster.

Guarde o seguinte texto num ficheiro denominado `oms-daemonset.yaml`.

```yaml
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
 name: omsagent
spec:
 template:
  metadata:
   labels:
    app: omsagent
    agentVersion: 1.4.3-174
    dockerProviderVersion: 1.0.0-30
  spec:
   containers:
     - name: omsagent
       image: "microsoft/oms"
       imagePullPolicy: Always
       securityContext:
         privileged: true
       ports:
       - containerPort: 25225
         protocol: TCP
       - containerPort: 25224
         protocol: UDP
       volumeMounts:
        - mountPath: /var/run/docker.sock
          name: docker-sock
        - mountPath: /var/log
          name: host-log
        - mountPath: /etc/omsagent-secret
          name: omsagent-secret
          readOnly: true
        - mountPath: /var/lib/docker/containers
          name: containerlog-path
       livenessProbe:
        exec:
         command:
         - /bin/bash
         - -c
         - ps -ef | grep omsagent | grep -v "grep"
        initialDelaySeconds: 60
        periodSeconds: 60
   nodeSelector:
    beta.kubernetes.io/os: linux
   # Tolerate a NoSchedule taint on master that ACS Engine sets.
   tolerations:
    - key: "node-role.kubernetes.io/master"
      operator: "Equal"
      value: "true"
      effect: "NoSchedule"
   volumes:
    - name: docker-sock
      hostPath:
       path: /var/run/docker.sock
    - name: host-log
      hostPath:
       path: /var/log
    - name: omsagent-secret
      secret:
       secretName: omsagent-secret
    - name: containerlog-path
      hostPath:
       path: /var/lib/docker/containers
```

Crie o DaemonSet com o seguinte comando:

```console
kubectl create -f oms-daemonset.yaml
```

Para ver se o DaemonSet está criado, execute:

```console
kubectl get daemonset
```

O resultado é semelhante ao seguinte:

```output
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE-SELECTOR   AGE
omsagent   3         3         3         0            3           <none>          5m
```

Após os agentes estarem em execução, demora alguns minutos para o Log Analytics ingerir e processar os dados.

## <a name="access-monitoring-data"></a>Aceder aos dados de monitorização

Veja e analise os dados de monitorização do contentor com a [solução Contentor](../../azure-monitor/insights/containers.md) no portal do Log Analytics ou no portal do Azure.

Para instalar a solução Contentor através do [portal do Log Analytics](https://mms.microsoft.com), aceda à **Galeria de Soluções**. Em seguida, adicione **Solução Contentor**. Em alternativa, adicione a solução Contentores do [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft.containersoms?tab=Overview).

No portal do Log Analytics, procure um mosaico de resumo **Contentores** no dashboard. Clique no mosaico para obter detalhes, incluindo: eventos, erros, estado, inventário de imagens e a utilização da CPU e memória dos contentores. Para obter informações mais detalhadas, clique num registo em qualquer mosaico ou efetue uma [pesquisa de registos](../../log-analytics/log-analytics-log-searches.md).

![Dashboard Contentores no portal do Azure](./media/container-service-tutorial-kubernetes-monitor/oms-containers-dashboard.png)

Da mesma forma, no portal do Azure, aceda ao **Log Analytics** e selecione o nome da sua área de trabalho. Para ver o mosaico de resumo **Contentores**, clique em **Soluções** > **Contentores**. Para ver os detalhes, clique no mosaico.

Veja a [documentação do Azure Log Analytics](../../azure-monitor/log-query/log-query-overview.md) para obter documentação de orientação detalhada sobre a consulta e análise de dados de monitorização.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, monitorizou o cluster de Kubernetes com o Log Analytics. Tarefas abrangidas incluídas:

> [!div class="checklist"]
> * Definições Obter Área de Trabalho do Log Analytics
> * Configurar os agentes do Log Analytics em nós do Kubernetes
> * Aceder a informações de monitorização no portal do Log Analytics ou no portal do Azure


Siga esta ligação para ver os exemplos de scripts pré-criados do Container Service.

> [!div class="nextstepaction"]
> [Exemplos de script do Azure Container Service](cli-samples.md)
