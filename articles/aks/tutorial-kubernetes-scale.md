---
title: Tutorial do Kubernetes no Azure - Dimensionar aplicação
description: Neste tutorial do Azure Kubernetes Service (AKS), irá aprender a dimensionar nós e pods no Kubernetes e a implementar o dimensionamento automático horizontal de pods.
services: container-service
ms.topic: tutorial
ms.date: 01/12/2021
ms.custom: mvc
ms.openlocfilehash: dfebb6561e83c51063515ec655153aaaa7a09c0c
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2021
ms.locfileid: "98251374"
---
# <a name="tutorial-scale-applications-in-azure-kubernetes-service-aks"></a>Tutorial: Dimensionar aplicações no Serviço Kubernetes do Azure (AKS)

Se seguiu os tutoriais, tem um cluster Kubernetes em funcionamento na AKS e implementou a aplicação de votação Azure. Neste tutorial, parte cinco de sete, aumenta horizontalmente pods na aplicação e tenta dimensionar automaticamente. Também irá aprender a dimensionar o número de nós da VM do Azure, para alterar a capacidade do cluster para alojar cargas de trabalho. Saiba como:

> [!div class="checklist"]
> * Dimensionar os nós do Kubernetes
> * Dimensionar manualmente pods do Kubernetes que executam a sua aplicação
> * Configurar pods de dimensionamento automático que executam a aplicação de front-end

Em tutoriais posteriores, a aplicação Azure Vote é atualizada para uma nova versão.

## <a name="before-you-begin"></a>Before you begin

Em tutoriais anteriores, uma aplicação foi embalada numa imagem de contentor. Esta imagem foi enviada para o Registo de Contentores Azure, e você criou um cluster AKS. A aplicação foi então implantada no cluster AKS. Se não fez estes passos e gostaria de seguir em frente, comece com [Tutorial 1 – Crie imagens de contentores.][aks-tutorial-prepare-app]

Este tutorial requer que esteja a executar a versão 2.0.53 ou mais tarde do Azure CLI. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli-install].

## <a name="manually-scale-pods"></a>Dimensionar pods manualmente

Quando o front-end do Azure Vote e a instância de Redis foram implementados nos tutoriais anteriores, foi criada uma réplica única. Para ver o número e o estado de pods no seu cluster, utilize o comando [kubectl get][kubectl-get] da seguinte forma:

```console
kubectl get pods
```

O resultado do exemplo seguinte mostra um pod de front-end e um pod de back-end:

```output
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2549686872-4d2r5   1/1       Running   0          31m
azure-vote-front-848767080-tf34m   1/1       Running   0          31m
```

Para alterar manualmente o número de pods na implementação do *azure-vote-front* com o comando [kubectl scale][kubectl-scale]. O exemplo seguinte aumenta o número de pods de front-end para *5*:

```console
kubectl scale --replicas=5 deployment/azure-vote-front
```

Executar [kubectl obter pods][kubectl-get] novamente para verificar se a AKS cria com sucesso as cápsulas adicionais. Após um minuto ou mais, as cápsulas estão disponíveis no seu cluster:

```console
kubectl get pods

                                    READY     STATUS    RESTARTS   AGE
azure-vote-back-2606967446-nmpcf    1/1       Running   0          15m
azure-vote-front-3309479140-2hfh0   1/1       Running   0          3m
azure-vote-front-3309479140-bzt05   1/1       Running   0          3m
azure-vote-front-3309479140-fvcvm   1/1       Running   0          3m
azure-vote-front-3309479140-hrbf2   1/1       Running   0          15m
azure-vote-front-3309479140-qphz8   1/1       Running   0          3m
```

## <a name="autoscale-pods"></a>Dimensionar pods automaticamente

O Kubernetes suporta [dimensionamento automático horizontal de pods][kubernetes-hpa] para ajustar o número de pods numa implementação, consoante a utilização da CPU ou de outras métricas selecionadas. O [Servidor de Métricas][metrics-server] é utilizado para fornecer a utilização de recursos ao Kubernetes e é implementado automaticamente em clusters do AKS da versão 1.10 e superior. Para ver a versão do cluster AKS, utilize o comando [az aks show][az-aks-show], conforme mostrado no exemplo seguinte:

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query kubernetesVersion --output table
```

> [!NOTE]
> Se o seu cluster AKS for inferior *a 1.10,* o Servidor Métricas não é instalado automaticamente. Os manifestos de instalação do Metrics Server estão disponíveis como um `components.yaml` ativo nas versões do Metrics Server, o que significa que pode instalá-los através de um url. Para saber mais sobre estas definições YAML, consulte a secção [de implementação][metrics-server-github] da leitura.
> 
> Instalação exemplo:
> ```console
> kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/download/v0.3.6/components.yaml
> ```

Para utilizar o autoscaler, todos os recipientes das suas cápsulas e das suas cápsulas devem ter pedidos de CPU e limites definidos. Na `azure-vote-front` implantação, o contentor frontal já solicita 0,25 CPU, com um limite de 0,5 CPU. Estes pedidos e limites de recursos são definidos como mostrado no seguinte exemplo:

```yaml
resources:
  requests:
     cpu: 250m
  limits:
     cpu: 500m
```

O exemplo seguinte utiliza o comando [kubectl autoscale][kubectl-autoscale] para dimensionar automaticamente o número de pods na implementação *azure-vote-front*. Se a utilização média do CPU em todas as cápsulas exceder 50% da sua utilização solicitada, o autoscaler aumenta as cápsulas até um máximo de *10* instâncias. É então definido um mínimo de *3* instâncias para a implantação:

```console
kubectl autoscale deployment azure-vote-front --cpu-percent=50 --min=3 --max=10
```

Em alternativa, pode criar um ficheiro manifesto para definir o comportamento do autoescalador e os limites de recursos. Segue-se um exemplo de um ficheiro manifesto denominado `azure-vote-hpa.yaml` .

```yaml
apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: azure-vote-back-hpa
spec:
  maxReplicas: 10 # define max replica count
  minReplicas: 3  # define min replica count
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: azure-vote-back
  targetCPUUtilizationPercentage: 50 # target CPU utilization

---

apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: azure-vote-front-hpa
spec:
  maxReplicas: 10 # define max replica count
  minReplicas: 3  # define min replica count
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: azure-vote-front
  targetCPUUtilizationPercentage: 50 # target CPU utilization
```

Utilize `kubectl apply` para aplicar o autoescalador definido no ficheiro `azure-vote-hpa.yaml` manifesto.

```console
kubectl apply -f azure-vote-hpa.yaml
```

Para ver o estado do dimensionamento automático, utilize o comando `kubectl get hpa` da seguinte forma:

```
kubectl get hpa

NAME               REFERENCE                     TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
azure-vote-front   Deployment/azure-vote-front   0% / 50%   3         10        3          2m
```

Após alguns minutos, com carga mínima na aplicação do Azure Vote, o número de réplicas de pod diminui automaticamente para três. Pode utilizar `kubectl get pods` novamente para ver os pods que não são precisos a serem removidos.

## <a name="manually-scale-aks-nodes"></a>Dimensionar nós do AKS manualmente

Se criou o seu cluster Kubernetes usando os comandos no tutorial anterior, tem dois nós. Pode ajustar o número de nós manualmente, se pretender mais ou menos cargas de trabalho do contentor no seu cluster.

O exemplo seguinte aumenta o número de nós para três no cluster do Kubernetes denominado *myAKSCluster*. O comando demora poucos minutos a concluir.

```azurecli
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 3
```

Quando o cluster tiver escalado com sucesso, a saída é semelhante ao seguinte exemplo:

```output
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

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, utilizou diferentes funcionalidades de dimensionamento no seu cluster Kubernetes. Aprendeu a:

> [!div class="checklist"]
> * Dimensionar manualmente pods do Kubernetes que executam a sua aplicação
> * Configurar pods de dimensionamento automático que executam a aplicação de front-end
> * Escalar manualmente os nosdes de Kubernetes

Avance para o próximo tutorial para saber como atualizar a aplicação no Kubernetes.

> [!div class="nextstepaction"]
> [Atualizar uma aplicação no Kubernetes][aks-tutorial-update-app]

<!-- LINKS - external -->
[kubectl-autoscale]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#autoscale
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-scale]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#scale
[kubernetes-hpa]: https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/
[metrics-server-github]: https://github.com/kubernetes-sigs/metrics-server/blob/master/README.md#deployment
[metrics-server]: https://kubernetes.io/docs/tasks/debug-application-cluster/resource-metrics-pipeline/#metrics-server

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-update-app]: ./tutorial-kubernetes-app-update.md
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
