---
title: Melhores práticas para funcionalidades de programador
titleSuffix: Azure Kubernetes Service
description: Aprenda as melhores práticas do operador do cluster para utilizar funcionalidades avançadas de programador, tais como manchas e tolerações, seletores de nó e afinidade, ou afinidade inter-pod e anti-afinidade no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: conceptual
ms.date: 03/09/2021
ms.openlocfilehash: 971916c3fc903ff5d69db2e0f82fd884acf807b3
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831587"
---
# <a name="best-practices-for-advanced-scheduler-features-in-azure-kubernetes-service-aks"></a>Melhores práticas para funcionalidades avançadas do agendador no Azure Kubernetes Service (AKS)

À medida que gere clusters no Serviço Azure Kubernetes (AKS), muitas vezes precisa isolar equipas e cargas de trabalho. Funcionalidades avançadas fornecidas pelo programador Kubernetes permitem-lhe controlar:
* Que cápsulas podem ser programadas em certos nós.
* Como as aplicações multi-pod podem ser adequadamente distribuídas por todo o cluster. 

Este artigo de boas práticas centra-se nas funcionalidades avançadas de agendamento de Kubernetes para os operadores de cluster. Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Use manchas e tolerâncias para limitar que cápsulas podem ser programadas em nós.
> * Dê preferência aos pods para correr em certos nós com seletores de nó ou afinidade de nó.
> * Divida ou agrupe as cápsulas com afinidade inter-pod ou anti-afinidade.

## <a name="provide-dedicated-nodes-using-taints-and-tolerations"></a>Fornecer nódes dedicados usando manchas e tolerâncias

> **Orientação de boas práticas:** 
>
> Limite o acesso a aplicações intensivas de recursos, como controladores de entrada, a nós específicos. Mantenha os recursos de nó disponíveis para cargas de trabalho que os exijam, e não permita agendamento de outras cargas de trabalho nos nós.

Quando criar o seu cluster AKS, pode implantar nós com suporte GPU ou um grande número de CPUs poderosos. Pode utilizar estes nós para grandes cargas de trabalho de processamento de dados, tais como machine learning (ML) ou inteligência artificial (IA). 

Uma vez que este hardware de recursos de nó é tipicamente caro de ser implementado, limite as cargas de trabalho que podem ser programadas nestes nós. Em vez disso, dedicaria alguns nós no cluster para executar serviços de entrada e prevenir outras cargas de trabalho.

Este suporte para diferentes nós é fornecido usando várias piscinas de nós. Um cluster AKS fornece uma ou mais piscinas de nós.

O programador Kubernetes usa manchas e tolerâncias para restringir que cargas de trabalho podem funcionar em nós.

* Aplique uma **mancha** num nó para indicar que apenas podem ser programadas cápsulas específicas.
* Em seguida, aplique uma **tolerância** a uma vagem, permitindo-lhes *tolerar* a mancha de nó.

Quando coloca uma cápsula num cluster AKS, a Kubernetes apenas programa cápsulas em nós cuja mancha se alinha com a tolerância. Por exemplo, assuma que adicionou uma piscina de nó no seu cluster AKS para nós com suporte gpu. Define-se o nome, como *o GPU,* e depois um valor para o agendamento. Definir este valor para *NoSchedule* restringe o programador Kubernetes de agendar cápsulas com tolerância indefinida no nó.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name taintnp \
    --node-taints sku=gpu:NoSchedule \
    --no-wait
```

Com uma mancha aplicada aos nós na piscina do nó, você definirá uma tolerância na especificação do casulo que permite agendar os nós. O exemplo a seguir define a `sku: gpu` mancha aplicada à piscina de nó no passo `effect: NoSchedule` anterior:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: mcr.microsoft.com/azuredocs/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
  tolerations:
  - key: "sku"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
```

Quando esta cápsula é implantada `kubectl apply -f gpu-toleration.yaml` utilizando, a Kubernetes pode agendar com sucesso a cápsula nos nós com a mancha aplicada. Este isolamento lógico permite-lhe controlar o acesso aos recursos dentro de um cluster.

Quando aplicar manchas, trabalhe com os desenvolvedores e proprietários da sua aplicação para permitir que definam as tolerâncias necessárias nas suas implementações.

Para obter mais informações sobre como usar várias piscinas de nó em AKS, consulte [Criar e gerir várias piscinas de nó para um cluster em AKS.][use-multiple-node-pools]

### <a name="behavior-of-taints-and-tolerations-in-aks"></a>Comportamento de manchas e tolerâncias na AKS

Quando atualiza uma piscina de nó em AKS, as manchas e as tolerâncias seguem um padrão definido à medida que são aplicadas a novos nóleiros:

#### <a name="default-clusters-that-use-vm-scale-sets"></a>Clusters predefinidos que usam conjuntos de escala VM
Você pode [manchar uma piscina][taint-node-pool] de nó da AKS API para ter os nós recém-dimensionados receber manchas de nó especificados API.

Vamos supor:
1. Começa-se com um aglomerado de dois nós: *nó1* e *nó2*. 
1. Você melhora a piscina de nós.
1. São criados dois nós adicionais: *nó3* e *nó4*. 
1. As manchas são transmitidas respectivamente.
1. O *nó 1* e *o nó 2* originais são apagados.

#### <a name="clusters-without-vm-scale-set-support"></a>Clusters sem suporte de conjunto de escala VM

Mais uma vez, vamos supor:
1. Você tem um cluster de dois nós: *nó1* e *nó2*. 
1. Você atualiza então a piscina de nó.
1. Um nó adicional é criado: *nó3*.
1. As manchas do *nó1* são aplicadas ao *nó3*.
1. *o nó1* é apagado.
1. Um novo *nó1* é criado para substituir o *nó original1*.
1. As manchas *de nó2* são aplicadas ao novo *nó1*. 
1. *o nó2* é apagado.

Na *essência, o nó 1* torna-se *node3,* e *o nó2* torna-se o novo *nó1*.

Quando escala uma piscina de nó em AKS, manchas e tolerações não são transportados por design.

## <a name="control-pod-scheduling-using-node-selectors-and-affinity"></a>Agendamento do pod de controlo usando seletores de nó e afinidade

> **Orientação de melhor prática** 
> 
> Controle o agendamento de cápsulas nos nós utilizando seletores de nó, afinidade do nó ou afinidade entre pods. Estas definições permitem ao programador Kubernetes isolar logicamente cargas de trabalho, como por exemplo por hardware no nó.

Manchas e tolerâncias logicamente isolam os recursos com um corte duro. Se a cápsula não tolerar a mancha de um nó, não está programada no nó.

Em alternativa, pode utilizar os seletores de nó. Por exemplo, rotula os nós para indicar o armazenamento SSD ligado localmente ou uma grande quantidade de memória e, em seguida, define na especificação do pod um seletor de nó. Kubernetes programa as cápsulas num nó correspondente.

Ao contrário das tolerâncias, as cápsulas sem um seletor de nó correspondente ainda podem ser agendadas em nós rotulados. Este comportamento permite que os recursos não reutilizados nos nós consumam, mas prioriza as cápsulas que definem o seletor de nó correspondente.

Vejamos um exemplo de nós com uma grande quantidade de memória. Estes nós priorizam cápsulas que pedem uma grande quantidade de memória. Para garantir que os recursos não ficam parados, também permitem que outras cápsulas corram. O comando exemplo seguinte adiciona um conjunto de nós com o *hardware da etiqueta=highmem* ao *myAKSCluster* no *myResourceGroup*. Todos os nós naquela piscina de nós terão esta etiqueta.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name labelnp \
    --node-count 1 \
    --labels hardware=highmem \
    --no-wait
```

Uma especificação do pod adiciona então a `nodeSelector` propriedade para definir um seletor de nó que corresponda ao conjunto de etiquetas num nó:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: mcr.microsoft.com/azuredocs/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
  nodeSelector:
      hardware: highmem
```

Quando utilizar estas opções de programadores, trabalhe com os desenvolvedores e proprietários da sua aplicação para permitir que definam corretamente as suas especificações de pod.

Para obter mais informações sobre a utilização de seletores de nó, consulte [atribuindo pods a nós][k8s-node-selector].

### <a name="node-affinity"></a>Afinidade do nó

Um seletor de nó é uma solução básica para atribuir cápsulas a um dado nó. *A afinidade* do nó proporciona mais flexibilidade, permitindo-lhe definir o que acontece se a vagem não puder ser acompanhada por um nó. Pode: 
* *Exija* que o programador Kubernetes corresponda a uma cápsula com um hospedeiro rotulado. Ou,
* *Prefere* um jogo, mas deixe que o pod seja agendado num anfitrião diferente se não houver correspondência disponível.

O exemplo a seguir define a afinidade do nó para *o necessárioDuringSchedulingIgnoredDuringExecution*. Esta afinidade requer que o calendário de Kubernetes utilize um nó com uma etiqueta correspondente. Se não houver nó disponível, a cápsula tem de esperar que o agendamento continue. Para permitir que a cápsula seja programada num nó diferente, pode, em vez disso, definir o valor para ***preferida** durante a continuação de InschedulingIgnoreDursingExecution*:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: mcr.microsoft.com/azuredocs/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: hardware
            operator: In
            values: highmem
```

A parte *deexecução ignorada* indica que a cápsula não deve ser despejada do nó se as etiquetas do nó mudarem. O programador Kubernetes apenas utiliza as etiquetas de nó atualizados para novas cápsulas que estão a ser programadas, e não cápsulas já programadas nos nós.

Para mais informações, consulte [Affinity e anti-afinidade.][k8s-affinity]

### <a name="inter-pod-affinity-and-anti-affinity"></a>Afinidade inter-pod e anti-afinidade

Uma abordagem final para o programador Kubernetes isolar logicamente cargas de trabalho é usar afinidade inter-pod ou anti-afinidade. Estas definições definem que as cápsulas *não devem* ou *devem* ser programadas num nó que tem uma cápsula de correspondência existente. Por padrão, o programador Kubernetes tenta agendar várias cápsulas numa réplica definida através dos nós. Pode definir regras mais específicas em torno deste comportamento.

Por exemplo, tem uma aplicação web que também usa um Cache Azure para Redis. 
1. Usa regras anti-afinidade para solicitar que o programador Kubernetes distribua réplicas através de nós. 
1. Utiliza regras de afinidade para garantir que cada componente de aplicações web está programado no mesmo anfitrião que uma cache correspondente. 

A distribuição de cápsulas através de nóleiros parece o seguinte exemplo:

| **Nó 1** | **Nó 2** | **Nó 3** |
|------------|------------|------------|
| webapp-1   | webapp-2   | webapp-3   |
| cache-1    | cache-2    | cache-3    |

A afinidade inter-pod e a anti-afinidade proporcionam uma implementação mais complexa do que os seletores de nó ou afinidade do nó. Com a implantação, você logicamente isola recursos e controla como Kubernetes agenda cápsulas em nós. 

Para um exemplo completo desta aplicação web com Azure Cache para exemplo Redis, consulte [cápsulas de Co-localização no mesmo nó][k8s-pod-affinity].

## <a name="next-steps"></a>Passos seguintes

Este artigo focou-se nas funcionalidades avançadas do programador Kubernetes. Para obter mais informações sobre as operações de cluster em AKS, consulte as seguintes boas práticas:

* [Isolamento multi-inquilinos e de clusters][aks-best-practices-scheduler]
* [Funcionalidades básicas do programador Kubernetes][aks-best-practices-scheduler]
* [Autenticação e autorização][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->
[k8s-taints-tolerations]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[k8s-node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[k8s-affinity]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#affinity-and-anti-affinity
[k8s-pod-affinity]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#always-co-located-in-the-same-node

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[aks-best-practices-identity]: operator-best-practices-identity.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[taint-node-pool]: use-multiple-node-pools.md#specify-a-taint-label-or-tag-for-a-node-pool
