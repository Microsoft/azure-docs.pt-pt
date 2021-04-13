---
title: Fragmentos de reequilíbrio - Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Distribua os fragmentos uniformemente através dos servidores para um melhor desempenho
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 04/09/2021
ms.openlocfilehash: 63322fac4c6ad5b705deedcd8a80466ddd803814
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305706"
---
# <a name="rebalance-shards-in-hyperscale-citus-server-group"></a>Fragmentos de reequilíbrio no grupo de servidores Hyperscale (Citus)

Para tirar partido dos nós recém-adicionados, é necessário reequilibrar os fragmentos de mesa [distribuídos,](concepts-hyperscale-distributed-data.md#shards)o que significa mover alguns fragmentos dos nós existentes para os novos.

## <a name="determine-if-the-server-group-needs-a-rebalance"></a>Determinar se o grupo de servidor precisa de um reequilíbrio

O portal Azure pode mostrar-lhe se os dados são distribuídos igualmente entre nós de trabalhadores num grupo de servidores. Para vê-lo, vá à página **do reequilíbrio Shard** no menu de gestão do **grupo Server.** Se os dados forem distorcidos entre os trabalhadores, verá que a mensagem **Reequilibramento é recomendada**, juntamente com uma lista do tamanho de cada nó.

Se os dados já estiverem equilibrados, verá que a mensagem **Reequilibramento não é recomendada neste momento**.

## <a name="run-the-shard-rebalancer"></a>Executar o reequilíbrio de fragmentos

Para iniciar o reequilíbrio de fragmentos, é necessário ligar-se ao nó coordenador do grupo do servidor e executar a função [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards) SQL em tabelas distribuídas. A função reequilibra todas as tabelas do grupo de [colocação](concepts-hyperscale-colocation.md) da tabela nomeada no seu argumento. Assim, não é preciso chamar a função para cada mesa distribuída, basta chamá-la numa mesa representativa de cada grupo de colocação.

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

## <a name="monitor-rebalance-progress"></a>Monitorizar o progresso do reequilíbrio

Para ver o reequilíbrio depois de o iniciar, volte ao portal Azure. Abra a página **do reequilíbrio Shard** na **gestão do grupo Server**. Mostrará a mensagem **Reequilíbrio está em curso** juntamente com duas tabelas.

A primeira tabela mostra o número de fragmentos que se movem para dentro ou para fora de um nó, por exemplo, "6 de 24 se mudaram para cá". A segunda tabela mostra o progresso por tabela de bases de dados: nome, contagem de fragmentos afetados, tamanho dos dados afetados e estado de reequilíbrio.

Selecione o botão **'Renovar'** para atualizar a página. Quando o reequilíbrio estiver completo, dirá novamente que **o reequilíbrio não é recomendado neste momento**.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [as opções](concepts-hyperscale-configuration-options.md)de desempenho do grupo do servidor .
- [Escalar um grupo de servidor](howto-hyperscale-scale-grow.md) para cima ou para fora
- Consulte o material de referência [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards)
