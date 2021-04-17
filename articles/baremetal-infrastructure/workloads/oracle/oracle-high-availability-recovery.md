---
title: Recupere a sua base de dados Oráculo na Infraestrutura Azure BareMetal
description: Saiba como pode recuperar a sua base de dados Oráculo na Infraestrutura Azure BareMetal.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/15/2021
ms.openlocfilehash: fc7464474d01314a52a77e0f28b1df160a9f42a0
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107590277"
---
# <a name="recover-your-oracle-database-on-azure-baremetal-infrastructure"></a>Recupere a sua base de dados Oráculo na Infraestrutura Azure BareMetal

Embora nenhuma tecnologia única proteja de todos os cenários de falha, combinar funcionalidades oferece aos administradores de bases de dados a capacidade de recuperar a sua base de dados em quase qualquer situação.

## <a name="causes-of-database-failure"></a>Causas da falha da base de dados

Falhas na base de dados podem ocorrer por muitas razões, mas normalmente se enquadram em várias categorias:

- Erros de manipulação de dados.
- Perda de registos de redo online.
- Perda de ficheiros de controlo de bases de dados.
- Perda de ficheiros de dados de base de dados.
- Corrupção de dados físicos.

## <a name="choose-your-method-of-recovery"></a>Escolha o seu método de recuperação

O tipo de recuperação depende do tipo de falha. Digamos que um objeto é largado ou os dados são modificados incorretamente. Em seguida, a solução mais rápida é normalmente fazer uma operação de base de dados de flashback. Noutros casos, a recuperação através de um instantâneo Azure NetApp Files pode fornecer a recuperação desejada. A árvore de decisão do seguinte número representa cenários comuns de falha e recuperação se todas as opções de proteção de dados acima descritas forem implementadas.

:::image type="content" source="media/oracle-high-availability/db-recovery-decision-tree.png" alt-text="Diagrama da árvore de decisão de recuperação da base de dados." lightbox="media/oracle-high-availability/db-recovery-decision-tree.png":::

Tenha em mente que esta árvore de decisão exemplo só é vista a partir da lente de um administrador de base de dados. Cada implementação pode ter diferentes requisitos que podem alterar a ordem das escolhas. Por exemplo, a realização de uma mudança de função de base de dados para uma região diferente através da Data Guard pode ter um efeito adverso no desempenho da aplicação. Poderia dar ao método de recuperação de instantâneos um RTO mais baixo. Para garantir que os requisitos de RTO/RPO são cumpridos, recomendamos que faça estas operações e crie procedimentos documentados para executá-los quando necessário.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a Infraestruturas BareMetal:

- [O que é a Infraestrutura BareMetal em Azure?](../../concepts-baremetal-infrastructure-overview.md)
- [Ligar instâncias de infraestrutura BareMetal em Azure](../../connect-baremetal-infrastructure.md)
