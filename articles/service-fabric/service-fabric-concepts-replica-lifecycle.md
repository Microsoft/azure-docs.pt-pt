---
title: Réplicas e instâncias em Tecido de Serviço Azure
description: Conheça as réplicas e instâncias em Tecido de Serviço, incluindo uma visão geral dos seus ciclos de vida e funções.
author: appi101
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: aprameyr
ms.openlocfilehash: cf21af43de553a2802289e44eaece12952d077d3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84710750"
---
# <a name="replicas-and-instances"></a>Réplicas e instâncias 
Este artigo apresenta uma visão geral do ciclo de vida de réplicas de serviços estatais e casos de serviços apátridas.

## <a name="instances-of-stateless-services"></a>Casos de serviços apátridas
Um caso de serviço apátrida é uma cópia da lógica de serviço que funciona num dos nós do cluster. Um caso dentro de uma partição é identificado exclusivamente pelo seu **InstanceId**. O ciclo de vida de um caso é modelado no seguinte diagrama:

![Exemplo ciclo de vida](./media/service-fabric-concepts-replica-lifecycle/instance.png)

### <a name="inbuild-ib"></a>InBuild (IB)
Depois de o Cluster Resource Manager determinar uma colocação, por exemplo, entra neste estado de ciclo de vida. O caso começou no nó. O anfitrião da aplicação é iniciado, o caso é criado e depois aberto. Após o fim da startup, o caso passa para o estado pronto. 

Se o anfitrião ou nó de aplicação, para este caso, falhar, ele transite para o estado de queda.

### <a name="ready-rd"></a>Pronto (RD)
No estado de preparação, o caso está a funcionar no nó. Se este caso for um serviço fiável, **o RunAsync** foi invocado. 

Se o anfitrião ou nó de aplicação, para este caso, falhar, ele transite para o estado de queda.

### <a name="closing-cl"></a>Encerramento (CL)
No estado de encerramento, a Azure Service Fabric está em vias de encerrar a instância deste nó. Esta paralisação pode dever-se a muitas razões -- por exemplo, uma atualização de aplicação, equilíbrio de carga ou o serviço a ser eliminado. Após o encerramento terminar, passa para o estado de queda.

### <a name="dropped-dd"></a>Caiu (DD)
No estado de queda, o caso já não está a funcionar no nó. Neste momento, a Service Fabric mantém os metadados sobre este caso, que acabam por ser eliminados também.

> [!NOTE]
> É possível transitar de qualquer estado para o estado abandonado utilizando a opção **ForceRemove** em `Remove-ServiceFabricReplica` .
>

## <a name="replicas-of-stateful-services"></a>Réplicas de serviços estatais
Uma réplica de um serviço imponente é uma cópia da lógica de serviço que funciona num dos nós do cluster. Além disso, a réplica mantém uma cópia do estado desse serviço. Dois conceitos relacionados descrevem o ciclo de vida e o comportamento de réplicas imponentes:
- Réplica do ciclo de vida
- Função réplica

A seguinte discussão descreve serviços estatais persistentes. Para serviços voláteis (ou na memória), os estados em baixo e os estados abandonados são equivalentes.

![Réplica do ciclo de vida](./media/service-fabric-concepts-replica-lifecycle/replica.png)

### <a name="inbuild-ib"></a>InBuild (IB)
Uma réplica inBuild é uma réplica criada ou preparada para se juntar ao conjunto de réplicas. Dependendo do papel de réplica, o IB tem semântica diferente. 

Se o anfitrião da aplicação ou o nó de uma réplica inBuild falharem, ele transite para o estado de baixo.

   - **Réplicas primárias do InBuild**: O InBuild primário são as primeiras réplicas para uma partição. Esta réplica geralmente acontece quando a partição está sendo criada. As réplicas primárias do InBuild também surgem quando todas as réplicas de uma partição reiniciam ou são largadas.

   - **Réplicas inBuild IdleSecondary**: Estas são réplicas novas que são criadas pelo Cluster Resource Manager, ou réplicas existentes que desceram e precisam de ser adicionadas de volta ao conjunto. Estas réplicas são semeadas ou construídas pelas primárias antes de poderem juntar-se ao conjunto de réplicas como ActiveSecondary e participar no reconhecimento quórum das operações.

   - **ActiveSecondary InBuild replicas**: Este estado é observado em algumas consultas. É uma otimização onde o conjunto de réplicas não está a mudar, mas uma réplica precisa de ser construída. A réplica em si segue as transições normais da máquina do estado (conforme descrito na secção sobre funções de réplica).

### <a name="ready-rd"></a>Pronto (RD)
Uma réplica Ready é uma réplica que está a participar na replicação e reconhecimento de quórum das operações. O estado pronto é aplicável às réplicas secundárias primárias e ativas.

Se o anfitrião da aplicação ou o nó para uma réplica pronta falhar, ele transite para o estado de descida.

### <a name="closing-cl"></a>Encerramento (CL)
Uma réplica entra no estado de encerramento nos seguintes cenários:

- **Desligar o código da réplica**: O Tecido de Serviço pode ter de desligar o código de funcionamento para uma réplica. Esta paralisação pode ser por muitas razões. Por exemplo, pode acontecer devido a uma aplicação, tecido ou atualização de infraestrutura, ou devido a uma falha reportada pela réplica. Quando a réplica termina, a réplica passa para o estado de baixo. O estado persistido associado a esta réplica que está armazenada no disco não é limpo.

- **Remover a réplica do cluster**: O Tecido de Serviço poderá ter de remover o estado persistido e desligar o código de funcionamento de uma réplica. Esta paralisação pode ser por muitas razões, por exemplo, o equilíbrio de carga.

### <a name="dropped-dd"></a>Caiu (DD)
No estado de queda, o caso já não está a funcionar no nó. Também não há mais estado no nó. Neste momento, a Service Fabric mantém os metadados sobre este caso, que acabam por ser eliminados também.

### <a name="down-d"></a>Para baixo (D)
No estado de baixo, o código de réplica não está em execução, mas o estado persistido para que a réplica exista nesse nó. Uma réplica pode ser baixa por muitas razões -- por exemplo, o nó estar em baixo, uma falha no código de réplica, uma atualização de aplicação ou falhas de réplica.

Uma réplica para baixo é aberta pela Service Fabric, conforme necessário, por exemplo, quando a atualização termina no nó.

O papel de réplica não é relevante no estado de baixo.

### <a name="opening-op"></a>Abertura (OP)
Uma réplica para baixo entra no estado de abertura quando o Tecido de Serviço precisa de trazer a réplica de volta. Por exemplo, este estado pode estar depois de uma atualização de código para a aplicação terminar num nó. 

Se o anfitrião da aplicação ou o nó para uma réplica de abertura falharem, ele transite para o estado de descida.

O papel de réplica não é relevante no estado de abertura.

### <a name="standby-sb"></a>StandBy (SB)
Uma réplica de StandBy é uma réplica de um serviço persistido que foi abatido e foi então aberto. Esta réplica pode ser usada pela Service Fabric se precisar de adicionar outra réplica ao conjunto de réplicas (porque a réplica já tem alguma parte do estado e o processo de construção é mais rápido). Após o fim da StandByReplicaKeepDuration, a réplica de espera é descartada.

Se o anfitrião da aplicação ou o nó para uma réplica de espera se despenhar, ele transite para o estado de descida.

O papel de réplica não é relevante no estado de espera.

> [!NOTE]
> Qualquer réplica que não seja para baixo ou largada é considerada *para cima.*
>

> [!NOTE]
> É possível transitar de qualquer estado para o estado abandonado usando a opção **ForceRemove** em `Remove-ServiceFabricReplica` .
>

## <a name="replica-role"></a>Função réplica 
O papel da réplica determina a sua função no conjunto de réplicas:

- **Primário (P)**: Há uma primária no conjunto de réplicas que é responsável pela realização de operações de leitura e escrita. 
- **ActiveSecondary (S)**: Estas são réplicas que recebem atualizações do estado a partir das primárias, aplicam-nas e depois enviam de volta os avisos. Existem vários secundários ativos no conjunto de réplicas. O número destes secundários ativos determina o número de falhas que o serviço pode suportar.
- **IdleSecondary (I)**: Estas réplicas estão a ser construídas pelas primárias. Estão a receber estado desde as primárias antes de poderem ser promovidos a secundários ativos. 
- **Nenhuma (N)**: Estas réplicas não têm responsabilidade no conjunto de réplicas.
- **Desconhecido (U)**: Este é o papel inicial de uma réplica antes de receber qualquer chamada de API **changeRole** da Service Fabric.

O diagrama a seguir ilustra as transições de funções de réplica e alguns cenários de exemplo em que podem ocorrer:

![Função réplica](./media/service-fabric-concepts-replica-lifecycle/role.png)

- U -> P: Criação de uma nova réplica primária.
- U -> I: Criação de uma nova réplica ociosa.
- U -> N: Eliminação de uma réplica de espera.
- I -> S: Promoção do secundário ocioso ao secundário ativo para que os seus reconhecimentos contribuam para o quórum.
- I -> P: Promoção do ocioso secundário ao primário. Isto pode acontecer sob reconfigurações especiais quando o secundário ocioso é o candidato correto para ser primário.
- I -> N: Eliminação da réplica secundária ociosa.
- S -> P: Promoção do secundário ativo ao primário. Isto pode ser devido ao fracasso do movimento primário ou primário iniciado pelo Cluster Resource Manager. Por exemplo, pode ser uma resposta a um upgrade de aplicação ou equilíbrio de carga.
- S -> N: Eliminação da réplica secundária ativa.
- P-> S: Despromoção da réplica primária. Isto pode ser devido a um movimento primário iniciado pelo Cluster Resource Manager. Por exemplo, pode ser uma resposta a um upgrade de aplicação ou equilíbrio de carga.
- P-> N: Eliminação da réplica primária.

> [!NOTE]
> Modelos de programação de alto nível, como [Reliable Actors](service-fabric-reliable-actors-introduction.md) e [Reliable Services,](service-fabric-reliable-services-introduction.md)escondem o conceito de funções réplicas do desenvolvedor. Nos atores, a noção de um papel é desnecessária. Nos Serviços, é amplamente simplificado para a maioria dos cenários.
>

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre os conceitos de Tecido de Serviço, consulte o seguinte artigo:

[Ciclo de vida do Reliable Services - C#](service-fabric-reliable-services-lifecycle.md)

