---
title: Práticas recomendadas do operador-recursos do Agendador avançado nos serviços Kubernetess do Azure (AKS)
description: Conheça as práticas recomendadas do operador de cluster para usar recursos avançados do Agendador, como os tolerationss, seletores de nó e afinidade ou afinidade entre pod e antiafinidade no serviço de kubernetes do Azure (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: mlearned
ms.openlocfilehash: 798c368edb4a738124fce965f8990e6805fbdeba
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73472609"
---
# <a name="best-practices-for-advanced-scheduler-features-in-azure-kubernetes-service-aks"></a>Práticas recomendadas para recursos avançados do Agendador no serviço de kubernetes do Azure (AKS)

Ao gerenciar clusters no AKS (serviço kubernetes do Azure), muitas vezes você precisa isolar equipes e cargas de trabalho. O Agendador kubernetes fornece recursos avançados que permitem controlar quais pods podem ser agendados em determinados nós ou como os aplicativos multipod podem ser distribuídos adequadamente pelo cluster. 

Este artigo de práticas recomendadas se concentra em recursos avançados de agendamento de kubernetes para operadores de cluster. Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Use os conteúdo e os Tolerations para limitar o que os pods podem ser agendados nos nós
> * Dar preferência ao pods para executar em determinados nós com seletores de nó ou afinidade de nó
> * Separar ou agrupar os pods com afinidade entre Pod ou antiafinidade

## <a name="provide-dedicated-nodes-using-taints-and-tolerations"></a>Fornecer nós dedicados usando os e Tolerations

**Diretrizes de práticas recomendadas** -limite o acesso para aplicativos com uso intensivo de recursos, como controladores de entrada, para nós específicos. Mantenha os recursos de nó disponíveis para cargas de trabalho que os exigem e não permita o agendamento de outras cargas de trabalho nos nós.

Ao criar o cluster AKS, você pode implantar nós com suporte de GPU ou um grande número de CPUs poderosas. Esses nós são frequentemente usados para cargas de trabalho de processamento de dados grandes, como o aprendizado de máquina (ML) ou a inteligência artificial (ia). Como esse tipo de hardware é normalmente um recurso de nó caro para implantar, limite as cargas de trabalho que podem ser agendadas nesses nós. Em vez disso, talvez você queira dedicar alguns nós no cluster para executar serviços de entrada e evitar outras cargas de trabalho.

Esse suporte para nós diferentes é fornecido usando vários pools de nó. Um cluster AKS fornece um ou mais pools de nós.

O Agendador kubernetes pode usar os conteúdo e os Tolerations para restringir quais cargas de trabalho podem ser executadas em nós.

* Um seu **pods é aplicado** a um nó que indica que apenas um inconsistente específico pode ser agendado neles.
* Em seguida, um **toleration** é aplicado a um pod que permite *tolerar* o de um nó.

Quando você implanta um pod em um cluster AKS, o kubernetes só agenda pods em nós em que um toleration é alinhado com o o seu erro. Como exemplo, suponha que você tenha um pool de nós no cluster AKS para nós com suporte a GPU. Você define o nome, como *GPU*, e um valor para agendamento. Se você definir esse valor como *NoSchedule*, o Agendador kubernetes não poderá agendar pods no nó se o Pod não definir o toleration apropriado.

```console
kubectl taint node aks-nodepool1 sku=gpu:NoSchedule
```

Com um toleration aplicado a nós, você definirá um submeio na especificação Pod que permite o agendamento nos nós. O exemplo a seguir define o `sku: gpu` e o `effect: NoSchedule` para tolerar o o o seu que é aplicado ao nó na etapa anterior:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: microsoft/samples-tf-mnist-demo:gpu
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

Quando esse pod é implantado, como usar `kubectl apply -f gpu-toleration.yaml`, kubernetes pode agendar com êxito o pod nos nós com o seu Esse isolamento lógico permite controlar o acesso a recursos em um cluster.

Quando você aplica os seus aplicativos, trabalhe com os desenvolvedores e os proprietários de seu aplicativo para permitir que eles definam o Tolerations necessário em suas implantações.

Para obter mais informações sobre o Tolerations e o Tolerations, consulte [aplicando][k8s-taints-tolerations]os seus

Para obter mais informações sobre como usar vários pools de nós no AKS, consulte [criar e gerenciar vários pools de nós para um cluster no AKs][use-multiple-node-pools].

### <a name="behavior-of-taints-and-tolerations-in-aks"></a>Comportamento de e Tolerations no AKs

Quando você atualiza um pool de nós em AKS, os tolerationss seguem um padrão definido à medida que são aplicados a novos nós:

- **Clusters padrão que usam conjuntos de dimensionamento de máquinas virtuais**
  - Vamos supor que você tenha um cluster de dois nós- *Node1* e *NODE2*. Você atualiza o pool de nós.
  - Dois nós adicionais são criados, *Node3* e *Nó4*, e os são passados em respectivamente.
  - Os *Node1* e *NODE2* originais são excluídos.

- **Clusters sem suporte ao conjunto de dimensionamento de máquinas virtuais**
  - Novamente, vamos supor que você tenha um cluster de dois nós- *Node1* e *NODE2*. Quando você atualiza, um nó adicional (*Node3*) é criado.
  - Os de *Node1* são aplicados ao *Node3*, então *Node1* é excluído.
  - Outro novo nó é criado (chamado *Node1*, uma vez que o *Node1* anterior foi excluído) e *os* os seus Em seguida, *NODE2* é excluído.
  - Em essência, *Node1* se torna *Node3*e *NODE2* se torna *Node1*.

Quando você dimensiona um pool de nós em AKS, os projetos e Tolerations não são transferidos por design.

## <a name="control-pod-scheduling-using-node-selectors-and-affinity"></a>Controlar o agendamento de Pod usando seletores de nó e afinidade

**Diretrizes de práticas recomendadas** -controle o agendamento de pods em nós usando seletores de nó, afinidade de nó ou afinidade entre Pod. Essas configurações permitem que o Agendador de kubernetes Isole as cargas de trabalho logicamente, como por hardware no nó.

Os resíduos e os Tolerations são usados para isolar logicamente os recursos com um desligamento fixo. se o Pod não tolerar um de nó, ele não estará agendado no nó. Uma abordagem alternativa é usar seletores de nó. Você rotula nós, como para indicar o armazenamento SSD localmente anexado ou uma grande quantidade de memória e, em seguida, define na especificação de pod um seletor de nó. Kubernetes, em seguida, agenda esses pods em um nó correspondente. Ao contrário de Tolerations, pods sem um seletor de nó correspondente pode ser agendado em nós rotulados. Esse comportamento permite que recursos não utilizados nos nós consumam, mas dá prioridade a pods que definem o seletor de nó correspondente.

Vejamos um exemplo de nós com uma grande quantidade de memória. Esses nós podem dar preferência a pods que solicitam uma grande quantidade de memória. Para certificar-se de que os recursos não ficam ociosos, eles também permitem que outros pods sejam executados.

```console
kubectl label node aks-nodepool1 hardware:highmem
```

Em seguida, uma especificação Pod adiciona a propriedade `nodeSelector` para definir um seletor de nó que corresponde ao conjunto de rótulos em um nó:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: microsoft/samples-tf-mnist-demo:gpu
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

Quando você usa essas opções de Agendador, trabalhe com os desenvolvedores e os proprietários de seu aplicativo para permitir que eles definam corretamente suas especificações de Pod.

Para obter mais informações sobre como usar seletores de nó, consulte [atribuindo pods a nós][k8s-node-selector].

### <a name="node-affinity"></a>Afinidade de nó

Um seletor de nó é uma maneira básica de atribuir pods a um determinado nó. Mais flexibilidade está disponível usando a *afinidade de nó*. Com a afinidade de nó, você define o que acontece se o Pod não puder ser correspondido com um nó. Você pode *exigir* que o Agendador do kubernetes corresponda a um pod com um host rotulado. Ou você pode *preferir* uma correspondência, mas permitir que o Pod seja agendado em um host diferente se a correspondência não estiver disponível.

O exemplo a seguir define a afinidade de nó como *requiredDuringSchedulingIgnoredDuringExecution*. Essa afinidade requer que a agenda kubernetes use um nó com um rótulo correspondente. Se nenhum nó estiver disponível, o Pod precisará aguardar até que o agendamento continue. Para permitir que o Pod seja agendado em um nó diferente, você pode definir o valor como *preferredDuringScheduledIgnoreDuringExecution*:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: microsoft/samples-tf-mnist-demo:gpu
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

A parte *IgnoredDuringExecution* da configuração indica que, se os rótulos de nó forem alterados, o Pod não deverá ser removido do nó. O Agendador kubernetes usa somente os rótulos de nó atualizados para que novos pods sejam agendados, e não os pods já agendados nos nós.

Para obter mais informações, consulte [afinidade e antiafinidade][k8s-affinity].

### <a name="inter-pod-affinity-and-anti-affinity"></a>Afinidade entre pod e antiafinidade

Uma abordagem final para o Agendador de kubernetes isolar logicamente as cargas de trabalho está usando afinidade ou antiafinidade entre Pod. As configurações definem que pods não *deve* ser agendado em um nó que tenha um pod correspondente existente ou que eles *devam* ser agendados. Por padrão, o Agendador kubernetes tenta agendar vários pods em um conjunto de réplicas entre nós. Você pode definir regras mais específicas sobre esse comportamento.

Um bom exemplo é um aplicativo Web que também usa um cache do Azure para Redis. Você pode usar regras de antiafinidade de pod para solicitar que o Agendador de kubernetes distribua réplicas entre nós. Você pode usar as regras de afinidade para garantir que cada componente de aplicativo Web esteja agendado no mesmo host que um cache correspondente. A distribuição de pods entre nós é semelhante ao exemplo a seguir:

| **Nó 1** | **Nó 2** | **Nó 3** |
|------------|------------|------------|
| webapp-1   | webapp-2   | webapp-3   |
| cache-1    | cache-2    | cache-3    |

Este exemplo é uma implantação mais complexa do que o uso de seletores de nó ou afinidade de nó. A implantação lhe dá controle sobre como o kubernetes agenda pods em nós e pode isolar os recursos logicamente. Para obter um exemplo completo deste aplicativo Web com o cache do Azure para Redis exemplo, consulte [co-localizar pods no mesmo nó][k8s-pod-affinity].

## <a name="next-steps"></a>Passos seguintes

Este artigo se concentrou nos recursos avançados do Agendador de kubernetes. Para obter mais informações sobre as operações de cluster no AKS, consulte as seguintes práticas recomendadas:

* [Multilocação e isolamento de cluster][aks-best-practices-scheduler]
* [Recursos básicos do Agendador do kubernetes][aks-best-practices-scheduler]
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
