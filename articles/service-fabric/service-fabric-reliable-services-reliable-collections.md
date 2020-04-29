---
title: Introdução a Coleções Fiáveis
description: Serviço Fabric serviços imponentes fornecem coleções fiáveis que permitem escrever aplicações cloud altamente disponíveis, escaláveis e de baixa latência.
ms.topic: conceptual
ms.date: 3/10/2020
ms.openlocfilehash: 78ecc57a4da43bf416839226253e6d0e2f4c1651
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81398437"
---
# <a name="introduction-to-reliable-collections-in-azure-service-fabric-stateful-services"></a>Introdução a Coleções Fiáveis em Serviços De Serviço Azure Tecido

As Coleções Fiáveis permitem-lhe escrever aplicações em nuvem altamente disponíveis, escaláveis e de baixa latência como se estivesse a escrever aplicações de computador únicas. As aulas no **Microsoft.ServiceFabric.Data.Collections** namespace fornecem um conjunto de coleções que automaticamente tornam o seu estado altamente disponível. Os desenvolvedores precisam de programar apenas para as APIs de Recolha Fiável e deixar que as Coleções Fiáveis gerem o estado replicado e local.

A principal diferença entre coleções fiáveis e outras tecnologias de alta disponibilidade (como redis, serviço de mesa Azure e serviço de fila Azure) é que o estado é mantido localmente na instância de serviço, ao mesmo tempo que está altamente disponível. Isto significa que:

* Todas as leituras são locais, o que resulta em baixa latência e leituras de alta qualidade.
* Todos os escritos incorrem no número mínimo de IOs da rede, o que resulta em baixa latência e escritas de alta-adesão.

![Imagem de evolução das coleções.](media/service-fabric-reliable-services-reliable-collections/ReliableCollectionsEvolution.png)

Coleções fiáveis podem ser consideradas como a evolução natural das classes **System.Collections:** um novo conjunto de coleções que são projetadas para a nuvem e aplicações multi-computador sem aumentar a complexidade para o desenvolvedor. Como tal, as Coleções Fiáveis são:

* Replicado: As alterações de Estado são replicadas para alta disponibilidade.
* AsAPHonios: As APIs são assíncronas para garantir que os fios não são bloqueados quando incorrem em IO.
* Transacional: As APIs utilizam a abstração de transações para que possa gerir facilmente várias Coleções Fiáveis dentro de um serviço.
* Persistidos ou voláteis: Os dados podem ser persistidos no disco para durabilidade contra interrupções em larga escala (por exemplo, uma falha de energia do datacenter). Algumas Coleções Fiáveis também suportam um modo volátil (com [Caveats)](service-fabric-reliable-services-reliable-collections-guidelines.md#volatile-reliable-collections)onde todos os dados são mantidos na memória, como uma cache replicada na memória.

As Coleções Fiáveis fornecem fortes garantias de consistência fora da caixa para facilitar o raciocínio sobre o estado de aplicação.
A forte consistência é conseguida garantindo que as transações só terminam depois de toda a transação ter sido registada num quórum maioritário de réplicas, incluindo as primárias.
Para obter uma consistência mais fraca, as aplicações podem reconhecê-lo de volta ao cliente/requester antes da devolução de compromisso síncrono.

As APIs de Coleções Fiáveis são uma evolução das APIs de coleções simultâneas (encontradas no **System.Collections.Scurrent** namespace):

* Assíncronas: Devolve uma tarefa uma vez que, ao contrário das coleções simultâneas, as operações são replicadas e persistidas.
* Sem parâmetros `ConditionalValue<T>` fora: `bool` Usa para devolver um e um valor em vez de fora parâmetros. `ConditionalValue<T>`é `Nullable<T>` como, mas não requer que T seja uma estrutura.
* Transações: Utiliza um objeto de transação para permitir ao utilizador agrupar ações em várias Coleções Fiáveis numa transação.

Hoje, **microsoft.ServiceFabric.Data.Collections** contém três coleções:

* [Dicionário Fiável](https://msdn.microsoft.com/library/azure/dn971511.aspx): Representa uma coleção replicada, transacional e assíncrona de pares chave/valor. Semelhante ao **Dicionário Concurrent,** tanto a chave como o valor podem ser de qualquer tipo.
* [Fila fiável](https://msdn.microsoft.com/library/azure/dn971527.aspx): Representa uma fila replicada, transacional e assíncrona rigorosa em primeiro lugar, primeira vez (FIFO). Semelhante à **Fila De Simultânea,** o valor pode ser de qualquer tipo.
* [Fila Simultânea Fiável](service-fabric-reliable-services-reliable-concurrent-queue.md): Representa uma fila de encomendas replicada, transacional e assíncrona para uma alta entrada. Semelhante à **Fila De Simultânea,** o valor pode ser de qualquer tipo.

## <a name="next-steps"></a>Passos seguintes

* [Diretrizes de recolha fiáveis & recomendações](service-fabric-reliable-services-reliable-collections-guidelines.md)
* [Trabalhar com as Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Transações e Fechaduras](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* Gestão de Dados
  * [Backup e Restauro](service-fabric-reliable-services-backup-restore.md)
  * [Notificações](service-fabric-reliable-services-notifications.md)
  * [Serialização das Reliable Collections](service-fabric-reliable-services-reliable-collections-serialization.md)
  * [Serialização e Upgrade](service-fabric-application-upgrade-data-serialization.md)
  * [Configuração fiável do Gestor do Estado](service-fabric-reliable-services-configuration.md)
* Outros
  * [Serviços fiáveis arranque rápido](service-fabric-reliable-services-quick-start.md)
  * [Referência do desenvolvedor para Coleções Fiáveis](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
