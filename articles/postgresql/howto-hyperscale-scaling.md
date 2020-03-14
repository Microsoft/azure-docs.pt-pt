---
title: Grupo de servidores de escala - Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Ajuste os recursos de memória de grupo de servidores, disco e CPU para lidar com o aumento da carga
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: bec2a40d8cf5fb178418ec6bb59a52a0bfe3eb8c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79280446"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>Escala de um grupo de servidores de hiperescala (Citus)

A Base de Dados Azure para PostgreSQL - Hyperscale (Citus) fornece escala de autosserviço para lidar com o aumento da carga. O portal Azure facilita a adição de novos nós operários e o aumento dos vCores dos nós existentes.

## <a name="add-worker-nodes"></a>Adicione os nódosos dos trabalhadores

Para adicionar nós, vá ao **separador Configure** no seu grupo de servidores Hyperscale (Citus).  Arrastar o slider para a contagem do **nó do Trabalhador** altera o valor.

![Sliders de recursos](./media/howto-hyperscale-scaling/01-sliders-workers.png)

Clique no botão **Guardar** para que o valor alterado faça efeito.

> [!NOTE]
> Uma vez aumentado e guardado, o número de nós dos trabalhadores não pode ser diminuído utilizando o slider.

### <a name="rebalance-shards"></a>Reequilibrar fragmentos

Para tirar partido dos nós recém-adicionados, é preciso reequilibrar os fragmentos de mesa [distribuídos,](concepts-hyperscale-distributed-data.md#shards)o que significa mover alguns fragmentos dos nós existentes para os novos. Primeiro, verifique se os novos trabalhadores terminaram com êxito o fornecimento. Em seguida, inicie o reequilíbrio do fragmento, ligando-se ao nó coordenador do cluster com psql e funcionando:

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

A função `rebalance_table_shards` reequilibra todas as tabelas do grupo de [co-localização](concepts-hyperscale-colocation.md) da tabela nomeada no seu argumento. Assim, não é necessário ligar para a função de cada mesa distribuída, basta chamá-la numa tabela representativa de cada grupo de co-localização.

## <a name="increase-vcores"></a>Aumentar vCores

Além de adicionar novos nós, pode aumentar as capacidades dos nós existentes. Esta funcionalidade encontra-se atualmente em pré-visualização — para solicitar vCores aumentados para nós no seu grupo de servidores, contacte o [suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [as opções](concepts-hyperscale-configuration-options.md)de desempenho do grupo de servidores.
