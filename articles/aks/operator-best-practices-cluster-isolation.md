---
title: Melhores práticas de isolamento de cluster
titleSuffix: Azure Kubernetes Service
description: Conheça as melhores práticas do operador de cluster para isolamento no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: conceptual
ms.date: 03/09/2021
ms.openlocfilehash: e51689d33711f127f775c63c9d7fc8ad4c901604
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105175"
---
# <a name="best-practices-for-cluster-isolation-in-azure-kubernetes-service-aks"></a>Melhores práticas para isolamento de clusters no Serviço Azure Kubernetes (AKS)

À medida que gere clusters no Serviço Azure Kubernetes (AKS), muitas vezes precisa isolar equipas e cargas de trabalho. A AKS proporciona flexibilidade na forma como pode gerir clusters multi-inquilinos e isolar recursos. Para maximizar o seu investimento em Kubernetes, primeiro compreenda e implemente funcionalidades de multi-arrendamento e isolamento AKS.

Este artigo de boas práticas centra-se no isolamento dos operadores de clusters. Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Plano para aglomerados multi-inquilinos e separação de recursos
> * Use isolamento lógico ou físico nos seus clusters AKS

## <a name="design-clusters-for-multi-tenancy"></a>Clusters de design para multi-arrendamento

Kubernetes permite-lhe isolar logicamente equipas e cargas de trabalho no mesmo cluster. O objetivo é fornecer o menor número de privilégios, a visão dos recursos de que cada equipa necessita. Um Espaço [de Nome][k8s-namespaces] Kubernetes cria um limite de isolamento lógico. As características e considerações adicionais de Kubernetes para isolamento e multi-arrendamento incluem as seguintes áreas:

### <a name="scheduling"></a>Agendamento

*O agendamento* utiliza funcionalidades básicas, tais como quotas de recursos e orçamentos de interrupção de cápsulas. Para obter mais informações sobre estas [funcionalidades, consulte as melhores práticas para funcionalidades básicas de programador em AKS][aks-best-practices-scheduler].

As funcionalidades mais avançadas do programador incluem:
* Manchas e tolerâncias
* Seletores de nó
* Nó e pod afinidade ou anti-afinidade. 

Para obter mais informações sobre estas [funcionalidades, consulte as melhores práticas para funcionalidades avançadas de programadores em AKS][aks-best-practices-advanced-scheduler].

### <a name="networking"></a>Rede

*A rede* utiliza políticas de rede para controlar o fluxo de tráfego dentro e fora das cápsulas.

### <a name="authentication-and-authorization"></a>Autenticação e autorização

*Utilizações de autenticação e autorização:*
* Controlo de acesso baseado em funções (RBAC)
* Integração do Diretório Ativo Azure (AD)
* Identidades de pod
* Segredos no Cofre da Chave Azure 

Para obter mais informações sobre estas [funcionalidades, consulte as melhores práticas de autenticação e autorização em AKS.][aks-best-practices-identity]

### <a name="containers"></a>Contentores
*Os contentores* incluem:
* O Azure Policy Add-on para a AKS para impor a segurança da cápsula.
* O uso de contextos de segurança de pod.
* Digitalizar ambas as imagens e o tempo de execução para vulnerabilidades. 
* Utilizando a App Armor ou Seccomp (Secure Computing) para restringir o acesso do contentor ao nó subjacente.

## <a name="logically-isolate-clusters"></a>Isolar logicamente aglomerados

> **Orientação de melhor prática**
>
> Equipas e projetos separados utilizando *o isolamento lógico.* Minimize o número de clusters AKS físicos que implementa para isolar equipas ou aplicações.

Com isolamento lógico, um único cluster AKS pode ser usado para várias cargas de trabalho, equipas ou ambientes. Kubernetes [Namespaces][k8s-namespaces] formam o limite de isolamento lógico para cargas de trabalho e recursos.

![Isolamento lógico de um aglomerado de Kubernetes em AKS](media/operator-best-practices-cluster-isolation/logical-isolation.png)

A separação lógica dos clusters geralmente proporciona uma densidade de vagem mais alta do que os aglomerados fisicamente isolados, com menos capacidade de computação em excesso sentada inativa no cluster. Quando combinado com o autoescalador de cluster Kubernetes, pode escalar o número de nós para cima ou para baixo para satisfazer as exigências. Esta abordagem de boas práticas para a autoscalagem minimiza os custos executando apenas o número de nós necessários.

Atualmente, os ambientes de Kubernetes não são completamente seguros para o uso hostil de vários inquilinos. Num ambiente multi-inquilino, vários inquilinos estão trabalhando numa infraestrutura comum e partilhada. Se não é de confiança a todos os inquilinos, você precisará de um planeamento extra para evitar que os inquilinos impactem a segurança e o serviço de outros.

Funcionalidades de segurança adicionais, como *Pod Security Policies* ou Kubernetes RBAC para nós, bloqueiam eficientemente as explorações. Para uma verdadeira segurança quando se executa cargas de trabalho hostis de vários inquilinos, só se deve confiar num hipervisor. O domínio de segurança de Kubernetes torna-se todo o cluster, não um nó individual. 

Para este tipo de cargas de trabalho hostis multi-inquilinos, você deve usar aglomerados fisicamente isolados.

## <a name="physically-isolate-clusters"></a>Aglomerados fisicamente isolados

> **Orientação de melhor prática** 
>
> Minimizar a utilização do isolamento físico para cada equipa ou implementação de aplicações separadas. Em vez disso, utilize o isolamento *lógico,* como discutido na secção anterior.

Separar fisicamente os aglomerados AKS é uma abordagem comum ao isolamento do cluster. Neste modelo de isolamento, as equipas ou cargas de trabalho são atribuídas ao seu próprio cluster AKS. Embora o isolamento físico possa parecer a forma mais fácil de isolar cargas de trabalho ou equipas, acrescenta gestão e despesas financeiras. Agora, você deve manter estes múltiplos clusters e individualmente fornecer acesso e atribuir permissões. Também será cobrado por cada nó individual.

![Isolamento físico de aglomerados individuais de Kubernetes em AKS](media/operator-best-practices-cluster-isolation/physical-isolation.png)

Os aglomerados fisicamente separados geralmente têm uma baixa densidade de vagem. Uma vez que cada equipa ou carga de trabalho tem o seu próprio cluster AKS, o cluster é muitas vezes sobre-a provisionado com recursos computativos. Muitas vezes, um pequeno número de cápsulas são programadas nesses nós. A capacidade do nó não reclamado não pode ser utilizada para aplicações ou serviços em desenvolvimento por outras equipas. Estes recursos excedentários contribuem para os custos adicionais em agrupamentos fisicamente separados.

## <a name="next-steps"></a>Passos seguintes

Este artigo focou-se no isolamento do aglomerado. Para obter mais informações sobre as operações de cluster em AKS, consulte as seguintes boas práticas:

* [Funcionalidades básicas do programador Kubernetes][aks-best-practices-scheduler]
* [Recursos avançados do programador Kubernetes][aks-best-practices-advanced-scheduler]
* [Autenticação e autorização][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->

<!-- INTERNAL LINKS -->
[k8s-namespaces]: concepts-clusters-workloads.md#namespaces
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-identity]: operator-best-practices-identity.md
