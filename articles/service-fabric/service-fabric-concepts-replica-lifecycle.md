---
title: Réplicas e instâncias no Azure Service Fabric | Documentos da Microsoft
description: Compreender as réplicas e instâncias – sua função e ciclos de vida
services: service-fabric
documentationcenter: .net
author: appi101
manager: anuragg
editor: ''
ms.assetid: d5ab75ff-98b9-4573-a2e5-7f5ab288157a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/10/2018
ms.author: aprameyr
ms.openlocfilehash: 7f8638365b40395a5dd82457c40e5c15209ba1a7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60882417"
---
# <a name="replicas-and-instances"></a>Réplicas e instâncias 
Este artigo fornece uma descrição geral do ciclo de vida das réplicas de serviços com estado e as instâncias de serviços sem estado.

## <a name="instances-of-stateless-services"></a>Instâncias de serviços sem estado
Uma instância de um serviço sem estado é uma cópia da lógica do serviço que é executado em um de nós do cluster. Uma instância dentro de uma partição é identificada exclusivamente pela respetiva **InstanceId**. O ciclo de vida de uma instância é modelado no diagrama seguinte:

![Ciclo de vida de instância](./media/service-fabric-concepts-replica-lifecycle/instance.png)

### <a name="inbuild-ib"></a>InBuild (IB)
Depois do Gestor de recursos de Cluster determina um posicionamento para a instância, ele entra neste estado do ciclo de vida. A instância é iniciada no nó. O anfitrião de aplicação é iniciado, a instância é criada e, em seguida, aberta. Após a conclusão da inicialização, a instância faz a transição para o estado preparado. 

Se o anfitrião de aplicação ou o nó para esta instância falhar, ele passa para o estado de ignorados.

### <a name="ready-rd"></a>Preparado para o (RD)
No estado pronto, a instância está em execução no nó. Se esta instância é um serviço fiável, **RunAsync** ser invocada. 

Se o anfitrião de aplicação ou o nó para esta instância falhar, ele passa para o estado de ignorados.

### <a name="closing-cl"></a>Fecho (CL)
No estado de encerramento, o Azure Service Fabric está no processo de encerrar a instância neste nó. Este encerramento pode ser devido a vários motivos – por exemplo, uma atualização da aplicação, balanceamento de carga ou o serviço a ser eliminado. Após a conclusão de encerramento, faz a transição para o estado ignorado.

### <a name="dropped-dd"></a>Removida (DD)
No estado de ignorados, a instância já não está em execução no nó. Neste momento, o Service Fabric mantém os metadados relativos a esta instância, o que, eventualmente, é eliminado também.

> [!NOTE]
> É possível efetuar a transição de qualquer Estado para o estado ignorado ao utilizar o **ForceRemove** opção `Remove-ServiceFabricReplica`.
>

## <a name="replicas-of-stateful-services"></a>Réplicas dos serviços com estado
Uma réplica de um serviço com monitorização de estado é uma cópia da lógica do serviço em execução num de nós do cluster. Além disso, a réplica mantém uma cópia do Estado do serviço. Dois conceitos relacionados descrevem o ciclo de vida e o comportamento das réplicas com monitoração de estado:
- Ciclo de vida de réplica
- Função de réplica

A discussão a seguir descreve os serviços com estado persistentes. Para serviços com estado volátil (ou na memória), os Estados de baixo e não processados são equivalentes.

![Ciclo de vida de réplica](./media/service-fabric-concepts-replica-lifecycle/replica.png)

### <a name="inbuild-ib"></a>InBuild (IB)
Uma réplica InBuild é uma réplica que tenha criado ou preparado para associar o conjunto de réplicas. Consoante a função de réplica, o IB tem uma semântica diferente. 

Se o anfitrião de aplicação ou o nó para uma réplica InBuild falhar, faz a transição de estado para baixo.

   - **Réplicas primárias de InBuild**: InBuild primário são as réplicas primeiro para uma partição. Esta réplica normalmente acontece quando a partição está a ser criada. Primário InBuild réplicas também surgem quando reiniciar todas as réplicas de uma partição ou são ignorados.

   - **Réplicas IdleSecondary InBuild**: Estes são réplicas novos que são criadas pelo Gestor de recursos de Cluster ou réplicas existentes que tenha ficado inativo e tem de ser adicionada novamente ao conjunto. Essas réplicas são semeadas ou criadas por primário antes de poderem Junte-se o conjunto de réplicas como ActiveSecondary e participar de confirmação de quórum de operações.

   - **Réplicas ActiveSecondary InBuild**: Este estado é observado em algumas consultas. É uma otimização onde definir a réplica não está mudando, mas uma réplica precisa ser compilado. A réplica em si segue as transições de máquina de estado normal (conforme descrito na seção sobre as funções de réplica).

### <a name="ready-rd"></a>Preparado para o (RD)
Uma réplica pronta é uma réplica que está a participar na replicação e quórum confirmação de operações. Estado de prontidão é aplicável a primárias e Active Directory réplicas secundárias.

Se o anfitrião de aplicação ou o nó para uma réplica pronto falhar, faz a transição de estado para baixo.

### <a name="closing-cl"></a>Fecho (CL)
Uma réplica entra no estado de encerramento nos seguintes cenários:

- **A encerrar o código para a réplica**: Recursos de infraestrutura do serviço poderá ter de encerrar o código em execução para uma réplica. Este encerramento pode ser por muitos motivos. Por exemplo, pode acontecer devido a uma aplicação, recursos de infraestrutura ou atualização de infra-estrutura, ou devido a uma falha comunicado pela réplica. Quando a réplica fechar estiver concluída, a réplica faz a transição para o estado para baixo. O estado persistente associado a esta réplica armazenados no disco não foram limpas.

- **Remover a réplica do cluster**: Recursos de infraestrutura do serviço poderá ter de remover o estado persistente e encerrar o código em execução para uma réplica. Este encerramento pode ser por diversos motivos, por exemplo, balanceamento de carga.

### <a name="dropped-dd"></a>Removida (DD)
No estado de ignorados, a instância já não está em execução no nó. Também não há Estado deixado no nó. Neste momento, o Service Fabric mantém os metadados relativos a esta instância, o que, eventualmente, é eliminado também.

### <a name="down-d"></a>Baixo (D)
O estado para baixo, o código de réplica não está em execução, mas o estado persistente para essa réplica existe nesse nó. Uma réplica pode ficar indisponíveis por muitos motivos – por exemplo, o nó de inatividade, uma falha no código de réplica, uma atualização da aplicação ou falhas de réplica.

Uma réplica de baixo é aberta por recursos de infraestrutura do serviço conforme necessário, por exemplo, quando a atualização é concluída no nó.

A função de réplica não é relevante no estado para baixo.

### <a name="opening-op"></a>Abrir (OP)
Uma réplica de baixo insere o estado de abertura, quando o Service Fabric tem de colocar a réplica de cópia de segurança novamente. Por exemplo, este estado pode ser após a conclusão de uma atualização de código para a aplicação num nó. 

Se o anfitrião de aplicação ou o nó para uma réplica de abertura falhar, faz a transição de estado para baixo.

A função de réplica não é relevante no estado de abertura.

### <a name="standby-sb"></a>Modo de espera (SB)
Uma réplica de modo de espera é uma réplica de um serviço persistente que tenha ficado inativo e, em seguida, foi aberto. Esta réplica pode ser utilizada por recursos de infraestrutura do serviço, se precisar de adicionar outra réplica para a réplica definir (porque a réplica já tem uma parte do Estado e o processo de compilação é mais rápido). Depois do StandByReplicaKeepDuration expira, a réplica em modo de espera é descartada.

Se o anfitrião de aplicação ou o nó para uma réplica em espera falhar, faz a transição de estado para baixo.

A função de réplica não é relevante no Estado em espera.

> [!NOTE]
> Qualquer réplica que não está parado ou removida é considerada como estando *cópia*.
>

> [!NOTE]
> É possível efetuar a transição de qualquer Estado para o estado ignorado ao utilizar o **ForceRemove** opção `Remove-ServiceFabricReplica`.
>

## <a name="replica-role"></a>Função de réplica 
A função da réplica determina sua função no conjunto de réplica:

- **Principal (P)**: Há um principal no conjunto de réplicas que é responsável por executar as operações leitura e escrita. 
- **ActiveSecondary (S)**: Estas são as réplicas que recebem atualizações do Estado do primário, aplicá-las e, em seguida, enviar as confirmações de back. Existem vários secundários Active Directory no conjunto de réplicas. O número destas bases de dados secundárias Active Directory determina o número de falhas, que o serviço pode processar.
- **IdleSecondary (I)**: Essas réplicas estão sendo criadas pelo principal. Antes de eles podem ser promovidos para o secundário ativo que está ganhando estado dos principais. 
- **Nenhum (N)**: Essas réplicas não tem uma responsabilidade no conjunto de réplicas.
- **Desconhecida (U)**: Esta é a função inicial de uma réplica antes de receber qualquer **ChangeRole** chamada à API do Service Fabric.

O diagrama seguinte ilustra as transições de função de réplica e alguns cenários de exemplo em que pode ocorrer:

![Função de réplica](./media/service-fabric-concepts-replica-lifecycle/role.png)

- U -> P: Criação de uma nova réplica primária.
- U -> I: Criação de uma nova réplica inativa.
- U -> N: Eliminação de uma réplica em espera.
- I -> S: Promoção da inatividade secundário para o secundário ativo para que suas confirmações contribuem para com o quórum.
- I -> P: Promoção da inatividade secundária para primária. Isto pode acontecer em reconfigurações especiais quando ocioso secundário é o candidato correto para primária.
- I -> N: Eliminação da réplica secundária inativa.
- S -> P: Promoção de Active Directory secundária para primária. Isto pode dever-se a ativação pós-falha do primário ou um movimento principal iniciada pelo Gestor de recursos de Cluster. Por exemplo, poderá ser em resposta a uma atualização da aplicação ou o balanceamento de carga.
- S -> N: Eliminação da réplica secundária Active Directory.
- P -> S: Despromoção da réplica primária. Isto pode dever-se um movimento principal iniciado pelo Gestor de recursos de Cluster. Por exemplo, poderá ser em resposta a uma atualização da aplicação ou o balanceamento de carga.
- P -> N: Eliminação da réplica primária.

> [!NOTE]
> Modelos de programação de nível mais alto, como [Reliable Actors](service-fabric-reliable-actors-introduction.md) e [Reliable Services](service-fabric-reliable-services-introduction.md), ocultar o conceito de funções de réplica do desenvolvedor. No Actors, a noção de uma função é desnecessária. Nos serviços, ele é simplificado em grande parte na maioria dos cenários.
>

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre conceitos do Service Fabric, consulte o seguinte artigo:

[Ciclo de vida do Reliable Services - C#](service-fabric-reliable-services-lifecycle.md)

