---
title: Práticas recomendadas do operador-isolamento de cluster nos serviços Kubernetess do Azure (AKS)
description: Conheça as práticas recomendadas do operador de cluster para isolamento no serviço kubernetes do Azure (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: mlearned
ms.openlocfilehash: e9f7a10f19ed23e4f3b4fefa38fbb2d1912f2ac0
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71348783"
---
# <a name="best-practices-for-cluster-isolation-in-azure-kubernetes-service-aks"></a>Práticas recomendadas para o isolamento de cluster no serviço kubernetes do Azure (AKS)

Ao gerenciar clusters no AKS (serviço kubernetes do Azure), muitas vezes você precisa isolar equipes e cargas de trabalho. O AKS fornece flexibilidade em como você pode executar clusters de vários locatários e isolar recursos. Para maximizar seu investimento no kubernetes, esses recursos de multilocação e de isolamento devem ser compreendidos e implementados.

Este artigo de práticas recomendadas se concentra no isolamento de operadores de cluster. Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Planejar clusters de vários locatários e separação de recursos
> * Usar isolamento lógico ou físico em seus clusters AKS

## <a name="design-clusters-for-multi-tenancy"></a>Clusters de design para multilocação

O kubernetes fornece recursos que permitem isolar logicamente as equipes e cargas de trabalho no mesmo cluster. O objetivo deve ser fornecer o menor número de privilégios, no escopo dos recursos de que cada equipe precisa. Um [namespace][k8s-namespaces] no kubernetes cria um limite de isolamento lógico. Os recursos e considerações adicionais do kubernetes para isolamento e multilocação incluem as seguintes áreas:

* O **agendamento** inclui o uso de recursos básicos, como cotas de recursos e orçamentos de interrupção de Pod. Para obter mais informações sobre esses recursos, consulte [práticas recomendadas para recursos básicos do Agendador no AKs][aks-best-practices-scheduler].
  * Os recursos mais avançados do Agendador incluem os tolerationss, os seletores de nó e a afinidade de nó e Pod ou a antiafinidade. Para obter mais informações sobre esses recursos, consulte [práticas recomendadas para recursos avançados do Agendador no AKs][aks-best-practices-advanced-scheduler].
* A **rede** inclui o uso de políticas de rede para controlar o fluxo de entrada e saída de tráfego de pods.
* **Autenticação e autorização** incluem o usuário de controle de acesso baseado em função (RBAC) e integração de Azure Active Directory (AD), identidades de Pod e segredos no Azure Key Vault. Para obter mais informações sobre esses recursos, consulte [práticas recomendadas para autenticação e autorização no AKs][aks-best-practices-identity].
* Os **contêineres** incluem políticas de segurança de Pod, contextos de segurança Pod, verificação de imagens e tempos de execução para vulnerabilidades. Também envolve o uso de proteção de aplicativo ou Seccomp (computação segura) para restringir o acesso de contêiner ao nó subjacente.

## <a name="logically-isolate-clusters"></a>Isolar logicamente os clusters

**Diretrizes de práticas recomendadas** -use o isolamento lógico para separar equipes e projetos. Tente minimizar o número de clusters físicos de AKS que você implanta para isolar equipes ou aplicativos.

Com o isolamento lógico, um único cluster AKS pode ser usado para várias cargas de trabalho, equipes ou ambientes. Os [namespaces][k8s-namespaces] kubernetes formam o limite de isolamento lógico para cargas de trabalho e recursos.

![Isolamento lógico de um cluster kubernetes no AKS](media/operator-best-practices-cluster-isolation/logical-isolation.png)

A separação lógica de clusters geralmente fornece uma densidade maior de Pod que os clusters isolados fisicamente. Há menos capacidade de computação em excesso que fica ociosa no cluster. Quando combinado com o cluster de kubernetes, você pode dimensionar o número de nós para cima ou para baixo para atender às demandas. Essa abordagem de prática recomendada para dimensionamento automático permite que você execute apenas o número de nós necessários e minimize os custos.

Os ambientes kubernetes, no AKS ou em outro lugar, não são totalmente seguros para o uso hostil de vários locatários. Em um ambiente multilocatário, vários locatários estão trabalhando em uma infraestrutura comum compartilhada. Como resultado, se todos os locatários não puderem ser confiáveis, você precisará fazer um planejamento adicional para evitar que um locatário afete a segurança e o serviço de outro. Recursos de segurança adicionais, como *política de segurança de Pod* e RBAC (controles de acesso baseado em função) mais refinados para nós tornam as explorações mais difíceis. No entanto, para uma verdadeira segurança ao executar cargas de trabalho de multilocatário hostil, um hipervisor é o único nível de segurança que você deve confiar. O domínio de segurança para kubernetes se torna o cluster inteiro, não um nó individual. Para esses tipos de cargas de trabalho de vários locatários hostis, você deve usar clusters isolados fisicamente.

## <a name="physically-isolate-clusters"></a>Isolar fisicamente os clusters

**Diretrizes de práticas recomendadas** -minimize o uso de isolamento físico para cada implantação de aplicativo ou equipe separada. Em vez disso, use o isolamento *lógico* , conforme discutido na seção anterior.

Uma abordagem comum ao isolamento de cluster é usar clusters AKS separados fisicamente. Nesse modelo de isolamento, as equipes ou cargas de trabalho recebem seu próprio cluster AKS. Essa abordagem geralmente se parece com a maneira mais fácil de isolar cargas de trabalho ou equipes, mas adiciona gerenciamento adicional e sobrecarga financeira. Agora você precisa manter esses vários clusters e ter que fornecer acesso individualmente e atribuir permissões. Você também será cobrado por todos os nós individuais.

![Isolamento físico de clusters kubernetes individuais no AKS](media/operator-best-practices-cluster-isolation/physical-isolation.png)

Os clusters separados fisicamente geralmente têm uma densidade de Pod baixo. Como cada equipe ou carga de trabalho tem seu próprio cluster AKS, o cluster é muitas vezes provisionado com recursos de computação. Muitas vezes, um pequeno número de pods é agendado nesses nós. A capacidade não utilizada nos nós não pode ser usada para aplicativos ou serviços em desenvolvimento por outras equipes. Esses recursos em excesso contribuem para os custos adicionais em clusters separados fisicamente.

## <a name="next-steps"></a>Passos seguintes

Este artigo se concentrou no isolamento de cluster. Para obter mais informações sobre as operações de cluster no AKS, consulte as seguintes práticas recomendadas:

* [Recursos básicos do Agendador do kubernetes][aks-best-practices-scheduler]
* [Recursos do Agendador do kubernetes avançado][aks-best-practices-advanced-scheduler]
* [Autenticação e autorização][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->

<!-- INTERNAL LINKS -->
[k8s-namespaces]: concepts-clusters-workloads.md#namespaces
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-identity]: operator-best-practices-identity.md
