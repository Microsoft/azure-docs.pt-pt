---
title: Melhores práticas do operador - Funcionalidades básicas de programador nos Serviços Azure Kubernetes (AKS)
description: Aprenda as melhores práticas do operador do cluster para utilizar funcionalidades básicas de programador, tais como quotas de recursos e orçamentos de interrupção de cápsulas no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: 087c1d2efc93b8460a3683a4e66916d73fd4e885
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87015685"
---
# <a name="best-practices-for-basic-scheduler-features-in-azure-kubernetes-service-aks"></a>Melhores práticas para funcionalidades básicas de programador no Serviço Azure Kubernetes (AKS)

À medida que gere clusters no Serviço Azure Kubernetes (AKS), muitas vezes precisa isolar equipas e cargas de trabalho. O programador Kubernetes fornece funcionalidades que permitem controlar a distribuição de recursos computacional ou limitar o impacto de eventos de manutenção.

Este artigo de boas práticas centra-se nas funcionalidades básicas de agendamento de Kubernetes para os operadores de cluster. Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Utilize quotas de recursos para fornecer uma quantidade fixa de recursos às equipas ou cargas de trabalho
> * Limitar o impacto da manutenção programada utilizando orçamentos de interrupção da cápsula
> * Verifique se faltam pedidos de recursos de pod e limites utilizando a `kube-advisor` ferramenta

## <a name="enforce-resource-quotas"></a>Impor quotas de recursos

**Orientação de boas práticas** - Planeie e aplique quotas de recursos ao nível do espaço de nome. Se as cápsulas não definirem pedidos e limites de recursos, rejeite a implantação. Monitorize a utilização dos recursos e ajuste as quotas conforme necessário.

Os pedidos e limites de recursos são colocados na especificação do casulo. Estes limites são usados pelo programador Kubernetes na hora de implantação para encontrar um nó disponível no cluster. Estes limites e pedidos funcionam a nível de cada vagem. Para obter mais informações sobre como definir estes valores, consulte [Definir pedidos e limites de recursos do pod][resource-limits]

Para fornecer uma forma de reservar e limitar recursos através de uma equipa ou projeto de desenvolvimento, você deve usar *quotas de recursos.* Estas quotas são definidas num espaço de nome, podendo ser utilizadas para definir quotas na seguinte base:

* **Recursos de cálculo**, como CPU e memória, ou GPUs.
* **Recursos de armazenamento**, inclui o número total de volumes ou quantidade de espaço em disco para uma determinada classe de armazenamento.
* **A contagem de objetos**, como o número máximo de segredos, serviços ou empregos pode ser criado.

Kubernetes não compromete recursos excessivos. Uma vez que o total acumulado de pedidos ou limites de recursos passa a quota atribuída, nenhuma outra implementação é bem sucedida.

Quando define as quotas de recursos, todas as cápsulas criadas no espaço de nomes devem fornecer limites ou pedidos nas suas especificações de vagem. Se não fornecerem estes valores, pode rejeitar a implantação. Em vez disso, pode [configurar pedidos e limites predefinidos para um espaço de nome][configure-default-quotas].

O seguinte exemplo yaml manifesto nomeado *dev-app-team-quotas.yaml* define um limite rígido de um total de *10* CPUs, *20Gi* de memória e *10* cápsulas:

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

Esta quota de recursos pode ser aplicada especificando o espaço de nome, como *dev-apps*:

```console
kubectl apply -f dev-app-team-quotas.yaml --namespace dev-apps
```

Trabalhe com os desenvolvedores e proprietários da sua aplicação para compreender as suas necessidades e aplicar as quotas de recursos apropriadas.

Para obter mais informações sobre objetos de recursos disponíveis, âmbitos e prioridades, consulte [as quotas de recursos em Kubernetes.][k8s-resource-quotas]

## <a name="plan-for-availability-using-pod-disruption-budgets"></a>Plano de disponibilidade utilizando orçamentos de interrupção do casulo

**Orientação para as melhores práticas** - Para manter a disponibilidade de aplicações, defina os Orçamentos de Interrupção do Pod (PDBs) para garantir que um número mínimo de cápsulas estão disponíveis no cluster.

Há dois eventos disruptivos que fazem com que as cápsulas sejam removidas:

* *Perturbações involuntárias* são eventos fora do controlo típico do operador de cluster ou proprietário de aplicações.
  * Estas perturbações involuntárias incluem uma falha de hardware na máquina física, um pânico de núcleo, ou a eliminação de um VM de nó
* *Perturbações voluntárias* são eventos solicitados pelo operador do cluster ou pelo proprietário da aplicação.
  * Estas perturbações voluntárias incluem upgrades de cluster, um modelo de implementação atualizado, ou acidentalmente eliminar uma cápsula.

As perturbações involuntárias podem ser atenuadas utilizando múltiplas réplicas das suas cápsulas numa implantação. Executar múltiplos nós no cluster AKS também ajuda com estas perturbações involuntárias. Para interrupções voluntárias, a Kubernetes fornece *orçamentos de interrupção do pod* que permitem ao operador do cluster definir uma contagem mínima de recursos disponível ou máxima indisponível. Estes orçamentos de disrupção da cápsula permitem-lhe planear como as implementações ou conjuntos de réplicas respondem quando ocorre um evento de interrupção voluntária.

Se um cluster for atualizado ou um modelo de implementação atualizado, o programador Kubernetes garante que as cápsulas adicionais são agendadas em outros nós antes que os eventos de interrupção voluntária possam continuar. O agendador aguarda antes de um nó ser reiniciado até que o número definido de cápsulas seja programado com sucesso em outros nós no cluster.

Vejamos um exemplo de uma réplica com cinco cápsulas que executam o NGINX. As cápsulas do conjunto de réplicas são atribuídas à etiqueta `app: nginx-frontend` . Durante um evento de interrupção voluntária, como um upgrade de cluster, você deve ter certeza de que pelo menos três cápsulas continuam a funcionar. O seguinte manifesto YAML para um objeto *PodDisruptionBudget* define estes requisitos:

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

Também pode definir uma percentagem, como *60%,* o que lhe permite compensar automaticamente a réplica configurando o número de cápsulas.

Pode definir um número máximo de casos indisponíveis num conjunto de réplicas. Mais uma vez, uma percentagem para as cápsulas indisponíveis máximas também pode ser definida. O seguinte orçamento de rutura do casulo YAML manifesto define que não mais do que duas cápsulas no conjunto de réplicas não estão disponíveis:

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

Uma vez definido o orçamento de interrupção do seu pod, cria-o no seu cluster AKS como em qualquer outro objeto Kubernetes:

```console
kubectl apply -f nginx-pdb.yaml
```

Trabalhe com os desenvolvedores e proprietários da sua aplicação para compreender as suas necessidades e aplicar os orçamentos apropriados de interrupção do casulo.

Para obter mais informações sobre a utilização de orçamentos de interrupção do pod, consulte [Especificar um orçamento de interrupção para a sua aplicação.][k8s-pdbs]

## <a name="regularly-check-for-cluster-issues-with-kube-advisor"></a>Verifique regularmente se há problemas de cluster com o kube-advisor

**Orientação para as melhores práticas** - Execute regularmente a versão mais recente da `kube-advisor` ferramenta open source para detetar problemas no seu cluster. Se aplicar quotas de recursos num cluster AKS existente, corra `kube-advisor` primeiro para encontrar cápsulas que não tenham pedidos de recursos e limites definidos.

A ferramenta [kube-advisor][kube-advisor] é um projeto de open source AKS associado que analisa um cluster Kubernetes e reporta sobre problemas que encontra. Uma verificação útil é identificar cápsulas que não têm pedidos de recursos e limites no lugar.

A ferramenta kube-advisor pode reportar sobre o pedido de recursos e os limites em falta em PodSpecs para aplicações Windows, bem como aplicações Linux, mas a própria ferramenta de kube-advisor deve ser agendada num pod Linux. Pode agendar um pod para correr numa piscina de nó com um sistema operativo específico utilizando um [seletor de nó][k8s-node-selector] na configuração do pod.

Num cluster AKS que acolhe várias equipas e aplicações de desenvolvimento, pode ser difícil rastrear cápsulas sem estes pedidos de recursos e limites definidos. Como uma boa prática, execute regularmente `kube-advisor` os seus clusters AKS, especialmente se não atribuir quotas de recursos a espaços de nome.

## <a name="next-steps"></a>Passos seguintes

Este artigo focou-se nas funcionalidades básicas do programador Kubernetes. Para obter mais informações sobre as operações de cluster em AKS, consulte as seguintes boas práticas:

* [Isolamento multi-inquilinos e de clusters][aks-best-practices-cluster-isolation]
* [Recursos avançados do programador Kubernetes][aks-best-practices-advanced-scheduler]
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
