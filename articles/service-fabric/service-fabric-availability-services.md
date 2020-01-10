---
title: Disponibilidade de serviços de Service Fabric
description: Descreve a detecção de falhas, o failover e a recuperação de um serviço em um aplicativo Service Fabric do Azure.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 5306439184561e8dec8303a7b149f51d6c2f6e08
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75551867"
---
# <a name="availability-of-service-fabric-services"></a>Disponibilidade de serviços de Service Fabric
Este artigo fornece uma visão geral de como o Azure Service Fabric mantém a disponibilidade de um serviço.

## <a name="availability-of-service-fabric-stateless-services"></a>Disponibilidade de serviços sem estado Service Fabric
Os serviços de Service Fabric podem ser com ou sem estado. Um serviço sem estado é um serviço de aplicativo que não tem um [estado local](service-fabric-concepts-state.md) que precisa ser altamente disponível ou confiável.

A criação de um serviço sem estado requer a definição de um `InstanceCount`. A contagem de instâncias define o número de instâncias da lógica do aplicativo do serviço sem estado que devem ser executadas no cluster. Aumentar o número de instâncias é a maneira recomendada de escalar horizontalmente um serviço sem estado.

Quando uma instância de um serviço nomeado sem estado falha, uma nova instância é criada em um nó qualificado no cluster. Por exemplo, uma instância de serviço sem estado pode falhar no Node1 e ser recriada no Nó5.

## <a name="availability-of-service-fabric-stateful-services"></a>Disponibilidade de serviços com estado Service Fabric
Um serviço com estado tem um estado associado a ele. No Service Fabric, um serviço com estado é modelado como um conjunto de réplicas. Cada réplica é uma instância em execução do código do serviço. A réplica também tem uma cópia do estado para esse serviço. As operações de leitura e gravação são executadas em uma réplica, chamada *primária*. As alterações no estado de operações de gravação são *replicadas* para as outras réplicas no conjunto de réplicas, chamadas de *secundárias ativas*e aplicadas. 

Pode haver apenas uma réplica primária, mas pode haver várias réplicas secundárias ativas. O número de réplicas secundárias ativas é configurável e um número maior de réplicas pode tolerar um número maior de falhas de software e hardware simultâneas.

Se a réplica primária ficar inativa, Service Fabric fará com que uma das réplicas secundárias ativas seja a nova réplica primária. Essa réplica secundária ativa já tem a versão atualizada do estado, via *replicação*, e pode continuar processando outras operações de leitura/gravação. Esse processo é conhecido como *reconfiguração* e é descrito mais detalhadamente no artigo [reconfiguração](service-fabric-concepts-reconfiguration.md) .

O conceito de uma réplica sendo primária ou secundária ativa é conhecido como a *função de réplica*. Essas réplicas são descritas mais detalhadamente no artigo [réplicas e instâncias](service-fabric-concepts-replica-lifecycle.md) . 

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre conceitos de Service Fabric, consulte os seguintes artigos:

- [Dimensionamento de serviços de Service Fabric](service-fabric-concepts-scalability.md)
- [Particionando serviços de Service Fabric](service-fabric-concepts-partitioning.md)
- [Definindo e gerenciando o estado](service-fabric-concepts-state.md)
- [Reliable Services](service-fabric-reliable-services-introduction.md)

