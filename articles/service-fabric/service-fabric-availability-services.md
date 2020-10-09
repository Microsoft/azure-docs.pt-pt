---
title: Disponibilidade de serviços de tecido de serviço
description: Descreve a deteção de falhas, falha e recuperação de um serviço numa aplicação Azure Service Fabric.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 5306439184561e8dec8303a7b149f51d6c2f6e08
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75551867"
---
# <a name="availability-of-service-fabric-services"></a>Disponibilidade de serviços de tecido de serviço
Este artigo dá uma visão geral de como a Azure Service Fabric mantém a disponibilidade de um serviço.

## <a name="availability-of-service-fabric-stateless-services"></a>Disponibilidade de serviços de serviços apátridas de tecido
Os serviços de tecido de serviço podem ser imponentes ou apátridas. Um serviço apátrida é um serviço de aplicação que não tem um [estado local](service-fabric-concepts-state.md) que precisa de ser altamente disponível ou confiável.

Criar um serviço apátrida requer a definição de `InstanceCount` um . A contagem de casos define o número de casos da lógica de aplicação do serviço apátrida que deve estar a funcionar no cluster. Aumentar o número de casos é a forma recomendada de escalonar um serviço apátrida.

Quando um caso de um serviço apátrida falha, um novo caso é criado em um nó elegível no cluster. Por exemplo, uma instância de serviço apátrida pode falhar no Node1 e ser recriada no Node5.

## <a name="availability-of-service-fabric-stateful-services"></a>Disponibilidade de serviços estatais de tecido
Um serviço estatal tem um estado associado a ele. Em Service Fabric, um serviço imponente é modelado como um conjunto de réplicas. Cada réplica é uma instância em execução do código do serviço. A réplica também tem uma cópia do estado para esse serviço. As operações de leitura e escrita são realizadas numa réplica, chamada *Primária.* As alterações ao estado das operações de escrita são *replicadas* para as outras réplicas no conjunto de *réplicas, chamados Ative Secundários, e aplicadas.* 

Pode haver apenas uma réplica primária, mas pode haver várias réplicas secundárias ativas. O número de réplicas secundárias ativas é configurável, e um maior número de réplicas pode tolerar um maior número de falhas de software e hardware simultâneas.

Se a réplica primária cair, o Tecido de Serviço faz uma das réplicas secundárias ativas da nova réplica primária. Esta réplica Ative Secondary já tem a versão atualizada do estado, através de *replicação,* e pode continuar a processar mais operações de leitura/escrita. Este processo é conhecido como *reconfiguração* e é descrito mais adiante no artigo [de Reconfiguração.](service-fabric-concepts-reconfiguration.md)

O conceito de réplica sendo um Secundário Primário ou Ativo, é conhecido como o *papel de réplica*. Estas réplicas são descritas mais adiante no artigo [de Replicas e Instâncias.](service-fabric-concepts-replica-lifecycle.md) 

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre os conceitos de Tecido de Serviço, consulte os seguintes artigos:

- [Serviços de tecido de escala](service-fabric-concepts-scalability.md)
- [Serviços de tecido de partilha](service-fabric-concepts-partitioning.md)
- [Definição e gestão do estado](service-fabric-concepts-state.md)
- [Reliable Services](service-fabric-reliable-services-introduction.md)

