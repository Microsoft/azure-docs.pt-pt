---
title: Melhores práticas de isolamento de cluster
titleSuffix: Azure Kubernetes Service
description: Conheça as melhores práticas do operador de cluster para isolamento no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: cdeecabf569e3c6f9b280e6b0179e5378f5b1c95
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88003114"
---
# <a name="best-practices-for-cluster-isolation-in-azure-kubernetes-service-aks"></a>Melhores práticas para isolamento de clusters no Serviço Azure Kubernetes (AKS)

À medida que gere clusters no Serviço Azure Kubernetes (AKS), muitas vezes precisa isolar equipas e cargas de trabalho. A AKS proporciona flexibilidade na forma como pode gerir clusters multi-inquilinos e isolar recursos. Para maximizar o seu investimento em Kubernetes, estas características de multi-arrendamento e isolamento devem ser compreendidas e implementadas.

Este artigo de boas práticas centra-se no isolamento dos operadores de clusters. Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Plano para aglomerados multi-inquilinos e separação de recursos
> * Use isolamento lógico ou físico nos seus clusters AKS

## <a name="design-clusters-for-multi-tenancy"></a>Clusters de design para multi-arrendamento

O Kubernetes fornece funcionalidades que lhe permitem isolar logicamente equipas e cargas de trabalho no mesmo cluster. O objetivo deve ser fornecer o menor número de privilégios, orientados para os recursos necessários para cada equipa. Um [espaço de nome][k8s-namespaces] em Kubernetes cria um limite de isolamento lógico. As características e considerações adicionais de Kubernetes para isolamento e multi-arrendamento incluem as seguintes áreas:

* **O agendamento** inclui a utilização de funcionalidades básicas, tais como quotas de recursos e orçamentos de interrupção de cápsulas. Para obter mais informações sobre estas [funcionalidades, consulte as melhores práticas para funcionalidades básicas de programador em AKS][aks-best-practices-scheduler].
  * As funcionalidades de programador mais avançadas incluem manchas e tolerâncias, seletores de nó, e afinidade de nó e pod ou anti-afinidade. Para obter mais informações sobre estas [funcionalidades, consulte as melhores práticas para funcionalidades avançadas de programadores em AKS][aks-best-practices-advanced-scheduler].
* **A rede** inclui a utilização de políticas de rede para controlar o fluxo de tráfego dentro e fora das cápsulas.
* **A autenticação e autorização** incluem o utilizador do controlo de acesso baseado em funções (RBAC) e a integração do Azure Ative Directory (AD), identidades de pod e segredos no Cofre da Chave Azure. Para obter mais informações sobre estas [funcionalidades, consulte as melhores práticas de autenticação e autorização em AKS.][aks-best-practices-identity]
* **Os contentores** incluem o Add-on de Política Azure para a AKS para impor a segurança do casulo, o uso de contextos de segurança do pod e a digitalização de imagens e o tempo de execução de vulnerabilidades. Também envolve a utilização de App Armor ou Seccomp (Secure Computing) para restringir o acesso do contentor ao nó subjacente.

## <a name="logically-isolate-clusters"></a>Isolar logicamente aglomerados

**Orientação de boas práticas** - Use o isolamento lógico para separar equipas e projetos. Tente minimizar o número de clusters AKS físicos que implementa para isolar equipas ou aplicações.

Com isolamento lógico, um único cluster AKS pode ser usado para várias cargas de trabalho, equipas ou ambientes. Kubernetes [Namespaces][k8s-namespaces] formam o limite de isolamento lógico para cargas de trabalho e recursos.

![Isolamento lógico de um aglomerado de Kubernetes em AKS](media/operator-best-practices-cluster-isolation/logical-isolation.png)

A separação lógica dos aglomerados geralmente proporciona uma densidade de vagem mais alta do que os aglomerados fisicamente isolados. Há menos capacidade de computação em excesso que fica inativo no cluster. Quando combinado com o autoescalador de cluster Kubernetes, pode escalar o número de nós para cima ou para baixo para satisfazer as exigências. Esta abordagem de boas práticas para a autoscalagem permite-lhe executar apenas o número de nós necessários e minimiza os custos.

Os ambientes kubernetes, em AKS ou em qualquer outro lugar, não são completamente seguros para uso hostil de vários inquilinos. Num ambiente multi-inquilino, vários inquilinos estão trabalhando numa infraestrutura comum e partilhada. Como resultado, se não é de confiança a todos os inquilinos, você precisa fazer um planeamento adicional para evitar que um inquilino impacte a segurança e o serviço de outro. Funcionalidades de segurança adicionais, como a Política de *Segurança do Pod* e o controlo de acesso baseado em papéis mais finos (RBAC) para os nós dificultam as explorações. No entanto, para uma verdadeira segurança ao executar cargas de trabalho hostis de vários inquilinos, um hipervisor é o único nível de segurança em que deve confiar. O domínio de segurança de Kubernetes torna-se todo o cluster, não um nó individual. Para este tipo de cargas de trabalho hostis multi-inquilinos, você deve usar aglomerados fisicamente isolados.

## <a name="physically-isolate-clusters"></a>Aglomerados fisicamente isolados

**Orientação para as melhores práticas** - Minimize o uso do isolamento físico para cada equipa ou implementação de aplicações separadas. Em vez disso, utilize o isolamento *lógico,* como discutido na secção anterior.

Uma abordagem comum ao isolamento do cluster é usar aglomerados AKS fisicamente separados. Neste modelo de isolamento, as equipas ou cargas de trabalho são atribuídas ao seu próprio cluster AKS. Esta abordagem parece muitas vezes a forma mais fácil de isolar cargas de trabalho ou equipas, mas adiciona uma gestão adicional e despesas financeiras. Agora tem que manter estes múltiplos clusters, e tem que fornecer individualmente acesso e atribuir permissões. Também é cobrado por todos os nós individuais.

![Isolamento físico de aglomerados individuais de Kubernetes em AKS](media/operator-best-practices-cluster-isolation/physical-isolation.png)

Os aglomerados fisicamente separados geralmente têm uma baixa densidade de vagem. Como cada equipa ou carga de trabalho tem o seu próprio cluster AKS, o cluster é muitas vezes sobre-a provisionado com recursos computativos. Muitas vezes, um pequeno número de cápsulas são programadas nesses nós. A capacidade não utilizada nos nós não pode ser utilizada para aplicações ou serviços em desenvolvimento por outras equipas. Estes recursos excedentários contribuem para os custos adicionais em agrupamentos fisicamente separados.

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
