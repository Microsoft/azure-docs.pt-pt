---
title: SQL Server migração de banco de dados para um banco de dados único/em pool
description: Saiba mais sobre como SQL Server a migração de banco de dados para um único banco de dados ou um pool elástico no banco de dados SQL do Azure.
keywords: migração de base de dados, migração de base de dados do sql server, ferramentas de migração de base de dados, migrar base de dados, migrar base de dados sql
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 02/11/2019
ms.openlocfilehash: df1ef21da43bc74809bd9fd71b5dde3906cdb343
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820979"
---
# <a name="sql-server-database-migration-to-azure-sql-database"></a>SQL Server migração de banco de dados para o banco de dados SQL do Azure

Neste artigo, você aprende sobre os principais métodos para migrar um banco de dados SQL Server 2005 ou posterior para um banco de dados único ou em pool no banco de dados SQL do Azure. Para obter informações sobre como migrar para um Instância Gerenciada, consulte [migrar para SQL Server instância para instância gerenciada do banco de dados SQL do Azure](sql-database-managed-instance-migrate.md). Para obter informações de migração sobre como migrar de outras plataformas, consulte [Guia de migração de banco de dados do Azure](https://datamigration.microsoft.com/).

## <a name="migrate-to-a-single-database-or-a-pooled-database"></a>Migrar para um banco de dados individual ou um banco de dados em pool

Há dois métodos principais para migrar um banco de dados SQL Server 2005 ou posterior para um banco de dados único ou em pool no banco de dados SQL do Azure. O primeiro método é mais simples, mas necessita de algum, possivelmente substancial, período de indisponibilidade durante a migração. O segundo método é mais complexo, mas elimina significativamente o período de indisponibilidade durante a migração.

Em ambos os casos, você precisa garantir que o banco de dados de origem seja compatível com o banco de dados SQL do Azure usando o [Assistente de migração de dados (DMA)](https://www.microsoft.com/download/details.aspx?id=53595). O banco de dados SQL V12 está se aproximando da [paridade de recursos](sql-database-features.md) com SQL Server, além de problemas relacionados a operações de nível de servidor e de banco de dados. Bases de dados e aplicações que dependem de [funções parcialmente suportadas ou não suportadas](sql-database-transact-sql-information.md) precisam de alguma [reengenharia para corrigir estas incompatibilidades](sql-database-single-database-migrate.md#resolving-database-migration-compatibility-issues) antes que as bases de dados do SQL Server possam ser migradas.

> [!NOTE]
> Para migrar uma base de dados não SQL Server, incluindo Microsoft Access, Sybase, MySQL Oracle e DB2 para a Base de Dados SQL do Azure, veja [SQL Server Migration Assistant (Assistente de Migração do SQL Server)](https://blogs.msdn.microsoft.com/datamigration/2017/09/29/release-sql-server-migration-assistant-ssma-v7-6/).

## <a name="method-1-migration-with-downtime-during-the-migration"></a>Método 1: migração com período de indisponibilidade durante a migração

 Use esse método para migrar para um banco de dados único ou em pool se você puder pagar algum tempo de inatividade ou se estiver executando uma migração de teste de um banco de dados de produção para migração posterior. Para obter um tutorial, consulte [migrar um banco de dados SQL Server](../dms/tutorial-sql-server-to-azure-sql.md).

A lista a seguir contém o fluxo de trabalho geral para uma migração de banco de dados SQL Server de um banco de dados único ou em pool usando esse método. Para a migração para Instância Gerenciada, consulte [migração para um instância gerenciada](sql-database-managed-instance-migrate.md).

  ![Diagrama de migração de VSSSDT](./media/sql-database-cloud-migrate/azure-sql-migration-sql-db.png)

1. [Avalie](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem) a compatibilidade do banco de dados usando a versão mais recente do [Assistente de migração de dados (DMA)](https://www.microsoft.com/download/details.aspx?id=53595).
2. Prepare quaisquer correções necessárias como scripts do Transact-SQL.
3. Faça uma cópia transacionalmente consistente do banco de dados de origem que está sendo migrado ou pare a ocorrência de novas transações no banco de dados de origem durante a migração. Os métodos para realizar essa última opção incluem desabilitar a conectividade do cliente ou criar um [instantâneo do banco de dados](https://msdn.microsoft.com/library/ms175876.aspx). Após a migração, você poderá usar a replicação transacional para atualizar os bancos de dados migrados com alterações que ocorrem após o ponto de corte para a migração. Consulte [migrar usando a migração transacional](sql-database-single-database-migrate.md#method-2-use-transactional-replication).  
4. Implemente os scripts do Transact-SQL para aplicar as correções na cópia da base de dados.
5. [Migre](https://docs.microsoft.com/sql/dma/dma-migrateonpremsql) a cópia do banco de dados para um novo banco de dados SQL do Azure usando o assistente de migração de dados.

> [!NOTE]
> Em vez de usar o DMA, você também pode usar um arquivo BACPAC. Consulte [importar um arquivo BACPAC para um novo banco de dados SQL do Azure](sql-database-import.md).

### <a name="optimizing-data-transfer-performance-during-migration"></a>Otimizar o desempenho da transferência de dados durante a migração

A lista seguinte contém recomendações para um melhor desempenho durante o processo de importação.

- Escolha a camada de serviço e o tamanho de computação mais altos que seu orçamento permite para maximizar o desempenho de transferência. Pode reduzir verticalmente depois de a migração ser concluída para economizar.
- Minimize a distância entre o arquivo BACPAC e o data center de destino.
- Desative as estatísticas automáticas durante a migração
- Tabelas e índices de partição
- Remova as vistas indexadas e recrie-as quando estiver terminado
- Remova os dados históricos raramente consultados para outra base de dados e migre-os para uma base de dados SQL do Azure separada. Em seguida, pode consultar este dados históricos com [consultas elásticas](sql-database-elastic-query-overview.md).

### <a name="optimize-performance-after-the-migration-completes"></a>Otimizar o desempenho após a migração estar concluída

[Atualize as estatísticas](https://msdn.microsoft.com/library/ms187348.aspx) com uma análise completa após a migração estar concluída.

## <a name="method-2-use-transactional-replication"></a>Método 2: Utilizar a Replicação Transacional

Quando não pode remover a base de dados do SQL Server da produção enquanto a migração está a ocorrer, pode utilizar a replicação transacional do SQL Server como a sua solução de migração. Para utilizar este método, a base de dados de origem tem de cumprir os [requisitos para a replicação transacional](https://msdn.microsoft.com/library/mt589530.aspx) e ser compatível com a Base de Dados SQL do Azure. Para obter informações sobre a replicação do SQL com Always On, consulte [Configurar a replicação para Always on grupos de disponibilidade (SQL Server)](/sql/database-engine/availability-groups/windows/configure-replication-for-always-on-availability-groups-sql-server).

Para utilizar esta solução, configure a Base de Dados SQL do Azure como um subscritor para a instância do SQL Server que pretende migrar. O distribuidor de replicação transacional sincroniza os dados da base de dados a serem sincronizados (o editor) enquanto continuam a ocorrer novas transações.

Com a replicação transacional, todas as alterações aos seus dados ou esquema aparecem na sua Base de Dados SQL do Azure. Assim que a sincronização estiver concluída e estiver pronto para migrar, altere a cadeia de ligação das suas aplicações para apontá-las para a Base de Dados SQL do Azure. Assim que a replicação transacional drena quaisquer alterações restantes na sua base de dados de origem e todas as suas aplicações apontam para a BD do Azure, pode desinstalar a replicação transacional. A Base de Dados SQL do Azure é agora o seu sistema de produção.

 ![Diagrama de SeedCloudTR](./media/sql-database-cloud-migrate/SeedCloudTR.png)

> [!TIP]
> Também pode utilizar a replicação transacional para migrar um subconjunto da sua base de dados de origem. A publicação que replica para a Base de Dados SQL do Azure pode ser limitada a um subconjunto de tabelas na base de dados a ser replicada. Para cada tabela a ser replicada, pode limitar os dados a um subconjunto das linhas e/ou um subconjunto das colunas.

## <a name="migration-to-sql-database-using-transaction-replication-workflow"></a>Migração para a Base de Dados SQL com fluxo de trabalho de Replicação de Transação

> [!IMPORTANT]
> Utilize a versão mais recente do SQL Server Management Studio, para permanecer sincronizado com as atualizações do Microsoft Azure e da Base de Dados SQL. As versões anteriores do SQL Server Management Studio não podem configurar a Base de Dados SQL como um subscritor. [Atualize o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

1. Configurar Distribuição
   - [Com o SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms151192.aspx#Anchor_1)
   - [Com o Transact-SQL](https://msdn.microsoft.com/library/ms151192.aspx#Anchor_2)

2. Criar Publicação
   - [Com o SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms151160.aspx#Anchor_1)
   - [Com o Transact-SQL](https://msdn.microsoft.com/library/ms151160.aspx#Anchor_2)
3. Criar Subscrição
   - [Com o SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms152566.aspx#Anchor_0)
   - [Com o Transact-SQL](https://msdn.microsoft.com/library/ms152566.aspx#Anchor_1)

Algumas sugestões e diferenças de migração para a Base de Dados SQL

- Utilizar um distribuidor local
  - Isso causa um impacto no desempenho no servidor.
  - Se o impacto no desempenho for inaceitável pode utilizar outro servidor, mas aumenta a complexidade na gestão e na administração.
- Quando seleciona uma pasta de instantâneos, certifique-se de que a pasta que seleciona é suficientemente grande para conter um BCP de cada tabela que pretende replicar.
- A criação do instantâneo bloqueia as tabelas associadas até estar concluída, por isso, agende adequadamente o instantâneo.
- Apenas são suportadas subscrições push na Base de Dados SQL do Azure. Apenas pode adicionar subscritores da base de dados de origem.

## <a name="resolving-database-migration-compatibility-issues"></a>Corrigir problemas de compatibilidade de migração de bases de dados

Existe uma ampla variedade de problemas de compatibilidade que pode encontrar, dependendo da versão do SQL Server na base de dados de origem e da complexidade da base de dados que está a migrar. As versões mais antigas do SQL Server têm mais problemas de compatibilidade. Utilize os seguintes recursos, para além de uma pesquisa na Internet direcionada com o seu motor de busca preferido:

- [Funcionalidades da base de dados do SQL Server não suportadas na Base de Dados SQL do Azure](sql-database-transact-sql-information.md)
- [Funcionalidade do Motor da Base de Dados Descontinuada no SQL Server 2016](https://msdn.microsoft.com/library/ms144262%28v=sql.130%29)
- [Funcionalidade do Motor da Base de Dados Descontinuada no SQL Server 2014](https://msdn.microsoft.com/library/ms144262%28v=sql.120%29)
- [Funcionalidade do Motor da Base de Dados Descontinuada no SQL Server 2012](https://msdn.microsoft.com/library/ms144262%28v=sql.110%29)
- [Funcionalidade do Motor da Base de Dados Descontinuada no SQL Server 2008 R2](https://msdn.microsoft.com/library/ms144262%28v=sql.105%29)
- [Funcionalidade do Motor da Base de Dados Descontinuada no SQL Server 2005](https://msdn.microsoft.com/library/ms144262%28v=sql.90%29)

Para além de procurar na Internet e utilizar estes recursos, utilize os [fóruns da comunidade do SQL Server do MSDN](https://social.msdn.microsoft.com/Forums/sqlserver/home?category=sqlserver) ou o [StackOverflow](https://stackoverflow.com/).

> [!IMPORTANT]
> Instância Gerenciada do Banco de Dados SQL permite migrar uma instância existente do SQL Server e seus bancos de dados com problemas mínimos ou sem compatibilidade. Consulte [o que é um instância gerenciada](sql-database-managed-instance.md).

## <a name="next-steps"></a>Passos seguintes

- Utilize o script no blogue de Engenheiros do Azure SQL EMEA para [Monitorizar a utilização de tempdb durante a migração](https://blogs.msdn.microsoft.com/azuresqlemea/2016/12/28/lesson-learned-10-monitoring-tempdb-usage/).
- Utilize o script no blogue de Engenheiros do Azure SQL EMEA para [Monitorizar o espaço do registo de transações da base de dados enquanto a migração está a ocorrer](https://blogs.msdn.microsoft.com/azuresqlemea/2016/10/31/lesson-learned-7-monitoring-the-transaction-log-space-of-my-database/0).
- Para saber mais sobre a migração com ficheiros BACPAC num blogue da Equipa de Aconselhamento ao Cliente do SQL Server, consulte [Migrating from SQL Server to Azure SQL Database using BACPAC Files (Migrar a partir do SQL Server para a Base de Dados SQL do Azure com Ficheiros BACPAC)](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).
- Para obter informações sobre como trabalhar com a hora UTC após a migração, consulte [Modifying the default time zone for your local time zone (Modificar o fuso horário predefinido para o fuso horário local)](https://blogs.msdn.microsoft.com/azuresqlemea/2016/07/27/lesson-learned-4-modifying-the-default-time-zone-for-your-local-time-zone/).
- Para obter informações sobre como alterar o idioma predefinido de uma base de dados após a migração, consulte [How to change the default language of Azure SQL Database (Como alterar o idioma predefinido da Base de Dados SQL do Azure)](https://blogs.msdn.microsoft.com/azuresqlemea/2017/01/13/lesson-learned-16-how-to-change-the-default-language-of-azure-sql-database/).
