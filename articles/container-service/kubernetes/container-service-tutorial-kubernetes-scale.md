---
title: (PRETERIDO) Tutorial do Azure Container Service – Dimensionar aplicação
description: Tutorial do Azure Container Service – Dimensionar Aplicação
services: container-service
author: dlepow
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 09/14/2017
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: b16682535e7311648e832286e1e876ee0e694712
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61230390"
---
# <a name="deprecated-scale-kubernetes-pods-and-kubernetes-infrastructure"></a>(PRETERIDO) Dimensionar pods e infraestrutura do Kubernetes

> [!TIP]
> Para a versão atualizada neste tutorial que utiliza o Azure Kubernetes Service, consulte [Tutorial: Dimensionar aplicações no Azure Kubernetes Service (AKS)](../../aks/tutorial-kubernetes-scale.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

Se tiver seguido os tutoriais, terá um cluster do Kubernetes a trabalhar no Azure Container Service e implementado a aplicação do Azure Voting. 

Neste tutorial, parte cinco de sete, aumenta horizontalmente pods na aplicação e tenta dimensionar automaticamente. Também irá aprender a dimensionar o número de nós de agente da VM do Azure, para alterar a capacidade do cluster para alojar cargas de trabalho. As tarefas concluídas incluem:

> [!div class="checklist"]
> * Dimensionar manualmente pods do Kubernetes
> * Configurar pods de Dimensionamento automático com o front-end da aplicação
> * Dimensionar os nós de agente do Azure do Kubernetes

Nos tutoriais subsequentes, a aplicação Azure Vote é atualizada e o Log Analytics é configurado para monitorizar o cluster de Kubernetes.

## <a name="before-you-begin"></a>Antes de começar

Nos tutoriais anteriores, foi compactada uma aplicação numa imagem de contentor, carregada esta imagem para o Azure Container Registry e criado um cluster de Kubernetes. A aplicação foi, em seguida, executada no cluster de Kubernetes. 

Se ainda não concluiu estes passos e pretende acompanhar, regresse ao [Tutorial 1 – Criar imagens de contentor](./container-service-tutorial-kubernetes-prepare-app.md). 

## <a name="manually-scale-pods"></a>Dimensionar pods manualmente

Deste modo, o front-end do Azure Vote e a instância de Redis foi implementada, cada um com uma réplica única. Para verificar, execute o comando [kubectl get](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get).

```azurecli-interactive
kubectl get pods
```

Saída:

```bash
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2549686872-4d2r5   1/1       Running   0          31m
azure-vote-front-848767080-tf34m   1/1       Running   0          31m
```

Altere manualmente o número de pods na implementação `azure-vote-front` com o comando [kubectl scale](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#scale). Este exemplo aumenta o número para 5.

```azurecli-interactive
kubectl scale --replicas=5 deployment/azure-vote-front
```

Execute [kubectl get pods](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) para verificar se o Kubernetes está a criar os pods. Um ou dois minutos depois, os pods adicionais estão em execução:

```azurecli-interactive
kubectl get pods
```

Saída:

```bash
NAME                                READY     STATUS    RESTARTS   AGE
azure-vote-back-2606967446-nmpcf    1/1       Running   0          15m
azure-vote-front-3309479140-2hfh0   1/1       Running   0          3m
azure-vote-front-3309479140-bzt05   1/1       Running   0          3m
azure-vote-front-3309479140-fvcvm   1/1       Running   0          3m
azure-vote-front-3309479140-hrbf2   1/1       Running   0          15m
azure-vote-front-3309479140-qphz8   1/1       Running   0          3m
```

## <a name="autoscale-pods"></a>Dimensionar pods automaticamente

O Kubernetes suporta [dimensionamento automático horizontal de pods](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) para ajustar o número de pods numa implementação, consoante a utilização da CPU ou de outras métricas selecionadas. 

Para utilizar o dimensionamento automático, os seus pods têm de ter os pedidos de CPU e os limites definidos. Na implementação `azure-vote-front`, o contentor de front-end pede 0,25 de CPU, com um limite de 0,5 de CPU. As definições têm o seguinte aspeto:

```YAML
resources:
  requests:
     cpu: 250m
  limits:
     cpu: 500m
```

O exemplo seguinte utiliza o comando [kubectl autoscale](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#autoscale) para dimensionar automaticamente o número de pods na implementação `azure-vote-front`. Aqui, se a utilização da CPU exceder 50%, o dimensionamento automático aumenta os pods para um máximo de 10.


```azurecli-interactive
kubectl autoscale deployment azure-vote-front --cpu-percent=50 --min=3 --max=10
```

Para ver o estado do dimensionamento automático, execute o seguinte comando:

```azurecli-interactive
kubectl get hpa
```

Saída:

```bash
NAME               REFERENCE                     TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
azure-vote-front   Deployment/azure-vote-front   0% / 50%   3         10        3          2m
```

Após alguns minutos, com carga mínima na aplicação do Azure Vote, o número de réplicas de pod diminui automaticamente para 3.

## <a name="scale-the-agents"></a>Dimensionar os agentes

Se criou o seu cluster Kubernetes com comandos predefinidos no tutorial anterior, tem três nós de agente. Pode ajustar o número de agentes manualmente, se pretender mais ou menos cargas de trabalho do contentor no seu cluster. Utilize o comando [az acs scale](/cli/azure/acs#az-acs-scale) e especifique o número de agentes com o parâmetro `--new-agent-count`.

O exemplo seguinte aumenta o número de nós de agente para 4 no cluster do Kubernetes denominado *myK8sCluster*. O comando demora poucos minutos a concluir.

```azurecli-interactive
az acs scale --resource-group=myResourceGroup --name=myK8SCluster --new-agent-count 4
```

A saída do comando mostra o número de nós de agente no valor de `agentPoolProfiles:count`:

```azurecli
{
  "agentPoolProfiles": [
    {
      "count": 4,
      "dnsPrefix": "myK8SCluster-myK8SCluster-e44f25-k8s-agents",
      "fqdn": "",
      "name": "agentpools",
      "vmSize": "Standard_D2_v2"
    }
  ],
...

```

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, utilizou diferentes funcionalidades de dimensionamento no seu cluster Kubernetes. Tarefas abrangidas incluídas:

> [!div class="checklist"]
> * Dimensionar manualmente pods do Kubernetes
> * Configurar pods de Dimensionamento automático com o front-end da aplicação
> * Dimensionar os nós de agente do Azure do Kubernetes

Avance para o próximo tutorial para saber mais sobre a atualização da aplicação no Kubernetes.

> [!div class="nextstepaction"]
> [Atualizar uma aplicação no Kubernetes](./container-service-tutorial-kubernetes-app-update.md)

