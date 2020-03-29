---
title: Grupo de servidores de escala - Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Ajuste os recursos de memória de grupo de servidores, disco e CPU para lidar com o aumento da carga
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 3/16/2020
ms.openlocfilehash: fa48ca287c248155a0271b5134be782d8db1c785
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063107"
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

A `rebalance_table_shards` função reequilibra todas as tabelas do grupo de [co-localização](concepts-hyperscale-colocation.md) da tabela nomeada no seu argumento. Assim, não é necessário ligar para a função de cada mesa distribuída, basta chamá-la numa tabela representativa de cada grupo de co-localização.

## <a name="increase-or-decrease-vcores-on-nodes"></a>Aumentar ou diminuir vCores em nódosos

> [!NOTE]
> Esta funcionalidade encontra-se em pré-visualização. Para solicitar uma alteração nos vCores para nós no seu grupo de servidores, contacte o [suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

Além de adicionar novos nós, pode aumentar as capacidades dos nós existentes. Ajustar a capacidade computacional para cima e para baixo pode ser útil para experiências de desempenho, bem como alterações a curto ou longo prazo nas exigências de tráfego.

Para alterar os vCores para todos os nós dos trabalhadores, ajuste o slider **vCores** em **Configuração (por nó de trabalhador)**. Os vCores do nó coordenador podem ser ajustados de forma independente. Clique no link de **configuração Alterar** sob **o nó do coordenador**. Aparecerá um diálogo com sliders para os vCores e capacidade de armazenamento do coordenador. Mude os sliders conforme desejado e selecione **OK**.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [as opções](concepts-hyperscale-configuration-options.md)de desempenho do grupo de servidores.
