---
title: Instâncias de contentores e orquestração de contentores
description: Entenda como as instâncias de contentores Azure interagem com os orquestradores de contentores.
ms.topic: article
ms.date: 04/15/2019
ms.custom: mvc
ms.openlocfilehash: f3f8693d1a9a12e7c35d126ab3e3ca53448e5e40
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74533667"
---
# <a name="azure-container-instances-and-container-orchestrators"></a>Instâncias de contentores azure e orquestradores de contentores

Devido ao seu pequeno tamanho e orientação de aplicação, os recipientes são adequados para ambientes ágeis de entrega e arquiteturas baseadas em microserviços. A tarefa de automatizar e gerir um grande número de contentores e como interagem é conhecida como *orquestração.* Os orquestradores de contentores populares incluem Kubernetes, DC/OS e Docker Swarm.

As Instâncias de Contentores Azure fornecem algumas das capacidades básicas de agendamento das plataformas de orquestração. E embora não cubra os serviços de maior valor que essas plataformas fornecem, as Instâncias de Contentores Azure podem ser complementares a eles. Este artigo descreve o âmbito do que as Instâncias de Contentores Azure manipulam e como os orquestradores de contentores completos podem interagir com ele.

## <a name="traditional-orchestration"></a>Orquestração tradicional

A definição padrão de orquestração inclui as seguintes tarefas:

- **Agendamento**: Dada a imagem do recipiente e um pedido de recurso, encontre uma máquina adequada para executar o recipiente.
- **Afinidade/Anti-afinidade**: Especifique que um conjunto de recipientes deve correr nas proximidades uns dos outros (para desempenho) ou suficientemente distantes (para disponibilidade).
- **Monitorização da saúde**: Tenha cuidado com as falhas dos contentores e remarque-as automaticamente.
- **Failover**: Acompanhe o que está a funcionar em cada máquina e reagene os recipientes de máquinas falhadas para nós saudáveis.
- **Escalação**: Adicione ou remova as instâncias do recipiente para corresponder à procura, manual ou automaticamente.
- **Ligação em rede**: Fornecer uma rede de sobreposição para coordenar os recipientes para comunicar através de várias máquinas hospedeiras.
- **Descoberta do serviço**: Ativar os contentores para se localizarem automaticamente, mesmo que se movam entre máquinas hospedeiras e alterem os endereços IP.
- **Atualizações coordenadas da aplicação:** Gerir atualizações de contentores para evitar o tempo de inatividade da aplicação e ativar a reversão se algo correr mal.

## <a name="orchestration-with-azure-container-instances-a-layered-approach"></a>Orquestração com instâncias de contentores azure: Uma abordagem em camadas

As Instâncias de Contentores Azure permitem uma abordagem em camadas à orquestração, fornecendo todas as capacidades de agendamento e gestão necessárias para executar um único recipiente, permitindo ao mesmo tempo plataformas orquestradoras gerir tarefas multi-contentores em cima dele.

Uma vez que a infraestrutura subjacente aos casos de contentores é gerida pelo Azure, uma plataforma orquestradora não precisa de se preocupar em encontrar uma máquina de acolhimento adequada para executar um único contentor. A elasticidade da nuvem garante que está sempre disponível. Em vez disso, o orquestrador pode focar-se nas tarefas que simplificam o desenvolvimento de arquiteturas multi-contentores, incluindo a escala e atualizações coordenadas.

## <a name="scenarios"></a>Cenários

Embora a integração orquestradora com os casos de contentores azure ainda esteja em ascensão, prevemos que surjam alguns ambientes diferentes:

### <a name="orchestration-of-container-instances-exclusively"></a>Orquestração de instâncias de contentores exclusivamente

Como começam rapidamente e faturam pelo segundo, um ambiente baseado exclusivamente em Casos de Contentores Azure oferece a maneira mais rápida de começar e lidar com cargas de trabalho altamente variáveis.

### <a name="combination-of-container-instances-and-containers-in-virtual-machines"></a>Combinação de instâncias de contentores e contentores em máquinas virtuais

Para cargas de trabalho de longa duração e estáveis, orquestrar contentores num conjunto de máquinas virtuais dedicadas é tipicamente mais barato do que executar os mesmos contentores com instâncias de contentores Azure. No entanto, os casos de contentores oferecem uma ótima solução para expandir e contrair rapidamente a sua capacidade global para lidar com picos inesperados ou de curta duração no uso.

Em vez de aumentar o número de máquinas virtuais no seu cluster, em seguida, colocar recipientes adicionais nessas máquinas, o orquestrador pode simplesmente agendar os recipientes adicionais em Instâncias de Contentores Azure, e eliminá-los quando já não são necessários.

## <a name="sample-implementation-virtual-nodes-for-azure-kubernetes-service-aks"></a>Implementação da amostra: nós virtuais para o Serviço Azure Kubernetes (AKS)

Para escalar rapidamente as cargas de trabalho de aplicação num cluster do [Serviço Azure Kubernetes](../aks/intro-kubernetes.md) (AKS), pode utilizar *nós virtuais criados* dinamicamente em Instâncias de Contentores Azure. Os nós virtuais permitem a comunicação de rede entre as cápsulas que funcionam no ACI e no cluster AKS. 

Os nódosos virtuais suportam atualmente as instâncias dos contentores Linux. Inicie-se com nódosos virtuais utilizando o portal [Azure CLI](https://go.microsoft.com/fwlink/?linkid=2047538) ou [Azure](https://go.microsoft.com/fwlink/?linkid=2047545).

Os nódosos virtuais usam o [Kubelet Virtual][aci-connector-k8s] de código aberto para imitar o [kubelet][kubelet-doc] Kubernetes, registando-se como um nó com capacidade ilimitada. O Kubelet Virtual despacha a criação de cápsulas como [grupos][pod-doc] de contentores em Instâncias de Contentores Azure.

Consulte o projeto [Virtual Kubelet](https://github.com/virtual-kubelet/virtual-kubelet) para exemplos adicionais de extensão da API Kubernetes em plataformas de contentores sem servidores.

## <a name="next-steps"></a>Passos seguintes

Crie o seu primeiro recipiente com instâncias de contentores Azure utilizando o [guia de arranque rápido](container-instances-quickstart.md).

<!-- IMAGES -->

<!-- LINKS -->
[aci-connector-k8s]: https://github.com/virtual-kubelet/azure-aci
[kubelet-doc]: https://kubernetes.io/docs/admin/kubelet/
[pod-doc]: https://kubernetes.io/docs/concepts/workloads/pods/pod/
