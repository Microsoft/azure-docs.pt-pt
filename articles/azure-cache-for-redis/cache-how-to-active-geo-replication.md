---
title: Configurar a geo-replicação ativa para a Enterprise Azure Cache para instâncias Redis
description: Saiba como replicar o seu Azure Cache para instâncias da Redis Enterprise em regiões do Azure
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: yegu
ms.openlocfilehash: edf7a7cdbd24249205fedb4654aa092755700910
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102035658"
---
# <a name="configure-active-geo-replication-for-enterprise-azure-cache-for-redis-instances-preview"></a>Configure a geo-replicação ativa para a Enterprise Azure Cache para instâncias Redis (Preview)

Neste artigo, você vai aprender a configurar um Cache Azure geo-replicado ativo usando o portal Azure.

Grupos de geo-replicação ativos dois ou mais Enterprise Azure Cache para instâncias Redis em uma única cache que se estende por regiões de Azure. Todos os casos funcionam como as primárias locais. Um requerimento decide quais as instâncias a utilizar para ler e escrever pedidos.

## <a name="create-or-join-an-active-geo-replication-group"></a>Criar ou juntar-se a um grupo de geo-replicação ativo

> [!IMPORTANT]
> A geo-replicação ativa deve ser ativada no momento em que é criada uma Cache Azure para Redis.
>
>

1. Na UI de criação **new Redis Cache,** clique em **Configurar** para configurar a **geo-replicação Ativa** no separador **Avançado.**

    ![Configurar a georreplicação ativa](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-not-configured.png)

1. Crie um novo grupo de replicação, para uma primeira instância de cache, ou selecione um existente da lista.

    ![Caches de ligação](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-new-group.png)

1. Clique **em Configurar** para terminar.

    ![Geo-replicação ativa configurada](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-configured.png)

1. Repita os passos acima para cada instância de cache adicional no grupo de geo-replicação.

## <a name="remove-from-an-active-geo-replication-group"></a>Remover de um grupo de geo-replicação ativo

Para remover uma instância de cache de um grupo de geo-replicação ativo, basta eliminar o caso. As restantes instâncias reconfigurar-se-ão automaticamente.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o Azure Cache para funcionalidades redis.

* [Cache Azure para os níveis de serviço redis](cache-overview.md#service-tiers)
* [Alta disponibilidade para Azure Cache para Redis](cache-high-availability.md)
