---
title: Grupo de servidores de escala - Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Ajuste a memória do grupo do servidor, o disco e os recursos do CPU para lidar com o aumento da carga
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 3/16/2020
ms.openlocfilehash: f8e8e1672f754e843a3bd1c75d496599d31e5f11
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82583996"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>Escalar um grupo de servidores de hiperescala (Citus)

A Azure Database for PostgreSQL - Hyperscale (Citus) fornece escala de autosserviço para lidar com o aumento da carga. O portal Azure facilita a adição de novos nós de trabalhadores e o aumento dos vCores dos nós existentes.

## <a name="add-worker-nodes"></a>Adicione os nó de trabalhador

Para adicionar nós, vá ao **separador Configurar** no seu grupo de servidor Hyperscale (Citus).  Arrastar o slider para **a contagem de nóiros** altera o valor.

![Sliders de recursos](./media/howto-hyperscale-scaling/01-sliders-workers.png)

Clique no botão **Guardar** para fazer com que o valor alterado faça efeito.

> [!NOTE]
> Uma vez aumentado e guardado, o número de nós de trabalhador não pode ser diminuído usando o slider.

### <a name="rebalance-shards"></a>Fragmentos de reequilíbrio

Para tirar partido dos nós recém-adicionados, é necessário reequilibrar os fragmentos de mesa [distribuídos,](concepts-hyperscale-distributed-data.md#shards)o que significa mover alguns fragmentos dos nós existentes para os novos. Verifique primeiro se os novos trabalhadores terminaram com êxito o fornecimento. Em seguida, inicie o reequilíbrio de fragmentos, ligando-o ao nó coordenador do cluster com o psql e a correr:

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

A `rebalance_table_shards` função reequilibra todas as tabelas do grupo de [colocação](concepts-hyperscale-colocation.md) da tabela nomeada no seu argumento. Assim, não é preciso chamar a função para cada mesa distribuída, basta chamá-la numa mesa representativa de cada grupo de colocação.

## <a name="increase-or-decrease-vcores-on-nodes"></a>Aumentar ou diminuir vCores nos nos nos acenos

> [!NOTE]
> Esta funcionalidade encontra-se em pré-visualização. Para solicitar uma alteração em vCores para nós no seu grupo de servidores, [contacte o suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

Além de adicionar novos nós, pode aumentar as capacidades dos nós existentes. Ajustar a capacidade de computação para cima e para baixo pode ser útil para experiências de desempenho, bem como alterações a curto ou longo prazo nas exigências de tráfego.

Para alterar os vCores para todos os nós dos trabalhadores, ajuste o slider **vCores** em **Configuração (por nó de trabalhador)**. Os vCores do nó coordenador podem ser ajustados de forma independente. Clique no link **de configuração Change** sob **o nó do coordenador**. Aparecerá um diálogo com sliders para a capacidade de vCores e armazenamento do coordenador. Mude os sliders conforme desejado e selecione **OK**.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [as opções](concepts-hyperscale-configuration-options.md)de desempenho do grupo do servidor .
