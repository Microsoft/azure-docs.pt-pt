---
title: Resolução de problemas comum Azure Arc permitiu problemas de Kubernetes (Preview)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Resolver problemas comuns com o Arc permitiu que os aglomerados de Kubernetes.
keywords: Kubernetes, Arc, Azure, contentores
ms.openlocfilehash: 1527f8d4ca06c2deaf4ce18b73bfdb515dcadc63
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83725589"
---
# <a name="azure-arc-enabled-kubernetes-troubleshooting-preview"></a>Azure Arc permitiu a resolução de problemas de Kubernetes (Preview)

Este documento fornece alguns cenários comuns de resolução de problemas com conectividade, permissões e agentes.

## <a name="general-troubleshooting"></a>Resolução geral de problemas

### <a name="azure-cli-set-up"></a>Azure CLI configurado
Antes de utilizar os comandos CLI de conexão az ou az k8s, certifique-se de que o AZ está preparado para funcionar contra a correta subscrição do Azure.

```console
az account set --subscription 'subscriptionId'
az account show
```

### <a name="azure-arc-agents"></a>agentes azure-arc
Todos os agentes do Azure Arc habilitados Kubernetes são implantados como cápsulas no `azure-arc` espaço de nomes. Em operações normais, todas as cápsulas devem estar a funcionar e a passar os seus exames de saúde.

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

Se a libertação do Leme não for encontrada ou em falta, tente embarcar novamente no aglomerado.

Se a libertação do Leme estiver presente e `STATUS: deployed` determinar o estado dos agentes que `kubectl` utilizam:

```console
$ kubectl -n azure-arc get deployments,pods
NAME                                        READY   UP-TO-DATE AVAILABLE AGE
deployment.apps/cluster-metadata-operator   1/1     1           1        16h
deployment.apps/clusteridentityoperator     1/1     1           1        16h
deployment.apps/config-agent                1/1     1           1        16h
deployment.apps/controller-manager          1/1     1           1        16h
deployment.apps/flux-logs-agent             1/1     1           1        16h
deployment.apps/metrics-agent               1/1     1           1        16h
deployment.apps/resource-sync-agent         1/1     1           1        16h

NAME                                            READY   STATUS   RESTART AGE
pod/cluster-metadata-operator-7fb54d9986-g785b  2/2     Running  0       16h
pod/clusteridentityoperator-6d6678ffd4-tx8hr    3/3     Running  0       16h
pod/config-agent-544c4669f9-4th92               3/3     Running  0       16h
pod/controller-manager-fddf5c766-ftd96          3/3     Running  0       16h
pod/flux-logs-agent-7c489f57f4-mwqqv            2/2     Running  0       16h
pod/metrics-agent-58b765c8db-n5l7k              2/2     Running  0       16h
pod/resource-sync-agent-5cf85976c7-522p5        3/3     Running  0       16h
```

Todos os Pods devem mostrar `STATUS` como e devem ser `Running` `READY` `3/3` `2/2` ou. Pegue os registos e descreva as cápsulas que estão a regressar `Error` ou `CrashLoopBackOff` .

## <a name="unable-to-connect-my-kubernetes-cluster-to-azure"></a>Incapaz de ligar o meu cluster Kubernetes a Azure

A ligação de clusters ao Azure requer acesso a uma subscrição do Azure e `cluster-admin` acesso a um cluster alvo. Se o cluster não puder ser atingido ou tiver permissões insuficientes a bordo falhará.

### <a name="insufficient-cluster-permissions"></a>Permissões insuficientes do cluster

Se o ficheiro kubeconfig fornecido não tiver permissões suficientes para instalar os agentes Azure Arc, o comando Azure CLI retornará um erro ao tentar ligar para a API de Kubernetes.

```console
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...

Error: list: failed to list: secrets is forbidden: User "myuser" cannot list resource "secrets" in API group "" at the cluster scope
```

O proprietário do cluster deve utilizar um utilizador Kubernetes com permissões de administrador de cluster.

### <a name="installation-timeouts"></a>Intervalos de tempo de instalação

A instalação do agente Azure Arc requer a execução de um conjunto de contentores no aglomerado de alvos. Se o cluster estiver a passar por uma ligação lenta à internet, a imagem do contentor pode demorar mais tempo do que os intervalos de tempo do Azure CLI.

```console
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...

There was a problem with connect-agent deployment. Please run 'kubectl -n azure-arc logs -l app.kubernetes.io/component=connect-agent -c connect-agent' to debug the error.
```

## <a name="configuration-management"></a>Gestão da configuração

### <a name="general"></a>Geral
Para ajudar a resolver problemas com a configuração do controlo de origem, executar comandos az com o interruptor depuração.

```console
az provider show -n Microsoft.KubernetesConfiguration --debug
az k8sconfiguration create <parameters> --debug
```

### <a name="create-source-control-configuration"></a>Criar configuração de controlo de fontes
O papel do contribuinte no recurso Microsoft.Kubernetes/connectedCluster é necessário e suficiente para criar o recurso Microsoft.KubernetesConfiguration/sourceControlConfiguration.

### <a name="configuration-remains-pending"></a>A configuração permanece`Pending`

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
