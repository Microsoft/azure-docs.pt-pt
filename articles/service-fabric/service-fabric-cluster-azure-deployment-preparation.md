---
title: Planear uma implementação de cluster do Azure Service Fabric | Documentos da Microsoft
description: Saiba mais sobre o planeamento e preparação para uma implementação de cluster do Service Fabric para o Azure de produção.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: aljo
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/20/2019
ms.author: ryanwi
ms.openlocfilehash: d29f9af4201eee41720fe60cc9b7a21abc7fc63d
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58450064"
---
# <a name="plan-and-prepare-for-a-cluster-deployment"></a>Planear e preparar para uma implementação de cluster

Planear e preparar para uma implementação de cluster de produção são muito importante.  Existem muitos fatores a considerar.  Este artigo orienta-o pelos passos de preparação da sua implementação de cluster.

## <a name="read-the-best-practices-information"></a>Ler as informações de melhores práticas
Para gerir clusters e aplicações do Azure Service Fabric com êxito, existem operações que é altamente recomendável que efetuar para otimizar a fiabilidade do seu ambiente de produção.  Para obter mais informações, leia [melhores práticas de aplicação do Service Fabric e o cluster](service-fabric-best-practices-overview.md).

## <a name="select-the-os-for-the-cluster"></a>Selecione o sistema operacional para o cluster
Service Fabric permite a criação de clusters do Service Fabric em quaisquer VMs ou computadores que executam o Windows Server ou Linux.  Antes de implementar o cluster, tem de escolher o sistema operacional:  Windows ou Linux.  Cada nó (máquina virtual) do cluster é executado o mesmo sistema operacional, não é possível misturar o Windows e VMs do Linux no mesmo cluster.

## <a name="capacity-planning"></a>Planeamento de capacidade
Para qualquer implementação de produção, planeamento de capacidade é um passo importante. Seguem-se alguns aspetos a considerar como parte do processo.

* O número inicial de tipos de nó para o seu cluster 
* As propriedades de cada tipo de nó (tamanho, o número de instâncias, primários, de acesso à internet, número de VMs, etc.)
* As características de fiabilidade e durabilidade do cluster

### <a name="select-the-initial-number-of-node-types"></a>Selecione o número inicial de tipos de nó
Em primeiro lugar, terá de descobrir o que o cluster que estiver a criar irá ser utilizado para. Que tipos de aplicativos estiver a planear implementar para este cluster? A aplicação tem vários serviços, e qualquer um deles precisa de ser público ou com acesso à internet? Seus serviços (que tornam a sua aplicação) tem necessidades de infraestrutura diferentes, como o maior de RAM ou superior ciclos de CPU? Um cluster do Service Fabric pode consistir em mais de um tipo de nó: um tipo de nó primário e um ou mais tipos de nó não principal. Cada tipo de nó é mapeado para um conjunto de dimensionamento de máquina virtual. Cada tipo de nó pode então ser aumentado ou reduzido verticalmente de forma independente, pode ter conjuntos diferentes de portas abertas e ter métricas de capacidade diferente. [Propriedades de nó e restrições de posicionamento] [ placementconstraints] podem ser configuradas para restringir os serviços específicos para tipos de nó específico.  Para obter mais informações, leia [o número de tipos de nó do cluster tem de começar com](service-fabric-cluster-capacity.md#the-number-of-node-types-your-cluster-needs-to-start-out-with).

### <a name="select-node-properties-for-each-node-type"></a>Selecionar propriedades de nó para cada tipo de nó
Tipos de nó definem os SKU de VM, número e as propriedades das VMs no conjunto de dimensionamento associados.

O tamanho mínimo de VMs para cada tipo de nó é determinado pelos [escalão de durabilidade] [ durability] que escolher para o tipo de nó.

O número mínimo de VMs para o tipo de nó primário é determinado pelos [escalão de fiabilidade] [ reliability] que escolher.

Ver as recomendações relativas a mínimas [tipos de nó primário](service-fabric-cluster-capacity.md#primary-node-type---capacity-guidance), [cargas de trabalho com monitorização de estado em tipos de nó não primário](service-fabric-cluster-capacity.md#non-primary-node-type---capacity-guidance-for-stateful-workloads), e [cargas de trabalho sem monitorização de estado em tipos de nó não primário](service-fabric-cluster-capacity.md#non-primary-node-type---capacity-guidance-for-stateless-workloads). 

Mais do que o número mínimo de nós devem basear-se no número de réplicas de aplicações/serviços que pretende executar neste tipo de nó.  [Planeamento da capacidade para aplicações do Service Fabric](service-fabric-capacity-planning.md) ajuda a estimar os recursos necessários para executar as suas aplicações. Sempre pode aumentar o cluster ou para baixo mais tarde para ajustar para alterar a carga de trabalho de aplicação. 

### <a name="select-the-durability-and-reliability-levels-for-the-cluster"></a>Selecione os níveis de durabilidade e fiabilidade para o cluster
O escalão de durabilidade é utilizado para indicar ao sistema os privilégios que tem das suas VMs com a infraestrutura do Azure subjacente. O tipo de nó primário, esse privilégio permite que o Service Fabric colocar em pausa qualquer solicitação de infraestrutura de nível de VM (por exemplo, um reinício da VM, uma recriação de imagem de VM ou uma migração de VM) que têm impacto sobre os requisitos de quórum para os serviços do sistema e seus serviços com estado. Os tipos de nó não primário, esse privilégio permite que o Service Fabric colocar em pausa quaisquer solicitações de infraestrutura de nível de VM (por exemplo, o reinício da VM, a recriação de imagem de VM e a migração de VM) que têm impacto sobre os requisitos de quórum para os seus serviços com estado.  Para as vantagens dos diferentes níveis e recomendações sobre o nível de usar e quando, consulte [as características de durabilidade do cluster][durability].

O escalão de fiabilidade é usado para definir o número de réplicas dos serviços do sistema que deseja executar neste cluster, o tipo de nó primário. Mais o número de réplicas, mais confiáveis são os serviços do sistema no seu cluster.  Para as vantagens dos diferentes níveis e recomendações sobre o nível de usar e quando, consulte [as características de fiabilidade do cluster][reliability]. 

## <a name="enable-reverse-proxy-andor-dns"></a>Ativar o proxy inverso e/ou de DNS
Ligar entre si dentro de um cluster em geral de serviços podem acessar diretamente os pontos finais de outros serviços porque os nós num cluster estão na mesma rede local. Para tornar mais fácil ligar entre os serviços, o Service Fabric fornece serviços adicionais: R [serviço DNS](service-fabric-dnsservice.md) e uma [inverter o serviço de proxy](service-fabric-reverseproxy.md).  Ambos os serviços podem ser ativados ao implementar um cluster.

Já que muitos serviços, especialmente em contentores de serviços, pode ter um nome de URL existente, ser capaz de resolver estes usando o DNS padrão protocol (em vez do protocolo de Naming Service) é conveniente, especialmente em cenários de "lift- and -shift" de aplicações. Isso é exatamente o que faz o serviço DNS. Permite-lhe mapear nomes DNS para um nome de serviço e, por conseguinte, resolver endereços IP do ponto final.

O proxy inverso lida com serviços do cluster que expõem pontos finais HTTP (incluindo HTTPS). O proxy inverso simplifica bastante a chamar outros serviços ao fornecer um formato URI específico.  O proxy inverso também manipula a resolver, ligar e repita os passos necessários para um serviço comunicar com o outro.

## <a name="prepare-for-disaster-recovery"></a>Preparar para a recuperação após desastre
Uma parte crítica do fornecimento de elevada disponibilidade é garantir que os serviços podem sobreviver todos os diferentes tipos de falhas. Isto é especialmente importante para as falhas são não planeadas e fora do seu controlo. [Preparar para a recuperação após desastre](service-fabric-disaster-recovery.md) descreve alguns modos de falha comuns que podem ser desastres se não modelado e gerido corretamente. Ele também aborda atenuações e ações de caso de qualquer forma, um desastre.

## <a name="production-readiness-checklist"></a>Lista de verificação da preparação de produção
É a aplicação e o cluster estão prontos para dar o tráfego de produção? Antes de implementar o cluster para produção, percorrer o [lista de verificação de preparação de produção](service-fabric-production-readiness-checklist.md). Mantenha a sua aplicação e o cluster em execução sem problemas ao trabalhar com os itens nesta lista de verificação. Recomendamos vivamente que todos estes itens para assinalou antes de entrar em produção.

## <a name="next-steps"></a>Passos Seguintes
* [Criar um cluster do Service Fabric com o Windows](service-fabric-best-practices-overview.md)
* [Criar um cluster do Service Fabric em execução no Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md)

[placementconstraints]: service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints
[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster