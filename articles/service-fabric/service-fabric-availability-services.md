---
title: Disponibilidade de serviços de Tecido de Serviço
description: Descreve a deteção de falhas, a falha e a recuperação de um serviço numa aplicação Azure Service Fabric.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 5306439184561e8dec8303a7b149f51d6c2f6e08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551867"
---
# <a name="availability-of-service-fabric-services"></a>Disponibilidade de serviços de Tecido de Serviço
Este artigo dá uma visão geral de como o Azure Service Fabric mantém a disponibilidade de um serviço.

## <a name="availability-of-service-fabric-stateless-services"></a>Disponibilidade de serviços apátridas de Tecido de Serviço
Serviço Os serviços de tecido podem ser audais ou apátridas. Um serviço apátrida é um serviço de aplicação que não tem um [estado local](service-fabric-concepts-state.md) que precisa de ser altamente disponível ou confiável.

A criação de um serviço `InstanceCount`apátrida requer a definição de um . A contagem de exemplos define o número de casos da lógica de aplicação do serviço apátrida que deveriam estar em execução no cluster. Aumentar o número de casos é a forma recomendada de escalonar um serviço apátrida.

Quando um caso de um serviço de nome apátrida falha, uma nova instância é criada num nó elegível no cluster. Por exemplo, uma instância de serviço apátrida pode falhar no Nó1 e ser recriada no Node5.

## <a name="availability-of-service-fabric-stateful-services"></a>Disponibilidade de serviços de estado de tecido de serviço
Um serviço imponente tem um estado associado a ele. No Tecido de Serviço, um serviço imponente é modelado como um conjunto de réplicas. Cada réplica é uma instância de execução do código do serviço. A réplica também tem uma cópia do Estado para esse serviço. As operações de leitura e escrita são realizadas numa réplica, chamada *Primária.* As alterações ao estado das operações de escrita são *replicadas* para as outras réplicas do conjunto de réplicas, denominadas *Ative Secondáriories*, e aplicadas. 

Pode haver apenas uma réplica primária, mas pode haver múltiplas réplicas secundárias ativas. O número de réplicas secundárias ativas é configurável, e um maior número de réplicas pode tolerar um maior número de falhas de software e hardware simultâneas.

Se a réplica primária cair, o Tecido de Serviço faz uma das réplicas secundárias ativas da nova réplica primária. Esta réplica secundária ativa já tem a versão atualizada do Estado, através da *replicação,* e pode continuar a processar mais operações de leitura/escrita. Este processo é conhecido como *reconfiguração* e é descrito ainda mais no artigo [de reconfiguração.](service-fabric-concepts-reconfiguration.md)

O conceito de uma réplica sendo um Secundário Primário ou Ativo, é conhecido como o *papel de réplica.* Estas réplicas são descritas ainda mais no artigo [das Réplicas e instâncias.](service-fabric-concepts-replica-lifecycle.md) 

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre os conceitos de Tecido de Serviço, consulte os seguintes artigos:

- [Serviço de Serviço de Serviço de Fabricação](service-fabric-concepts-scalability.md)
- [Serviço de Partição Serviço De Tecido](service-fabric-concepts-partitioning.md)
- [Definição e gestão do Estado](service-fabric-concepts-state.md)
- [Reliable Services](service-fabric-reliable-services-introduction.md)

