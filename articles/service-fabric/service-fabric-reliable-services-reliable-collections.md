---
title: Introdução ao Reliable Collections nos serviços de monitorização de estado do Azure Service Fabric | Documentos da Microsoft
description: Serviços do Service Fabric com monitorização de estado fornecem as coleções fiáveis que permitem escrever aplicações na cloud altamente disponíveis, escalonáveis e de baixa latência.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: masnider,rajak,zhol
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 1/3/2019
ms.author: aljo
ms.openlocfilehash: 944bb69ed028338efb243c8646035089566ccf80
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56806509"
---
# <a name="introduction-to-reliable-collections-in-azure-service-fabric-stateful-services"></a>Introdução ao Reliable Collections nos serviços de monitorização de estado do Azure Service Fabric

As coleções fiáveis permitem-lhe escrever aplicações na cloud altamente disponíveis, escalonáveis e de baixa latência, como se estivesse escrevendo aplicativos único computador. As classes no **Microsoft.ServiceFabric.Data.Collections** espaço de nomes fornecem um conjunto de coleções que tornam automaticamente seu estado de elevada disponibilidade. Os desenvolvedores precisam programar apenas para as APIs de coleção fiável e permitir que as coleções fiáveis gerir o estado replicado e local.

A principal diferença entre coleções fiáveis e outras tecnologias de alta disponibilidade (por exemplo, Redis, o serviço de tabela do Azure e o serviço filas do Azure) é que o estado é mantido localmente na instância do serviço enquanto também feitas elevada disponibilidade. Isso significa que:

* Todas as leituras são locais, que resulta em baixa latência e alto débito lê.
* Todas as escritas implicar o número mínimo de IOs de rede, o que resulta em baixa latência e alto débito escreve.

![Imagem da evolução de coleções.](media/service-fabric-reliable-services-reliable-collections/ReliableCollectionsEvolution.png)

As coleções fiáveis podem ser consideradas como a evolução natural da **Collections** classes: um novo conjunto de coleções que foram concebidos para as aplicações na cloud e com vários computadores, sem aumentar a complexidade para o programador. Como tal, as coleções fiáveis são:

* Replicadas: Alterações de estado são replicadas para elevada disponibilidade.
* Persistidas: Os dados são mantidos no disco para uma durabilidade contra interrupções em grande escala (por exemplo, uma queda de energia do datacenter).
* Porque as escritas são mantidas e replicadas, não é possível criar um ReliableDictionary volátil, ReliableQueue ou outra coleção fiável que mantém apenas os dados na memória.
* Assíncrona: As APIs são assíncronas para garantir que threads não estão bloqueados durante a incorrer em e/s.
* Transacional: APIs utilizam a abstração de transações para que possa gerir facilmente várias coleções fiáveis dentro de um serviço.

As coleções fiáveis fornecem garantias de consistência forte prontos a utilizar para facilitar o raciocínio sobre estado da aplicação.
Consistência forte é obtida ao garantir que a transação consolidações concluir apenas depois de toda a transação foi registada numa maioria do quórum de réplicas, incluindo as primárias.
Para obter consistência mais fraca, aplicativos podem reconhecer novamente para o cliente/autor do pedido antes de retorna a consolidação assíncrona.

As APIs de coleções fiáveis são uma evolução de APIs de coleções em simultâneo (encontrada no **Concurrent** espaço de nomes):

* Assíncrona: Devolve uma tarefa uma vez que, ao contrário das coleções em simultâneo, as operações são replicadas e persistidas.
* Não parâmetros de saída: Utiliza `ConditionalValue<T>` para devolver uma `bool` e um valor em vez de parâmetros de saída. `ConditionalValue<T>` é como `Nullable<T>` mas não exige que T é um struct.
* Transações: Utiliza um objeto de transação para permitir que o usuário para ações de grupo em várias coleções fiáveis numa transação.

Hoje em dia, **Microsoft.ServiceFabric.Data.Collections** contém três coleções:

* [Dicionário fiável](https://msdn.microsoft.com/library/azure/dn971511.aspx): Representa uma coleção de replicados, transacional e assíncrona de pares chave/valor. Semelhante à **ConcurrentDictionary**, a chave e o valor podem ser de qualquer tipo.
* [Fila do Reliable](https://msdn.microsoft.com/library/azure/dn971527.aspx): Representa uma replicados, transacional e assíncrona strict first in, First Out (FIFO) fila. Semelhante à **ConcurrentQueue**, o valor pode ser de qualquer tipo.
* [Fila do Reliable Concurrent](service-fabric-reliable-services-reliable-concurrent-queue.md): Representa replicado, transacional e assíncrono dentro dos melhor esforços ordenação fila para um débito elevado. Semelhante a **ConcurrentQueue**, o valor pode ser de qualquer tipo.

## <a name="next-steps"></a>Passos Seguintes

* [Diretrizes de coleção fiável e recomendações](service-fabric-reliable-services-reliable-collections-guidelines.md)
* [Trabalhar com as Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Transações e bloqueios](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* Gestão de dados
  * [Cópia de Segurança e Restauro](service-fabric-reliable-services-backup-restore.md)
  * [Notificações](service-fabric-reliable-services-notifications.md)
  * [Serialização das Reliable Collections](service-fabric-reliable-services-reliable-collections-serialization.md)
  * [Serialização e a atualização](service-fabric-application-upgrade-data-serialization.md)
  * [Configuração do Reliable State Manager](service-fabric-reliable-services-configuration.md)
* Outros
  * [Início rápido de serviços fiável](service-fabric-reliable-services-quick-start.md)
  * [Referência do desenvolvedor do Reliable Collections](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
