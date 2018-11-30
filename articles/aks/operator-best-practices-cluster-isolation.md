---
title: Práticas recomendadas do operador – o isolamento de Cluster nos serviços do Azure Kubernetes (AKS)
description: Conheça as práticas recomendadas de operador de cluster para isolamento no Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: iainfou
ms.openlocfilehash: 3a4b62fb16745a3b226bda6c0574812278a34456
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/27/2018
ms.locfileid: "52428733"
---
# <a name="best-practices-for-cluster-isolation-in-azure-kubernetes-service-aks"></a>Melhores práticas para isolamento de cluster no Azure Kubernetes Service (AKS)

Como gerir clusters no Azure Kubernetes Service (AKS), precisa, muitas vezes isolar cargas de trabalho e as equipes. AKS fornece flexibilidade em como pode executar clusters de vários inquilinos e isolar os recursos. Para maximizar seus investimentos no Kubernetes, estas funcionalidades de isolamento de vários inquilinos e devem ser compreendidas e implementadas.

Este artigo de melhores práticas que se concentra em isolamento para os operadores de cluster. Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Planear clusters do multi-inquilinos e a separação de recursos
> * Utilizar o isolamento lógico ou físico no seus clusters do AKS

## <a name="design-clusters-for-multi-tenancy"></a>Clusters de conceção para multi-inquilinos

O Kubernetes dispõe de funcionalidades que lhe permitem logicamente isolar as equipes e cargas de trabalho no mesmo cluster. O objetivo deve ser para fornecer o menor número de privilégios, um âmbito para os recursos de cada equipe precisa. R [espaço de nomes] [ k8s-namespaces] no Kubernetes cria um limite de isolamento lógico. Recursos do kubernetes adicionais e considerações de isolamento e de vários inquilinos incluem as seguintes áreas:

* **Agendamento** inclui a utilização de funcionalidades básicas, tais como quotas de recursos e os orçamentos de interrupção de pod. Para obter mais informações sobre estas funcionalidades, consulte [melhores práticas para as funcionalidades básicas do scheduler no AKS][aks-best-practices-scheduler].
  * Recursos mais avançados do agendador incluem taints e tolerations, seletores de nó e afinidade de nó e pod ou antiafinidade. Para obter mais informações sobre estas funcionalidades, consulte [melhores práticas para as funcionalidades avançadas do scheduler no AKS][aks-best-practices-advanced-scheduler].
* **Funcionamento em rede** inclui a utilização de políticas de rede para controlar o fluxo de tráfego de entrada e saída de pods.
* **Autenticação e autorização** incluem o utilizador de controlo de acesso baseado em funções (RBAC) e integração do Azure Active Directory (AD), as identidades de pod e segredos no Azure Key Vault. Para obter mais informações sobre estas funcionalidades, consulte [melhores práticas para autenticação e autorização no AKS][aks-best-practices-identity].
* **Contentores** incluem as políticas de segurança de pod, contextos de segurança de pod, análise de imagens e tempos de execução de vulnerabilidades. Também envolve a utilização de armadura de aplicação ou Seccomp (computação segura) para restringir o acesso do contentor para o nó subjacente.

## <a name="logically-isolate-clusters"></a>Isolar logicamente clusters

**Melhores diretrizes de práticas** -utilize isolação lógica para separar as equipes e projetos. Tentar minimizar o número de clusters físicos do AKS implementa para isolar as equipes ou aplicações.

Com o isolamento lógico, um único cluster AKS pode ser utilizado para várias cargas de trabalho, as equipes ou ambientes. Kubernetes [espaços de nomes] [ k8s-namespaces] formam o limite de isolamento lógico para cargas de trabalho e recursos.

![Isolamento lógico de um cluster de Kubernetes no AKS](media/operator-best-practices-cluster-isolation/logical-isolation.png)

A separação lógica dos clusters, normalmente, fornece uma maior densidade de pod de clusters fisicamente isolados. Há menos excesso de capacidade de computação encontra-se inativo no cluster. Quando combinado com o dimensionamento automático de cluster de Kubernetes, pode aumentar o número de nós de cópia de segurança ou para baixo para atender demandas. Esta abordagem recomendada para dimensionamento automático permite-lhe executar apenas o número de nós necessários e minimiza os custos.

## <a name="physically-isolate-clusters"></a>Isolar fisicamente clusters

**Melhores diretrizes de práticas** -minimizar a utilização de isolamento físico para cada equipa separada ou implementação de aplicação. Em alternativa, utilize *lógico* isolamento, conforme discutido na seção anterior.

Uma abordagem comum para isolamento de cluster é utilizar clusters do AKS fisicamente separados. Neste modelo de isolamento, as equipes ou cargas de trabalho são atribuídas seu próprio cluster do AKS. Essa abordagem, muitas vezes, se parece com a forma mais fácil para isolar cargas de trabalho ou equipes, mas adiciona a gestão adicional e a sobrecarga financeira. Agora precisa manter esses vários clusters e tem de fornecer acesso e atribuir permissões de individualmente. Também é cobrado para todos os nós individuais.

![Clusters de isolamento físico do individuais Kubernetes no AKS](media/operator-best-practices-cluster-isolation/physical-isolation.png)

Normalmente, os clusters separados fisicamente têm uma densidade de pod baixa. Como cada equipe ou a carga de trabalho tem seu próprio cluster do AKS, o cluster é muitas vezes provisionado em excesso com recursos de computação. Muitas vezes, um pequeno número de pods está agendado em nós. Capacidade não utilizada em nós não pode ser utilizada para aplicações ou serviços no desenvolvimento por outras equipes. Estes recursos em excesso contribuem para os custos adicionais em clusters separados fisicamente.

## <a name="next-steps"></a>Passos Seguintes

Este artigo concentra-se no isolamento de cluster. Para obter mais informações sobre as operações de cluster no AKS, consulte as seguintes práticas recomendadas:

* [Funcionalidades de agendador básicas do Kubernetes][aks-best-practices-scheduler]
* [Funcionalidades avançadas de scheduler de Kubernetes][aks-best-practices-advanced-scheduler]
* [Autenticação e autorização][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->

<!-- INTERNAL LINKS -->
[k8s-namespaces]: concepts-clusters-workloads.md#namespaces
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-identity]: operator-best-practices-identity.md
