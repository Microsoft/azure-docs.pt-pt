---
title: Réplicas e instâncias no Azure Service Fabric
description: Saiba mais sobre réplicas e instâncias no Service Fabric, incluindo uma visão geral de seus ciclos de vida e funções.
author: appi101
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: aprameyr
ms.openlocfilehash: cf21af43de553a2802289e44eaece12952d077d3
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75614609"
---
# <a name="replicas-and-instances"></a>Réplicas e instâncias 
Este artigo fornece uma visão geral do ciclo de vida de réplicas de serviços com estado e instâncias de serviços sem estado.

## <a name="instances-of-stateless-services"></a>Instâncias de serviços sem estado
Uma instância de um serviço sem estado é uma cópia da lógica de serviço que é executada em um dos nós do cluster. Uma instância dentro de uma partição é identificada exclusivamente por sua **InstanceId**. O ciclo de vida de uma instância é modelado no diagrama a seguir:

![Ciclo de vida da instância](./media/service-fabric-concepts-replica-lifecycle/instance.png)

### <a name="inbuild-ib"></a>Incompilação (IB)
Depois que o Gerenciador de recursos de cluster determina um posicionamento para a instância, ele entra nesse estado de ciclo de vida. A instância é iniciada no nó. O host de aplicativo é iniciado, a instância é criada e aberta. Após a conclusão da inicialização, a instância faz a transição para o estado pronto. 

Se o host ou o nó do aplicativo para essa instância falhar, ele faz a transição para o estado removido.

### <a name="ready-rd"></a>Pronto (RD)
No estado pronto, a instância está ativa e em execução no nó. Se essa instância for um serviço confiável, o **RunAsync** foi invocado. 

Se o host ou o nó do aplicativo para essa instância falhar, ele faz a transição para o estado removido.

### <a name="closing-cl"></a>Fechamento (CL)
No estado de fechamento, o Azure Service Fabric está no processo de desligar a instância neste nó. Esse desligamento pode ser devido a muitos motivos: por exemplo, atualização de aplicativo, balanceamento de carga ou o serviço que está sendo excluído. Após a conclusão do desligamento, ele faz a transição para o estado removido.

### <a name="dropped-dd"></a>Removido (DD)
No estado removido, a instância não está mais em execução no nó. Neste ponto, Service Fabric mantém os metadados sobre essa instância, que, eventualmente, são excluídos também.

> [!NOTE]
> É possível fazer a transição de qualquer Estado para o estado removido usando a opção **ForceRemove** em `Remove-ServiceFabricReplica`.
>

## <a name="replicas-of-stateful-services"></a>Réplicas de serviços com estado
Uma réplica de um serviço com estado é uma cópia da lógica de serviço em execução em um dos nós do cluster. Além disso, a réplica mantém uma cópia do estado desse serviço. Dois conceitos relacionados descrevem o ciclo de vida e o comportamento de réplicas com estado:
- Ciclo de vida da réplica
- Função de réplica

A discussão a seguir descreve os serviços com estado persistentes. Para serviços com estado volátil (ou na memória), os Estados inativos e descartados são equivalentes.

![Ciclo de vida da réplica](./media/service-fabric-concepts-replica-lifecycle/replica.png)

### <a name="inbuild-ib"></a>Incompilação (IB)
Uma réplica de incompilação é uma réplica que é criada ou preparada para ingressar no conjunto de réplicas. Dependendo da função de réplica, o IB tem semânticas diferentes. 

Se o host de aplicativo ou o nó de uma réplica de incompilação falhar, ele faz a transição para o estado inoperante.

   - **Réplicas de Incompilação primária**: a incompilação principal são as primeiras réplicas de uma partição. Essa réplica geralmente acontece quando a partição está sendo criada. Réplicas de incompilação primária também surgem quando todas as réplicas de uma partição são reiniciadas ou descartadas.

   - **Réplicas de Incompilação do IdleSecondary**: são novas réplicas criadas pelo Gerenciador de recursos de cluster ou réplicas existentes que foram inativas e precisam ser adicionadas de volta ao conjunto. Essas réplicas são propagadas ou criadas pela primária antes de poderem ingressar no conjunto de réplicas como ActiveSecondary e participar da confirmação de quorum de operações.

   - **Réplicas de Incompilação ActiveSecondary**: esse estado é observado em algumas consultas. É uma otimização em que o conjunto de réplicas não está mudando, mas uma réplica precisa ser compilada. A réplica em si segue as transições de máquina de estado normal (conforme descrito na seção sobre funções de réplica).

### <a name="ready-rd"></a>Pronto (RD)
Uma réplica pronta é uma réplica que está participando da replicação e da confirmação de quorum de operações. O estado pronto é aplicável às réplicas primárias e secundárias ativas.

Se o host do aplicativo ou o nó de uma réplica pronta falhar, ele faz a transição para o estado inoperante.

### <a name="closing-cl"></a>Fechamento (CL)
Uma réplica entra no estado de fechamento nos seguintes cenários:

- **Desligando o código para a réplica**: Service Fabric pode precisar desligar o código em execução para uma réplica. Esse desligamento pode ser por vários motivos. Por exemplo, isso pode ocorrer devido a uma atualização de aplicativo, malha ou infraestrutura, ou devido a uma falha relatada pela réplica. Quando o fechamento da réplica é concluído, a réplica faz a transição para o estado inoperante. O estado persistente associado a essa réplica armazenada em disco não é limpo.

- **Removendo a réplica do cluster**: Service Fabric pode precisar remover o estado persistente e desligar o código em execução para uma réplica. Esse desligamento pode ser por vários motivos, por exemplo, balanceamento de carga.

### <a name="dropped-dd"></a>Removido (DD)
No estado removido, a instância não está mais em execução no nó. Também não há nenhum estado restante no nó. Neste ponto, Service Fabric mantém os metadados sobre essa instância, que, eventualmente, são excluídos também.

### <a name="down-d"></a>Para baixo (D)
No estado inoperante, o código de réplica não está em execução, mas o estado persistente dessa réplica existe nesse nó. Uma réplica pode ficar inativa por muitos motivos – por exemplo, o nó está inoperante, uma falha no código da réplica, uma atualização do aplicativo ou falhas de réplica.

Uma réplica inoperante é aberta por Service Fabric conforme necessário, por exemplo, quando a atualização é concluída no nó.

A função de réplica não é relevante no estado inoperante.

### <a name="opening-op"></a>Abertura (OP)
Uma réplica inoperante entra no estado de abertura quando Service Fabric precisa colocar novamente a réplica novamente. Por exemplo, esse Estado pode ser após a conclusão de uma atualização de código para o aplicativo em um nó. 

Se o host do aplicativo ou o nó de uma réplica de abertura falhar, ele faz a transição para o estado inoperante.

A função de réplica não é relevante no estado de abertura.

### <a name="standby-sb"></a>Em espera (SB)
Uma réplica em espera é uma réplica de um serviço persistente que foi desativado e foi aberto. Essa réplica pode ser usada por Service Fabric se precisar adicionar outra réplica ao conjunto de réplicas (porque a réplica já tem alguma parte do estado e o processo de compilação é mais rápido). Depois que o StandByReplicaKeepDuration expira, a réplica em espera é descartada.

Se o host do aplicativo ou o nó de uma réplica em espera falha, ele faz a transição para o estado inoperante.

A função de réplica não é relevante no estado de espera.

> [!NOTE]
> Todas as réplicas que não estão inativas ou descartadas são consideradas como *ativadas*.
>

> [!NOTE]
> É possível fazer a transição de qualquer Estado para o estado removido usando a opção **ForceRemove** em `Remove-ServiceFabricReplica`.
>

## <a name="replica-role"></a>Função de réplica 
A função da réplica determina sua função no conjunto de réplicas:

- **Primário (P)** : há um primário no conjunto de réplicas que é responsável por executar operações de leitura e gravação. 
- **ActiveSecondary (S)** : são réplicas que recebem atualizações de estado do primário, as aplicam e enviam confirmações de volta. Há várias secundárias ativas no conjunto de réplicas. O número desses secundários ativos determina o número de falhas que o serviço pode manipular.
- **IdleSecondary (I)** : essas réplicas estão sendo criadas pelo primário. Eles estão recebendo o estado da primária antes que possam ser promovidos para o secundário ativo. 
- **Nenhum (N)** : essas réplicas não têm uma responsabilidade no conjunto de réplicas.
- **Desconhecido (U)** : essa é a função inicial de uma réplica antes de receber qualquer chamada à API **ChangeRole** de Service Fabric.

O diagrama a seguir ilustra as transições de função de réplica e alguns cenários de exemplo nos quais elas podem ocorrer:

![Função de réplica](./media/service-fabric-concepts-replica-lifecycle/role.png)

- U-> P: criação de uma nova réplica primária.
- U-> I: criação de uma nova réplica ociosa.
- U-> N: exclusão de uma réplica em espera.
- I-> S: promoção do secundário ocioso ao secundário ativo para que suas confirmações contribuam para o quorum.
- I-> P: promoção do secundário ocioso para o primário. Isso pode ocorrer em reconfigurações especiais quando o secundário ocioso é o candidato correto para ser primário.
- I-> N: exclusão da réplica secundária ociosa.
- S-> P: promoção do secundário ativo para o primário. Isso pode ser devido ao failover do movimento primário ou primário iniciado pelo Gerenciador de recursos de cluster. Por exemplo, pode ser em resposta a uma atualização de aplicativo ou balanceamento de carga.
- S-> N: exclusão da réplica secundária ativa.
- P-> S: rebaixamento da réplica primária. Isso pode ser devido a um movimento primário iniciado pelo Gerenciador de recursos de cluster. Por exemplo, pode ser em resposta a uma atualização de aplicativo ou balanceamento de carga.
- P-> N: exclusão da réplica primária.

> [!NOTE]
> Modelos de programação de nível superior, como [Reliable Actors](service-fabric-reliable-actors-introduction.md) e [Reliable Services](service-fabric-reliable-services-introduction.md), ocultam o conceito de funções de réplica do desenvolvedor. Em atores, a noção de uma função é desnecessária. Em serviços, ele é amplamente simplificado para a maioria dos cenários.
>

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre conceitos de Service Fabric, consulte o seguinte artigo:

[Ciclo de vida do Reliable Services - C#](service-fabric-reliable-services-lifecycle.md)

