---
title: Grupo de servidores de escala - Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Ajuste a memória do grupo do servidor, o disco e os recursos do CPU para lidar com o aumento da carga
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 04/07/2021
ms.openlocfilehash: 905224119b9df4e4003b1736443406e9548a49e3
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012534"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>Escalar um grupo de servidores de hiperescala (Citus)

A Azure Database for PostgreSQL - Hyperscale (Citus) fornece escala de autosserviço para lidar com o aumento da carga. O portal Azure facilita a adição de novos nós de trabalhadores e o aumento dos vCores dos nós existentes. A adição de nós não provoca tempo de inatividade, e mesmo mover fragmentos para os novos nós (chamado [reequilíbrio de fragmentos](howto-hyperscale-scale-rebalance.md)) acontece sem interromper consultas.

## <a name="add-worker-nodes"></a>Adicione os nó de trabalhador

Para adicionar nós, vá ao separador **de armazenamento Compute +** no seu grupo de servidor Hyperscale (Citus).  Arrastar o slider para **a contagem de nóiros** altera o valor.

> [!NOTE]
>
> Um grupo de servidores Hyperscale (Citus) criado com o [nível básico (pré-visualização)](concepts-hyperscale-tiers.md) não tem trabalhadores. Aumentar a contagem de trabalhadores gradua automaticamente o grupo de servidor para o nível padrão.
> Depois de formar um grupo de servidores para o nível padrão, não pode desclassificá-lo de volta ao nível básico.

:::image type="content" source="./media/howto-hyperscale-scaling/01-sliders-workers.png" alt-text="Sliders de recursos":::

Clique no botão **Guardar** para fazer com que o valor alterado faça efeito.

> [!NOTE]
> Depois de aumentar e guardar o número de nós de trabalho, não o pode reduzir com o controlo de deslize.

> [!NOTE]
> Para tirar partido dos nós recém-adicionados, é necessário [reequilibrar os fragmentos de mesa distribuídos,](howto-hyperscale-scale-rebalance.md)o que significa mover [alguns fragmentos dos](concepts-hyperscale-distributed-data.md#shards) nós existentes para os novos.

## <a name="increase-or-decrease-vcores-on-nodes"></a>Aumentar ou diminuir os vCores em nós

Para além de adicionar novos nós, pode aumentar as capacidades dos nós existentes. Ajustar a capacidade de computação para cima e para baixo pode ser útil para experiências de desempenho, e alterações a curto ou longo prazo nas exigências de tráfego.

Para alterar os vCores para todos os nós dos trabalhadores, ajuste o slider **vCores** em **Configuração (por nó de trabalhador)**. Os vCores do nó coordenador podem ser ajustados de forma independente. Ajuste o slider **vCores** na  **Configuração (nó coordenador)**.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [as opções](concepts-hyperscale-configuration-options.md)de desempenho do grupo do servidor .
- [Reequilibrar fragmentos de mesa distribuídos](howto-hyperscale-scale-rebalance.md) para que todos os nós dos trabalhadores possam participar em consultas paralelas
