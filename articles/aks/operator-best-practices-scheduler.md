---
title: Práticas recomendadas do operador – recursos de agendador básica dos serviços de Kubernetes no Azure (AKS)
description: Conheça as práticas recomendadas de operador de cluster para a utilização de recursos do agendador básica, tais como quotas de recursos e pod orçamentos de interrupção no Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: iainfou
ms.openlocfilehash: 8233330973946e552e36a85a11bdbbfb06c739f0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58178140"
---
# <a name="best-practices-for-basic-scheduler-features-in-azure-kubernetes-service-aks"></a>Práticas recomendadas para recursos do agendador básica no Azure Kubernetes Service (AKS)

Como gerir clusters no Azure Kubernetes Service (AKS), precisa, muitas vezes isolar cargas de trabalho e as equipes. O agendador de Kubernetes fornece funcionalidades que permitem-lhe controlar a distribuição de recursos de computação ou limitar o impacto de eventos de manutenção.

Este artigo de melhores práticas se concentra em Kubernetes básica funcionalidades de agendamento para operadores de cluster. Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Utilizar quotas de recursos para fornecer uma quantidade fixa de recursos para as equipes ou cargas de trabalho
> * Limitar o impacto da manutenção agendada com orçamentos de interrupção de pod
> * Verificação de ausentes solicitações de recursos de pod e limites usando o `kube-advisor` ferramenta

## <a name="enforce-resource-quotas"></a>Enforce resource quotas

**Melhores diretrizes de práticas** -planear e a aplicar quotas de recursos ao nível do espaço de nomes. Se não definem o pods solicitações de recursos e limites, rejeite a implementação. Monitorizar a utilização de recursos e ajustar as quotas conforme necessário.

Pedidos de recursos e os limites são colocados na especificação pod. Estes limites são utilizados pelo agendador do Kubernetes no momento da implementação para encontrar um nó disponível no cluster. Estes limites e pedidos de funcionam ao nível do pod individuais. Para obter mais informações sobre como definir esses valores, consulte [solicitações de recursos de pod de definir e limites][resource-limits]

Para fornecer uma forma de reservar e limitar os recursos numa equipe de desenvolvimento ou projeto, deve usar *quotas de recursos*. Estas quotas estão definidas num espaço de nomes e podem ser utilizadas para definir as quotas com base no facto seguinte:

* **Recursos de computação**, tais como CPU e memória ou GPUs.
* **Recursos de armazenamento**, inclui o número total de volumes ou a quantidade de espaço em disco para uma classe de armazenamento fornecida.
* **Contagem de objectos**, como o número máximo de segredos, serviços ou tarefas podem ser criados.

Kubernetes não sobreconsolidar recursos. Assim que o total cumulativo de pedidos de recursos ou limites passa a quota atribuída, sem implementações adicionais são com êxito.

Ao definir quotas de recursos, todos os pods criados no espaço de nomes tem de fornecer limites ou pedidos nas suas especificações de pod. Se eles não fornecem estes valores, pode rejeitar a implementação. Em vez disso, pode [configurar pedidos de padrão e limites para um espaço de nomes][configure-default-quotas].

O manifesto YAML de exemplo seguinte com o nome *dev-aplicação-equipe-quotas.yaml* define um limite restritivo de um total de *10* CPUs, *20Gi* de memória, e *10*pods:

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: dev-app-team
spec:
  hard:
    cpu: "10"
    memory: 20Gi
    pods: "10"
```

Esta quota de recursos pode ser aplicada ao especificar o espaço de nomes, tal como *dev-apps*:

```console
kubectl apply -f dev-app-team-quotas.yaml --namespace dev-apps
```

Trabalhar com os seus desenvolvedores de aplicativos e os proprietários para compreender suas necessidades e aplicar as quotas de recursos adequados.

Para obter mais informações sobre objetos de recursos disponíveis, âmbitos e as prioridades, consulte [quotas de recursos no Kubernetes][k8s-resource-quotas].

## <a name="plan-for-availability-using-pod-disruption-budgets"></a>Planear a disponibilidade com orçamentos de interrupção de pod

**Melhores diretrizes de práticas** - para manter a disponibilidade de aplicações, definir orçamentos de interrupção de Pod (PDBs) para se certificar de que um número mínimo de pods está disponível no cluster.

Existem dois eventos disruptivos que fazer com que os pods ser removido:

* *As interrupções involuntários* são eventos além do controlo típico do operador de cluster ou proprietário da aplicação.
  * Essas interrupções involuntários incluem uma falha de hardware na máquina física, um entre em pânico do kernel ou a eliminação de um nó de VM
* *As interrupções voluntárias* são eventos que solicitou pela operadora de rede de cluster ou proprietário da aplicação.
  * Essas interrupções voluntárias incluem atualizações de cluster, um modelo de implementação atualizada ou eliminar acidentalmente um pod.

As interrupções involuntários podem ser atenuadas utilizando várias réplicas dos seus pods numa implementação. Execução de vários nós do cluster do AKS também ajuda a com essas interrupções involuntários. Para as interrupções voluntárias, Kubernetes fornece *pod orçamentos de interrupção* que permitem que o operador de cluster, definir uma contagem mínima disponível ou máximo disponível de recursos. Estes orçamentos de interrupção de pod permitem-lhe planear a forma como as implementações ou conjuntos de réplicas respondem quando ocorre um evento de interrupção voluntária.

Se um cluster está a ser atualizado ou um modelo de implementação atualizado, o agendador de Kubernetes torna-se de que adicionais pods são agendadas noutros nós antes de continuarem com os eventos de interrupção voluntária. O agendador aguarda antes de um nó é reiniciado, até que o número definido de pods é agendado com êxito nos outros nós do cluster.

Vamos examinar um exemplo de uma réplica definido com cinco pods com o NGINX. Os pods na réplica definido como atribuídos ao rótulo `app: nginx-frontend`. Durante um evento de interrupção voluntária, como uma atualização de cluster e pretender certificar-se de que, pelo menos, três pods continuam a ser executado. A seguinte YAML de manifesto para um *PodDisruptionBudget* objeto define estes requisitos:

```yaml
apiVersion: policy/v1beta1
kind: PodDisruptionBudget
metadata:
   name: nginx-pdb
spec:
   minAvailable: 3
   selector:
   matchLabels:
      app: nginx-frontend
```

Também pode definir como uma percentagem *60%*, que permite-lhe compensar automaticamente para a réplica de definir a aumentar o número de pods.

Pode definir um número máximo de instâncias indisponíveis num conjunto de réplicas. Novamente, também pode ser definida uma percentagem para os pods indisponíveis máximos. O manifesto YAML do orçamento de interrupção de pod seguinte define o que não mais do que dois pods na réplica do conjunto não estar disponível:

```yaml
apiVersion: policy/v1beta1
kind: PodDisruptionBudget
metadata:
   name: nginx-pdb
spec:
   maxUnavailable: 2
   selector:
   matchLabels:
      app: nginx-frontend
```

Assim que o seu orçamento de interrupção de pod é definido, criá-la no cluster do AKS tal como acontece com qualquer outro objeto de Kubernetes:

```console
kubectl apply -f nginx-pdb.yaml
```

Trabalhar com os seus desenvolvedores de aplicativos e os proprietários para compreender suas necessidades e aplicar os orçamentos de interrupção de pod apropriado.

Para obter mais informações sobre como utilizar os orçamentos de interrupção de pod, consulte [especifiquem um orçamento de interrupção para seu aplicativo][k8s-pdbs].

## <a name="regularly-check-for-cluster-issues-with-kube-advisor"></a>Verificar regularmente a existência de problemas de cluster com o Assistente do kube

**Melhores diretrizes de práticas** -regularmente executam a versão mais recente do `kube-advisor` ferramenta de código-fonte aberto para detetar problemas no seu cluster. Se aplicar quotas de recursos num cluster do AKS existente, execute `kube-advisor` primeiro para encontrar os pods que não têm pedidos de recursos e os limites definidos.

O [kube advisor] [ kube-advisor] ferramenta é um projeto de código-fonte aberto associado do AKS que verifica a existência de um cluster do Kubernetes, relatórios de problemas que encontrar. É uma verificação útil identificar os pods que não têm limites e pedidos de recursos no local.

Num cluster do AKS que hospeda várias equipes de desenvolvimento e aplicações, pode ser difícil de controlar os pods sem esses recursos solicitam e nos limites do conjunto. Como melhor prática, executar regularmente `kube-advisor` nos seus clusters do AKS, especialmente se não atribuir quotas de recursos para espaços de nomes.

## <a name="next-steps"></a>Passos Seguintes

Este artigo concentra-se nas funcionalidades básicas do agendador de Kubernetes. Para obter mais informações sobre as operações de cluster no AKS, consulte as seguintes práticas recomendadas:

* [Isolamento de vários inquilinos e de cluster][aks-best-practices-cluster-isolation]
* [Funcionalidades avançadas de scheduler de Kubernetes][aks-best-practices-advanced-scheduler]
* [Autenticação e autorização][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->
[k8s-resource-quotas]: https://kubernetes.io/docs/concepts/policy/resource-quotas/
[configure-default-quotas]: https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/memory-default-namespace/
[kube-advisor]: https://github.com/Azure/kube-advisor
[k8s-pdbs]: https://kubernetes.io/docs/tasks/run-application/configure-pdb/

<!-- INTERNAL LINKS -->
[resource-limits]: developer-best-practices-resource-management.md#define-pod-resource-requests-and-limits
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-identity]: operator-best-practices-identity.md
