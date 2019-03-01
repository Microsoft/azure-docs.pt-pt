---
title: Migrar o Azure SQL Data Warehouse existente para o ger2 | Documentos da Microsoft
description: Instruções para migrar um armazém de dados existente para geração 2 e a agenda de migração por região.
services: sql-data-warehouse
author: mlee3gsd
ms.author: anumjs
ms.reviewer: jrasnick
manager: craigg
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: sql-data-warehouse
ms.topic: article
ms.date: 02/09/2019
ms.openlocfilehash: ddc363e6dd9fda1c8c97a0dd89bf3764c48bf06d
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57195309"
---
# <a name="upgrade-your-data-warehouse-to-gen2"></a>Atualizar o seu armazém de dados para a geração 2

Microsoft está ajudando a reduzir o custo ao nível da entrada de executar um armazém de dados.  Mais baixo de computação escalões capazes de processar exigentes consultas estão agora disponíveis para o Azure SQL Data Warehouse. Leia o anúncio completo [inferior computação suporte de camada para Gen2](https://azure.microsoft.com/blog/azure-sql-data-warehouse-gen2-now-supports-lower-compute-tiers/). A nova oferta está disponível nas regiões indicados na tabela abaixo. Regiões suportadas, armazéns de dados de geração 1 existentes podem ser atualizados para ger2 através de um:

- **O processo de atualização automática:** As atualizações automáticas de não começam assim que o serviço está disponível numa região.  Quando as atualizações automáticas de iniciar uma região específica, as atualizações individuais do armazém de dados terá lugar durante a sua agenda de manutenção selecionado.
- [**Self-Atualize para ger2:**](#Self-upgrade-to-Gen2) Pode controlar quando atualizar, fazendo uma atualização automática para a geração 2. Se a sua região ainda não é suportado, pode restaurar a partir de um ponto de restauro diretamente a uma instância de geração 2 numa região suportada.

## <a name="automated-schedule-and-region-availability-table"></a>Agenda automatizada e tabela de disponibilidade de região

A tabela seguinte resume por região quando a escala de computação de geração 2 inferior estará disponível e quando as atualizações automáticas de iniciar. As datas estão sujeitos a alterações. Verifique novamente quando a sua região se tornar disponível.

\* indica que uma agenda específica para a região está atualmente indisponível.

| **Região** | **Inferior Gen2 disponíveis** | **As atualizações automáticas de começar** |
|:--- |:--- |:--- |
| Leste da Austrália |Disponível |1 de Maio de 2019 |
| Sudeste da Austrália |2 de abril de 2019 |1 de Junho de 2019 |
| Sul do Brasil |15 de Maio de 2019 |\* |
| Canadá Central |Disponível |1 de Maio de 2019 |
| Leste do Canadá |\* |\* |
| EUA Central |Disponível |1 de Maio de 2019 |
| Leste da China |\* |\* |
| Leste da China 2 |\* |\* |
| China Norte |\* |\* |
| Norte da China 2 |\* |\* |
| Ásia Oriental |Disponível |1 de Maio de 2019 |
| EUA Leste |Disponível |2 de abril de 2019 |
| EUA Leste 2 |Disponível |2 de abril de 2019 |
| França Central |\* |\* |
| Alemanha Central |\* |\* |
| Alemanha Oeste-Central |1 de Setembro de 2019|2 de Janeiro de 2020 |
| Índia Central |Disponível |1 de Maio de 2019 |
| Índia do Sul |2 de abril de 2019 |1 de Junho de 2019 |
| Leste do Japão |Disponível |1 de Maio de 2019 |
| Oeste do Japão |Disponível |1 de Junho de 2019 |
| Coreia do Sul Central |2 de abril de 2019 |1 de Maio de 2019 |
| Coreia do Sul |2 de abril de 2019 |1 de Junho de 2019 |
| EUA Centro-Norte |2 de abril de 2019 |1 de Junho de 2019 |
| Europa do Norte |Disponível |2 de abril de 2019 |
| EUA Centro-Sul |Disponível |1 de Maio de 2019 |
| Sudeste Asiático |Disponível |2 de abril de 2019 |
| Reino Unido Sul |2 de abril de 2019 |1 de Maio de 2019 |
| Reino Unido Oeste |\*|\* |
| EUA Centro-Oeste |2 de Setembro de 2019 |2 de Janeiro de 2020|
| Europa Ocidental |Disponível |2 de abril de 2019 |
| EUA Oeste |2 de abril de 2019 |1 de Junho de 2019 |
| EUA Oeste 2 |Disponível |2 de abril de 2019 |

## <a name="automatic-upgrade-process"></a>Processo de atualização automática

Com base no gráfico de disponibilidade acima, vai ser agendamento de atualizações automáticas para as suas instâncias de geração 1. Para evitar qualquer interrupção inesperada na disponibilidade do armazém de dados, as atualizações automáticas serão agendadas durante a sua agenda de manutenção. Para obter mais informações sobre agendas, consulte [ver um agendamento de manutenção](viewing-maintenance-schedule.md)

O processo de atualização envolverá uma breve queda da conectividade (aprox. 5 min) como vamos reiniciar o seu armazém de dados.  Assim que o seu armazém de dados foi reiniciado, ele será totalmente disponível para utilização. No entanto, pode ocorrer uma degradação do desempenho enquanto o processo de atualização continua a atualizar os ficheiros de dados em segundo plano. O tempo total para a degradação do desempenho irá variar depende do tamanho dos ficheiros de dados.

Também pode agilizar o processo de atualização de ficheiro de dados, executando [recompilação de índice Alter](sql-data-warehouse-tables-index.md) em todas as tabelas de columnstore primário com uma classe de recursos e SLO maior após o reinício.

> [!NOTE]
> A recriação de ALTER Index é uma operação offline e as tabelas não estarão disponíveis até que a recompilação de seja concluída.

## <a name="self-upgrade-to-gen2"></a>Self-atualizar para ger2

Pode optar por Self-atualização ao seguir estes passos num armazém de dados de geração 1 existente. Se optar por Self-atualizar o, tem de conclui-lo antes de iniciar o processo de atualização automática na sua região. Isso faz com que evite qualquer risco das atualizações automáticas provocam conflito.

Existem duas opções ao conduzir uma atualização automática.  Pode optar por atualizar o atual data warehouse no local ou pode restaurar um armazém de dados de geração 1 para uma instância de geração 2.

- [Atualização in-loco](upgrade-to-latest-generation.md) -esta opção irá atualizar o seu armazém de dados de geração 1 existente para geração 2. O processo de atualização envolverá uma breve queda da conectividade (aprox. 5 min) como vamos reiniciar o seu armazém de dados.  Assim que o seu armazém de dados foi reiniciado, ele será totalmente disponível para utilização. Se ocorrerem problemas durante a atualização, abra um [pedido de suporte](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) e fazer referência a "Atualização de geração 2" como a causa possível.
- [Atualizar a partir do ponto de restauro](sql-data-warehouse-restore.md) - criar um ponto de restauro definidas pelo utilizador no seu armazém de dados de geração 1 atual e, em seguida, restaurar diretamente para uma instância de geração 2. O armazém de dados de geração 1 existente permanecerão em vigor. Quando o restauro estiver concluído, o armazém de dados de geração 2 será totalmente disponível para utilização.  Depois de executar todos os processos de teste e validação na instância de geração 2 restaurada, a instância de geração 1 original pode ser eliminada.

   - Passo 1: No portal do Azure, [criar um ponto de restauro definidas pelo utilizador](sql-data-warehouse-restore.md#create-a-user-defined-restore-point-using-the-azure-portal).
   - Passo 2: Ao restaurar a partir um definidas pelo utilizador do ponto de restauro, defina o "nível de desempenho" camada de geração 2 preferencial.

Poderá ter um período de degradação do desempenho enquanto o processo de atualização continua a atualizar os ficheiros de dados em segundo plano. O tempo total para a degradação do desempenho irá variar depende do tamanho dos ficheiros de dados.

Para agilizar o processo de migração de dados em segundo plano, pode forçar imediatamente movimento de dados, executando [recompilação de índice Alter](sql-data-warehouse-tables-index.md) em todas as tabelas columnstore principal teria de ser consultar numa classe de recursos e SLO maior.

> [!NOTE]
> A recriação de ALTER Index é uma operação offline e as tabelas não estarão disponíveis até que a recompilação de seja concluída.

Se encontrar algum problema com o data warehouse, crie uma [pedido de suporte](sql-data-warehouse-get-started-create-support-ticket.md) e fazer referência a "Atualização de geração 2" como a causa possível.

Para obter mais informações, consulte [atualizar para ger2](upgrade-to-latest-generation.md).

## <a name="migration-frequently-asked-questions"></a>Perguntas freqüentes de migração

**P: Geração 2 custa o mesmo que a geração 1?**

- R: Sim.

**P: Como é que as atualizações afeta os meus scripts de automatização?**

- R: Qualquer script de automação que faz referência a um objetivo de nível de serviço deve ser alterada para corresponder ao equivalente de geração 2.  Ver detalhes [aqui](upgrade-to-latest-generation.md#sign-in-to-the-azure-portal).

**P: O tempo que uma atualização automática normalmente demora?**

- R: Pode atualizar no local ou atualizar a partir de um ponto de restauro.  
   - Atualização in-loco fará com que o seu armazém de dados em instantes, colocar em pausa e retomar.  Um processo em segundo plano continuará enquanto o armazém de dados está online.  
   - Demora mais tempo se estiver a atualizar um ponto de restauro, porque a atualização irá passar pelo processo de restauro completa.

**P: Quanto tempo demorará a atualização automática?**

- R: O tempo de inatividade atual para a atualização é apenas o tempo que demora a colocar em pausa e retomar o serviço, o que é entre 5 a 10 minutos. Após o período de indisponibilidade breve, um processo em segundo plano será executado uma migração de armazenamento. O período de tempo para o processo em segundo plano é depende do tamanho do seu armazém de dados.

**P: Quando a atualização automática terá lugar?**

- R: Durante a sua agenda de manutenção. Tirar partido da sua agenda de manutenção escolhido irá minimizar a interrupção para o seu negócio.

**P: O que devo fazer se o meu processo de atualização em segundo plano parece estar bloqueada?**

 - R: Inicie um reindex das tabelas Columnstore. Tenha em atenção que reindexação da tabela estarão offline durante esta operação.

**P: E se a geração 2 não tem o objetivo de nível de serviço tem na geração 1?**
- R: Se estiver a executar um DW600 ou DW1200 na geração 1, recomenda-se para utilizar DW500c ou DW1000c respectivamente, uma vez que a geração 2 fornece mais memória, recursos e um desempenho mais elevado de geração 1.

**P: Pode desativar cópia de segurança geo?**
- R: Não. Cópia de segurança GEO é uma funcionalidade empresarial para preservar seus dados do armazém de disponibilidade no caso de uma região fica indisponível. Abra um [pedido de suporte](sql-data-warehouse-get-started-create-support-ticket.md) caso tenha outras preocupações.

**P: Existe uma diferença na sintaxe de T-SQL entre a geração 1 e geração 2?**

- R: Não há nenhuma alteração na sintaxe de linguagem T-SQL de geração 1, a geração 2.

**P: Geração 2 suporta o Windows de manutenção?**

- R: Sim.

**P: Será capaz de criar uma nova instância de geração 1 depois de atualizar minha região?**

- R: Não. Depois de atualizar uma região, a criação de novas instâncias de geração 1 será desativada.

## <a name="next-steps"></a>Passos Seguintes

- [Passos de atualização](upgrade-to-latest-generation.md)
- [Janelas de manutenção](maintenance-scheduling.md)
- [Monitor de estado de funcionamento de recursos](https://docs.microsoft.com/azure/service-health/resource-health-overview)
- [Rever antes de iniciar uma migração](upgrade-to-latest-generation.md#before-you-begin)
- [Atualizar no local e atualizar a partir de um ponto de restauro](upgrade-to-latest-generation.md)
- [Criar um ponto de restauro definidas pelo utilizador](sql-data-warehouse-restore.md#restore-through-the-azure-portal)
- [Aprender a restaurar para a geração 2](sql-data-warehouse-restore.md#restore-an-active-or-paused-database-using-the-azure-portal)
- [Abra um pedido de suporte do SQL Data Warehouse](https://go.microsoft.com/fwlink/?linkid=857950)
