---
title: Instâncias de contêiner do Azure e orquestração de contêiner
description: Entenda como as instâncias de contêiner do Azure interagem com orquestradores de contêiner.
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 04/15/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: c83648124f616670423b2ef459530c191d7e17e4
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325765"
---
# <a name="azure-container-instances-and-container-orchestrators"></a>Instâncias de contêiner do Azure e orquestradores de contêiner

Devido à sua pequena orientação de tamanho e aplicativo, os contêineres são adequados para ambientes de entrega ágil e arquiteturas baseadas em microserviço. A tarefa de automatizar e gerenciar um grande número de contêineres e como eles interagem é conhecida como orquestração. Orquestradores de contêiner populares incluem kubernetes, DC/so e Docker Swarm.

As instâncias de contêiner do Azure fornecem alguns dos recursos básicos de agendamento das plataformas de orquestração. E, embora não aborde os serviços de valor mais alto que essas plataformas fornecem, as instâncias de contêiner do Azure podem ser complementares a elas. Este artigo descreve o escopo do que as instâncias de contêiner do Azure trata e como os orquestradores de contêiner completos podem interagir com ela.

## <a name="traditional-orchestration"></a>Orquestração tradicional

A definição padrão da orquestração inclui as seguintes tarefas:

- **Agendamento**: Dada uma imagem de contêiner e uma solicitação de recurso, encontre um computador adequado no qual executar o contêiner.
- **Afinidade/antiafinidade**: Especifique que um conjunto de contêineres deve ser executado próximos um do outro (para desempenho) ou suficientemente distante (para disponibilidade).
- **Monitoramento de integridade**: Verifique se há falhas de contêiner e agende-as automaticamente.
- **Failover**: Acompanhe o que está em execução em cada computador e reagende contêineres de computadores com falha para nós íntegros.
- **Dimensionamento**: Adicionar ou remover instâncias de contêiner para corresponder à demanda, seja manualmente ou automaticamente.
- **Rede**: Forneça uma rede de sobreposição para coordenar contêineres para se comunicarem entre vários computadores host.
- **Descoberta de serviço**: Habilitar contêineres para localizarem um ao outro automaticamente, mesmo quando eles se movem entre computadores host e alteram endereços IP.
- **Atualizações de aplicativo coordenadas**: Gerencie atualizações de contêiner para evitar o tempo de inatividade do aplicativo e habilite a reversão se algo der errado.

## <a name="orchestration-with-azure-container-instances-a-layered-approach"></a>Orquestração com instâncias de contêiner do Azure: Uma abordagem em camadas

As instâncias de contêiner do Azure permitem uma abordagem em camadas para orquestração, fornecendo todos os recursos de agendamento e gerenciamento necessários para executar um único contêiner, permitindo ao mesmo tempo que as plataformas do Orchestrator gerenciem tarefas de vários contêineres sobre ele.

Como a infraestrutura subjacente para instâncias de contêiner é gerenciada pelo Azure, uma plataforma de orquestrador não precisa se preocupar com a localização de um computador host apropriado no qual executar um único contêiner. A elasticidade da nuvem garante que uma esteja sempre disponível. Em vez disso, o orquestrador pode se concentrar nas tarefas que simplificam o desenvolvimento de arquiteturas de vários contêineres, incluindo dimensionamento e atualizações coordenadas.

## <a name="scenarios"></a>Cenários

Embora a integração do Orchestrator com as instâncias de contêiner do Azure ainda seja recente, prevemos que alguns ambientes diferentes surgirão:

### <a name="orchestration-of-container-instances-exclusively"></a>Orquestração de instâncias de contêiner exclusivamente

Como elas começam rapidamente e são cobradas por segundo, um ambiente baseado exclusivamente em instâncias de contêiner do Azure oferece a maneira mais rápida de começar e lidar com cargas de trabalho altamente variáveis.

### <a name="combination-of-container-instances-and-containers-in-virtual-machines"></a>Combinação de instâncias de contêiner e contêineres em máquinas virtuais

Para cargas de trabalho estáveis de longa execução, a orquestração de contêineres em um cluster de máquinas virtuais dedicadas é normalmente mais barata do que executar os mesmos contêineres com instâncias de contêiner do Azure. No entanto, as instâncias de contêiner oferecem uma ótima solução para expandir e contratar rapidamente sua capacidade geral para lidar com picos inesperados ou de curta duração de uso.

Em vez de expandir o número de máquinas virtuais no cluster e, em seguida, implantar contêineres adicionais nessas máquinas, o orquestrador pode simplesmente agendar os contêineres adicionais nas instâncias de contêiner do Azure e excluí-los quando não forem mais precisa.

## <a name="sample-implementation-virtual-nodes-for-azure-kubernetes-service-aks"></a>Exemplo de implementação: nós virtuais para o serviço kubernetes do Azure (AKS)

Para dimensionar rapidamente cargas de trabalho de aplicativos em um cluster AKs ( [serviço kubernetes do Azure](../aks/intro-kubernetes.md) ), você pode usar *nós virtuais* criados dinamicamente em instâncias de contêiner do Azure. Os nós virtuais habilitam a comunicação de rede entre os pods que são executados no ACI e no cluster AKS. 

Nós virtuais atualmente dão suporte a instâncias de contêiner do Linux. Introdução aos nós virtuais usando o [CLI do Azure](https://go.microsoft.com/fwlink/?linkid=2047538) ou [portal do Azure](https://go.microsoft.com/fwlink/?linkid=2047545).

Os nós virtuais usam o [Kubelet][aci-connector-k8s] to mimic the Kubernetes [kubelet][kubelet-doc] virtual de software livre registrando-se como um nó com capacidade ilimitada. O Kubelet virtual despacha a [criação de um][documento Pod-doc] como grupos de contêineres em instâncias de contêiner do Azure.

Consulte o projeto [Kubelet virtual](https://github.com/virtual-kubelet/virtual-kubelet) para obter exemplos adicionais de como estender a API kubernetes em plataformas de contêiner sem servidor.

## <a name="next-steps"></a>Passos Seguintes

Crie seu primeiro contêiner com as instâncias de contêiner do Azure usando o [Guia de início rápido](container-instances-quickstart.md).

<!-- IMAGES -->

<!-- LINKS -->
[aci-connector-k8s]: https://github.com/virtual-kubelet/virtual-kubelet/tree/master/providers/azure
[kubelet-doc]: https://kubernetes.io/docs/admin/kubelet/
[pod-doc]: https://kubernetes.io/docs/concepts/workloads/pods/pod/