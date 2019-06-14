---
title: Disponibilidade dos serviços do Service Fabric | Documentos da Microsoft
description: Descreve a deteção de falhas, a ativação pós-falha e a recuperação de serviços
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: 279ba4a4-f2ef-4e4e-b164-daefd10582e4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: dd10af0d3c8a57168a27a039286ea0ec4c1dad02
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60310949"
---
# <a name="availability-of-service-fabric-services"></a>Disponibilidade dos serviços do Service Fabric
Este artigo fornece uma visão geral de como o Azure Service Fabric mantém a disponibilidade de um serviço.

## <a name="availability-of-service-fabric-stateless-services"></a>Disponibilidade dos serviços sem monitoração de estado do Service Fabric
Serviços do Service Fabric podem ser com monitoração de estado ou sem monitoração de estado. Um serviço sem estado é um serviço de aplicações que não tem um [estado local](service-fabric-concepts-state.md) que tem de ser altamente disponível ou confiável.

Criar um serviço sem estado necessário definir um `InstanceCount`. A contagem de instâncias define o número de instâncias do serviço sem monitoração de estado lógica do aplicativo que deve estar em execução no cluster. Aumentar o número de instâncias é a forma recomendada de ampliar um serviço sem estado.

Quando uma instância de um serviço sem estado com o nome-falha, uma nova instância é criada num nó elegível no cluster. Por exemplo, uma instância de serviço sem estado poderá falhar no Node1 e voltar a criar no Nó5.

## <a name="availability-of-service-fabric-stateful-services"></a>Disponibilidade dos serviços de monitorização de estado do Service Fabric
Um serviço com estado tem um Estado associado a ele. No Service Fabric, um serviço com estado é modelado como um conjunto de réplicas. Cada réplica é uma instância em execução do código do serviço. A réplica também tem uma cópia do Estado para esse serviço. Ler e escrever as operações são executadas numa réplica, chamada de *primário*. As operações de escrita de alterações para o estado da *replicados* para as outras réplicas no conjunto de réplicas, chamado *Active Directory de bases de dados secundárias*e aplicadas. 

Pode haver apenas uma réplica primária, mas pode haver várias réplicas secundárias do Active Directory. O número de réplicas secundárias Active Directory é configurável e um número superior de réplicas pode tolerar um número maior de software em simultâneo e falhas de hardware.

Se a réplica primária ficar inativo, o Service Fabric garante uma das réplicas secundárias do Active Directory primário nova réplica. Esta réplica secundária do Active Directory já tem a versão atualizada do Estado, via *replicação*, e ele pode continuar a processar mais operações de leitura/escrita. Este processo é conhecido como *reconfiguração* e é descrito mais detalhadamente no [reconfiguração](service-fabric-concepts-reconfiguration.md) artigo.

O conceito de uma réplica que está a ser um principal ou secundária ativa, é conhecido como o *função de réplica*. Essas réplicas são descritas mais detalhadamente no [réplicas e instâncias](service-fabric-concepts-replica-lifecycle.md) artigo. 

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre conceitos do Service Fabric, consulte os artigos seguintes:

- [Dimensionar os serviços do Service Fabric](service-fabric-concepts-scalability.md)
- [A criação de partições de serviços do Service Fabric](service-fabric-concepts-partitioning.md)
- [Definir e gerir o Estado](service-fabric-concepts-state.md)
- [Reliable Services](service-fabric-reliable-services-introduction.md)

