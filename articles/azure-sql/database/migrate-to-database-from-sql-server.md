---
title: Migração da base de dados SQL Server para Base de Dados Azure SQL
description: Saiba mais sobre a migração da base de dados do SQL Server para a Base de Dados Azure SQL.
keywords: migração de base de dados, migração de base de dados do sql server, ferramentas de migração de base de dados, migrar base de dados, migrar base de dados sql
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/11/2019
ms.openlocfilehash: fab52fcea03a2f65c868cfac27f8a8cef115b2be
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94917652"
---
# <a name="sql-server-database-migration-to-azure-sql-database"></a>Migração da base de dados SQL Server para Base de Dados Azure SQL
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Neste artigo, você aprende sobre os métodos primários para migrar um SQL Server 2005 ou mais tarde base de dados para Azure SQL Database. Para obter informações sobre a migração para Azure SQL Managed Instance, consulte [Migrar uma instância do Servidor SQL para Azure SQL Gerenciada Instância](../migration-guides/managed-instance/sql-server-to-managed-instance-overview.md). Para obter orientações sobre a escolha de opções e ferramentas de migração para a Azure SQL, consulte [Migrar para Azure SQL](../migration-guides/index.yml)


## <a name="migrate-to-a-single-database-or-a-pooled-database"></a>Migrar para uma única base de dados ou uma base de dados agrizada

Existem dois métodos primários para migrar uma base de dados SQL Server 2005 ou posterior para Azure SQL Database. O primeiro método é mais simples, mas necessita de algum, possivelmente substancial, período de indisponibilidade durante a migração. O segundo método é mais complexo, mas elimina significativamente o período de indisponibilidade durante a migração.

Em ambos os casos, é necessário garantir que a base de dados de origem é compatível com a Base de Dados Azure SQL utilizando o [Assistente de Migração de Dados (DMA)](https://www.microsoft.com/download/details.aspx?id=53595). A SQL Database está a aproximar-se da paridade de [funcionalidades](features-comparison.md) com o SQL Server, com além de problemas relacionados com operações de nível de servidor e de base de dados cruzadas. Bases de dados e aplicações que dependem de [funções parcialmente suportadas ou não suportadas](transact-sql-tsql-differences-sql-server.md) precisam de alguma [reengenharia para corrigir estas incompatibilidades](migrate-to-database-from-sql-server.md#resolving-database-migration-compatibility-issues) antes que as bases de dados do SQL Server possam ser migradas.

> [!NOTE]
> Para migrar uma base de dados não SQL Server, incluindo Microsoft Access, Sybase, MySQL Oracle e DB2 para a Base de Dados SQL do Azure, veja [SQL Server Migration Assistant (Assistente de Migração do SQL Server)](https://blogs.msdn.microsoft.com/datamigration/2017/09/29/release-sql-server-migration-assistant-ssma-v7-6/).

## <a name="method-1-migration-with-downtime-during-the-migration"></a>Método 1: migração com período de indisponibilidade durante a migração

 Use este método para migrar para uma base de dados única ou agamada se puder pagar algum tempo de inatividade ou se estiver a realizar uma migração de teste de uma base de dados de produção para migração posterior. Para obter um tutorial, consulte [a base de dados do SQL Server](../../dms/tutorial-sql-server-to-azure-sql.md).

A lista seguinte contém o fluxo de trabalho geral para uma migração da base de dados SQL Server de uma base de dados única ou de uma base de dados agrária utilizando este método. Para a migração para o SQL Managed Instance, consulte [a Migração para a SQL Managed Instance](../managed-instance/migrate-to-instance-from-sql-server.md).

  ![Diagrama de migração de VSSSDT](./media/migrate-to-database-from-sql-server/azure-sql-migration-sql-db.png)

1. [Avaliar](/sql/dma/dma-assesssqlonprem) a base de dados para compatibilidade utilizando a versão mais recente do [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595).
2. Prepare quaisquer correções necessárias como scripts do Transact-SQL.
3. Faça uma cópia transaccionalmente consistente da base de dados de origem que está a ser migrada ou impeça que novas transações ocorram na base de dados de origem enquanto a migração está a ocorrer. Os métodos para realizar esta última opção incluem a desativação da conectividade do cliente ou a criação de uma [imagem de base de dados](/sql/relational-databases/databases/create-a-database-snapshot-transact-sql)instantânea. Após a migração, poderá utilizar a replicação transacional para atualizar as bases de dados migradas com alterações que ocorrem após o ponto de corte para a migração. Consulte [a Migração utilizando a Migração Transacional](migrate-to-database-from-sql-server.md#method-2-use-transactional-replication).  
4. Implemente os scripts do Transact-SQL para aplicar as correções na cópia da base de dados.
5. [Migrar](/sql/dma/dma-migrateonpremsql) a cópia da base de dados para uma nova base de dados na Base de Dados Azure SQL utilizando o Assistente de Migração de Dados.

> [!NOTE]
> Em vez de utilizar o DMA, também pode utilizar um ficheiro BACPAC. Consulte [um ficheiro BACPAC para uma nova base de dados na Base de Dados Azure SQL](database-import.md).

### <a name="optimizing-data-transfer-performance-during-migration"></a>Otimizar o desempenho da transferência de dados durante a migração

A lista seguinte contém recomendações para um melhor desempenho durante o processo de importação.

- Escolha o nível de serviço mais elevado e o tamanho do cálculo que o seu orçamento permite maximizar o desempenho da transferência. Pode reduzir verticalmente depois de a migração ser concluída para economizar.
- Minimize a distância entre o seu ficheiro BACPAC e o centro de dados de destino.
- Desativar autoestatística durante a migração
- Tabelas e índices de partição
- Remova as vistas indexadas e recrie-as quando estiver terminado
- Remova dados históricos raramente consultados para outra base de dados e migrar estes dados históricos para uma base de dados separada na Base de Dados Azure SQL. Em seguida, pode consultar este dados históricos com [consultas elásticas](elastic-query-overview.md).

### <a name="optimize-performance-after-the-migration-completes"></a>Otimizar o desempenho após a migração estar concluída

[Atualize as estatísticas](/sql/t-sql/statements/update-statistics-transact-sql) com uma análise completa após a migração estar concluída.

## <a name="method-2-use-transactional-replication"></a>Método 2: Utilizar a Replicação Transacional

Quando não pode remover a base de dados do SQL Server da produção durante a migração, pode utilizar a replicação transacional do SQL Server como solução de migração. Para utilizar este método, a base de dados de origem tem de cumprir os [requisitos para a replicação transacional](./replication-to-sql-database.md) e ser compatível com a Base de Dados SQL do Azure. Para obter informações sobre a replicação SQL com Always On, consulte a replicação de [configuração para grupos de disponibilidade sempre em disponibilidade (SQL Server)](/sql/database-engine/availability-groups/windows/configure-replication-for-always-on-availability-groups-sql-server).

Para utilizar esta solução, configura a sua base de dados na Base de Dados Azure SQL como assinante da instância SQL Server que pretende migrar. O distribuidor de replicação transacional sincroniza os dados da base de dados a serem sincronizados (o editor) enquanto continuam a ocorrer novas transações.

Com a replicação transacional, todas as alterações aos seus dados ou esquema aparecem na sua base de dados na Base de Dados Azure SQL. Assim que a sincronização estiver concluída e estiver pronto a migrar, altere a cadeia de ligação das suas aplicações para as indicar para a sua base de dados. Assim que a replicação transacional drena quaisquer alterações restantes na sua base de dados de origem e todas as suas aplicações apontam para a BD do Azure, pode desinstalar a replicação transacional. A sua base de dados na Base de Dados Azure SQL é agora o seu sistema de produção.

 ![Diagrama de SeedCloudTR](./media/migrate-to-database-from-sql-server/SeedCloudTR.png)

> [!TIP]
> Também pode utilizar a replicação transacional para migrar um subconjunto da sua base de dados de origem. A publicação que replica para a Base de Dados SQL do Azure pode ser limitada a um subconjunto de tabelas na base de dados a ser replicada. Para cada tabela a ser replicada, pode limitar os dados a um subconjunto das linhas e/ou um subconjunto das colunas.

## <a name="migration-to-sql-database-using-transaction-replication-workflow"></a>Migração para a Base de Dados SQL com fluxo de trabalho de Replicação de Transação

> [!IMPORTANT]
> Utilize a versão mais recente do SQL Server Management Studio para permanecer sincronizado com atualizações para Azure e SQL Database. As versões anteriores do SQL Server Management Studio não podem configurar a Base de Dados SQL como um subscritor. [Atualize o SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).

1. Configurar Distribuição
   - [Com o SQL Server Management Studio (SSMS)](/sql/relational-databases/replication/configure-publishing-and-distribution/)
   - [Com o Transact-SQL](/sql/relational-databases/replication/configure-publishing-and-distribution/)

2. Criar Publicação
   - [Com o SQL Server Management Studio (SSMS)](/sql/relational-databases/replication/configure-publishing-and-distribution/)
   - [Com o Transact-SQL](/sql/relational-databases/replication/configure-publishing-and-distribution/)
3. Criar Subscrição
   - [Com o SQL Server Management Studio (SSMS)](/sql/relational-databases/replication/create-a-push-subscription/)
   - [Com o Transact-SQL](/sql/relational-databases/replication/create-a-push-subscription/)

Algumas sugestões e diferenças de migração para a Base de Dados SQL

- Utilizar um distribuidor local
  - Ao fazê-lo, o desempenho tem um impacto no servidor.
  - Se o impacto no desempenho for inaceitável pode utilizar outro servidor, mas aumenta a complexidade na gestão e na administração.
- Quando seleciona uma pasta de instantâneos, certifique-se de que a pasta que seleciona é suficientemente grande para conter um BCP de cada tabela que pretende replicar.
- A criação instantânea bloqueia as tabelas associadas até estar completa, por isso agende o seu instantâneo adequadamente.
- Apenas são suportadas subscrições push na Base de Dados SQL do Azure. Apenas pode adicionar subscritores da base de dados de origem.

## <a name="resolving-database-migration-compatibility-issues"></a>Corrigir problemas de compatibilidade de migração de bases de dados

Existem uma grande variedade de problemas de compatibilidade que poderá encontrar, dependendo tanto da versão do SQL Server na base de dados de origem como da complexidade da base de dados que está a migrar. As versões mais antigas do SQL Server têm mais problemas de compatibilidade. Utilize os seguintes recursos, para além de uma pesquisa na Internet direcionada com o seu motor de busca preferido:

- [Funcionalidades da base de dados do SQL Server não suportadas na Base de Dados SQL do Azure](transact-sql-tsql-differences-sql-server.md)
- [Funcionalidade do Motor da Base de Dados Descontinuada no SQL Server 2016](/sql/database-engine/discontinued-database-engine-functionality-in-sql-server)
- [Funcionalidade do Motor da Base de Dados Descontinuada no SQL Server 2014](/sql/database-engine/discontinued-database-engine-functionality-in-sql-server?viewFallbackFrom=sql-server-2014)
- [Funcionalidade do Motor da Base de Dados Descontinuada no SQL Server 2012](/previous-versions/sql/sql-server-2012/ms144262(v=sql.110))
- [Funcionalidade do Motor da Base de Dados Descontinuada no SQL Server 2008 R2](/previous-versions/sql/sql-server-2008-r2/ms144262(v=sql.105))
- [Funcionalidade do Motor da Base de Dados Descontinuada no SQL Server 2005](/previous-versions/sql/sql-server-2005/ms144262(v=sql.90))

Além de pesquisar na Internet e utilizar estes recursos, utilize a [página de perguntas do Microsoft Q&Uma página de perguntas para Azure SQL Database](/answers/topics/azure-sql-database.html) ou [StackOverflow](https://stackoverflow.com/).

> [!IMPORTANT]
> Azure SQL Managed Instance permite-lhe migrar uma instância do SQL Server existente e as suas bases de dados com problemas mínimos de compatibilidade. Ver [O que é um caso gerido](../managed-instance/sql-managed-instance-paas-overview.md).

## <a name="next-steps"></a>Passos seguintes

- Utilize o script no blogue de Engenheiros do Azure SQL EMEA para [Monitorizar a utilização de tempdb durante a migração](/archive/blogs/azuresqlemea/lesson-learned-10-monitoring-tempdb-usage).
- Utilize o script no blogue de Engenheiros do Azure SQL EMEA para [Monitorizar o espaço do registo de transações da base de dados enquanto a migração está a ocorrer](/archive/blogs/azuresqlemea/lesson-learned-7-monitoring-the-transaction-log-space-of-my-database).
- Para saber mais sobre a migração com ficheiros BACPAC num blogue da Equipa de Aconselhamento ao Cliente do SQL Server, consulte [Migrating from SQL Server to Azure SQL Database using BACPAC Files (Migrar a partir do SQL Server para a Base de Dados SQL do Azure com Ficheiros BACPAC)](/archive/blogs/sqlcat/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files).
- Para obter informações sobre como trabalhar com a hora UTC após a migração, consulte [Modifying the default time zone for your local time zone (Modificar o fuso horário predefinido para o fuso horário local)](/archive/blogs/azuresqlemea/lesson-learned-4-modifying-the-default-time-zone-for-your-local-time-zone).
- Para obter informações sobre como alterar o idioma predefinido de uma base de dados após a migração, consulte [How to change the default language of Azure SQL Database (Como alterar o idioma predefinido da Base de Dados SQL do Azure)](/archive/blogs/azuresqlemea/lesson-learned-16-how-to-change-the-default-language-of-azure-sql-database).
