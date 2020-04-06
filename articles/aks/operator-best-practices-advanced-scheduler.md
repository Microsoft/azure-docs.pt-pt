---
title: Boas práticas para funcionalidades de programadores
titleSuffix: Azure Kubernetes Service
description: Aprenda as melhores práticas do operador de cluster para utilizar funcionalidades avançadas de programadores, tais como manchas e tolerâncias, selecionadores de nós e afinidade, ou afinidade inter-pod e anti-afinidade no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: 47b2d78f7dc831c4314c4215f5e0a9e17f75f0dc
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668363"
---
# <a name="best-practices-for-advanced-scheduler-features-in-azure-kubernetes-service-aks"></a>Boas práticas para funcionalidades avançadas de programadores no Serviço Azure Kubernetes (AKS)

Como gere clusters no Serviço Azure Kubernetes (AKS), muitas vezes precisa de isolar equipas e cargas de trabalho. O programador Kubernetes fornece funcionalidades avançadas que permitem controlar quais as cápsulas que podem ser programadas em certos nós, ou como as aplicações multi-cápsula podem ser distribuídas adequadamente pelo cluster. 

Este artigo de boas práticas centra-se nas funcionalidades avançadas de agendamento de Kubernetes para operadores de cluster. Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Use manchas e tolerâncias para limitar o que as cápsulas podem ser programadas em nódosos
> * Dê preferência a casulos para correr em certos nódosos com seledores de nó ou afinidade de nó
> * Dividam ou agrupam cápsulas com afinidade inter-pod ou anti-afinidade

## <a name="provide-dedicated-nodes-using-taints-and-tolerations"></a>Fornecer nóeiros dedicados usando manchas e tolerâncias

**Orientação de boas práticas** - Limitar o acesso a aplicações intensivas de recursos, tais como controladores de ingressos, a nós específicos. Mantenha os recursos do nó disponíveis para cargas de trabalho que as exijam, e não permita o agendamento de outras cargas de trabalho nos nós.

Quando cria o seu cluster AKS, pode implantar nós com suporte de GPU ou um grande número de CPUs poderosos. Estes nós são frequentemente utilizados para grandes cargas de processamento de dados, tais como machine learning (ML) ou inteligência artificial (IA). Como este tipo de hardware é tipicamente um recurso caro para implantar, limite as cargas de trabalho que podem ser programadas nestes nós. Em vez disso, pode querer dedicar alguns nós no cluster para executar serviços de ingresso, e evitar outras cargas de trabalho.

Este suporte para diferentes nós é fornecido usando várias piscinas de nós. Um cluster AKS fornece uma ou mais piscinas de nós.

O programador Kubernetes pode usar manchas e tolerâncias para restringir o que as cargas de trabalho podem ser executadas em nódosos.

* Uma **mancha** é aplicada a um nó que indica que apenas podem ser programadas cápsulas específicas.
* Uma **tolerância** é então aplicada a uma vagem que lhes permite *tolerar* a mancha de um nó.

Quando se implanta uma cápsula para um cluster AKS, a Kubernetes apenas programa cápsulas em nós onde uma tolerância está alinhada com a mancha. Como exemplo, assuma que tem um nó no seu cluster AKS para nós com suporte de GPU. Define o nome, como *o GPU,* e depois um valor para agendamento. Se definir este valor para *NoSchedule,* o programador Kubernetes não pode agendar cápsulas no nó se a cápsula não definir a toleração apropriada.

```console
kubectl taint node aks-nodepool1 sku=gpu:NoSchedule
```

Com uma mancha aplicada aos nós, define-se então uma tolerância na especificação da cápsula que permite agendar os nós. O exemplo que `sku: gpu` se `effect: NoSchedule` segue define o e tolerar a mancha aplicada ao nó no passo anterior:

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

Quando esta cápsula é implantada, como a utilização, `kubectl apply -f gpu-toleration.yaml`os Kubernetes podem agendar com sucesso a cápsula nos nós com a mancha aplicada. Este isolamento lógico permite controlar o acesso aos recursos dentro de um aglomerado.

Quando aplicar manchas, trabalhe com os desenvolvedores e proprietários de aplicações para permitir que definam as tolerâncias necessárias nas suas implementações.

Para obter mais informações sobre manchas e tolerâncias, consulte a aplicação de [manchas e tolerâncias][k8s-taints-tolerations].

Para obter mais informações sobre como usar várias piscinas de nós em AKS, consulte [Create e gerencie várias piscinas][use-multiple-node-pools]de nós para um cluster em AKS .

### <a name="behavior-of-taints-and-tolerations-in-aks"></a>Comportamento de manchas e tolerâncias em AKS

Ao atualizar uma piscina de nó em AKS, manchas e tolerâncias seguem um padrão definido à medida que são aplicadas a novos nós:

- **Clusters padrão que usam conjuntos de escala de máquina virtual**
  - Vamos supor que tem um aglomerado de dois nós - *nó1* e *nó2*. Melhora a piscina do nó.
  - São criados dois nós adicionais, *nó3* e *nó4,* e as manchas são transmitidas respectivamente.
  - O *nó* original 1 e *o nó2* são apagados.

- **Aglomerados sem suporte de conjunto de escala de máquina virtual**
  - Mais uma vez, vamos supor que tem um aglomerado de dois nós - *nó1* e *nó2*. Ao atualizar, é criado um nó adicional *(nó3).*
  - As manchas do *nó1* são aplicadas ao *nó3,* então o *nó1* é então apagado.
  - Outro nó novo é criado (nomeado *nó1*, desde que o *nó 1* anterior foi apagado), e as manchas *nó2* são aplicadas ao novo *nó1*. Então, o *nó2* é apagado.
  - Na essência, *o nó1* torna-se *nó3*, e *o nó2* torna-se *nó1*.

Quando escala uma piscina de nó em AKS, manchas e tolerâncias não são sobreportadas por design.

## <a name="control-pod-scheduling-using-node-selectors-and-affinity"></a>Controlo de agendamento de cápsulas utilizando selecionadores de nó e afinidade

**Orientação das melhores práticas** - Controle o agendamento de casulos em nós utilizando selecionadores de nó, afinidade do nó ou afinidade inter-pod. Estas definições permitem ao programador Kubernetes isolar logicamente cargas de trabalho, como por hardware no nó.

Manchas e tolerâncias são usadas logicamente para isolar recursos com um corte duro - se a cápsula não tolerar a mancha de um nó, não está agendada no nó. Uma abordagem alternativa é usar seletores de nó. Rotula nós, de modo a indicar armazenamento SSD ligado localmente ou uma grande quantidade de memória, e depois defina na especificação do pod um seletor de nó. Kubernetes então marca as cápsulas em um nó correspondente. Ao contrário das tolerações, as cápsulas sem um seletor de nó correspondente podem ser programadas em nós etiquetados. Este comportamento permite que recursos não utilizados nos nós consumam, mas dá prioridade a pods que definem o seletor de nó correspondente.

Vejamos um exemplo de nódosos com uma alta quantidade de memória. Estes nódosos podem dar preferência a pods que pedem uma elevada quantidade de memória. Para garantir que os recursos não ficam inativos, também permitem que outras cápsulas corram.

```console
kubectl label node aks-nodepool1 hardware:highmem
```

Uma especificação de `nodeSelector` pod adiciona então a propriedade para definir um seletor de nó que corresponde ao rótulo definido num nó:

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

Quando utilizar estas opções de programadores, trabalhe com os desenvolvedores e proprietários das suas aplicações para permitir que definam corretamente as especificações do seu casulo.

Para obter mais informações sobre a utilização de seletores de nó, consulte [Asatribui pods a nós][k8s-node-selector].

### <a name="node-affinity"></a>Afinidade do nó

Um seletor de nó é uma forma básica de atribuir cápsulas a um determinado nó. Mais flexibilidade está disponível usando a finção do *nó.* Com afinidade no nó, define-se o que acontece se a cápsula não puder ser igualada com um nó. Você pode *exigir* que o programador Kubernetes corresponda a uma cápsula com um hospedeiro rotulado. Ou, você pode *preferir* um fósforo, mas permitir que o casulo seja agendado em um anfitrião diferente se não estiver disponível.

O exemplo seguinte define a afinidade do nó ao *necessárioDurante agendamentoIgnoredDuringExecution*. Esta afinidade requer que o calendário kubernetes use um nó com um rótulo correspondente. Se não houver nó disponível, a cápsula tem de esperar que o agendamento continue. Para permitir que a cápsula seja programada num nó diferente, pode, em vez disso, definir o valor para *preferencialmenteDuranteOProgramaDurante execução:*

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

A parte ignorada durante a *execução* da definição indica que se as etiquetas do nó mudarem, a cápsula não deve ser despejada do nó. O programador Kubernetes apenas usa as etiquetas de nó atualizadas para novas cápsulas que estão sendo programadas, e não cápsulas já agendadas nos nós.

Para mais informações, consulte [a Affinity e a anti-afinidade.][k8s-affinity]

### <a name="inter-pod-affinity-and-anti-affinity"></a>Afinidade inter-pod e anti-afinidade

Uma abordagem final para o programador kubernetes isolar logicamente cargas de trabalho é usar afinidade inter-pod ou anti-afinidade. As definições definem que as cápsulas *não devem* ser programadas num nó que tenha uma cápsula correspondente existente, ou que *devem* ser programadas. Por padrão, o programador kubernetes tenta agendar várias cápsulas numa réplica definida em nós. Pode definir regras mais específicas em torno deste comportamento.

Um bom exemplo é uma aplicação web que também usa um Azure Cache para Redis. Você pode usar regras anti-afinidade pod para solicitar que o programador Kubernetes distribua réplicas em nódosos. Em seguida, pode utilizar regras de afinidade para garantir que cada componente de aplicação web está agendado no mesmo anfitrião que um cache correspondente. A distribuição de cápsulas através de nódosos parece ser o seguinte exemplo:

| **Nó 1** | **Nó 2** | **Nó 3** |
|------------|------------|------------|
| webapp-1   | webapp-2   | webapp-3   |
| cache-1    | cache-2    | cache-3    |

Este exemplo é uma implantação mais complexa do que a utilização de seletores de nó ou afinidade nó. A implementação dá-lhe controlo sobre como kubernetes programa matos em nódosos e pode logicamente isolar recursos. Para um exemplo completo desta aplicação web com azure cache para exemplo Redis, consulte [Co-localizar cápsulas no mesmo nó][k8s-pod-affinity].

## <a name="next-steps"></a>Passos seguintes

Este artigo focou-se nas funcionalidades avançadas do programador kubernetes. Para obter mais informações sobre operações de cluster no AKS, consulte as seguintes boas práticas:

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
