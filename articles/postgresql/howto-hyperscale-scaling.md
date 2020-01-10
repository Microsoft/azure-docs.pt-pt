---
title: Dimensionar grupo de servidores-hiperescala (Citus)-banco de dados do Azure para PostgreSQL
description: Ajuste os recursos de memória do grupo de servidores, disco e CPU para lidar com o aumento de carga
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: bec2a40d8cf5fb178418ec6bb59a52a0bfe3eb8c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453059"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>Dimensionar um grupo de servidores de hiperescala (Citus)

Banco de dados do Azure para PostgreSQL-Citus (hiperescala) fornece dimensionamento de autoatendimento para lidar com o aumento da carga. A portal do Azure facilita a adição de novos nós de trabalho e o aumento da vCores dos nós existentes.

## <a name="add-worker-nodes"></a>Adicionar nós de trabalho

Para adicionar nós, vá para a guia **Configurar** em seu grupo de servidores de hiperescala (Citus).  Arrastar o controle deslizante para **contagem de nós de trabalho** altera o valor.

![Controles deslizantes de recursos](./media/howto-hyperscale-scaling/01-sliders-workers.png)

Clique no botão **salvar** para fazer o valor alterado entrar em vigor.

> [!NOTE]
> Depois de aumentado e salvo, o número de nós de trabalho não pode ser reduzido usando o controle deslizante.

### <a name="rebalance-shards"></a>Reequilibrar fragmentos

Para aproveitar os nós recém-adicionados, você deve reequilibrar os [fragmentos](concepts-hyperscale-distributed-data.md#shards)de tabela distribuída, o que significa mover alguns fragmentos de nós existentes para os novos. Primeiro, verifique se os novos trabalhadores concluíram com êxito o provisionamento. Em seguida, inicie o rebalanceador de fragmento, conectando-se ao nó de coordenador de cluster com psql e executando:

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

A função `rebalance_table_shards` reequilibra todas as tabelas no grupo de [colocalização](concepts-hyperscale-colocation.md) da tabela nomeada em seu argumento. Portanto, você não precisa chamar a função para cada tabela distribuída, basta chamá-la em uma tabela representativa de cada grupo de colocalização.

## <a name="increase-vcores"></a>Aumentar vCores

Além de adicionar novos nós, você pode aumentar os recursos dos nós existentes. Este recurso está atualmente em visualização — para solicitar um aumento de vCores para nós no seu grupo de servidores, [entre em contato com o suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [as opções de desempenho](concepts-hyperscale-configuration-options.md)do grupo de servidores.
