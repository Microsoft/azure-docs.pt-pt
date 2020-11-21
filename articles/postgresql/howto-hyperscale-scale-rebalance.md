---
title: Fragmentos de reequilíbrio - Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Distribua os fragmentos uniformemente através dos servidores para um melhor desempenho
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 11/17/2020
ms.openlocfilehash: b69c4fc3ff16cf30181a3529f61af7126b92950b
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026391"
---
# <a name="rebalance-shards-in-hyperscale-citus-server-group"></a>Fragmentos de reequilíbrio no grupo de servidores Hyperscale (Citus)

Para tirar partido dos nós recém-adicionados, é necessário reequilibrar os fragmentos de mesa [distribuídos,](concepts-hyperscale-distributed-data.md#shards)o que significa mover alguns fragmentos dos nós existentes para os novos. Verifique primeiro se os novos trabalhadores terminaram com êxito o fornecimento. Em seguida, inicie o reequilíbrio de fragmentos, ligando-o ao nó coordenador do cluster com o psql e a correr:

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

A [função rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards) reequilibra todas as tabelas do grupo de [colocação](concepts-hyperscale-colocation.md) da tabela nomeada no seu argumento. Assim, não é preciso chamar a função para cada mesa distribuída, basta chamá-la numa mesa representativa de cada grupo de colocação.

**Próximos passos**


- Saiba mais sobre [as opções](concepts-hyperscale-configuration-options.md)de desempenho do grupo do servidor .
- [Escalar um grupo de servidor](howto-hyperscale-scale-grow.md) para cima ou para fora
- Consulte o material de referência [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards)
