---
title: Alta disponibilidade para Azure Cache para Redis
description: Saiba mais sobre a Azure Cache para funcionalidades e opções de alta disponibilidade do Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: yegu
ms.openlocfilehash: f0bb8fd2d0b0ac271a167ad5474a55646bdafc65
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92536798"
---
# <a name="high-availability-for-azure-cache-for-redis"></a>Alta disponibilidade para Azure Cache para Redis

Azure Cache para Redis tem alta disponibilidade incorporada. O objetivo da sua arquitetura de alta disponibilidade é garantir que a sua instância de Redis gerida esteja a funcionar mesmo quando as suas máquinas virtuais subjacentes (VMs) são impactadas por interrupções planeadas ou não planeadas. Oferece taxas percentuais muito maiores do que as que são alcançáveis ao hospedar redis num único VM.

Azure Cache para Redis implementa alta disponibilidade usando vários VMs, *chamados nós,* para uma cache. Configura estes nós de modo a que a replicação de dados e o fracasso ocorram de forma coordenada. Também orquestra operações de manutenção como patching de software Redis. Várias opções de alta disponibilidade estão disponíveis nos níveis Standard e Premium:

| Opção | Descrição | Disponibilidade | Standard | Premium |
| ------------------- | ------- | ------- | :------: | :---: |
| [Replicação padrão](#standard-replication)| Configuração replicada de duplo nó numa única zona de datacenter ou zona de disponibilidade (AZ), com falha automática | 99,9% |✔|✔|
| [Várias réplicas](#multiple-replicas) | Configuração replicada em vários sentidos em um ou mais AZs, com falha automática | 99,95% (com redundância de zona) |-|✔|
| [Redundância entre zonas](#zone-redundancy) | Configuração replicada em vários nóns em AZs, com falha automática | 99,95% (com réplicas múltiplas) |-|✔|
| [Georreplicação](#geo-replication) | Casos de cache ligados em duas regiões, com falha controlada pelo utilizador | 99,9% (para uma única região) |-|✔|

## <a name="standard-replication"></a>Replicação padrão

Um Cache Azure para Redis no nível Standard ou Premium funciona por defeito num par de servidores Redis. Os dois servidores estão hospedados em VMs dedicados. O Redis de código aberto permite que apenas um servidor lide com pedidos de escrita de dados. Este servidor é o nó *primário,* enquanto o outro *replica* . Depois de providenciar os nós do servidor, a Cache Azure para Redis atribui-lhes funções primárias e réplicas. O nó primário geralmente é responsável pela manutenção da escrita, bem como por pedidos de leitura de clientes Redis. Numa operação de escrita, compromete uma nova chave e uma atualização chave para a sua memória interna e responde imediatamente ao cliente. Remete a operação para a réplica assíncronea.

:::image type="content" source="media/cache-high-availability/replication.png" alt-text="Configuração da replicação de dados":::
   
>[!NOTE]
>Normalmente, um cliente Redis comunica com o nó primário numa cache Redis para todos os pedidos de leitura e escrita. Certos clientes redis podem ser configurados para ler a partir do nó de réplica.
>
>

Se o nó primário numa cache Redis não estiver disponível, a réplica promover-se-á para se tornar o novo primário automaticamente. Este processo é chamado de *failover* . A réplica vai esperar muito tempo antes de assumir o caso de o nó primário recuperar rapidamente. Quando um failover acontece, Azure Cache for Redis fornece um novo VM e junta-o à cache como o nó de réplica. A réplica executa uma sincronização completa de dados com a primária de modo a ter outra cópia dos dados da cache.

Um nó primário pode sair de serviço como parte de uma atividade de manutenção planeada, como software Redis ou atualização do sistema operativo. Também pode deixar de funcionar devido a eventos não planeados, como falhas em hardware, software ou rede subjacentes. [Failover e patching para Azure Cache para Redis](cache-failover.md) fornece uma explicação detalhada sobre tipos de failovers Redis. Um Azure Cache para Redis passará por muitas falhas durante a sua vida. A arquitetura de alta disponibilidade é projetada para tornar estas mudanças dentro de uma cache o mais transparente possível para os seus clientes.

## <a name="multiple-replicas"></a>Várias réplicas

>[!NOTE]
>Isto está disponível como pré-visualização.
>
>

Azure Cache para Redis permite nómada de réplica adicional no nível Premium. Uma [cache multi-réplica](cache-how-to-multi-replicas.md) pode ser configurada com até três nós de réplica. Ter mais réplicas geralmente melhora a resiliência devido aos nós adicionais que apoiam as primárias. Mesmo com mais réplicas, um Azure Cache para o caso Redis ainda pode ser severamente impactado por uma paragem de datacenter ou AZ- wide. Pode aumentar a disponibilidade de cache utilizando várias réplicas em conjunto com [a redundância de zona.](#zone-redundancy)

## <a name="zone-redundancy"></a>Redundância entre zonas

>[!NOTE]
>Isto está disponível como pré-visualização.
>
>

A azure Cache para Redis suporta configurações redundantes de zona no nível Premium. Uma [cache redundante de zona](cache-how-to-zone-redundancy.md) pode colocar os seus nós em [diferentes Zonas de Disponibilidade de Azure](../availability-zones/az-overview.md) na mesma região. Elimina o datacenter ou a paragem de AZ como um único ponto de falha e aumenta a disponibilidade global do seu cache.

O diagrama a seguir ilustra a configuração redundante da zona:

:::image type="content" source="media/cache-high-availability/zone-redundancy.png" alt-text="Configuração da replicação de dados":::
   
Azure Cache para Redis distribui nós numa área de cache redundante de forma redonda sobre as AZs que selecionou. Também determina qual o nó que servirá como principal inicialmente.

Uma cache redundante de zona proporciona falha automática. Quando o nó primário atual não estiver disponível, uma das réplicas assumirá. A sua aplicação pode experimentar um tempo de resposta de cache mais elevado se o novo nó primário estiver localizado num AZ diferente. As AZs estão geograficamente separadas. Mudar de um AZ para outro altera a distância física entre onde a sua aplicação e cache estão hospedados. Esta alteração tem impacto nas latências da rede de ida e volta da sua aplicação para a cache. Espera-se que a latência extra se encava dentro de um intervalo aceitável para a maioria das aplicações. Recomendamos que teste a sua aplicação para garantir que pode funcionar bem com uma cache redundante de zona.

## <a name="geo-replication"></a>Georreplicação

A geo-replicação é projetada principalmente para a recuperação de desastres. Dá-lhe a capacidade de configurar uma Cache Azure para o caso Redis, numa região de Azure diferente, para apoiar a sua cache primária. [Configurar a geo-replicação para Azure Cache para Redis](cache-how-to-geo-replication.md) dá uma explicação detalhada sobre como funciona a geo-replicação.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como configurar a Cache Azure para opções de alta disponibilidade do Redis.

* [Cache Azure para os níveis de serviço Redis Premium](cache-overview.md#service-tiers)
* [Adicione réplicas ao Cache Azure para Redis](cache-how-to-multi-replicas.md)
* [Permitir redundância de zona para Azure Cache para Redis](cache-how-to-zone-redundancy.md)
* [Configurar a geo-replicação para Azure Cache para Redis](cache-how-to-geo-replication.md)