---
title: Planejar uma implantação de Cluster Service Fabric do Azure | Microsoft Docs
description: Saiba mais sobre planejamento e preparação para uma implantação de cluster de Service Fabric de produção no Azure.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/20/2019
ms.author: atsenthi
ms.openlocfilehash: a130e9bc8859360704c9be1c0a7fe066d2ed4567
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599998"
---
# <a name="plan-and-prepare-for-a-cluster-deployment"></a>Planejar e preparar uma implantação de cluster

O planejamento e a preparação para uma implantação de cluster de produção são muito importantes.  Há muitos fatores a serem considerados.  Este artigo orienta você pelas etapas de preparação de sua implantação de cluster.

## <a name="read-the-best-practices-information"></a>Leia as informações de práticas recomendadas
Para gerenciar aplicativos e clusters do Azure Service Fabric com êxito, há operações que recomendamos que você execute para otimizar a confiabilidade do seu ambiente de produção.  Para obter mais informações, leia [Service Fabric práticas recomendadas de aplicativo e cluster](service-fabric-best-practices-overview.md).

## <a name="select-the-os-for-the-cluster"></a>Selecione o sistema operacional para o cluster
Service Fabric permite a criação de clusters de Service Fabric em qualquer VM ou computadores que executam o Windows Server ou o Linux.  Antes de implantar o cluster, você deve escolher o sistema operacional:  Windows ou Linux.  Cada nó (máquina virtual) no cluster executa o mesmo sistema operacional, não é possível misturar VMs Windows e Linux no mesmo cluster.

## <a name="capacity-planning"></a>Planeamento de capacidade
Para qualquer implantação de produção, o planejamento de capacidade é uma etapa importante. Seguem-se alguns aspetos a considerar como parte do processo.

* O número inicial de tipos de nó para o cluster 
* As propriedades de cada tipo de nó (tamanho, número de instâncias, primária, voltada para a Internet, número de VMs, etc.)
* As características de fiabilidade e durabilidade do cluster

### <a name="select-the-initial-number-of-node-types"></a>Selecionar o número inicial de tipos de nó
Primeiro, você precisa descobrir o que o cluster que está criando será usado para o. Que tipos de aplicativos você pretende implantar nesse cluster? Seu aplicativo tem vários serviços e qualquer um deles precisa ser público ou voltado para a Internet? Seus serviços (que compõem seu aplicativo) têm necessidades de infraestrutura diferentes, como maior RAM ou mais ciclos de CPU? Um Cluster Service Fabric pode consistir em mais de um tipo de nó: um tipo de nó primário e um ou mais tipos de nós não primários. Cada tipo de nó é mapeado para um conjunto de dimensionamento de máquinas virtuais. Cada tipo de nó pode então ser aumentado ou reduzido verticalmente de forma independente, pode ter conjuntos diferentes de portas abertas e ter métricas de capacidade diferente. [As propriedades de nó e as restrições de posicionamento][placementconstraints] podem ser configuradas para restringir serviços específicos a tipos de nó específicos.  Para obter mais informações, leia [o número de tipos de nós dos quais o cluster precisa iniciar](service-fabric-cluster-capacity.md#the-number-of-node-types-your-cluster-needs-to-start-out-with).

### <a name="select-node-properties-for-each-node-type"></a>Selecionar Propriedades de nó para cada tipo de nó
Os tipos de nó definem a SKU, o número e as propriedades da VM no conjunto de dimensionamento associado.

O tamanho mínimo das VMs para cada tipo de nó é determinado pela [camada][durability] de durabilidade que você escolhe para o tipo de nó.

O número mínimo de VMs para o tipo de nó primário é determinado pela [camada de confiabilidade][reliability] que você escolher.

Consulte as recomendações mínimas para [tipos de nós primários](service-fabric-cluster-capacity.md#primary-node-type---capacity-guidance), [cargas de trabalho com estado em tipos de nós não primários](service-fabric-cluster-capacity.md#non-primary-node-type---capacity-guidance-for-stateful-workloads)e [cargas de trabalho sem estado em tipos de nós não primários](service-fabric-cluster-capacity.md#non-primary-node-type---capacity-guidance-for-stateless-workloads). 

Um número mínimo de nós deve ser baseado no número de réplicas do aplicativo/serviços que você deseja executar nesse tipo de nó.  O [planejamento de capacidade para aplicativos Service Fabric](service-fabric-capacity-planning.md) ajuda a estimar os recursos de que você precisa para executar seus aplicativos. Você sempre pode dimensionar o cluster para cima ou para baixo mais tarde para ajustar a carga de trabalho do aplicativo em alteração. 

### <a name="select-the-durability-and-reliability-levels-for-the-cluster"></a>Selecione os níveis de durabilidade e confiabilidade para o cluster
A camada de durabilidade é usada para indicar ao sistema os privilégios que suas VMs têm com a infraestrutura subjacente do Azure. No tipo de nó primário, esse privilégio permite que Service Fabric Pause qualquer solicitação de infraestrutura de nível de VM (como uma reinicialização de VM, reimagem de VM ou migração de VM) que afete os requisitos de quorum para os serviços do sistema e seus serviços com estado. Nos tipos de nó não primários, esse privilégio permite que Service Fabric Pause quaisquer solicitações de infraestrutura de nível de VM (como reinicialização de VM, reimagem de VM e migração de VM) que afetem os requisitos de quorum para seus serviços com estado.  Para obter as vantagens dos diferentes níveis e recomendações sobre qual nível usar e quando, consulte [as características de durabilidade do cluster][durability].

A camada de confiabilidade é usada para definir o número de réplicas dos serviços do sistema que você deseja executar neste cluster no tipo de nó primário. Quanto mais o número de réplicas, mais confiável os serviços do sistema estão no cluster.  Para obter as vantagens dos diferentes níveis e recomendações sobre qual nível usar e quando, consulte [as características de confiabilidade do cluster][reliability]. 

## <a name="enable-reverse-proxy-andor-dns"></a>Habilitar proxy reverso e/ou DNS
Serviços que se conectam entre si dentro de um cluster geralmente podem acessar diretamente os pontos de extremidade de outros serviços, pois os nós em um cluster estão na mesma rede local. Para facilitar a conexão entre os serviços do, Service Fabric fornece serviços adicionais: Um [serviço DNS](service-fabric-dnsservice.md) e um [serviço de proxy reverso](service-fabric-reverseproxy.md).  Ambos os serviços podem ser habilitados durante a implantação de um cluster.

Como muitos serviços, especialmente serviços em contêineres, podem ter um nome de URL existente, poder resolvê-los usando o protocolo DNS padrão (em vez do protocolo Serviço de Nomenclatura) é conveniente, especialmente em cenários de "desvio e deslocamento" do aplicativo. Isso é exatamente o que o serviço DNS faz. Ele permite mapear nomes DNS para um nome de serviço e, portanto, resolver endereços IP de ponto de extremidade.

O proxy reverso lida com os serviços no cluster que expõem pontos de extremidade HTTP (incluindo HTTPS). O proxy reverso simplifica muito a chamada de outros serviços, fornecendo um formato de URI específico.  O proxy reverso também lida com as etapas de resolução, conexão e repetição necessárias para que um serviço se comunique com outro.

## <a name="prepare-for-disaster-recovery"></a>Preparar para a recuperação após desastre
Uma parte crítica da entrega de alta disponibilidade é garantir que os serviços possam sobreviver a todos os diferentes tipos de falhas. Isso é especialmente importante para falhas não planejadas e fora do seu controle. [Preparar para a recuperação de desastres](service-fabric-disaster-recovery.md) descreve alguns modos de falha comuns que poderiam ser desastres se não forem modelados e gerenciados corretamente. Ele também aborda atenuações e ações a serem tomadas se um desastre ocorrer mesmo assim.

## <a name="production-readiness-checklist"></a>Lista de verificação da preparação de produção
Seu aplicativo e cluster estão prontos para fazer o tráfego de produção? Antes de implantar o cluster para produção, execute a [lista de verificação de preparação de produção](service-fabric-production-readiness-checklist.md). Mantenha seu aplicativo e o cluster em execução sem problemas trabalhando nos itens desta lista de verificação. É altamente recomendável que todos esses itens sejam verificados antes de entrar em produção.

## <a name="next-steps"></a>Passos Seguintes
* [Criar um Cluster Service Fabric executando o Windows](service-fabric-best-practices-overview.md)
* [Criar um Cluster Service Fabric executando o Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md)

[placementconstraints]: service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints
[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster