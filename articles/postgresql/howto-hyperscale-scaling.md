---
title: Dimensionar um banco de dados do Azure para PostgreSQL-grupo de servidores Citus (hiperescala)
description: Ajuste os recursos de memória do grupo de servidores, disco e CPU para lidar com o aumento de carga
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 9/17/2019
ms.openlocfilehash: a69b4988a5ee835381de986edaa5899b09aa9e4e
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262495"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>Dimensionar um grupo de servidores de hiperescala (Citus)

Banco de dados do Azure para PostgreSQL-Citus (hiperescala) fornece dimensionamento de autoatendimento para lidar com o aumento da carga. A portal do Azure facilita a adição de novos nós de trabalho.

Para fazer isso, vá para a guia **Configurar** em seu grupo de servidores de hiperescala (Citus).
Arraste o controle deslizante da **contagem de nós de trabalho** para alterar o valor.

![Controles deslizantes de recursos](./media/howto-hyperscale-scaling/01-sliders-workers.png)

Clique no botão "salvar" para fazer com que o valor alterado tenha efeito.

> [!NOTE]
> Depois de aumentado e salvo, o número de nós de trabalho não pode ser reduzido usando o controle deslizante.
>
> Além disso, o vCores e o armazenamento ainda não podem ser ajustados no coordenador ou em trabalhadores com essa interface do usuário. Abra um tíquete de suporte se a computação em escala nos nós coordenador ou de trabalho for necessária.

Para aproveitar os nós recém-adicionados, você deve reequilibrar os [fragmentos](concepts-hyperscale-distributed-data.md#shards)de tabela distribuída, o que significa mover alguns fragmentos de nós existentes para os novos. Para iniciar o rebalanceador de fragmentos, conecte-se ao nó de coordenador de cluster com psql e execute:

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

A `rebalance_table_shards` função reequilibra todas as tabelas no grupo de [colocalização](concepts-hyperscale-colocation.md) da tabela chamada em seu argumento. Portanto, você não precisa chamar a função para cada tabela distribuída, basta chamá-la em uma tabela representativa de cada grupo de colocalização.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [as opções de desempenho](concepts-hyperscale-configuration-options.md)do grupo de servidores.
