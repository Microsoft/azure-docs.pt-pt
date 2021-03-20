---
title: Melhores práticas para funcionalidades de programador
titleSuffix: Azure Kubernetes Service
description: Aprenda as melhores práticas do operador do cluster para utilizar funcionalidades avançadas de programador, tais como manchas e tolerações, seletores de nó e afinidade, ou afinidade inter-pod e anti-afinidade no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: 1a8138b4b2fdab2cdef8d2cb4c27de8d12ef38cd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97107351"
---
# <a name="best-practices-for-advanced-scheduler-features-in-azure-kubernetes-service-aks"></a>Melhores práticas para funcionalidades avançadas do agendador no Azure Kubernetes Service (AKS)

À medida que gere clusters no Serviço Azure Kubernetes (AKS), muitas vezes precisa isolar equipas e cargas de trabalho. O programador Kubernetes fornece funcionalidades avançadas que permitem controlar quais cápsulas podem ser programadas em determinados nós, ou como as aplicações multi-pod podem ser adequadamente distribuídas por todo o cluster. 

Este artigo de boas práticas centra-se nas funcionalidades avançadas de agendamento de Kubernetes para os operadores de cluster. Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Use manchas e tolerâncias para limitar que cápsulas podem ser programadas em nos acenos
> * Dê preferência aos pods para correr em certos nos acenos com seletores de nó ou afinidade do nó
> * Separados ou grupos em conjunto com afinidade inter-pod ou anti-afinidade

## <a name="provide-dedicated-nodes-using-taints-and-tolerations"></a>Fornecer nódes dedicados usando manchas e tolerâncias

**Orientação para as melhores práticas** - Limitar o acesso a aplicações intensivas de recursos, como controladores de entrada, a nós específicos. Mantenha os recursos de nó disponíveis para cargas de trabalho que os exijam, e não permita agendamento de outras cargas de trabalho nos nós.

Quando criar o seu cluster AKS, pode implantar nós com suporte GPU ou um grande número de CPUs poderosos. Estes nós são frequentemente usados para grandes cargas de trabalho de processamento de dados, tais como machine learning (ML) ou inteligência artificial (IA). Como este tipo de hardware é tipicamente um recurso de nó caro para implementar, limite as cargas de trabalho que podem ser programadas nestes nós. Em vez disso, pode querer dedicar alguns nós no cluster para executar serviços de entrada e prevenir outras cargas de trabalho.

Este suporte para diferentes nós é fornecido usando várias piscinas de nós. Um cluster AKS fornece uma ou mais piscinas de nós.

O programador Kubernetes pode usar manchas e tolerâncias para restringir que cargas de trabalho podem funcionar em nós.

* Uma **mancha** é aplicada a um nó que indica que apenas podem ser programadas cápsulas específicas neles.
* Uma **tolerância** é então aplicada a uma vagem que lhes permite *tolerar* a mancha de um nó.

Quando coloca uma cápsula num cluster AKS, a Kubernetes apenas programa cápsulas em nós onde uma tolerância está alinhada com a mancha. Como exemplo, assuma que tem uma piscina de nó no seu cluster AKS para nós com suporte GPU. Define-se o nome, como *o GPU,* e depois um valor para o agendamento. Se definir este valor para *NoSchedule,* o programador Kubernetes não pode agendar cápsulas no nó se a cápsula não definir a tolerância apropriada.

```console
kubectl taint node aks-nodepool1 sku=gpu:NoSchedule
```

Com uma mancha aplicada aos nós, define-se então uma tolerância na especificação da vagem que permite agendar os nós. O exemplo a seguir define e `sku: gpu` tolerar a mancha aplicada ao nó no passo `effect: NoSchedule` anterior:

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

Quando esta cápsula é implantada, tal como a `kubectl apply -f gpu-toleration.yaml` utilização, a Kubernetes pode agendar com sucesso a cápsula nos nós com a mancha aplicada. Este isolamento lógico permite-lhe controlar o acesso aos recursos dentro de um cluster.

Quando aplicar manchas, trabalhe com os desenvolvedores e proprietários da sua aplicação para permitir que definam as tolerâncias necessárias nas suas implementações.

Para obter mais informações sobre como usar várias piscinas de nó em AKS, consulte [Criar e gerir várias piscinas de nó para um cluster em AKS.][use-multiple-node-pools]

### <a name="behavior-of-taints-and-tolerations-in-aks"></a>Comportamento de manchas e tolerâncias na AKS

Quando atualiza uma piscina de nó em AKS, as manchas e as tolerâncias seguem um padrão definido à medida que são aplicadas a novos nóleiros:

- **Clusters predefinidos que usam conjuntos de escala de máquina virtual**
  - Você pode [manchar um nodepool][taint-node-pool] da AKS API, para ter os nós recém-dimensionados receber manchas de nó especificados API.
  - Vamos supor que tem um aglomerado de dois nós - *nó1* e *nó2*. Você melhora a piscina de nós.
  - São criados dois nós adicionais, *nó3* e *nó4,* e as manchas são transmitidas respectivamente.
  - O *nó 1* e *o nó 2* originais são apagados.

- **Clusters sem suporte de conjunto de escala de máquina virtual**
  - Mais uma vez, vamos supor que você tem um aglomerado de dois nós - *nó1* e *nó2*. Ao atualizar, é criado um nó adicional *(nó3).*
  - As manchas do *nó1* são aplicadas ao *nó3,* em *seguida, o nó1* é então apagado.
  - Outro novo nó é criado (denominado *node1*, uma vez que o *nó anterior foi* eliminado), e os *nó2* taints são aplicados ao novo *nó1*. Em seguida, *o nó2* é apagado.
  - Na *essência, o nó 1* torna-se *node3,* e *o nó2* torna-se *node1*.

Quando escala uma piscina de nó em AKS, manchas e tolerações não são transportados por design.

## <a name="control-pod-scheduling-using-node-selectors-and-affinity"></a>Agendamento do pod de controlo usando seletores de nó e afinidade

**Orientação para as melhores práticas** - Controle o agendamento de cápsulas nos nós utilizando seletores de nó, afinidade do nó ou afinidade entre pods. Estas definições permitem ao programador Kubernetes isolar logicamente cargas de trabalho, como por exemplo por hardware no nó.

Manchas e tolerâncias são usadas para isolar logicamente os recursos com um corte duro - se a cápsula não tolerar a mancha de um nó, não está programada no nó. Uma abordagem alternativa é usar os seletores de nó. Rotula os nós, tais como indicar o armazenamento SSD ligado localmente ou uma grande quantidade de memória, e, em seguida, define na especificação do pod um seletor de nó. Kubernetes então agenda as cápsulas em um nó correspondente. Ao contrário das tolerâncias, as cápsulas sem um seletor de nó correspondente podem ser agendadas em nós rotulados. Este comportamento permite que os recursos não reutilizados nos nós consumam, mas dá prioridade aos pods que definem o seletor de nó correspondente.

Vejamos um exemplo de nós com uma grande quantidade de memória. Estes nós podem dar preferência a cápsulas que solicitam uma elevada quantidade de memória. Para garantir que os recursos não ficam parados, também permitem que outras cápsulas corram.

```console
kubectl label node aks-nodepool1 hardware=highmem
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

Um seletor de nó é uma forma básica de atribuir cápsulas a um dado nó. Mais flexibilidade está disponível usando *afinidade do nó.* Com a afinidade do nó, você define o que acontece se a vagem não pode ser combinado com um nó. Você pode *exigir* que o programador Kubernetes corresponda a um casulo com um anfitrião rotulado. Ou, você pode *preferir* um jogo, mas permitir que o pod seja agendado em um anfitrião diferente se não houver correspondência disponível.

O exemplo a seguir define a afinidade do nó para *o necessárioDuringSchedulingIgnoredDuringExecution*. Esta afinidade requer que o calendário de Kubernetes utilize um nó com uma etiqueta correspondente. Se não houver nó disponível, a cápsula tem de esperar que o agendamento continue. Para permitir que a cápsula seja programada num nó diferente, pode, em vez disso, definir o valor para *o preferidaDingSchedulingIgnoreDuringExecution*:

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

A parte *deexecução ignorada* indica que se as etiquetas do nó mudarem, a cápsula não deve ser despejada do nó. O programador Kubernetes apenas utiliza as etiquetas de nó atualizados para novas cápsulas que estão a ser programadas, e não cápsulas já programadas nos nós.

Para mais informações, consulte [Affinity e anti-afinidade.][k8s-affinity]

### <a name="inter-pod-affinity-and-anti-affinity"></a>Afinidade inter-pod e anti-afinidade

Uma abordagem final para o programador Kubernetes isolar logicamente cargas de trabalho é usar afinidade inter-pod ou anti-afinidade. As definições definem que as cápsulas *não devem* ser programadas num nó que tenha uma cápsula de correspondência existente, ou que *devem* ser programadas. Por padrão, o programador Kubernetes tenta agendar várias cápsulas numa réplica definida através dos nós. Pode definir regras mais específicas em torno deste comportamento.

Um bom exemplo é uma aplicação web que também usa um Cache Azure para Redis. Pode utilizar regras anti-afinidade do pod para solicitar que o programador Kubernetes distribua réplicas através de nós. Em seguida, pode utilizar regras de afinidade para garantir que cada componente de aplicações web é programado no mesmo anfitrião que uma cache correspondente. A distribuição de cápsulas através de nóleiros parece o seguinte exemplo:

| **Nó 1** | **Nó 2** | **Nó 3** |
|------------|------------|------------|
| webapp-1   | webapp-2   | webapp-3   |
| cache-1    | cache-2    | cache-3    |

Este exemplo é uma implementação mais complexa do que a utilização de seletores de nó ou afinidade de nó. A implementação dá-lhe controlo sobre como Kubernetes programa cápsulas em nós e pode logicamente isolar recursos. Para um exemplo completo desta aplicação web com Azure Cache para exemplo Redis, consulte [cápsulas de Co-localização no mesmo nó][k8s-pod-affinity].

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
