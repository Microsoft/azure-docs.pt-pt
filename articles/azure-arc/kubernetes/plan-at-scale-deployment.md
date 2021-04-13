---
title: Como planear e implantar o Arco Azure habilitado kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 04/12/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: A bordo um grande número de clusters para Azure Arc permitiu kubernetes para gestão de configuração
ms.openlocfilehash: 4b34cee08db508728f01d262ae4b1ee4ed4754e1
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315492"
---
# <a name="plan-and-deploy-azure-arc-enabled-kubernetes"></a>Plano e implantação do Arco Azure habilitado Kubernetes

A implantação de um serviço de infraestrutura de TI ou de uma aplicação de negócios é um desafio para qualquer empresa. Para evitar surpresas indesejáveis ou custos não planeados, precisa de planear cuidadosamente para que esteja o mais pronto possível. Este plano deve identificar critérios de conceção e de implantação que devem ser cumpridos para completar as tarefas.

Para que a implantação continue sem problemas, o seu plano deve estabelecer uma compreensão clara de:

* Papéis e responsabilidades.
* Inventário de todos os clusters Kubernetes
* Satisfaça os requisitos de networking.
* O conjunto de competências e a formação necessárias para permitir a implementação e gestão em curso com sucesso.
* Critérios de aceitação e como acompanha o seu sucesso.
* Ferramentas ou métodos a utilizar para automatizar as implementações.
* Riscos identificados e planos de mitigação para evitar atrasos e perturbações.
* Como evitar perturbações durante a colocação.
* Qual é o caminho da escalada quando ocorre um problema significativo?

O objetivo deste artigo é garantir que está preparado para uma implementação bem sucedida do Azure Arc, ativando Kubernetes em vários clusters de produção no seu ambiente.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Kubernetes existente. Se não tiver um, pode criar um cluster usando uma destas opções:
    - [Kubernetes em Docker (KIND)](https://kind.sigs.k8s.io/)
    - Crie um cluster Kubernetes usando Docker para [Mac](https://docs.docker.com/docker-for-mac/#kubernetes) ou [Windows](https://docs.docker.com/docker-for-windows/#kubernetes)
    - Cluster Kubernetes auto-gerido usando [Cluster API](https://cluster-api.sigs.k8s.io/user/quick-start.html)

* As suas máquinas têm conectividade desde a sua rede no local ou outro ambiente em nuvem até recursos em Azure, quer diretamente quer através de um servidor proxy. Mais detalhes podem ser encontrados em [pré-requisitos de rede](quickstart-connect-cluster.md#meet-network-requirements).

* Um `kubeconfig` ficheiro que aponta para o cluster que pretende ligar ao Arco Azure.
* Permissões de 'Ler' e 'Escrever' para o utilizador ou principal de serviço que cria o Arco Azure permitiu o tipo de recurso Kubernetes `Microsoft.Kubernetes/connectedClusters` de .

## <a name="pilot"></a>Piloto

Antes de implantar em todos os clusters de produção, comece por avaliar este processo de implantação antes de adotá-lo amplamente no seu ambiente. Para um piloto, identifique uma amostra representativa de clusters que não sejam críticos para a capacidade das suas empresas de realizarem negócios. Você vai querer ter certeza de dar tempo suficiente para executar o piloto e avaliar o seu impacto: recomendamos aproximadamente 30 dias.

Estabeleça um plano formal que descreva o âmbito e os detalhes do piloto. O seguinte plano de amostra deve ajudá-lo a começar.

* **Objetivos** - Descreve os condutores empresariais e técnicos que levaram à decisão de que um piloto é necessário.
* **Critérios de seleção** - Especifica os critérios utilizados para selecionar quais os aspetos da solução que serão demonstrados através de um piloto.
* **Âmbito** - Abrange componentes de solução, horário esperado, duração do piloto e número de clusters a destino.
* **Critérios e métricas** de sucesso - Definir os critérios de sucesso do piloto e as medidas específicas utilizadas para determinar o nível de sucesso.
* **Plano de formação** - Descreve o plano para engenheiros de sistemas de formação, administradores, etc. que são novos no Azure e que prestam serviços durante o piloto.
* **Plano de transição** - Descreve a estratégia e os critérios utilizados para orientar a transição de piloto para produção.
* **Reversão** - Descreve os procedimentos para reverter um piloto para estado de pré-implantação.
* **Riscos** - Enumerar todos os riscos identificados para a realização do piloto e associados à implantação da produção.

## <a name="phase-1-build-a-foundation"></a>Fase 1: Construir uma fundação

Nesta fase, os engenheiros ou administradores de sistemas realizam as atividades fundamentais tais como a criação de grupos de recursos, tags, atribuições de funções para que o Arco Azure habilitado recursos Kubernetes possa então ser criado e operado.

|Tarefa |Detalhes |Duração |
|-----|-------|---------|
| [Criar um grupo de recursos](../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) | Um grupo de recursos dedicado para incluir apenas recursos da Azure Arc permitiu recursos da Kubernetes e centralizar a gestão e monitorização destes recursos. | Uma hora |
| Aplicar [Tags](../../azure-resource-manager/management/tag-resources.md) para ajudar a organizar máquinas. | Avaliar e desenvolver uma estratégia de [marcação](/azure/cloud-adoption-framework/decision-guides/resource-tagging/)alinhada com as TI. Isto pode ajudar a reduzir a complexidade de gerir o seu Arco Azure permitiu recursos da Kubernetes e simplificar as decisões de gestão. | Um dia |
| Identificar [configurações](tutorial-use-gitops-connected-cluster.md) para GitOps | Identifique as configurações de aplicação ou linha de base tais `PodSecurityPolicy` como, `NetworkPolicy` que pretende implementar nos seus clusters | Um dia |
| Desenvolver um plano de governação [da Política Azure](../../governance/policy/overview.md) | Determine como implementará a governação do Azure Arc ativado pelos clusters kubernetes no âmbito de subscrição ou grupo de recursos com a Azure Policy. | Um dia |
| Configure [controlo de acesso baseado em funções](../../role-based-access-control/overview.md) (RBAC) | Desenvolver um plano de acesso para identificar quem leu/escreveu/todas as permissões nos seus clusters | Um dia |

## <a name="phase-2-deploy-azure-arc-enabled-kubernetes"></a>Fase 2: Implantar o Arco azul ativado kubernetes

Nesta fase, ligamos os seus clusters Kubernetes ao Azure:

|Tarefa |Detalhes |Duração |
|-----|-------|---------|
| [Ligue o seu primeiro cluster Kubernetes ao Arco de Azure](quickstart-connect-cluster.md) | Como parte da ligação do seu primeiro cluster ao Arco Azure, crie o seu ambiente de embarque com todas as ferramentas necessárias, tais como Azure CLI, Helm e `connectedk8s` extensão para Azure CLI. | 15 minutos |
| [Criar um principal de serviço](create-onboarding-service-principal.md) | Crie um principal de serviço para ligar clusters Kubernetes não interactivamente usando Azure CLI ou PowerShell. | Uma hora |


## <a name="phase-3-manage-and-operate"></a>Fase 3: Gerir e operar

Nesta fase, implementamos aplicações e configurações de base para os seus clusters Kubernetes.

|Tarefa |Detalhes |Duração |
|-----|-------|---------|
|[Crie configurações](tutorial-use-gitops-connected-cluster.md) nos seus clusters | Crie configurações para implementar as suas aplicações no seu recurso Azure Arc ativado por Kubernetes. | 15 minutos |
|[Utilizar a política Azure](use-azure-policy.md) para aplicação em escala das configurações | Crie atribuições de política para automatizar a implementação de configurações de base em todos os seus clusters sob um âmbito de grupo de subscrição ou recursos. | 15 minutos |
| [Atualizar agentes do Arco Azure](agent-upgrade.md) | Se tiver desativado a atualização automática de agentes nos seus clusters, atualize os seus agentes manualmente para a versão mais recente para se certificar de que tem as correções de segurança e erros mais recentes. | 15 minutos |

## <a name="next-steps"></a>Passos seguintes

* Use o nosso quickstart para [ligar um cluster Kubernetes ao Arco Azure.](./quickstart-connect-cluster.md)
* [Crie configurações](./tutorial-use-gitops-connected-cluster.md) no seu agrupamento de Kubernetes ativado pelo Arco Azure.
* [Utilize a Política Azure para aplicar configurações à escala](./use-azure-policy.md).