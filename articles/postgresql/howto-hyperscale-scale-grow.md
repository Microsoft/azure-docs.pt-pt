---
title: Grupo de servidores de escala - Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Ajuste a memória do grupo do servidor, o disco e os recursos do CPU para lidar com o aumento da carga
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 11/17/2020
ms.openlocfilehash: 59e6e73c99569b0a35c56d65c1a7ccdfcb394c0f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "95026425"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>Escalar um grupo de servidores de hiperescala (Citus)

A Azure Database for PostgreSQL - Hyperscale (Citus) fornece escala de autosserviço para lidar com o aumento da carga. O portal Azure facilita a adição de novos nós de trabalhadores e o aumento dos vCores dos nós existentes. A adição de nós não provoca tempo de inatividade, e mesmo mover fragmentos para os novos nós (chamado [reequilíbrio de fragmentos](howto-hyperscale-scale-rebalance.md)) acontece sem interromper consultas.

## <a name="add-worker-nodes"></a>Adicione os nó de trabalhador

Para adicionar nós, vá ao separador **de armazenamento Compute +** no seu grupo de servidor Hyperscale (Citus).  Arrastar o slider para **a contagem de nóiros** altera o valor.

:::image type="content" source="./media/howto-hyperscale-scaling/01-sliders-workers.png" alt-text="Sliders de recursos":::

Clique no botão **Guardar** para fazer com que o valor alterado faça efeito.

> [!NOTE]
> Depois de aumentar e guardar o número de nós de trabalho, não o pode reduzir com o controlo de deslize.

> [!NOTE]
> Para tirar partido dos nós recém-adicionados, é necessário [reequilibrar os fragmentos de mesa distribuídos,](howto-hyperscale-scale-rebalance.md)o que significa mover [alguns fragmentos dos](concepts-hyperscale-distributed-data.md#shards) nós existentes para os novos.

## <a name="increase-or-decrease-vcores-on-nodes"></a>Aumentar ou diminuir os vCores em nós

Para além de adicionar novos nós, pode aumentar as capacidades dos nós existentes. Ajustar a capacidade de computação para cima e para baixo pode ser útil para experiências de desempenho, bem como alterações a curto ou longo prazo nas exigências de tráfego.

Para alterar os vCores para todos os nós dos trabalhadores, ajuste o slider **vCores** em **Configuração (por nó de trabalhador)**. Os vCores do nó coordenador podem ser ajustados de forma independente. Ajuste o slider **vCores** na  **Configuração (nó coordenador)**.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [as opções](concepts-hyperscale-configuration-options.md)de desempenho do grupo do servidor .
- [Reequilibrar fragmentos de mesa distribuídos](howto-hyperscale-scale-rebalance.md) para que todos os nós dos trabalhadores possam participar em consultas paralelas
