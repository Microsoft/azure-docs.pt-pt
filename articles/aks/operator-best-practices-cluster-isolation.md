---
title: Práticas do operador - Isolamento de clusters nos Serviços Azure Kubernetes (AKS)
description: Conheça as melhores práticas do operador de cluster para o isolamento no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: 228b856d5c5ffa2bfac7df12094667e02f797690
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594860"
---
# <a name="best-practices-for-cluster-isolation-in-azure-kubernetes-service-aks"></a>Boas práticas para o isolamento de clusters no Serviço Azure Kubernetes (AKS)

Como gere clusters no Serviço Azure Kubernetes (AKS), muitas vezes precisa de isolar equipas e cargas de trabalho. A AKS proporciona flexibilidade na forma como pode gerir aglomerados multi-inquilinos e isolar recursos. Para maximizar o seu investimento em Kubernetes, estas características de vários arrendamentos e isolamento devem ser compreendidas e implementadas.

Este artigo de boas práticas centra-se no isolamento dos operadores de cluster. Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Plano para aglomerados multi-inquilinos e separação de recursos
> * Use isolamento lógico ou físico nos seus clusters AKS

## <a name="design-clusters-for-multi-tenancy"></a>Clusters de design para multi-arrendamento

O Kubernetes fornece funcionalidades que lhe permitem isolar logicamente equipas e cargas de trabalho no mesmo cluster. O objetivo deve ser fornecer o menor número de privilégios, orientados para os recursos necessários para cada equipa. Um [espaço de nome][k8s-namespaces] em Kubernetes cria um limite lógico de isolamento. As características e considerações adicionais da Kubernetes para o isolamento e o arrendamento multi-arrendamento incluem as seguintes áreas:

* **O agendamento** inclui a utilização de funcionalidades básicas, tais como quotas de recursos e orçamentos de disrupção de casulos. Para obter mais informações sobre estas funcionalidades, consulte [as melhores práticas para funcionalidades básicas de programadores no AKS][aks-best-practices-scheduler].
  * As funcionalidades mais avançadas do programador incluem manchas e tolerâncias, selecionadores de nós, e afinidade do nó e pod ou anti-afinidade. Para obter mais informações sobre estas funcionalidades, consulte [as melhores práticas para funcionalidades avançadas de programadores em AKS][aks-best-practices-advanced-scheduler].
* **A rede** inclui a utilização de políticas de rede para controlar o fluxo de tráfego dentro e fora das cápsulas.
* **A autenticação e a autorização** incluem o utilizador do controlo de acesso baseado em papéis (RBAC) e integração do Diretório Ativo Azure (AD), identidades de pod e segredos no Cofre de Chaves Azure. Para obter mais informações sobre estas [funcionalidades, consulte as melhores práticas de autenticação e autorização no AKS.][aks-best-practices-identity]
* **Os contentores** incluem políticas de segurança de pods, contextos de segurança do pod, digitalização de imagens e tempos de execução para vulnerabilidades. Envolve também a utilização de App Armor ou Seccomp (Secure Computing) para restringir o acesso do contentor ao nó subjacente.

## <a name="logically-isolate-clusters"></a>Grupos de isolados logicamente

**Orientação de boas práticas** - Use o isolamento lógico para separar equipas e projetos. Tente minimizar o número de clusters de AKS físicos que implementa para isolar equipas ou aplicações.

Com isolamento lógico, um único cluster AKS pode ser usado para várias cargas de trabalho, equipas ou ambientes. Kubernetes [Namespaces][k8s-namespaces] formam o limite lógico de isolamento para cargas de trabalho e recursos.

![Isolamento lógico de um aglomerado kubernetes em AKS](media/operator-best-practices-cluster-isolation/logical-isolation.png)

A separação lógica dos aglomerados geralmente proporciona uma densidade de pod maior do que os aglomerados fisicamente isolados. Há menos capacidade de computação em excesso que fica inativa no aglomerado. Quando combinado com o autoescalador de cluster Kubernetes, pode escalar o número de nós para cima ou para baixo para satisfazer as exigências. Esta abordagem de boas práticas para autoscalcificar permite-lhe executar apenas o número de nós necessários e minimiza os custos.

Os ambientes kubernetes, em AKS ou em qualquer outro lugar, não são completamente seguros para o uso hostil de multi-inquilinos. Num ambiente multi-inquilinos, vários inquilinos estão trabalhando numa infraestrutura comum e partilhada. Como resultado, se não for de confiança todos os inquilinos, é necessário fazer planos adicionais para evitar que um inquilino afete a segurança e o serviço de outro. Funcionalidades de segurança adicionais, como a Política de Segurança do *Pod* e os controlos de acesso baseados em funções mais finos (RBAC) para nós dificultam as explorações. No entanto, para uma verdadeira segurança quando se executam cargas de trabalho hostis de multi-inquilinos, um hipervisor é o único nível de segurança em que se deve confiar. O domínio de segurança de Kubernetes torna-se todo o cluster, não um nó individual. Para este tipo de cargas de trabalho hostis multi-inquilinos, você deve usar clusters fisicamente isolados.

## <a name="physically-isolate-clusters"></a>Agrupamentos fisicamente isolados

**Orientação das melhores práticas** - Minimize o uso do isolamento físico para cada equipa ou implementação de aplicações separadas. Em vez disso, use o isolamento *lógico,* como discutido na secção anterior.

Uma abordagem comum para o isolamento do cluster é usar clusters AKS fisicamente separados. Neste modelo de isolamento, as equipas ou cargas de trabalho são atribuídos ao seu próprio cluster AKS. Esta abordagem muitas vezes parece ser a forma mais fácil de isolar cargas de trabalho ou equipas, mas adiciona gestão adicional e despesas financeiras. Agora tem de manter estes múltiplos clusters e tem de fornecer individualmente acesso e atribuir permissões. Também é cobrado por todos os nós individuais.

![Isolamento físico de clusters kubernetes individuais em AKS](media/operator-best-practices-cluster-isolation/physical-isolation.png)

Os aglomerados fisicamente separados geralmente têm uma baixa densidade de pod. Como cada equipa ou carga de trabalho tem o seu próprio cluster AKS, o cluster é muitas vezes sobre-aprovisionado com recursos computacionais. Muitas vezes, um pequeno número de cápsulas são programadas nesses nós. A capacidade não utilizada nos nódosos não pode ser usada para aplicações ou serviços em desenvolvimento por outras equipas. Estes recursos excedentários contribuem para os custos adicionais em clusters fisicamente separados.

## <a name="next-steps"></a>Passos seguintes

Este artigo focou-se no isolamento do agrupamento. Para obter mais informações sobre operações de cluster no AKS, consulte as seguintes boas práticas:

* [Funcionalidades básicas do programador Kubernetes][aks-best-practices-scheduler]
* [Funcionalidades avançadas do programador kubernetes][aks-best-practices-advanced-scheduler]
* [Autenticação e autorização][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->

<!-- INTERNAL LINKS -->
[k8s-namespaces]: concepts-clusters-workloads.md#namespaces
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-identity]: operator-best-practices-identity.md
