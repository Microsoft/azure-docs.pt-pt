---
title: Migrar a sua piscina SQL para a Gen2
description: Instruções para migrar uma piscina SQL existente para a Gen2 e o calendário de migração por região.
services: synapse-analytics
author: mlee3gsd
ms.author: anjangsh
ms.reviewer: jrasnick
manager: craigg
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: synapse-analytics
ms.topic: article
ms.date: 01/21/2020
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 6f2af826473bfd13f8100796a540d41cbedbb037
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80631571"
---
# <a name="upgrade-your-sql-pool-to-gen2"></a>Atualize a sua piscina SQL para gen2

A Microsoft está a ajudar a reduzir o custo de entrada de gerir uma piscina SQL.  Níveis de computação mais baixos capazes de lidar com consultas exigentes estão agora disponíveis para piscina SQL. Leia o anúncio completo Suporte de nível de [computação inferior para gen2](https://azure.microsoft.com/blog/azure-sql-data-warehouse-gen2-now-supports-lower-compute-tiers/). A nova oferta está disponível nas regiões a seguir. Para as regiões apoiadas, as piscinas SQL gen1 existentes podem ser atualizadas para gen2 através de qualquer um:

- **O processo de atualização automática:** As atualizações automáticas não começam assim que o serviço está disponível numa região.  Quando as atualizações automáticas começarem numa região específica, as atualizações individuais dos armazéns de dados serão efetuadas durante o seu horário de manutenção selecionado.
- [**Auto-upgrade para gen2:**](#self-upgrade-to-gen2) Pode controlar quando fazer upgrade fazendo uma auto-actualização para gen2. Se a sua região ainda não for apoiada, pode restaurar diretamente um ponto de restauro para um caso de Gen2 numa região apoiada.

## <a name="automated-schedule-and-region-availability-table"></a>Tabela automatizada de horários e disponibilidade da região

O quadro seguinte resume por região quando o nível de computação da Baixa Gen2 estará disponível e quando as atualizações automáticas começarem. As datas estão sujeitas a alterações. Volte para ver quando a sua região fica disponível.

\*indica que um horário específico para a região não está atualmente disponível.

| **Região** | **Baixo Gen2 disponível** | **Atualizações automáticas começam** |
|:--- |:--- |:--- |
| Leste do Canadá |1 de junho de 2020 |1 de julho de 2020 |
| Leste da China |\* |\* |
| Norte da China |\* |\* |
| Alemanha Central |\* |\* |
| Alemanha West Central |Disponível |1 de maio de 2020 |
| Oeste da Índia |Disponível |1 de maio de 2020  |

## <a name="automatic-upgrade-process"></a>Processo de atualização automática

Com base no gráfico de disponibilidade acima, estaremos a agendar upgrades automatizados para os seus casos gen1. Para evitar interrupções inesperadas na disponibilidade da piscina SQL, as atualizações automatizadas serão agendadas durante o seu horário de manutenção. A capacidade de criar uma nova instância gen1 será desativada em regiões submetidas a upgrade automático para gen2. A Gen1 será depreciada assim que as atualizações automáticas estiverem concluídas. Para mais informações sobre os horários, consulte [um horário de manutenção](maintenance-scheduling.md#view-a-maintenance-schedule)

O processo de upgrade envolverá uma breve queda na conectividade (aproximadamente 5 min) à medida que reiniciarmos a sua piscina SQL.  Uma vez reiniciada a sua piscina SQL, estará totalmente disponível para utilização. No entanto, poderá experimentar uma degradação no desempenho enquanto o processo de atualização continua a atualizar os ficheiros de dados em segundo plano. O tempo total para a degradação do desempenho vai variar consoante o tamanho dos ficheiros de dados.

Também pode acelerar o processo de atualização de ficheiros de dados executando [a reconstrução](sql-data-warehouse-tables-index.md) do Alter Index em todas as tabelas primárias de colunas utilizando uma classe De SLO maior e de recursos após o reinício.

> [!NOTE]
> A reconstrução do Alter Index é uma operação offline e as tabelas não estarão disponíveis até que a reconstrução esteja concluída.

## <a name="self-upgrade-to-gen2"></a>Auto-upgrade para gen2

Você pode optar por auto-upgrade seguindo estes passos em uma piscina Gen1 SQL existente. Se optar por auto-upgrade, deve completá-lo antes do processo de atualização automática começar na sua região. Ao fazê-lo, evita qualquer risco de atualizações automáticas que causem um conflito.

Existem duas opções para realizar uma auto-actualização.  Você pode atualizar sua piscina SQL atual no lugar ou você pode restaurar uma piscina Gen1 SQL em uma instância Gen2.

- [Upgrade in-place](upgrade-to-latest-generation.md) - Esta opção irá atualizar a sua piscina Gen1 SQL existente para gen2. O processo de upgrade envolverá uma breve queda na conectividade (aproximadamente 5 min) à medida que reiniciarmos a sua piscina SQL.  Uma vez reiniciada a sua piscina SQL, estará totalmente disponível para utilização. Se tiver problemas durante a atualização, abra um pedido de [suporte](sql-data-warehouse-get-started-create-support-ticket.md) e refira -se "Gen2 upgrade" como a causa possível.
- [Upgrade a partir do ponto de restauro](sql-data-warehouse-restore-points.md) - Crie um ponto de restauro definido pelo utilizador na sua piscina SQL gen1 atual e, em seguida, restaure diretamente para uma instância gen2. A piscina Gen1 SQL existente permanecerá no lugar. Uma vez concluída a restauração, a sua piscina Gen2 SQL estará totalmente disponível para utilização.  Depois de ter executado todos os processos de teste e validação na instância Gen2 restaurada, a instância original de Gen1 pode ser eliminada.

  - Passo 1: A partir do portal Azure, [crie um ponto de restauro definido pelo utilizador](sql-data-warehouse-restore-active-paused-dw.md).
  - Passo 2: Ao restaurar de um ponto de restauro definido pelo utilizador, detetete o "Nível de desempenho" para o seu nível Gen2 preferido.

Pode observar um período de degradação do desempenho enquanto o processo de atualização continua a atualizar os ficheiros de dados em segundo plano. O tempo total para a degradação do desempenho vai variar consoante o tamanho dos ficheiros de dados.

Para acelerar o processo de migração de dados de fundo, pode forçar imediatamente o movimento de dados executando [a reconstrução](sql-data-warehouse-tables-index.md) do Alter Index em todas as tabelas primárias de colunas que estaria a consultar numa classe de SLO e recursos maiores.

> [!NOTE]
> A reconstrução do Alter Index é uma operação offline e as tabelas não estarão disponíveis até que a reconstrução esteja concluída.

Se encontrar algum problema com o seu pool SQL, crie um pedido de [suporte](sql-data-warehouse-get-started-create-support-ticket.md) e referência "Upgrade Gen2" como a causa possível.

Para mais informações, consulte [Upgrade para gen2](upgrade-to-latest-generation.md).

## <a name="migration-frequently-asked-questions"></a>Migração frequentemente colocada seleções

**P: A Gen2 custa o mesmo que a Gen1?**

- R: Sim.

**P: Como é que as atualizações vão afetar os meus scripts de automação?**

- R: Qualquer guião de automatização que refira um Objetivo de Nível de Serviço deve ser alterado para corresponder ao equivalente Gen2.  Veja os detalhes [aqui.](upgrade-to-latest-generation.md#upgrade-in-a-supported-region-using-the-azure-portal)

**P: Quanto tempo demora normalmente uma auto-actualização?**

- R: Pode atualizar no local ou atualizar a partir de um ponto de restauro.

  - A atualização no local fará com que a sua piscina SQL faça uma pausa e retoma momentaneamente.  Um processo de fundo continuará enquanto a piscina SQL está on-line.  
  - Demora mais tempo se estiver a atualizar através de um ponto de restauro, porque a atualização passará por todo o processo de restauro.

**P: Quanto tempo demorará a atualização automática?**

- R: O tempo real de paragem para a atualização é apenas o tempo necessário para parar e retomar o serviço, que é entre 5 a 10 minutos. Após o período de indisponibilidade breve, um processo em segundo plano vai executar uma migração de armazenamento. O tempo de duração do processo de fundo depende do tamanho da sua piscina SQL.

**P: Quando será feito este upgrade automático?**

- R: Durante o seu horário de manutenção. A alavancagem do seu horário de manutenção escolhido minimizará a perturbação do seu negócio.

**P: O que devo fazer se o meu processo de atualização de antecedentes parece estar preso?**

- R: Inicie um reíndice das suas tabelas de Colunas. Note que a reindexação da tabela estará offline durante esta operação.

**P: E se a Gen2 não tiver o Objetivo de Nível de Serviço que tenho na Gen1?**

- R: Se estiver a executar um DW600 ou DW1200 na Gen1, é aconselhável utilizar DW500c ou DW1000c, respectivamente, uma vez que a Gen2 fornece mais memória, recursos e desempenho superior ao da Gen1.

**P: Posso desativar o geo-backup?**

- R: Não. Geo-backup é uma funcionalidade da empresa para preservar a disponibilidade do seu pool SQL no caso de uma região ficar indisponível. Abra um pedido de [apoio](sql-data-warehouse-get-started-create-support-ticket.md) se tiver mais preocupações.

**P: Existe uma diferença na sintaxe T-SQL entre gen1 e Gen2?**

- R: Não há alteração na sintaxe linguística T-SQL de Gen1 para Gen2.

**P: A Gen2 suporta janelas de manutenção?**

- R: Sim.

**P: Serei capaz de criar um novo caso Gen1 depois da minha região ter sido melhorada?**

- R: Não. Depois de uma região ter sido melhorada, a criação de novos casos gen1 será desativada.

## <a name="next-steps"></a>Passos seguintes

- [Etapas de upgrade](upgrade-to-latest-generation.md)
- [Janelas de manutenção](maintenance-scheduling.md)
- [Monitor de saúde de recursos](../../service-health/resource-health-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
- [Rever Antes de iniciar uma migração](upgrade-to-latest-generation.md#before-you-begin)
- [Atualize no local e atualize a partir de um ponto de restauro](upgrade-to-latest-generation.md)
- [Criar um ponto de restauro definido pelo utilizador](sql-data-warehouse-restore-points.md)
- [Saiba como restaurar a Gen2](sql-data-warehouse-restore-active-paused-dw.md)
- [Abra um pedido de suporte do SQL Data Warehouse](https://go.microsoft.com/fwlink/?linkid=857950)
