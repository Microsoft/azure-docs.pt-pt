---
title: Resolução de problemas comum Azure Arc permitiu problemas de Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 03/02/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Resolver problemas comuns com o Arc permitiu que os aglomerados de Kubernetes.
keywords: Kubernetes, Arc, Azure, contentores
ms.openlocfilehash: 992ea75c48b2630032e1314610986fbc610eec7b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105025786"
---
# <a name="azure-arc-enabled-kubernetes-troubleshooting"></a>Azure Arc permitiu a resolução de problemas de Kubernetes

Este documento fornece guias de resolução de problemas para problemas com conectividade, permissões e agentes.

## <a name="general-troubleshooting"></a>Resolução geral de problemas

### <a name="azure-cli"></a>CLI do Azure

Antes de utilizar `az connectedk8s` ou `az k8s-configuration` comandos CLI, verifique se o Azure CLI está definido para funcionar contra a correta subscrição do Azure.

```azurecli
az account set --subscription 'subscriptionId'
az account show
```

### <a name="azure-arc-agents"></a>Agentes do Arco de Azure

Todos os agentes do Azure Arc habilitados Kubernetes são implantados como cápsulas no `azure-arc` espaço de nomes. Todas as cápsulas devem estar a correr e a passar os exames de saúde.

Primeiro, verifique a libertação do leme do Arco Azure:

```console
$ helm --namespace default status azure-arc
NAME: azure-arc
LAST DEPLOYED: Fri Apr  3 11:13:10 2020
NAMESPACE: default
STATUS: deployed
REVISION: 5
TEST SUITE: None
```

Se a libertação do Leme não for encontrada ou em falta, tente [ligar o cluster ao Arco Azure](./quickstart-connect-cluster.md) novamente.

Se a libertação do Leme estiver `STATUS: deployed` presente, verifique o estado dos agentes que `kubectl` utilizam:

```console
$ kubectl -n azure-arc get deployments,pods
NAME                                       READY  UP-TO-DATE  AVAILABLE  AGE
deployment.apps/clusteridentityoperator     1/1       1          1       16h
deployment.apps/config-agent                1/1       1          1       16h
deployment.apps/cluster-metadata-operator   1/1       1          1       16h
deployment.apps/controller-manager          1/1       1          1       16h
deployment.apps/flux-logs-agent             1/1       1          1       16h
deployment.apps/metrics-agent               1/1       1          1       16h
deployment.apps/resource-sync-agent         1/1       1          1       16h

NAME                                            READY   STATUS  RESTART  AGE
pod/cluster-metadata-operator-7fb54d9986-g785b  2/2     Running  0       16h
pod/clusteridentityoperator-6d6678ffd4-tx8hr    3/3     Running  0       16h
pod/config-agent-544c4669f9-4th92               3/3     Running  0       16h
pod/controller-manager-fddf5c766-ftd96          3/3     Running  0       16h
pod/flux-logs-agent-7c489f57f4-mwqqv            2/2     Running  0       16h
pod/metrics-agent-58b765c8db-n5l7k              2/2     Running  0       16h
pod/resource-sync-agent-5cf85976c7-522p5        3/3     Running  0       16h
```

Todas as cápsulas devem aparecer `STATUS` como com qualquer uma ou por baixo da `Running` `3/3` `2/2` `READY` coluna. Pegue os registos e descreva as cápsulas que retornam `Error` ou `CrashLoopBackOff` . Se alguma cápsula estiver presa no `Pending` estado, pode haver recursos insuficientes nos nós de cluster. [Aumentar o seu cluster](https://kubernetes.io/docs/tasks/administer-cluster/) pode fazer com que estas cápsulas transitem para `Running` o estado.

## <a name="connecting-kubernetes-clusters-to-azure-arc"></a>Ligando aglomerados de Kubernetes ao Arco de Azure

A ligação de clusters ao Azure requer tanto o acesso a uma subscrição do Azure como `cluster-admin` o acesso a um cluster alvo. Se não conseguir chegar ao cluster ou tiver permissões insuficientes, a ligação do cluster ao Arco de Azure falhará.

### <a name="insufficient-cluster-permissions"></a>Permissões insuficientes do cluster

Se o ficheiro kubeconfig fornecido não tiver permissões suficientes para instalar os agentes Azure Arc, o comando Azure CLI retornará um erro.

```azurecli
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...

Error: list: failed to list: secrets is forbidden: User "myuser" cannot list resource "secrets" in API group "" at the cluster scope
```

O utilizador que liga o cluster ao Arco Azure deve ter `cluster-admin` uma função que lhes seja atribuída no cluster.

### <a name="installation-timeouts"></a>Intervalos de tempo de instalação

A ligação de um cluster Kubernetes ao Arco Azure ativado kubernetes requer a instalação de agentes Azure Arc no cluster. Se o cluster estiver a passar por uma ligação lenta à internet, a imagem do contentor puxa para os agentes pode demorar mais tempo do que os intervalos do Azure CLI.

```azurecli
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...
```

### <a name="helm-issue"></a>Questão do leme

A `v3.3.0-rc.1` versão helm tem um [problema](https://github.com/helm/helm/pull/8527) em que a instalação/atualização do leme (utilizada pela `connectedk8s` extensão CLI) resulta na execução de todos os ganchos que conduzem ao seguinte erro:

```console
$ az connectedk8s connect -n shasbakstest -g shasbakstest
Ensure that you have the latest helm version installed before proceeding.
This operation might take a while...

Please check if the azure-arc namespace was deployed and run 'kubectl get pods -n azure-arc' to check if all the pods are in running state. A possible cause for pods stuck in pending state could be insufficientresources on the kubernetes cluster to onboard to arc.
ValidationError: Unable to install helm release: Error: customresourcedefinitions.apiextensions.k8s.io "connectedclusters.arc.azure.com" not found
```

Para recuperar desta edição, siga estes passos:

1. Elimine o recurso Azure Arc ativado pela Kubernetes no portal Azure.
2. Execute os seguintes comandos na sua máquina:
    
    ```console
    kubectl delete ns azure-arc
    kubectl delete clusterrolebinding azure-arc-operator
    kubectl delete secret sh.helm.release.v1.azure-arc.v1
    ```

3. [Instale uma versão estável](https://helm.sh/docs/intro/install/) do Helm 3 na sua máquina em vez da versão do candidato de lançamento.
4. Executar o `az connectedk8s connect` comando com os valores adequados para ligar o cluster ao Arco de Azure.

## <a name="configuration-management"></a>Gestão da configuração

### <a name="general"></a>Geral
Para ajudar a resolver problemas com o recurso de configuração, executar comandos az com `--debug` parâmetro especificado.

```console
az provider show -n Microsoft.KubernetesConfiguration --debug
az k8s-configuration create <parameters> --debug
```

### <a name="create-configurations"></a>Criar configurações

Escreva permissões no recurso Azure Arc ativado por Kubernetes ( `Microsoft.Kubernetes/connectedClusters/Write` ) são necessárias e suficientes para criar configurações nesse cluster.

### <a name="configuration-remains-pending"></a>A configuração permanece `Pending`

```console
kubectl -n azure-arc logs -l app.kubernetes.io/component=config-agent -c config-agent
$ k -n pending get gitconfigs.clusterconfig.azure.com  -o yaml
apiVersion: v1
items:
- apiVersion: clusterconfig.azure.com/v1beta1
  kind: GitConfig
  metadata:
    creationTimestamp: "2020-04-13T20:37:25Z"
    generation: 1
    name: pending
    namespace: pending
    resourceVersion: "10088301"
    selfLink: /apis/clusterconfig.azure.com/v1beta1/namespaces/pending/gitconfigs/pending
    uid: d9452407-ff53-4c02-9b5a-51d55e62f704
  spec:
    correlationId: ""
    deleteOperator: false
    enableHelmOperator: false
    giturl: git@github.com:slack/cluster-config.git
    helmOperatorProperties: null
    operatorClientLocation: azurearcfork8s.azurecr.io/arc-preview/fluxctl:0.1.3
    operatorInstanceName: pending
    operatorParams: '"--disable-registry-scanning"'
    operatorScope: cluster
    operatorType: flux
  status:
    configAppliedTime: "2020-04-13T20:38:43.081Z"
    isSyncedWithAzure: true
    lastPolledStatusTime: ""
    message: 'Error: {exit status 1} occurred while doing the operation : {Installing
      the operator} on the config'
    operatorPropertiesHashed: ""
    publicKey: ""
    retryCountPublicKey: 0
    status: Installing the operator
kind: List
metadata:
  resourceVersion: ""
  selfLink: ""
```
## <a name="monitoring"></a>Monitorização

O Azure Monitor para contentores requer que o seu DaemonSet seja executado em modo privilegiado. Para criar com sucesso um cluster canónico de Kubernetes encantado para monitorização, executar o seguinte comando:

```console
juju config kubernetes-worker allow-privileged=true
```