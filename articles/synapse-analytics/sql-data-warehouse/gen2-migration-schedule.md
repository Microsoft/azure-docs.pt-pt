---
title: Migrar a sua piscina SQL dedicada (anteriormente SQL DW) para a Gen2
description: Instruções para migrar uma piscina SQL dedicada existente (anteriormente SQL DW) para a Gen2 e o calendário de migração por região.
services: synapse-analytics
author: mlee3gsd
ms.author: anjangsh
ms.reviewer: jrasnick
manager: craigg
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: synapse-analytics
ms.topic: article
ms.subservice: sql-dw
ms.date: 01/21/2020
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 0ce07ff3ca5fbcc9776792129d3bfb4ef54efe7d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98120126"
---
# <a name="upgrade-your-dedicated-sql-pool-formerly-sql-dw-to-gen2"></a>Atualize a sua piscina SQL dedicada (anteriormente SQL DW) para a Gen2

A Microsoft está a ajudar a reduzir o custo de entrada de executar uma piscina SQL dedicada (anteriormente SQL DW).  Níveis de computação mais baixos capazes de lidar com consultas exigentes estão agora disponíveis para piscina SQL dedicada (anteriormente SQL DW). Leia o anúncio completo [Suporte de nível de computação inferior para a Gen2](https://azure.microsoft.com/blog/azure-sql-data-warehouse-gen2-now-supports-lower-compute-tiers/). A nova oferta está disponível nas regiões anotados no quadro abaixo. Para as regiões apoiadas, a piscina SQL dedicada à Gen1 existente (anteriormente SQL DW) pode ser atualizada para a Gen2 através de:

- **O processo de atualização automática:** As atualizações automáticas não arrancam assim que o serviço estiver disponível numa região.  Quando as atualizações automáticas começarem numa região específica, serão efetuadas atualizações individuais de armazém de dados durante o seu horário de manutenção selecionado.
- [**Auto-actualização para a Gen2:**](#self-upgrade-to-gen2) Pode controlar quando fazer um upgrade para a Gen2. Se a sua região ainda não estiver apoiada, pode restaurar de um ponto de restauração diretamente para uma instância Gen2 numa região apoiada.

## <a name="automated-schedule-and-region-availability-table"></a>Tabela de Disponibilidade de Horário Automatizado e Região

O quadro seguinte resume por região quando o nível de cálculo da Baixa Gen2 estará disponível e quando as atualizações automáticas começarem. As datas estão sujeitas a alterações. Volte a verificar quando a sua região fica disponível.

\* indica que um calendário específico para a região está atualmente indisponível.

| **Região** | **Menor Gen2 disponível** | **Começam as atualizações automáticas** |
|:--- |:--- |:--- |
| Leste do Canadá |1 de junho de 2020 |1 de julho de 2020 |
| Leste da China |\* |\* |
| Norte da China |\* |\* |
| Alemanha Central |\* |\* |
| Alemanha Centro-Oeste |Disponível |1 de maio de 2020 |
| Oeste da Índia |Disponível |1 de maio de 2020  |

## <a name="automatic-upgrade-process"></a>Processo de atualização automática

Com base no gráfico de disponibilidade acima, vamos agendar upgrades automatizados para as suas instâncias da Gen1. Para evitar interrupções inesperadas na disponibilidade da piscina SQL dedicada (anteriormente SQL DW), as atualizações automatizadas serão programadas durante o seu horário de manutenção. A capacidade de criar uma nova instância gen1 será desativada nas regiões submetidas a um upgrade automático para a Gen2. A Gen1 será depreciada assim que as atualizações automáticas estiverem concluídas. Para obter mais informações sobre os horários, consulte [um horário de manutenção](maintenance-scheduling.md#view-a-maintenance-schedule)

O processo de upgrade envolverá uma breve queda na conectividade (aproximadamente 5 min) à medida que reiniciamos a sua piscina DE SQL dedicada (anteriormente SQL DW).  Uma vez reiniciada a sua piscina SQL (anteriormente SQL DW), estará totalmente disponível para utilização. No entanto, poderá sofrer uma degradação no desempenho enquanto o processo de atualização continua a atualizar os ficheiros de dados em segundo plano. O tempo total para a degradação do desempenho vai variar consoante o tamanho dos ficheiros de dados.

Também pode acelerar o processo de atualização de ficheiros de dados executando [a reconstrução do Alter Index](sql-data-warehouse-tables-index.md) em todas as tabelas de colunas primárias utilizando uma SLO maior e classe de recursos após o reinício.

> [!NOTE]
> A reconstrução do Alter Index é uma operação offline e as tabelas não estarão disponíveis até que a reconstrução esteja concluída.

## <a name="self-upgrade-to-gen2"></a>Auto-upgrade para a Gen2

Você pode optar por auto-upgrade seguindo estes passos em uma piscina SQL dedicada gen1 existente (anteriormente SQL DW). Se optar por fazer um auto-upgrade, deve completá-lo antes do início do processo de atualização automática na sua região. Ao fazê-lo, evita-se qualquer risco de as atualizações automáticas provocarem um conflito.

Existem duas opções para realizar um auto-upgrade.  Você pode atualizar seu atual pool SQL dedicado (anteriormente SQL DW) no lugar ou você pode restaurar uma piscina SQL dedicada Gen1 (anteriormente SQL DW) em um exemplo Gen2.

- [Upgrade no local](upgrade-to-latest-generation.md) - Esta opção irá atualizar a sua piscina SQL dedicada à Gen1 (anteriormente SQL DW) para a Gen2. O processo de upgrade envolverá uma breve queda na conectividade (aproximadamente 5 min) à medida que reiniciamos a sua piscina DE SQL dedicada (anteriormente SQL DW).  Uma vez reiniciado, estará totalmente disponível para utilização. Se sentir problemas durante a atualização, abra um pedido de [apoio](sql-data-warehouse-get-started-create-support-ticket.md) e refira a "atualização gen2" como a causa possível.
- [Upgrade do ponto de restauro](sql-data-warehouse-restore-points.md) - Crie um ponto de restauro definido pelo utilizador na sua piscina SQL dedicada à Gen1 (anteriormente SQL DW) e, em seguida, restaure diretamente para uma instância Gen2. A piscina SQL dedicada à Gen1 existente (anteriormente SQL DW) permanecerá no lugar. Uma vez concluída a restauração, a sua piscina SQL dedicada à Gen2 (anteriormente SQL DW) estará totalmente disponível para utilização.  Uma vez executado todos os processos de teste e validação na instância de Gen2 restaurada, a instância original da Gen1 pode ser eliminada.

  - Passo 1: A partir do portal Azure, [crie um ponto de restauro definido pelo utilizador](sql-data-warehouse-restore-active-paused-dw.md).
  - Passo 2: Ao restaurar de um ponto de restauro definido pelo utilizador, desaponte o "Nível de desempenho" para o seu nível gen2 preferido.

Pode observar um período de degradação do desempenho enquanto o processo de atualização continua a atualizar os ficheiros de dados em segundo plano. O tempo total para a degradação do desempenho vai variar consoante o tamanho dos ficheiros de dados.

Para acelerar o processo de migração de dados de fundo, pode forçar imediatamente o movimento de dados executando [a reconstrução do Alter Index](sql-data-warehouse-tables-index.md) em todas as tabelas de colunas primárias que estaria a consultar numa SLO maior e classe de recursos.

> [!NOTE]
> A reconstrução do Alter Index é uma operação offline e as tabelas não estarão disponíveis até que a reconstrução esteja concluída.

Se encontrar algum problema com a sua piscina SQL dedicada (anteriormente SQL DW), crie um pedido de [apoio](sql-data-warehouse-get-started-create-support-ticket.md) e referência "atualização Gen2" como a causa possível.

Para mais informações, consulte [Upgrade para a Gen2](upgrade-to-latest-generation.md).

## <a name="migration-frequently-asked-questions"></a>Migração frequentemente fez perguntas

**P: A Gen2 custa o mesmo que a Gen1?**

- R: Sim.

**P: Como é que as atualizações vão afetar os meus scripts de automação?**

- R: Qualquer script de automatização que refira um Objetivo de Nível de Serviço deve ser alterado para corresponder ao equivalente gen2.  Consulte os detalhes [aqui.](upgrade-to-latest-generation.md#upgrade-in-a-supported-region-using-the-azure-portal)

**P: Quanto tempo demora normalmente um auto-upgrade?**

- R: Pode atualizar no local ou fazer upgrade a partir de um ponto de restauro.

  - A atualização no local fará com que a sua piscina SQL dedicada (anteriormente SQL DW) faça uma pausa momentânea e retome.  Um processo de fundo continuará enquanto a piscina de SQL dedicada (anteriormente SQL DW) está on-line.  
  - Demora mais tempo se estiver a atualizar através de um ponto de restauro, porque a atualização passará por todo o processo de restauro.

**P: Quanto tempo demorará a atualização automática?**

- R: O tempo de paragem real para a atualização é apenas o tempo necessário para parar e retomar o serviço, que está entre 5 a 10 minutos. Após o período de indisponibilidade breve, um processo em segundo plano vai executar uma migração de armazenamento. O tempo de duração do processo de fundo depende do tamanho da sua piscina SQL dedicada (anteriormente SQL DW).

**P: Quando será feita esta atualização automática?**

- R: Durante o seu horário de manutenção. Aproveitar o seu horário de manutenção escolhido minimizará a perturbação do seu negócio.

**P: O que devo fazer se o meu processo de atualização de fundo parece estar preso?**

- R: Inicie um reindexo das suas mesas de colunas. Note que a reindexexão da tabela estará offline durante esta operação.

**P: E se a Gen2 não tiver o objetivo de nível de serviço que tenho na Gen1?**

- R: Se estiver a executar um DW600 ou DW1200 na Gen1, é aconselhável utilizar DW500c ou DW1000c, respectivamente, uma vez que a Gen2 fornece mais memória, recursos e um desempenho superior ao da Gen1.

**P: Posso desativar o geo-backup?**

- R: Não. Geo-backup é uma funcionalidade de empresa para preservar a disponibilidade da sua piscina SQL (anteriormente SQL DW) no caso de uma região ficar indisponível. Abra um [pedido de apoio](sql-data-warehouse-get-started-create-support-ticket.md) se tiver mais preocupações.

**P: Existe uma diferença na sintaxe T-SQL entre a Gen1 e a Gen2?**

- R: Não há alteração na sintaxe linguística T-SQL da Gen1 para a Gen2.

**P: A Gen2 suporta janelas de manutenção?**

- R: Sim.

**P: Serei capaz de criar uma nova instância da Gen1 depois de a minha região ter sido atualizada?**

- R: Não. Após a atualização de uma região, a criação de novos casos da Gen1 será desativada.

## <a name="next-steps"></a>Passos seguintes

- [Atualizar passos](upgrade-to-latest-generation.md)
- [Janelas de manutenção](maintenance-scheduling.md)
- [Monitor de saúde de recursos](../../service-health/resource-health-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
- [Rever Antes de iniciar uma migração](upgrade-to-latest-generation.md#before-you-begin)
- [Atualizar no local e atualizar a partir de um ponto de restauro](upgrade-to-latest-generation.md)
- [Criar um ponto de restauro definido pelo utilizador](sql-data-warehouse-restore-points.md)
- [Saiba como restaurar a Gen2](sql-data-warehouse-restore-active-paused-dw.md)
- [Abra um pedido de apoio ao Azure Synapse Analytics](./sql-data-warehouse-get-started-create-support-ticket.md)