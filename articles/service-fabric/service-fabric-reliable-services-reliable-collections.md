---
title: Introdução a Coleções Fiáveis
description: Serviços estatais de tecido fornecem coleções fiáveis que lhe permitem escrever aplicações de nuvem altamente disponíveis, escaláveis e de baixa latência.
ms.topic: conceptual
ms.date: 3/10/2020
ms.openlocfilehash: 7d705f81b4ad31559886e43226febcd4cf1d345d
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98784381"
---
# <a name="introduction-to-reliable-collections-in-azure-service-fabric-stateful-services"></a>Introdução a Coleções Fiáveis em Serviços Azure Fabric serviços estatais

As Coleções Fiáveis permitem-lhe escrever aplicações em nuvem altamente disponíveis, escaláveis e de baixa latência como se estivesse a escrever aplicações de computador únicas. As classes no **microsoft.serviceFabric.Data.Collections** namespace fornecem um conjunto de coleções que automaticamente tornam o seu estado altamente disponível. Os desenvolvedores precisam programar apenas para as APIs de cobrança fiável e deixar que as Coleções Fiáveis gerem o estado replicado e local.

A principal diferença entre as Coleções Fiáveis e outras tecnologias de alta disponibilidade (como o redis, o serviço Azure Table e o serviço Azure Queue) é que o estado é mantido localmente na área de serviço, ao mesmo tempo que é altamente disponibilizado. Isto significa que:

* Todas as leituras são locais, o que resulta em baixa latência e leituras de alto rendimento.
* Todos os escritos incorrem no número mínimo de IOs de rede, o que resulta em baixa latência e alta produção de escritos.

![Imagem de evolução das coleções.](media/service-fabric-reliable-services-reliable-collections/ReliableCollectionsEvolution.png)

As Coleções Fiáveis podem ser consideradas como a evolução natural das classes **System.Collections:** um novo conjunto de coleções que são projetadas para as aplicações em nuvem e multi-computadores sem aumentar a complexidade para o desenvolvedor. Como tal, as Coleções Fiáveis são:

* Replicado: As alterações do estado são replicadas para uma elevada disponibilidade.
* Assíncronos: As APIs são assíncronos para garantir que os fios não estão bloqueados ao incorrer em IO.
* Transacional: As APIs utilizam a abstração de transações para que possa gerir facilmente várias Coleções Fiáveis dentro de um serviço.
* Persistido ou Volátil: Os dados podem ser persistidos no disco para durabilidade contra falhas em larga escala (por exemplo, uma falha de energia do datacenter). Algumas Coleções Fiáveis também suportam um modo volátil (com [Caveats)](service-fabric-reliable-services-reliable-collections-guidelines.md#volatile-reliable-collections)onde todos os dados são mantidos na memória, como uma cache replicada na memória.

As Coleções Fiáveis fornecem garantias de consistência fortes fora da caixa para facilitar o raciocínio sobre o estado de aplicação.
A forte consistência é alcançada garantindo que os compromissos de transação só terminam depois de toda a transação ter sido registada num quórum maioritário de réplicas, incluindo a primária.
Para obter uma consistência mais fraca, as aplicações podem reconhecer o cliente/solicitador antes que o compromisso assíncrona comprometa devoluções.

As APIs de Coleções Fiáveis são uma evolução das COLEÇÕES SIMultâneas (encontradas no **System.Collections.Concurrent** namespace):

* Assíncrona: Devolve uma tarefa uma vez que, ao contrário de coleções simultâneas, as operações são replicadas e persistiu.
* Sem parâmetros de saída: Usa `ConditionalValue<T>` para devolver um valor e um valor em vez de `bool` parâmetros fora. `ConditionalValue<T>` é `Nullable<T>` como, mas não requer T para ser uma estrutura.
* Transações: Utiliza um objeto de transação para permitir ao utilizador agrupar ações em várias Coleções Fiáveis numa transação.

Hoje, **microsoft.serviceFabric.Data.Collections** contém três coleções:

* [Dicionário Fiável](/dotnet/api/microsoft.servicefabric.data.collections.ireliabledictionary-2#microsoft_servicefabric_data_collections_ireliabledictionary_2): Representa uma coleção replicada, transacional e assíncronea de pares chave/valor. Semelhante ao **Simultâneo,** tanto a chave como o valor podem ser de qualquer tipo.
* [Fila fiável](/dotnet/api/microsoft.servicefabric.data.collections.ireliablequeue-1#microsoft_servicefabric_data_collections_ireliablequeue_1): Representa uma fila replicada, transacional e assíncronda e rígida de primeira para a primeira e primeira saída (FIFO). Semelhante ao **ConcurrentQueue,** o valor pode ser de qualquer tipo.
* [Fila simultânea fiável](service-fabric-reliable-services-reliable-concurrent-queue.md): Representa uma fila replicada, transacional e assíncronea de encomenda para alta produção. Semelhante ao **ConcurrentQueue,** o valor pode ser de qualquer tipo.

## <a name="next-steps"></a>Próximos passos

* [Diretrizes de cobrança fiáveis & recomendações](service-fabric-reliable-services-reliable-collections-guidelines.md)
* [Trabalhar com as Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Transações e Fechaduras](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* Gestão de Dados
  * [Cópia de Segurança e Restauro](service-fabric-reliable-services-backup-restore.md)
  * [Notificações](service-fabric-reliable-services-notifications.md)
  * [Serialização das Reliable Collections](service-fabric-reliable-services-reliable-collections-serialization.md)
  * [Serialização e Atualização](service-fabric-application-upgrade-data-serialization.md)
  * [Configuração fiável do Gestor de Estado](service-fabric-reliable-services-configuration.md)
* Outros
  * [Arranque rápido de serviços fiáveis](service-fabric-reliable-services-quick-start.md)
  * [Referência do programador para Coleções Fiáveis](/dotnet/api/microsoft.servicefabric.data.collections#microsoft_servicefabric_data_collections)
