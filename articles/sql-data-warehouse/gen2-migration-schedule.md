---
title: Migre seus SQL Data Warehouse do Azure existentes para o Gen2 | Microsoft Docs
description: Instruções para migrar um data warehouse existente para o Gen2 e o agendamento de migração por região.
services: sql-data-warehouse
author: mlee3gsd
ms.author: anjangsh
ms.reviewer: jrasnick
manager: craigg
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: sql-data-warehouse
ms.topic: article
ms.date: 07/22/2019
ms.openlocfilehash: d4724672510d6ccbbc819691d621400cb00d8c9a
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405453"
---
# <a name="upgrade-your-data-warehouse-to-gen2"></a>Atualizar seu data warehouse para o Gen2

A Microsoft está ajudando a reduzir o custo de nível de entrada da execução de um data warehouse.  As camadas de computação mais baixas capazes de lidar com consultas exigentes agora estão disponíveis para o Azure SQL Data Warehouse. Leia o suporte de [camada de computação inferior](https://azure.microsoft.com/blog/azure-sql-data-warehouse-gen2-now-supports-lower-compute-tiers/)do anúncio completo para Gen2. A nova oferta está disponível nas regiões indicadas na tabela a seguir. Para regiões com suporte, os data warehouses Gen1 existentes podem ser atualizados para Gen2 por meio de um dos dois:

- **O processo de atualização automática:** As atualizações automáticas não são iniciadas assim que o serviço está disponível em uma região.  Quando as atualizações automáticas forem iniciadas em uma região específica, as atualizações individuais do DW ocorrerão durante o agendamento de manutenção selecionado.
- [**Atualização automática para Gen2:** ](#self-upgrade-to-gen2) Você pode controlar quando atualizar fazendo uma autoatualização para Gen2. Se sua região ainda não tiver suporte, você poderá restaurar de um ponto de restauração diretamente para uma instância do Gen2 em uma região com suporte.

## <a name="automated-schedule-and-region-availability-table"></a>Agenda automatizada e tabela de disponibilidade de região

A tabela a seguir resume por região quando a camada de computação Gen2 inferior estará disponível e quando as atualizações automáticas forem iniciadas. As datas estão sujeitas a alterações. Verifique novamente para ver quando sua região se torna disponível.

\*indica que uma agenda específica para a região está indisponível no momento.

| **Região** | **Gen2 inferior disponíveis** | **Início de atualizações automáticas** |
|:--- |:--- |:--- |
| Leste da Austrália |Disponível |Completo |
| Sudeste da Austrália |Disponível |Completo |
| Sul do Brasil |Disponível |Completo |
| Canadá Central |Disponível |Completo |
| Leste do Canadá |1º de junho de 2020 |1º de julho de 2020 |
| EUA Central |Disponível |Completo |
| Leste da China |\* |\* |
| Leste da China 2 |Disponível |Completo |
| Norte da China |\* |\* |
| Norte da China 2 |Disponível |Completo |
| Ásia Oriental |Disponível |Completo |
| East US |Disponível |Completo |
| EUA Leste 2 |Disponível |Completo |
| França Central |Disponível |Em curso |
| Alemanha Central |\* |\* |
| Alemanha Oeste-Central |1º de setembro de 2019|1 de outubro de 2019 |
| Índia Central |Disponível |Completo |
| Índia do Sul |Disponível |Completo |
| Oeste da Índia |1º de julho de 2019 |Em curso |
| Leste do Japão |Disponível |Completo |
| Oeste do Japão |Disponível |Completo |
| Coreia do Sul Central |Disponível |Completo |
| Coreia do Sul |Disponível |Completo |
| EUA Centro-Norte |Disponível |Completo |
| Europa do Norte |Disponível |Completo |
| Norte da África do Sul |12 de julho de 2019 |Completo |
| EUA Centro-Sul |Disponível |Completo |
| Sudeste Asiático |Disponível |Completo |
| Norte dos E.A.U. |20 de julho de 2019 |Completo |
| Reino Unido Sul |Disponível |Em curso |
| Reino Unido Oeste |Disponível |Em curso |
| EUA Centro-Oeste |1º de setembro de 2019 |1 de outubro de 2019|
| Europa Ocidental |Disponível |Completo |
| EUA Oeste |Disponível |Completo |
| EUA Oeste 2 |Disponível |Completo |

## <a name="automatic-upgrade-process"></a>Processo de atualização automática

Com base no gráfico de disponibilidade acima, vamos agendar atualizações automatizadas para suas instâncias do Gen1. Para evitar quaisquer interrupções inesperadas na disponibilidade do data warehouse, as atualizações automatizadas serão agendadas durante o agendamento de manutenção. A capacidade de criar uma nova instância de Gen1 será desabilitada em regiões que passam por atualização automática para Gen2. O Gen1 será preterido depois que as atualizações automáticas forem concluídas. Para obter mais informações sobre agendas, consulte [exibir um agendamento de manutenção](viewing-maintenance-schedule.md)

O processo de atualização envolverá uma breve queda na conectividade (aproximadamente 5 min) à medida que reiniciamos o data warehouse.  Depois que o data warehouse for reiniciado, ele estará totalmente disponível para uso. No entanto, você pode enfrentar uma degradação no desempenho enquanto o processo de atualização continua a atualizar os arquivos de dados em segundo plano. O tempo total para a degradação do desempenho vai variar consoante o tamanho dos ficheiros de dados.

Você também pode agilizar o processo de atualização do arquivo de dados executando [ALTER INDEX REBUILD](sql-data-warehouse-tables-index.md) em todas as tabelas columnstore primárias usando uma classe de recurso e SLO maior após a reinicialização.

> [!NOTE]
> ALTER INDEX REBUILD é uma operação offline e as tabelas não estarão disponíveis até que a recompilação seja concluída.

## <a name="self-upgrade-to-gen2"></a>Atualização automática para Gen2

Você pode optar por fazer a atualização automática seguindo estas etapas em um data warehouse Gen1 existente. Se você optar por atualizar automaticamente, deverá concluí-lo antes que o processo de atualização automática comece na sua região. Isso garante que você evite qualquer risco de atualizações automáticas causando um conflito.

Há duas opções ao realizar uma autoatualização.  Você pode atualizar seu data warehouse atual no local ou pode restaurar um data warehouse Gen1 em uma instância do Gen2.

- [Atualização in-loco](upgrade-to-latest-generation.md) -essa opção atualizará o data warehouse Gen1 existente para Gen2. O processo de atualização envolverá uma breve queda na conectividade (aproximadamente 5 min) à medida que reiniciamos o data warehouse.  Depois que o data warehouse for reiniciado, ele estará totalmente disponível para uso. Se você tiver problemas durante a atualização, abra uma [solicitação de suporte](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) e faça referência a "atualização Gen2" como a possível causa.
- [Atualização do ponto de restauração](sql-data-warehouse-restore.md) – crie um ponto de restauração definido pelo usuário em seu Gen1 atual data warehouse e, em seguida, restaure diretamente para uma instância do Gen2. O data warehouse Gen1 existente permanecerá em vigor. Depois que a restauração for concluída, sua data warehouse Gen2 estará totalmente disponível para uso.  Depois de executar todos os processos de validação e teste na instância Gen2 restaurada, a instância Gen1 original poderá ser excluída.

   - Passo 1: No portal do Azure, [crie um ponto de restauração definido pelo usuário](sql-data-warehouse-restore.md#create-a-user-defined-restore-point-using-the-azure-portal).
   - Passo 2: Ao restaurar de um ponto de restauração definido pelo usuário, defina o "nível de desempenho" como sua camada de Gen2 preferida.

Pode observar um período de degradação do desempenho enquanto o processo de atualização continua a atualizar os ficheiros de dados em segundo plano. O tempo total para a degradação do desempenho vai variar consoante o tamanho dos ficheiros de dados.

Para agilizar o processo de migração de dados em segundo plano, você pode forçar a movimentação de dados imediatamente executando [ALTER INDEX REBUILD](sql-data-warehouse-tables-index.md) em todas as tabelas columnstore primárias que você estaria consultando em uma classe de recurso e SLO maior.

> [!NOTE]
> ALTER INDEX REBUILD é uma operação offline e as tabelas não estarão disponíveis até que a recompilação seja concluída.

Se você encontrar problemas com seu data warehouse, crie uma [solicitação de suporte](sql-data-warehouse-get-started-create-support-ticket.md) e faça referência a "atualização Gen2" como a possível causa.

Para obter mais informações, consulte [atualizar para Gen2](upgrade-to-latest-generation.md).

## <a name="migration-frequently-asked-questions"></a>Perguntas frequentes sobre migração

**P: O Gen2 custa o mesmo que Gen1?**

- R: Sim.

**P: Como as atualizações afetarão meus scripts de automação?**

- R: Qualquer script de automação que faz referência a um objetivo de nível de serviço deve ser alterado para corresponder ao equivalente de Gen2.  Veja os detalhes [aqui](upgrade-to-latest-generation.md#sign-in-to-the-azure-portal).

**P: Quanto tempo uma autoatualização normalmente leva?**

- R: Você pode atualizar no local ou atualizar de um ponto de restauração.  
   - A atualização in-loco fará com que o data warehouse Pause e retome momentaneamente.  Um processo em segundo plano continuará enquanto o data warehouse estiver online.  
   - Leva mais tempo se você estiver atualizando por meio de um ponto de restauração, pois a atualização passará pelo processo de restauração completa.

**P: Quanto tempo ocorrerá a atualização automática?**

- R: O tempo de inatividade real para a atualização é apenas o tempo que demora a colocar em pausa e a retomar o serviço, entre 5 a 10 minutos. Após o período de indisponibilidade breve, um processo em segundo plano vai executar uma migração de armazenamento. O período de tempo para o processo em segundo plano depende do tamanho do seu armazém de dados.

**P: Quando essa atualização automática ocorrerá?**

- R: Durante o agendamento de manutenção. Aproveitar sua agenda de manutenção escolhida minimizará a interrupção para seus negócios.

**P: O que devo fazer se meu processo de atualização em segundo plano parecer estar preso?**

 - R: Disparar uma reindexação de suas tabelas Columnstore. Observe que a reindexação da tabela ficará offline durante essa operação.

**P: E se Gen2 não tiver o objetivo de nível de serviço que tenho em Gen1?**
- R: Se você estiver executando um DW600 ou DW1200 no Gen1, é aconselhável usar DW500c ou DW1000c respectivamente, pois Gen2 fornece mais memória, recursos e desempenho mais alto do que o Gen1.

**P: Posso desabilitar o backup geográfico?**
- R: Não. O backup geográfico é um recurso empresarial para preservar sua disponibilidade de data warehouse no caso de uma região ficar indisponível. Abra uma [solicitação de suporte](sql-data-warehouse-get-started-create-support-ticket.md) se você tiver outras preocupações.

**P: Há uma diferença na sintaxe de T-SQL entre Gen1 e Gen2?**

- R: Não há nenhuma alteração na sintaxe da linguagem T-SQL de Gen1 para Gen2.

**P: O Gen2 dá suporte a janelas de manutenção?**

- R: Sim.

**P: Poderei criar uma nova instância de Gen1 depois que minha região for atualizada?**

- R: Não. Depois que uma região for atualizada, a criação de novas instâncias de Gen1 será desabilitada.

## <a name="next-steps"></a>Passos Seguintes

- [Etapas de atualização](upgrade-to-latest-generation.md)
- [Janelas de manutenção](maintenance-scheduling.md)
- [Monitor de integridade de recursos](https://docs.microsoft.com/azure/service-health/resource-health-overview)
- [Examine antes de iniciar uma migração](upgrade-to-latest-generation.md#before-you-begin)
- [Atualização in-loco e atualização a partir de um ponto de restauração](upgrade-to-latest-generation.md)
- [Criar um ponto de restauração definido pelo usuário](sql-data-warehouse-restore.md#restore-through-the-azure-portal)
- [Saiba como restaurar para o Gen2](sql-data-warehouse-restore.md#restore-an-active-or-paused-database-using-the-azure-portal)
- [Abrir uma solicitação de suporte SQL Data Warehouse](https://go.microsoft.com/fwlink/?linkid=857950)
