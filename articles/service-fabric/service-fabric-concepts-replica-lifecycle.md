---
title: Réplicas e instâncias em Tecido de Serviço Azure
description: Conheça réplicas e instâncias em Tecido de Serviço, incluindo uma visão geral dos seus ciclos de vida e funções.
author: appi101
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: aprameyr
ms.openlocfilehash: cf21af43de553a2802289e44eaece12952d077d3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79258567"
---
# <a name="replicas-and-instances"></a>Réplicas e instâncias 
Este artigo dá uma visão geral do ciclo de vida de réplicas de serviços estatais e instâncias de serviços apátridas.

## <a name="instances-of-stateless-services"></a>Casos de serviços apátridas
Um exemplo de um serviço apátrida é uma cópia da lógica de serviço que funciona em um dos nós do cluster. Uma instância dentro de uma divisória é identificada exclusivamente pelo seu **InstanceId**. O ciclo de vida de um caso é modelado no seguinte diagrama:

![Ciclo de vida de instância](./media/service-fabric-concepts-replica-lifecycle/instance.png)

### <a name="inbuild-ib"></a>InBuild (IB)
Depois de o Cluster Resource Manager determinar uma colocação por exemplo, entra neste estado de ciclo de vida. A ocorrência começa no nó. O anfitrião da aplicação é iniciado, a instância é criada e depois aberta. Após o acabamento da startup, a instância transita para o estado pronto. 

Se o hospedeiro ou nó da aplicação para este caso se despenhar, passa para o estado de queda.

### <a name="ready-rd"></a>Pronto (RD)
No estado de preparação, a instância está a funcionar no nó. Se este caso for um serviço fiável, a **RunAsync** foi invocada. 

Se o hospedeiro ou nó da aplicação para este caso se despenhar, passa para o estado de queda.

### <a name="closing-cl"></a>Fecho (CL)
No estado de encerramento, a Azure Service Fabric está em processo de desligar a instância neste nó. Esta paralisação pode dever-se a muitas razões - por exemplo, uma atualização da aplicação, um equilíbrio de carga ou o serviço a ser eliminado. Após o encerramento, transita para o estado de queda.

### <a name="dropped-dd"></a>Caído (DD)
No estado de queda, a instância já não está a correr no nó. Neste ponto, o Service Fabric mantém os metadados sobre esta instância, que acaba por ser eliminada também.

> [!NOTE]
> É possível transitar de qualquer estado para o estado `Remove-ServiceFabricReplica`abandonado utilizando a opção **ForceRemove** em .
>

## <a name="replicas-of-stateful-services"></a>Réplicas de serviços estatais
Uma réplica de um serviço imponente é uma cópia da lógica de serviço que funciona num dos nós do cluster. Além disso, a réplica mantém uma cópia do estado desse serviço. Dois conceitos relacionados descrevem o ciclo de vida e o comportamento de réplicas estatais:
- Ciclo de vida de réplica
- Papel de réplica

A discussão seguinte descreve os serviços estatais persistentes. Para serviços voláteis (ou em memória) audais, os estados de baixo e caídos são equivalentes.

![Ciclo de vida de réplica](./media/service-fabric-concepts-replica-lifecycle/replica.png)

### <a name="inbuild-ib"></a>InBuild (IB)
Uma réplica inBuild é uma réplica que é criada ou preparada para se juntar ao conjunto de réplicas. Dependendo do papel da réplica, o IB tem semântica diferente. 

Se o anfitrião da aplicação ou o nó de uma réplica inBuild se despenhar, passa para o estado de baixo.

   - **Réplicas primárias inBuild**: Primary InBuild são as primeiras réplicas para uma partição. Esta réplica geralmente acontece quando a partição está sendo criada. As réplicas primárias da InBuild também surgem quando todas as réplicas de um reinício da divisória ou são largadas.

   - **Réplicas inBuild ociosas**: Ou são novas réplicas que são criadas pelo Cluster Resource Manager, ou réplicas existentes que desceram e precisam de ser adicionadas de volta ao conjunto. Estas réplicas são semeadas ou construídas pelo primário antes de poderem juntar-se à réplica definida como ActiveSecondary e participar no reconhecimento de quórum das operações.

   - **Réplicas ActiveSecondary InBuild**: Este estado é observado em algumas consultas. É uma otimização onde o conjunto de réplicas não está a mudar, mas uma réplica precisa de ser construída. A réplica em si segue as transições normais da máquina do estado (como descrito na secção sobre funções de réplica).

### <a name="ready-rd"></a>Pronto (RD)
Uma réplica pronta é uma réplica que está a participar na replicação e no reconhecimento quórum das operações. O estado pronto é aplicável às réplicas secundárias primárias e ativas.

Se o hospedeiro de aplicação ou o nó para uma réplica pronta se despenhar, passa para o estado de baixo.

### <a name="closing-cl"></a>Fecho (CL)
Uma réplica entra no estado de encerramento nos seguintes cenários:

- **Desligar o código para a réplica**: O Tecido de Serviço pode ter de desligar o código de funcionamento de uma réplica. Esta paralisação pode ser por muitas razões. Por exemplo, pode acontecer por causa de uma aplicação, tecido ou upgrade de infraestrutura, ou por causa de uma falha reportada pela réplica. Quando a réplica termina, a réplica transita para o estado de baixo. O estado persistido associado a esta réplica que está armazenada no disco não é limpo.

- **Remoção da réplica do cluster**: O Tecido de Serviço pode ter de remover o estado persistente e desligar o código de funcionamento de uma réplica. Esta paralisação pode ser por muitas razões, por exemplo, o equilíbrio de carga.

### <a name="dropped-dd"></a>Caído (DD)
No estado de queda, a instância já não está a correr no nó. Também não há mais estado no nó. Neste ponto, o Service Fabric mantém os metadados sobre esta instância, que acaba por ser eliminada também.

### <a name="down-d"></a>Para baixo (D)
No estado de baixo, o código de réplica não está em execução, mas o estado persistido para essa réplica existe naquele nó. Uma réplica pode ser baixa por muitas razões - por exemplo, o nó estar em baixo, uma falha no código de réplica, uma atualização de aplicações ou falhas de réplica.

Uma réplica para baixo é aberta pelo Service Fabric conforme necessário, por exemplo, quando a atualização termina no nó.

O papel da réplica não é relevante no estado de baixo.

### <a name="opening-op"></a>Abertura (OP)
Uma réplica para baixo entra no estado de abertura quando o Tecido de Serviço precisa de trazer a réplica de volta. Por exemplo, este estado pode estar após uma atualização de código para a aplicação termina em um nó. 

Se o hospedeiro de aplicação ou o nó para uma réplica de abertura se despenhar, passa para o estado de baixo.

O papel da réplica não é relevante no estado de abertura.

### <a name="standby-sb"></a>Standby (SB)
Uma réplica de StandBy é uma réplica de um serviço persistente que desceu e foi então aberto. Esta réplica pode ser usada pela Service Fabric se precisar de adicionar outra réplica ao conjunto de réplicas (porque a réplica já tem alguma parte do estado e o processo de construção é mais rápido). Após a expiração da duração standByReplicaKeep, a réplica de espera é descartada.

Se o hospedeiro de aplicação ou o nó para uma réplica de prontidão se despenhar, passa para o estado de baixo.

O papel da réplica não é relevante no estado de espera.

> [!NOTE]
> Qualquer réplica que não seja baixa ou largada é considerada como *estando para cima.*
>

> [!NOTE]
> É possível transitar de qualquer estado para o estado `Remove-ServiceFabricReplica`abandonado usando a opção **ForceRemove** em .
>

## <a name="replica-role"></a>Papel de réplica 
O papel da réplica determina a sua função no conjunto de réplicas:

- **Primária (P)**: Há uma primária no conjunto de réplicas que é responsável pela realização de operações de leitura e escrita. 
- **ActiveSecondary (S)**: Estas são réplicas que recebem atualizações estatais a partir das primárias, aplicam-nas e, em seguida, enviam de volta reconhecimentos. Existem múltiplos secundários ativos no conjunto de réplicas. O número destes secundários ativos determina o número de falhas que o serviço pode suportar.
- **IdleSecondary (I)**: Estas réplicas estão a ser construídas pela primária. Estão a receber estado das primárias antes de poderem ser promovidos a secundários ativos. 
- **Nenhuma (N)**: Estas réplicas não têm uma responsabilidade no conjunto de réplicas.
- **Unknown (U)**: Este é o papel inicial de uma réplica antes de receber qualquer chamada da **ChangeRole** API da Fabric service.

O diagrama seguinte ilustra as transições de papéis da réplica e alguns cenários de exemplo em que podem ocorrer:

![Papel de réplica](./media/service-fabric-concepts-replica-lifecycle/role.png)

- U-> P: Criação de uma nova réplica primária.
- U-> I: Criação de uma nova réplica ociosa.
- U -> N: Eliminação de uma réplica de espera.
- I -> S: Promoção do secundário ocioso para o secundário ativo, de modo a que os seus agradecimentos contribuam para o quórum.
- I -> P: Promoção do secundário ocioso para as primárias. Isto pode acontecer sob reconfigurações especiais quando o secundário inativo é o candidato correto para ser primário.
- I -> N: Eliminação da réplica secundária ociosa.
- S -> P: Promoção do secundário ativo para o primário. Isto pode ser devido à falha do movimento primário ou primário iniciado pelo Cluster Resource Manager. Por exemplo, pode ser em resposta a uma atualização da aplicação ou ao equilíbrio de carga.
- S -> N: Eliminação da réplica secundária ativa.
- P -> S: Despromoção da réplica primária. Isto pode ser devido a um movimento primário iniciado pelo Cluster Resource Manager. Por exemplo, pode ser em resposta a uma atualização da aplicação ou ao equilíbrio de carga.
- P -> N: Eliminação da réplica primária.

> [!NOTE]
> Modelos de programação de alto nível, como [Reliable Actors](service-fabric-reliable-actors-introduction.md) e [Reliable Services,](service-fabric-reliable-services-introduction.md)escondem o conceito de réplica de papéis do desenvolvedor. Nos Atores, a noção de papel é desnecessária. Nos Serviços, é amplamente simplificado para a maioria dos cenários.
>

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre os conceitos de Tecido de Serviço, consulte o seguinte artigo:

[Ciclo de vida do Reliable Services - C#](service-fabric-reliable-services-lifecycle.md)

