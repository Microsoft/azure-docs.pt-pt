---
title: Práticas recomendadas do operador-recursos básicos do Agendador nos serviços Kubernetess do Azure (AKS)
description: Conheça as práticas recomendadas do operador de cluster para usar recursos básicos do Agendador, como cotas de recursos e orçamentos de interrupção de Pod no serviço de kubernetes do Azure (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: mlearned
ms.openlocfilehash: 3661f435b5c2dd88aa8e17ca396f9af43aea5224
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2020
ms.locfileid: "76293611"
---
# <a name="best-practices-for-basic-scheduler-features-in-azure-kubernetes-service-aks"></a>Práticas recomendadas para recursos básicos do Agendador no serviço de kubernetes do Azure (AKS)

Ao gerenciar clusters no AKS (serviço kubernetes do Azure), muitas vezes você precisa isolar equipes e cargas de trabalho. O Agendador kubernetes fornece recursos que permitem controlar a distribuição de recursos de computação ou limitar o impacto dos eventos de manutenção.

Este artigo de práticas recomendadas se concentra em recursos básicos de agendamento de kubernetes para operadores de cluster. Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Use cotas de recursos para fornecer uma quantidade fixa de recursos para equipes ou cargas de trabalho
> * Limitar o impacto da manutenção agendada usando orçamentos de interrupção de Pod
> * Verificar se há solicitações e limites de recursos Pod ausentes usando a ferramenta de `kube-advisor`

## <a name="enforce-resource-quotas"></a>Impor cotas de recursos

**Diretrizes de práticas recomendadas** -planeje e aplique cotas de recursos no nível de namespace. Se os pods não definirem limites e solicitações de recursos, rejeite a implantação. Monitore o uso de recursos e ajuste as cotas conforme necessário.

As solicitações de recursos e os limites são colocados na especificação de Pod. Esses limites são usados pelo Agendador kubernetes no momento da implantação para encontrar um nó disponível no cluster. Esses limites e solicitações funcionam no nível de Pod individual. Para obter mais informações sobre como definir esses valores, consulte [definir limites e solicitações de recursos Pod][resource-limits]

Para fornecer uma maneira de reservar e limitar recursos em uma equipe de desenvolvimento ou projeto, você deve usar *cotas de recursos*. Essas cotas são definidas em um namespace e podem ser usadas para definir cotas da seguinte maneira:

* **Recursos de computação**, como CPU e memória, ou GPUs.
* **Recursos de armazenamento**, inclui o número total de volumes ou a quantidade de espaço em disco para uma determinada classe de armazenamento.
* A **contagem de objetos**, como o número máximo de segredos, serviços ou trabalhos, pode ser criada.

Kubernetes não compromete os recursos. Depois que o total cumulativo de solicitações ou limites de recursos passar a cota atribuída, nenhuma implantação adicional será bem-sucedida.

Quando você define cotas de recursos, todos os pods criados no namespace devem fornecer limites ou solicitações em suas especificações de Pod. Se eles não fornecerem esses valores, você poderá rejeitar a implantação. Em vez disso, você pode [configurar as solicitações e os limites padrão para um namespace][configure-default-quotas].

O manifesto YAML de exemplo a seguir chamado *dev-app-Team-cotas. YAML* define um limite rígido de um total de *10* CPUs, *20Gi* de memória e *10* pods:

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

Essa cota de recursos pode ser aplicada especificando o namespace, como *dev-apps*:

```console
kubectl apply -f dev-app-team-quotas.yaml --namespace dev-apps
```

Trabalhe com seus desenvolvedores e proprietários de aplicativos para entender suas necessidades e aplicar as cotas de recursos apropriadas.

Para obter mais informações sobre objetos de recursos, escopos e prioridades disponíveis, consulte [cotas de recursos em kubernetes][k8s-resource-quotas].

## <a name="plan-for-availability-using-pod-disruption-budgets"></a>Planejar a disponibilidade usando orçamentos de interrupção de Pod

**Diretrizes de práticas recomendadas** -para manter a disponibilidade de aplicativos, defina PDBs (orçamentos de interrupção de Pod) para garantir que um número mínimo de pods esteja disponível no cluster.

Há dois eventos de interrupção que fazem com que pods sejam removidos:

* *Interrupções involuntárias* são eventos além do controle típico do proprietário do aplicativo ou do operador de cluster.
  * Essas interrupções involuntárias incluem uma falha de hardware no computador físico, um pane no kernel ou a exclusão de uma VM do nó
* As *interrupções voluntárias* são eventos solicitados pelo proprietário do aplicativo ou operador do cluster.
  * Essas interrupções voluntárias incluem atualizações de cluster, um modelo de implantação atualizado ou a exclusão acidental de um pod.

As interrupções involuntárias podem ser atenuadas usando várias réplicas de seu pods em uma implantação. A execução de vários nós no cluster AKS também ajuda com essas interrupções involuntárias. Para interrupções voluntárias, o kubernetes fornece *orçamentos de interrupção de Pod* que permitem que o operador de cluster defina uma contagem mínima de recursos disponível ou máximo indisponível. Esses orçamentos de interrupção de Pod permitem planejar como as implantações ou conjuntos de réplicas respondem quando ocorre um evento de interrupção voluntário.

Se um cluster for atualizado ou um modelo de implantação for atualizado, o Agendador kubernetes garantirá que os pods adicionais sejam agendados em outros nós antes que os eventos de interrupção voluntários possam continuar. O Agendador aguarda antes de um nó ser reinicializado até que o número definido de pods seja agendado com êxito em outros nós no cluster.

Vejamos um exemplo de um conjunto de réplicas com cinco pods que executam NGINX. O pods no conjunto de réplicas recebe o rótulo `app: nginx-frontend`. Durante um evento de interrupção voluntário, como uma atualização de cluster, você deseja garantir que pelo menos três pods continuem em execução. O seguinte manifesto YAML para um objeto *PodDisruptionBudget* define estes requisitos:

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

Você também pode definir uma porcentagem, como *60%* , que permite compensar automaticamente o conjunto de réplicas aumentando o número de pods.

Você pode definir um número máximo de instâncias não disponíveis em um conjunto de réplicas. Novamente, um percentual para o pods máximo indisponível também pode ser definido. O manifesto de YAML do orçamento de interrupção do pod a seguir define que não há mais do que dois pods no conjunto de réplicas disponíveis:

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

Depois que o orçamento de interrupção do pod for definido, você o criará no cluster AKS como com qualquer outro objeto kubernetes:

```console
kubectl apply -f nginx-pdb.yaml
```

Trabalhe com seus desenvolvedores e proprietários de aplicativos para entender suas necessidades e aplicar os orçamentos de interrupção de Pod apropriados.

Para obter mais informações sobre como usar orçamentos de interrupção de Pod, consulte [especificar um orçamento de interrupção para seu aplicativo][k8s-pdbs].

## <a name="regularly-check-for-cluster-issues-with-kube-advisor"></a>Verificar regularmente problemas de cluster com o Kube-Advisor

**Diretrizes de práticas recomendadas** -execute regularmente a versão mais recente do `kube-advisor` ferramenta de software livre para detectar problemas no cluster. Se você aplicar cotas de recursos em um cluster AKS existente, execute `kube-advisor` primeiro para localizar pods que não têm limites e solicitações de recursos definidos.

A ferramenta [Kube-Advisor][kube-advisor] é um projeto de código-fonte aberto AKs associado que examina um cluster kubernetes e relata os problemas encontrados. Uma verificação útil é identificar os pods que não têm solicitações de recursos e limites em vigor.

A ferramenta Kube-Advisor pode relatar a solicitação de recursos e os limites ausentes no PodSpecs para aplicativos do Windows, bem como aplicativos do Linux, mas a ferramenta Kube-Advisor em si deve ser agendada em um pod do Linux. Você pode agendar um pod para ser executado em um pool de nós com um sistema operacional específico usando um [seletor de nó][k8s-node-selector] na configuração do pod.

Em um cluster AKS que hospeda várias equipes de desenvolvimento e aplicativos, pode ser difícil rastrear pods sem essas solicitações de recursos e limites definidos. Como prática recomendada, execute regularmente `kube-advisor` em seus clusters AKS, especialmente se você não atribuir cotas de recursos a namespaces.

## <a name="next-steps"></a>Passos seguintes

Este artigo se concentrou nos recursos básicos do Agendador de kubernetes. Para obter mais informações sobre as operações de cluster no AKS, consulte as seguintes práticas recomendadas:

* [Multilocação e isolamento de cluster][aks-best-practices-cluster-isolation]
* [Recursos do Agendador do kubernetes avançado][aks-best-practices-advanced-scheduler]
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
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
