---
title: Casos de contentores e orquestração de contentores
description: Entenda como os casos de contentores Azure interagem com os orquestradores de contentores.
ms.topic: article
ms.date: 04/15/2019
ms.custom: mvc
ms.openlocfilehash: c7faeecfcc3a1d006cc923b48339a1242a0b9e9b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92148590"
---
# <a name="azure-container-instances-and-container-orchestrators"></a>Instâncias de contentores de Azure e orquestradores de contentores

Devido ao seu pequeno tamanho e orientação de aplicação, os recipientes são adequados para ambientes de entrega ágeis e arquiteturas baseadas em microservices. A tarefa de automatizar e gerir um grande número de contentores e como interagem é conhecida como *orquestração.* Os populares orquestradores de contentores incluem Kubernetes, DC/OS e Docker Swarm.

Azure Container Instances fornece algumas das capacidades básicas de agendamento das plataformas de orquestração. E embora não cubra os serviços de maior valor que essas plataformas fornecem, as Instâncias de Contentores Azure podem ser complementares a eles. Este artigo descreve o âmbito do que a Azure Container Instances lida e como os orquestradores de contentores completos podem interagir com ele.

## <a name="traditional-orchestration"></a>Orquestração tradicional

A definição padrão de orquestração inclui as seguintes tarefas:

- **Agendamento**: Dada a imagem do recipiente e um pedido de recurso, encontre uma máquina adequada para executar o recipiente.
- **Afinidade/Anti-afinidade**: Especifique que um conjunto de recipientes deve circular nas proximidades (para desempenho) ou suficientemente distante (para disponibilidade).
- **Monitorização da saúde**: Atenção às falhas do contentor e reprograma-as automaticamente.
- **Failover**: Mantenha-se atento ao que está a funcionar em cada máquina e reprograme os recipientes das máquinas falhadas para os nós saudáveis.
- **Dimensionamento**: Adicione ou remova as instâncias do recipiente para corresponder à procura, manualmente ou automaticamente.
- **Rede**: Forneça uma rede de sobreposição para coordenar os recipientes para comunicar através de várias máquinas hospedeiras.
- **Descoberta do serviço**: Permitir que os recipientes se localizem automaticamente, mesmo quando se deslocam entre máquinas hospedeiras e mudam os endereços IP.
- **Atualizações coordenadas da aplicação**: Gerencie as atualizações dos contentores para evitar o tempo de inatividade da aplicação e permita a reversão se algo correr mal.

## <a name="orchestration-with-azure-container-instances-a-layered-approach"></a>Orquestração com Instâncias de Contentores Azure: Uma abordagem em camadas

Azure Container Instances permite uma abordagem em camadas à orquestração, fornecendo todas as capacidades de agendamento e gestão necessárias para executar um único recipiente, ao mesmo tempo que permite que as plataformas de orquestradores gerem tarefas multi-contentores em cima dele.

Uma vez que a infraestrutura subjacente a casos de contentores é gerida pela Azure, uma plataforma de orquestrador não precisa de se preocupar em encontrar uma máquina hospedeira adequada para executar um único contentor. A elasticidade da nuvem garante que uma esteja sempre disponível. Em vez disso, o orquestrador pode focar-se nas tarefas que simplificam o desenvolvimento de arquiteturas multi-contentores, incluindo escalas e melhorias coordenadas.

## <a name="scenarios"></a>Cenários

Embora a integração do orquestrador com a Azure Container Instances ainda seja nascente, prevemos que surjam alguns ambientes diferentes:

### <a name="orchestration-of-container-instances-exclusively"></a>Orquestração de instâncias de contentores exclusivamente

Porque eles começam rapidamente e faturam a cada segundo, um ambiente baseado exclusivamente em Azure Container Instances oferece a maneira mais rápida de começar e lidar com cargas de trabalho altamente variáveis.

### <a name="combination-of-container-instances-and-containers-in-virtual-machines"></a>Combinação de instâncias e contentores de contentores em máquinas virtuais

Para cargas de trabalho longas e estáveis, orquestrar contentores num conjunto de máquinas virtuais dedicadas é tipicamente mais barato do que executar os mesmos recipientes com instâncias de contentores Azure. No entanto, os casos de contentores oferecem uma ótima solução para expandir e contrair rapidamente a sua capacidade global para lidar com picos inesperados ou de curta duração no uso.

Em vez de escalonar o número de máquinas virtuais no seu agrupamento, e depois colocar recipientes adicionais nessas máquinas, o orquestrador pode simplesmente agendar os recipientes adicionais em Instâncias de Contentores Azure, e apagá-los quando já não são necessários.

## <a name="sample-implementation-virtual-nodes-for-azure-kubernetes-service-aks"></a>Implementação da amostra: nós virtuais para o Serviço Azure Kubernetes (AKS)

Para escalar rapidamente as cargas de trabalho de aplicação num cluster [Azure Kubernetes Service](../aks/intro-kubernetes.md) (AKS), pode utilizar *nós virtuais criados* dinamicamente em Instâncias de Contentores Azure. Os nós virtuais permitem a comunicação de rede entre as cápsulas que funcionam no ACI e no cluster AKS. 

Os nós virtuais suportam atualmente as instâncias do contentor Linux. Começa com nós virtuais utilizando o [portal Azure CLI](../aks/virtual-nodes-cli.md) ou [Azure](../aks/virtual-nodes-portal.md).

Os nós virtuais usam o [Kubelet Virtual de][aci-connector-k8s] código aberto para imitar o [kubelet][kubelet-doc] Kubernetes, registando-se como um nó com capacidade ilimitada. O Kubelet Virtual despacha a criação de cápsulas como [grupos][pod-doc] de contentores em Instâncias de Contentores Azure.

Consulte o projeto [Virtual Kubelet](https://github.com/virtual-kubelet/virtual-kubelet) para obter exemplos adicionais de extensão da API de Kubernetes em plataformas de contentores sem servidor.

## <a name="next-steps"></a>Passos seguintes

Crie o seu primeiro recipiente com instâncias do recipiente Azure utilizando o [guia de arranque rápido](container-instances-quickstart.md).

<!-- IMAGES -->

<!-- LINKS -->
[aci-connector-k8s]: https://github.com/virtual-kubelet/azure-aci
[kubelet-doc]: https://kubernetes.io/docs/admin/kubelet/
[pod-doc]: https://kubernetes.io/docs/concepts/workloads/pods/pod/