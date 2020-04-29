---
title: Práticas do operador - Funcionalidades básicas do programador nos Serviços Azure Kubernetes (AKS)
description: Conheça as melhores práticas do operador de cluster para utilizar funcionalidades básicas do programador, tais como quotas de recursos e orçamentos de disrupção de casulos no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: cccc476a944b28d24c53a947e434d465c94f94ee
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79126567"
---
# <a name="best-practices-for-basic-scheduler-features-in-azure-kubernetes-service-aks"></a>Boas práticas para funcionalidades básicas de programadores no Serviço Azure Kubernetes (AKS)

Como gere clusters no Serviço Azure Kubernetes (AKS), muitas vezes precisa de isolar equipas e cargas de trabalho. O programador Kubernetes fornece funcionalidades que permitem controlar a distribuição de recursos computacionais ou limitar o impacto dos eventos de manutenção.

Este artigo de boas práticas centra-se nas funcionalidades básicas de agendamento de Kubernetes para operadores de cluster. Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Utilize quotas de recursos para fornecer uma quantidade fixa de recursos a equipas ou cargas de trabalho
> * Limite o impacto da manutenção programada utilizando orçamentos de disrupção da cápsula
> * Verifique se faltam pedidos e limites `kube-advisor` de recursos de casulo usando a ferramenta

## <a name="enforce-resource-quotas"></a>Impor quotas de recursos

**Orientação de boas práticas** - Planeie e aplique quotas de recursos ao nível do espaço de nome. Se as cápsulas não definirem pedidos e limites de recursos, rejeite a implementação. Monitorize a utilização dos recursos e ajuste as quotas conforme necessário.

Os pedidos e limites de recursos são colocados na especificação do casulo. Estes limites são utilizados pelo programador Kubernetes no momento da implantação para encontrar um nó disponível no cluster. Estes limites e pedidos funcionam ao nível da cápsula individual. Para obter mais informações sobre como definir estes valores, consulte Definir pedidos e limites de [recursos casulos][resource-limits]

Para fornecer uma forma de reservar e limitar os recursos através de uma equipa ou projeto de desenvolvimento, você deve usar quotas de *recursos.* Estas quotas são definidas num espaço de nome, e podem ser utilizadas para fixar quotas na seguinte base:

* **Recursos computacionais**, como CPU e memória, ou GPUs.
* **Os recursos**de armazenamento incluem o número total de volumes ou a quantidade de espaço em disco para uma determinada classe de armazenamento.
* **A contagem de objetos**, como o número máximo de segredos, serviços ou empregos pode ser criado.

Kubernetes não compromete recursos. Uma vez que o total acumulado de pedidos ou limites de recursos passe a quota atribuída, nenhuma nova implantação é bem sucedida.

Quando define quotas de recursos, todas as cápsulas criadas no espaço de nome devem fornecer limites ou pedidos nas especificações do seu casulo. Se não fornecerem estes valores, pode rejeitar a implantação. Em vez disso, pode [configurar pedidos e limites por defeito para um espaço de nome][configure-default-quotas].

O seguinte exemplo YAML manifesto denominado *dev-app-team-quotas.yaml* estabelece um limite duro de um total de *10* CPUs, *20Gi* de memória e *10* pods:

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

Esta quota de recursos pode ser aplicada especificando o espaço de nome, tais como *dev-apps:*

```console
kubectl apply -f dev-app-team-quotas.yaml --namespace dev-apps
```

Trabalhe com os desenvolvedores e proprietários de aplicações para compreender as suas necessidades e aplicar as quotas de recursos apropriadas.

Para obter mais informações sobre objetos de recursos, âmbitos e prioridades disponíveis, consulte quotas de [recursos em Kubernetes.][k8s-resource-quotas]

## <a name="plan-for-availability-using-pod-disruption-budgets"></a>Plano de disponibilidade utilizando orçamentos de disrupção de pod

**Orientação** de boas práticas - Para manter a disponibilidade de aplicações, defina os Orçamentos de Disrupção de Pod (PDBs) para garantir que um número mínimo de cápsulas está disponível no cluster.

Há dois eventos disruptivos que fazem com que as cápsulas sejam removidas:

* *As perturbações involuntárias* são eventos fora do controlo típico do operador do cluster ou do proprietário da aplicação.
  * Estas perturbações involuntárias incluem uma falha de hardware na máquina física, um pânico de kernel, ou a eliminação de um VM do nó
* *As perturbações voluntárias* são eventos solicitados pelo operador do cluster ou pelo proprietário da aplicação.
  * Estas perturbações voluntárias incluem upgrades de cluster, um modelo de implementação atualizado, ou acidentalmente apagar uma cápsula.

As perturbações involuntárias podem ser atenuadas utilizando múltiplas réplicas das suas cápsulas numa implantação. Executar vários nós no cluster AKS também ajuda com estas perturbações involuntárias. Para perturbações voluntárias, a Kubernetes fornece orçamentos de *disrupção* da cápsula que permitem ao operador do cluster definir uma contagem mínima de recursos disponíveis ou máxima indisponível. Estes orçamentos de disrupção da cápsula permitem-lhe planear como as implementações ou conjuntos de réplicas respondem quando ocorre um evento de perturbação voluntária.

Se um cluster for atualizado ou um modelo de implementação atualizado, o programador kubernetes garante que as cápsulas adicionais são programadas em outros nós antes que os eventos de interrupção voluntária possam continuar. O programador aguarda antes de um nó ser reiniciado até que o número definido de cápsulas seja programado com sucesso em outros nós do cluster.

Vejamos um exemplo de uma réplica com cinco cápsulas que executam o NGINX. As cápsulas do conjunto de réplicas são atribuídas à etiqueta `app: nginx-frontend`. Durante um evento de interrupção voluntária, como um upgrade de cluster, você deve ter certeza de que pelo menos três cápsulas continuam a funcionar. O seguinte manifesto YAML para um objeto *PodDisruptionBudget* define estes requisitos:

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

Também pode definir uma percentagem, como *60%,* que lhe permite compensar automaticamente o conjunto de réplicas, escalando o número de cápsulas.

Pode definir um número máximo de casos indisponíveis num conjunto de réplicas. Mais uma vez, uma percentagem para as cápsulas máximas indisponíveis também pode ser definida. O seguinte orçamento de rutura da cápsula YAML manifesto define que não mais de duas cápsulas no conjunto de réplicas não estão disponíveis:

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

Uma vez definido o orçamento de rutura da cápsula, crie-o no seu cluster AKS como qualquer outro objeto Kubernetes:

```console
kubectl apply -f nginx-pdb.yaml
```

Trabalhe com os desenvolvedores e proprietários de aplicações para compreender as suas necessidades e aplicar os orçamentos apropriados de interrupção do pod.

Para obter mais informações sobre a utilização de orçamentos de disrupção da cápsula, consulte [Especifique um orçamento de interrupção para a sua candidatura][k8s-pdbs].

## <a name="regularly-check-for-cluster-issues-with-kube-advisor"></a>Verifique regularmente se há problemas de cluster com kube-advisor

**Orientação** de boas práticas - `kube-advisor` Executar regularmente a versão mais recente da ferramenta open source para detetar problemas no seu cluster. Se aplicar quotas de recursos num cluster `kube-advisor` AKS existente, corra primeiro para encontrar cápsulas que não tenham pedidos de recursos e limites definidos.

A ferramenta [kube-advisor][kube-advisor] é um projeto de código aberto AKS associado que digitaliza um cluster Kubernetes e reporta sobre questões que encontra. Uma verificação útil é identificar cápsulas que não têm pedidos de recursos e limites no lugar.

A ferramenta kube-advisor pode reportar sobre o pedido de recursos e os limites em falta em PodSpecs para aplicações Windows, bem como aplicações Linux, mas a própria ferramenta de kube-advisor deve ser agendada num casulo Linux. Você pode agendar uma cápsula para correr em uma piscina de nó com um SISTEMA específico usando um [seletor][k8s-node-selector] de nó na configuração do casulo.

Num cluster AKS que acolhe várias equipas e aplicações de desenvolvimento, pode ser difícil rastrear cápsulas sem estes pedidos de recursos e limites definidos. Como uma boa prática, `kube-advisor` execute regularmente nos seus clusters AKS, especialmente se não atribuir quotas de recursos a espaços de nome.

## <a name="next-steps"></a>Passos seguintes

Este artigo focou-se nas funcionalidades básicas do programador kubernetes. Para obter mais informações sobre operações de cluster no AKS, consulte as seguintes boas práticas:

* [Isolamento multi-inquilinos e de clusters][aks-best-practices-cluster-isolation]
* [Funcionalidades avançadas do programador kubernetes][aks-best-practices-advanced-scheduler]
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
