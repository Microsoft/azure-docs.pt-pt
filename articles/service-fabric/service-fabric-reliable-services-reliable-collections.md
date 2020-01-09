---
title: Introdução às coleções confiáveis
description: Service Fabric serviços com estado fornecem coleções confiáveis que permitem que você escreva aplicativos de nuvem altamente disponíveis, escalonáveis e de baixa latência.
ms.topic: conceptual
ms.date: 1/3/2019
ms.openlocfilehash: 48fa682f4c017f66911729e1f581f3aa91cdc28d
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75609728"
---
# <a name="introduction-to-reliable-collections-in-azure-service-fabric-stateful-services"></a>Introdução às coleções confiáveis no Azure Service Fabric serviços com estado

As coleções confiáveis permitem que você escreva aplicativos de nuvem altamente disponíveis, escalonáveis e de baixa latência como se estivesse escrevendo aplicativos de computador único. As classes no namespace **Microsoft. infabric. Data. Collections** fornecem um conjunto de coleções que tornam o estado automaticamente altamente disponível. Os desenvolvedores precisam programar apenas para as APIs de coleção confiável e permitem que as coleções confiáveis gerenciem o estado replicado e local.

A principal diferença entre as coleções confiáveis e outras tecnologias de alta disponibilidade (como Redis, serviço de tabela do Azure e serviço Fila do Azure) é que o estado é mantido localmente na instância do serviço, enquanto também está altamente disponível. Isso significa que:

* Todas as leituras são locais, o que resulta em leituras de baixa latência e alta taxa de transferência.
* Todas as gravações incorrem no número mínimo de IOs de rede, o que resulta em gravações de baixa latência e alta taxa de transferência.

![Imagem de evolução das coleções.](media/service-fabric-reliable-services-reliable-collections/ReliableCollectionsEvolution.png)

As coleções confiáveis podem ser consideradas como a evolução natural das classes **System. Collections** : um novo conjunto de coleções projetadas para os aplicativos de nuvem e vários computadores sem aumentar a complexidade para o desenvolvedor. Assim, as coleções confiáveis são:

* Replicado: as alterações de estado são replicadas para alta disponibilidade.
* Persistido: os dados são persistidos em disco para durabilidade em caso de interrupções em grande escala (por exemplo, uma interrupção de energia do Datacenter).
* Como as gravações são persistidas e replicadas, você não pode criar um ReliableDictionary volátil, ReliableQueue ou outra coleção confiável que persista apenas os dados na memória.
* Assíncrono: as APIs são assíncronas para garantir que os threads não sejam bloqueados quando incorrerem e/s.
* Transacional: as APIs utilizam a abstração de transações para que você possa gerenciar várias coleções confiáveis dentro de um serviço facilmente.

As coleções confiáveis fornecem garantias fortes de consistência prontas para uso para facilitar o raciocínio sobre o estado do aplicativo.
A consistência forte é obtida garantindo que as confirmações de transação sejam concluídas somente depois que a transação inteira tiver sido registrada em um quorum de maioria das réplicas, incluindo a primária.
Para obter uma consistência mais fraca, os aplicativos podem reconhecer o cliente/solicitante antes que a confirmação assíncrona seja retornada.

As APIs de coleções confiáveis são uma evolução de APIs de coleções simultâneas (encontradas no namespace **System. Collections. decurrent** ):

* Assíncrono: retorna uma tarefa desde, ao contrário das coleções simultâneas, as operações são replicadas e persistidas.
* Sem parâmetros de saída: usa `ConditionalValue<T>` para retornar um `bool` e um valor em vez de parâmetros de saída. `ConditionalValue<T>` é como `Nullable<T>`, mas não requer que T seja um struct.
* Transações: usa um objeto de transação para permitir que o usuário agrupe ações em várias coleções confiáveis em uma transação.

Hoje, **o Microsoft. infabric. Data. Collections** contém três coleções:

* [Dicionário confiável](https://msdn.microsoft.com/library/azure/dn971511.aspx): representa uma coleção replicada, transacional e assíncrona de pares de chave/valor. Semelhante a **ConcurrentDictionary**, a chave e o valor podem ser de qualquer tipo.
* [Fila confiável](https://msdn.microsoft.com/library/azure/dn971527.aspx): representa uma fila PEPS (primeiro a entrar, primeiro a sair) estrita replicada, transacional e assíncrona. Semelhante a **ConcurrentQueue**, o valor pode ser de qualquer tipo.
* [Fila simultânea confiável](service-fabric-reliable-services-reliable-concurrent-queue.md): representa uma fila de ordenação de melhor esforço replicada, transacional e assíncrona para alta taxa de transferência. Semelhante ao **ConcurrentQueue**, o valor pode ser de qualquer tipo.

## <a name="next-steps"></a>Passos seguintes

* [Diretrizes de coleta confiável & recomendações](service-fabric-reliable-services-reliable-collections-guidelines.md)
* [Trabalhar com as Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Transações e bloqueios](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* Gerenciando dados
  * [Cópia de Segurança e Restauro](service-fabric-reliable-services-backup-restore.md)
  * [Notificações](service-fabric-reliable-services-notifications.md)
  * [Serialização das Reliable Collections](service-fabric-reliable-services-reliable-collections-serialization.md)
  * [Serialização e atualização](service-fabric-application-upgrade-data-serialization.md)
  * [Configuração do Gerenciador de estado confiável](service-fabric-reliable-services-configuration.md)
* Outros
  * [Início rápido de Reliable Services](service-fabric-reliable-services-quick-start.md)
  * [Referência do desenvolvedor para coleções confiáveis](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
