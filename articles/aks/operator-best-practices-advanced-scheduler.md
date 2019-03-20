---
title: Práticas recomendadas do operador – serviços de Kubernetes no Azure (AKS) do scheduler funcionalidades avançadas
description: Conheça as práticas recomendadas de operador de cluster para a utilização de funcionalidades de agendador avançadas, como taints e tolerations, seletores de nó e afinidade, ou entre pod afinidade e antiafinidade no Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: iainfou
ms.openlocfilehash: 27c9c872f4dfb82b4a1389189d62c4e1f06ee272
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58175986"
---
# <a name="best-practices-for-advanced-scheduler-features-in-azure-kubernetes-service-aks"></a>Práticas recomendadas para recursos do agendador avançadas no Azure Kubernetes Service (AKS)

Como gerir clusters no Azure Kubernetes Service (AKS), precisa, muitas vezes isolar cargas de trabalho e as equipes. O agendador de Kubernetes fornece recursos avançados que lhe permitam que controlar quais pods podem ser agendadas em determinados nós ou, como o pod vários aplicativos podem adequadamente distribuídas pelo cluster. 

Este artigo de melhores práticas que se concentra em Kubernetes avançadas funcionalidades de agendamento para operadores de cluster. Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Utilize taints e tolerations para limitar os pods podem ser agendadas em nós
> * Dar preferência ao pods para executar em determinados nós com seletores de nó ou uma afinidade de nó
> * Dividir a distância ou de grupo pods em conjunto com afinidade de pod entre ou antiafinidade

## <a name="provide-dedicated-nodes-using-taints-and-tolerations"></a>Fornecer nós dedicados usando taints e tolerations

**Melhores diretrizes de práticas** -limitar o acesso para aplicações com muitos recursos, tais como controladores de entrada, a nós específicos. Manter os recursos de nó disponíveis para cargas de trabalho que exigem-los e não permitir o agendamento de outras cargas de trabalho em nós.

Ao criar o cluster do AKS, pode implementar nós com suporte GPU ou um grande número de CPUs poderosas. Estes nós são frequentemente utilizados para cargas de trabalho de processamento de dados grandes, como a aprendizagem automática (ML) ou de inteligência artificial (IA). Como este tipo de hardware, normalmente, é um recurso de nó Caro para implementar, limite as cargas de trabalho que podem ser agendadas em nós. Em vez disso, pode pretender dedicar alguns nós do cluster para executar serviços de entrada e impedir que outras cargas de trabalho.

O agendador de Kubernetes pode utilizar taints e tolerations para restringir o que podem executar cargas de trabalho em nós.

* R **taint** é aplicada a um nó que indica que apenas os pods específicos podem ser agendados nos mesmos.
* R **toleration** , em seguida, é aplicado a um pod que lhes permita *tolerar* taint de um nó.

Quando implementar um pod num cluster do AKS, o Kubernetes agenda apenas pods em nós em que um toleration está alinhado com o taint. Por exemplo, partem do princípio de que tem um nodepool no cluster do AKS para nós com GPU com suporte. Definir o nome, tal como *gpu*, em seguida, um valor para o agendamento. Se definir este valor como *NoSchedule*, o agendador de Kubernetes não é possível agendar pods no nó, se o pod não define o toleration apropriado.

```console
kubectl taint node aks-nodepool1 sku=gpu:NoSchedule
```

Com um taint aplicada a nós,, em seguida, define um toleration na especificação pod que permite o agendamento em nós. O exemplo seguinte define a `sku: gpu` e `effect: NoSchedule` tolerar taint aplicado ao nó no passo anterior:

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

Quando este pod for implementada, como o uso de `kubectl apply -f gpu-toleration.yaml`, Kubernetes com êxito pode agendar o pod em nós com taint aplicada. Esse isolamento lógico permite-lhe controlar o acesso aos recursos dentro de um cluster.

Quando aplica taints, trabalhe com os seus desenvolvedores de aplicativos e os proprietários para lhes permitir para definir os tolerations necessários nas suas implementações.

Para obter mais informações sobre taints e tolerations, consulte [aplicar taints e tolerations][k8s-taints-tolerations].

## <a name="control-pod-scheduling-using-node-selectors-and-affinity"></a>Usando os seletores de nó e afinidade de agendamento de pod de controlo

**Melhores diretrizes de práticas** - controlar o agendamento de pods em nós usando os seletores de nó, afinidade de nó, ou entre pod afinidade. Estas definições permitem que o agendador de Kubernetes para isolar logicamente as cargas de trabalho, como por hardware no nó.

Taints e tolerations são utilizados para isolar logicamente recursos com um disco rígido truncado - se o pod não tolerar taint de um nó, que não é agendada no nó. Uma abordagem alternativa é usar seletores de nó. Etiqueta nós, por exemplo, para indicar o armazenamento SSD ligado localmente ou uma grande quantidade de memória e, em seguida, definir na especificação pod um Seletor de nó. Kubernetes agenda, em seguida, os pods num nó correspondente. Ao contrário de tolerations, pods sem um Seletor de nó correspondente podem ser agendadas em nós etiquetadas. Este comportamento permite que recursos não utilizados em nós para consumir, mas dá prioridade à pods que definem o Seletor de nó correspondente.

Vejamos um exemplo de nós com uma elevada quantidade de memória. Estes nós podem dar preferência ao pods que pedem uma elevada quantidade de memória. Certificar-se de que os recursos não ficam inativo, eles também permitem que outros pods executar.

```console
kubectl label node aks-nodepool1 hardware:highmem
```

Uma especificação de pod, em seguida, adiciona o `nodeSelector` definida para definir um Seletor de nó que corresponder ao rótulo num nó:

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

Ao utilizar estas opções de programador, trabalhe com os seus desenvolvedores de aplicativos e os proprietários para lhes permitir de definir corretamente das especificações de pod.

Para obter mais informações sobre a utilização de seletores de nó, veja [Pods de atribuir a nós][k8s-node-selector].

### <a name="node-affinity"></a>Afinidade de nó

Um Seletor de nó é uma maneira básica de atribuir os pods para um determinado nó. Mais flexibilidade é disponíveis através de *afinidade de nó*. Com afinidade do nó, define o que acontece se o pod não coincido com um nó. Pode *requerem* Kubernetes scheduler corresponde a um pod com um host etiquetado. Também pode *prefira* uma correspondência, mas permita o pod agendadas num host diferente se não houver correspondência está disponível.

O exemplo seguinte define a afinidade do nó *requiredDuringSchedulingIgnoredDuringExecution*. Este afinidade requer o agendamento de Kubernetes a utilizar um nó com um rótulo correspondente. Se nenhum nó estiver disponível, o pod tem de esperar para o agendamento para continuar. Para permitir que o pod agendadas num nó diferente, em vez disso, pode definir o valor *preferredDuringScheduledIgnoreDuringExecution*:

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

O *IgnoredDuringExecution* parte da definição indica que, se o nó das etiquetas de alteração, o pod não deve ser expulsas do nó. O agendador de Kubernetes utiliza apenas as etiquetas de nó atualizado para novos pods agendados, não os pods já agendados em nós.

Para obter mais informações, consulte [afinidade e a antiafinidade][k8s-affinity].

### <a name="inter-pod-affinity-and-anti-affinity"></a>Afinidade de pod entre e antiafinidade

Uma abordagem final para o agendador de Kubernetes para isolar logicamente cargas de trabalho está a utilizar a afinidade de pod entre ou antiafinidade. As definições de definem esse pods *não deve* agendado num nó que tem um pod correspondente existente ou que eles *deve* ser agendada. Por predefinição, o agendador de Kubernetes tenta agendar vários pods numa réplica definida em todos os nós. Pode definir regras mais específicas sobre este comportamento.

Um bom exemplo é uma aplicação web que também utiliza uma Cache do Azure para Redis. Pode utilizar regras de antiafinidade pod para solicitar que o agendador de Kubernetes distribui réplicas em todos os nós. Em seguida, pode utilizar regras de afinidade para se certificar de que cada componente da aplicação web está agendada no mesmo anfitrião como uma cache correspondente. A distribuição de pods em todos os nós terá um aspeto semelhante ao seguinte exemplo:

| **Nó 1** | **Nó 2** | **Nó 3** |
|------------|------------|------------|
| webapp-1   | webapp-2   | webapp-3   |
| cache-1    | cache-2    | cache-3    |

Neste exemplo é uma implementação mais complexa que a utilização de seletores de nó ou de afinidade de nó. A oferece implantação controlo sobre como o Kubernetes agenda pods em nós e logicamente pode isolar os recursos. Para obter um exemplo completo desta aplicação web com Cache do Azure para o exemplo de Redis, consulte [Colocalizar pods no mesmo nó][k8s-pod-affinity].

## <a name="next-steps"></a>Passos Seguintes

Este artigo concentra-se nas funcionalidades avançadas de scheduler de Kubernetes. Para obter mais informações sobre as operações de cluster no AKS, consulte as seguintes práticas recomendadas:

* [Isolamento de vários inquilinos e de cluster][aks-best-practices-scheduler]
* [Funcionalidades de agendador básicas do Kubernetes][aks-best-practices-scheduler]
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
