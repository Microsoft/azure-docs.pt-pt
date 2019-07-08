---
title: Tutorial do Kubernetes no Azure - Dimensionar aplicação
description: Neste tutorial do Azure Kubernetes Service (AKS), irá aprender a dimensionar nós e pods no Kubernetes e a implementar o dimensionamento automático horizontal de pods.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: 5a942aa10f36df55ac232defa610102700e3995b
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614187"
---
# <a name="tutorial-scale-applications-in-azure-kubernetes-service-aks"></a>Tutorial: Aplicações no Azure Kubernetes Service (AKS)

Se seguiu os tutoriais, terá um cluster do Kubernetes a trabalhar no AKS e implementado a aplicação de votação do Azure de exemplo. Neste tutorial, parte cinco de sete, aumenta horizontalmente pods na aplicação e tenta dimensionar automaticamente. Também irá aprender a dimensionar o número de nós da VM do Azure, para alterar a capacidade do cluster para alojar cargas de trabalho. Saiba como:

> [!div class="checklist"]
> * Dimensionar os nós do Kubernetes
> * Dimensionar manualmente pods do Kubernetes que executam a sua aplicação
> * Configurar pods de dimensionamento automático que executam a aplicação de front-end

Tutoriais adicionais, a aplicação Azure Vote é atualizada para uma nova versão.

## <a name="before-you-begin"></a>Antes de começar

Nos tutoriais anteriores, foi compactada uma aplicação numa imagem de contentor. A imagem foi carregada para o Azure Container Registry e criado um cluster do AKS. A aplicação foi implementada, em seguida, para o cluster do AKS. Se ainda não concluiu estes passos e pretende acompanhar, inicie com [Tutorial 1 – criar imagens de contentor][aks-tutorial-prepare-app].

Este tutorial requer que está a executar a CLI do Azure versão 2.0.53 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure][azure-cli-install].

## <a name="manually-scale-pods"></a>Dimensionar pods manualmente

Quando o front-end do Azure Vote e a instância de Redis foram implementados nos tutoriais anteriores, foi criada uma réplica única. Para ver o número e o estado de pods no seu cluster, utilize o [kubectl obter][kubectl-get] comando da seguinte forma:

```console
kubectl get pods
```

O resultado do exemplo seguinte mostra um pod de front-end e um pod de back-end:

```
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2549686872-4d2r5   1/1       Running   0          31m
azure-vote-front-848767080-tf34m   1/1       Running   0          31m
```

Para alterar manualmente o número de pods na *azure-vote-front* implementação, utilize o [kubectl dimensionamento][kubectl-scale] comando. O exemplo seguinte aumenta o número de pods de front-end para *5*:

```console
kubectl scale --replicas=5 deployment/azure-vote-front
```

Execute [kubectl obter pods][kubectl-get] novamente para verificar que o AKS cria os pods adicionais. Um ou dois minutos depois, os pods adicionais estão disponíveis no seu cluster:

```console
$ kubectl get pods

                                    READY     STATUS    RESTARTS   AGE
azure-vote-back-2606967446-nmpcf    1/1       Running   0          15m
azure-vote-front-3309479140-2hfh0   1/1       Running   0          3m
azure-vote-front-3309479140-bzt05   1/1       Running   0          3m
azure-vote-front-3309479140-fvcvm   1/1       Running   0          3m
azure-vote-front-3309479140-hrbf2   1/1       Running   0          15m
azure-vote-front-3309479140-qphz8   1/1       Running   0          3m
```

## <a name="autoscale-pods"></a>Dimensionar pods automaticamente

Kubernetes suporta [dimensionamento automático horizontal de pods][kubernetes-hpa] to adjust the number of pods in a deployment depending on CPU utilization or other select metrics. The [Metrics Server][metrics-server] é utilizado para fornecer a utilização de recursos para o Kubernetes e será automaticamente implantado em clusters do AKS versões 1.10 e superiores. Para ver a versão do cluster do AKS, utilize o [show do az aks][az-aks-show] de comando, conforme mostrado no exemplo a seguir:

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query kubernetesVersion
```

Se a versão do cluster AKS for inferior a *1.10*, instale o Servidor de Métricas; caso contrário, ignore este passo. Para instalar, clone o `metrics-server` repositório do GitHub e instalem as definições de recursos de exemplo. Para ver o conteúdo dessas definições de YAML, consulte [servidor de métricas para Kuberenetes 1.8 +][metrics-server-github].

```console
git clone https://github.com/kubernetes-incubator/metrics-server.git
kubectl create -f metrics-server/deploy/1.8+/
```

Para utilizar o dimensionamento automático, todos os contentores em seus pods e seus pods tem de ter os pedidos de CPU e os limites definidos. Na `azure-vote-front` implementação, o contentor de front-end já pede 0,25 de CPU, com um limite de 0,5 de CPU. Estes pedidos de recursos e os limites são definidos como mostrado no seguinte fragmento de exemplo:

```yaml
resources:
  requests:
     cpu: 250m
  limits:
     cpu: 500m
```

O exemplo seguinte utiliza a [kubectl autoscale][kubectl-autoscale] comando para dimensionar automaticamente o número de pods na *do azure-vote-front* implementação. Se a utilização da CPU exceder 50%, o dimensionamento automático aumenta os pods até um máximo de *10* instâncias. Um mínimo de *3* instâncias, em seguida, é definida para a implementação:

```console
kubectl autoscale deployment azure-vote-front --cpu-percent=50 --min=3 --max=10
```

Para ver o estado do dimensionamento automático, utilize o comando `kubectl get hpa` da seguinte forma:

```
$ kubectl get hpa

NAME               REFERENCE                     TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
azure-vote-front   Deployment/azure-vote-front   0% / 50%   3         10        3          2m
```

Após alguns minutos, com carga mínima na aplicação do Azure Vote, o número de réplicas de pod diminui automaticamente para três. Pode utilizar `kubectl get pods` novamente para ver os pods que não são precisos a serem removidos.

## <a name="manually-scale-aks-nodes"></a>Dimensionar nós do AKS manualmente

Se criou o seu cluster Kubernetes com os comandos no tutorial anterior, tem um nó. Pode ajustar o número de nós manualmente, se pretender mais ou menos cargas de trabalho do contentor no seu cluster.

O exemplo seguinte aumenta o número de nós para três no cluster do Kubernetes denominado *myAKSCluster*. O comando demora poucos minutos a concluir.

```azurecli
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 3
```

Quando o cluster foi dimensionada com êxito, o resultado é semelhante ao exemplo seguinte:

```
"agentPoolProfiles": [
  {
    "count": 3,
    "dnsPrefix": null,
    "fqdn": null,
    "name": "myAKSCluster",
    "osDiskSizeGb": null,
    "osType": "Linux",
    "ports": null,
    "storageProfile": "ManagedDisks",
    "vmSize": "Standard_D2_v2",
    "vnetSubnetId": null
  }
```

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, utilizou diferentes funcionalidades de dimensionamento no seu cluster Kubernetes. Aprendeu a:

> [!div class="checklist"]
> * Dimensionar manualmente pods do Kubernetes que executam a sua aplicação
> * Configurar pods de dimensionamento automático que executam a aplicação de front-end
> * Dimensionar manualmente os nós do Kubernetes

Avance para o próximo tutorial para saber como atualizar a aplicação no Kubernetes.

> [!div class="nextstepaction"]
> [Atualizar uma aplicação no Kubernetes][aks-tutorial-update-app]

<!-- LINKS - external -->
[kubectl-autoscale]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#autoscale
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-scale]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#scale
[kubernetes-hpa]: https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/
[metrics-server-github]: https://github.com/kubernetes-incubator/metrics-server/tree/master/deploy/1.8%2B
[metrics-server]: https://v1-12.docs.kubernetes.io/docs/tasks/debug-application-cluster/core-metrics-pipeline/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-update-app]: ./tutorial-kubernetes-app-update.md
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
