---
title: Migração da base de dados SQL Server para uma base de dados Azure SQL única ou agrinada
description: Saiba mais sobre a migração da base de dados Do Servidor SQL para a Base de Dados Azure SQL.
keywords: migração de base de dados, migração de base de dados do sql server, ferramentas de migração de base de dados, migrar base de dados, migrar base de dados sql
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 02/11/2019
ms.openlocfilehash: f1e89b46f25fd4213b09680b441d3ecfd7a1e13b
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84044172"
---
# <a name="sql-server-database-migration-to-azure-sql-database"></a>Migração da base de dados SQL Server para base de dados Azure SQL
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Neste artigo, você aprende sobre os métodos primários para migrar um SQL Server 2005 ou posterior base de dados para uma única ou pooled Azure SQL Database. Para obter informações sobre a migração para uma instância gerida pelo Azure SQL, consulte [migrar para a instância do Servidor SQL para a instância gerida pelo Azure SQL](../managed-instance/migrate-to-instance-from-sql-server.md). Para obter informações sobre migração de outras plataformas, consulte o Guia de Migração da Base de [Dados Azure.](https://datamigration.microsoft.com/)

## <a name="migrate-to-a-single-database-or-a-pooled-database"></a>Migrar para uma única base de dados ou uma base de dados agruparada

Existem dois métodos primários para migrar um SQL Server 2005 ou posterior base de dados para uma única ou piscina de bases de dados na Base de Dados Azure SQL. O primeiro método é mais simples, mas necessita de algum, possivelmente substancial, período de indisponibilidade durante a migração. O segundo método é mais complexo, mas elimina significativamente o período de indisponibilidade durante a migração.

Em ambos os casos, é necessário garantir que a base de dados de origem é compatível com a Base de Dados Azure SQL utilizando o Assistente de [Migração de Dados (DMA)](https://www.microsoft.com/download/details.aspx?id=53595). A Base de Dados SQL está a aproximar-se da paridade de [funcionalidades](features-comparison.md) com o SQL Server, para além de problemas relacionados com operações de nível de servidor e de base de dados cruzadas. Bases de dados e aplicações que dependem de [funções parcialmente suportadas ou não suportadas](transact-sql-tsql-differences-sql-server.md) precisam de alguma [reengenharia para corrigir estas incompatibilidades](migrate-to-database-from-sql-server.md#resolving-database-migration-compatibility-issues) antes que as bases de dados do SQL Server possam ser migradas.

> [!NOTE]
> Para migrar uma base de dados não SQL Server, incluindo Microsoft Access, Sybase, MySQL Oracle e DB2 para a Base de Dados SQL do Azure, veja [SQL Server Migration Assistant (Assistente de Migração do SQL Server)](https://blogs.msdn.microsoft.com/datamigration/2017/09/29/release-sql-server-migration-assistant-ssma-v7-6/).

## <a name="method-1-migration-with-downtime-during-the-migration"></a>Método 1: migração com período de indisponibilidade durante a migração

 Utilize este método para migrar para uma única ou uma base de dados agrupada se puder pagar algum tempo de inatividade ou se estiver a realizar uma migração de teste de uma base de dados de produção para posterior migração. Para um tutorial, consulte Migrate uma base de [dados do SQL Server](../../dms/tutorial-sql-server-to-azure-sql.md).

A lista seguinte contém o fluxo de trabalho geral para uma migração de base de dados SQL Server de uma única ou uma base de dados agrofada utilizando este método. Para a migração para O Exemplo Gerido, consulte [migração para um instância gerido](../managed-instance/migrate-to-instance-from-sql-server.md).

  ![Diagrama de migração de VSSSDT](./media/migrate-to-database-from-sql-server/azure-sql-migration-sql-db.png)

1. [Avaliar](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem) a base de dados para compatibilidade utilizando a versão mais recente do Assistente de Migração de [Dados (DMA)](https://www.microsoft.com/download/details.aspx?id=53595).
2. Prepare quaisquer correções necessárias como scripts do Transact-SQL.
3. Faça uma cópia transaccionalmente consistente da base de dados de origem que está a ser migrada ou impeça que novas transações ocorram na base de dados de origem enquanto ocorre a migração. Os métodos para realizar esta última opção incluem desativar a conectividade do cliente ou criar um instantâneo de base de [dados.](https://msdn.microsoft.com/library/ms175876.aspx) Após a migração, poderá utilizar a replicação transacional para atualizar as bases de dados migradas com alterações que ocorrem após o ponto de corte para a migração. Ver [Migrar utilizando migração transacional](migrate-to-database-from-sql-server.md#method-2-use-transactional-replication).  
4. Implemente os scripts do Transact-SQL para aplicar as correções na cópia da base de dados.
5. [Migrar](https://docs.microsoft.com/sql/dma/dma-migrateonpremsql) a cópia da base de dados para uma nova Base de Dados Azure SQL utilizando o Assistente de Migração de Dados.

> [!NOTE]
> Em vez de utilizar O DMA, também pode utilizar um ficheiro BACPAC. Consulte importar um ficheiro BACPAC para uma nova Base de [Dados Azure SQL](database-import.md).

### <a name="optimizing-data-transfer-performance-during-migration"></a>Otimizar o desempenho da transferência de dados durante a migração

A lista seguinte contém recomendações para um melhor desempenho durante o processo de importação.

- Escolha o maior nível de serviço e dimensão de cálculo que o seu orçamento permite maximizar o desempenho da transferência. Pode reduzir verticalmente depois de a migração ser concluída para economizar.
- Minimize a distância entre o seu ficheiro BACPAC e o centro de dados de destino.
- Desative as estatísticas automáticas durante a migração
- Tabelas e índices de partição
- Remova as vistas indexadas e recrie-as quando estiver terminado
- Remova dados históricos raramente consultados para outra base de dados e emigra estes dados históricos para uma base de dados Azure SQL separada. Em seguida, pode consultar este dados históricos com [consultas elásticas](elastic-query-overview.md).

### <a name="optimize-performance-after-the-migration-completes"></a>Otimizar o desempenho após a migração estar concluída

[Atualize as estatísticas](https://docs.microsoft.com/sql/t-sql/statements/update-statistics-transact-sql) com uma análise completa após a migração estar concluída.

## <a name="method-2-use-transactional-replication"></a>Método 2: Utilizar a Replicação Transacional

Quando não pode remover a base de dados do SQL Server da produção enquanto a migração está a ocorrer, pode utilizar a replicação transacional do SQL Server como a sua solução de migração. Para utilizar este método, a base de dados de origem tem de cumprir os [requisitos para a replicação transacional](https://msdn.microsoft.com/library/mt589530.aspx) e ser compatível com a Base de Dados SQL do Azure. Para obter informações sobre a replicação do SQL com Always On, consulte [A Replicação de Configurar para Grupos Sempre Na Disponibilidade (Servidor SQL)](/sql/database-engine/availability-groups/windows/configure-replication-for-always-on-availability-groups-sql-server).

Para utilizar esta solução, configure a Base de Dados SQL do Azure como um subscritor para a instância do SQL Server que pretende migrar. O distribuidor de replicação transacional sincroniza os dados da base de dados a serem sincronizados (o editor) enquanto continuam a ocorrer novas transações.

Com a replicação transacional, todas as alterações aos seus dados ou esquema aparecem na sua Base de Dados SQL do Azure. Assim que a sincronização estiver concluída e estiver pronto para migrar, altere a cadeia de ligação das suas aplicações para apontá-las para a Base de Dados SQL do Azure. Assim que a replicação transacional drena quaisquer alterações restantes na sua base de dados de origem e todas as suas aplicações apontam para a BD do Azure, pode desinstalar a replicação transacional. A Base de Dados SQL do Azure é agora o seu sistema de produção.

 ![Diagrama de SeedCloudTR](./media/migrate-to-database-from-sql-server/SeedCloudTR.png)

> [!TIP]
> Também pode utilizar a replicação transacional para migrar um subconjunto da sua base de dados de origem. A publicação que replica para a Base de Dados SQL do Azure pode ser limitada a um subconjunto de tabelas na base de dados a ser replicada. Para cada tabela a ser replicada, pode limitar os dados a um subconjunto das linhas e/ou um subconjunto das colunas.

## <a name="migration-to-sql-database-using-transaction-replication-workflow"></a>Migração para a Base de Dados SQL com fluxo de trabalho de Replicação de Transação

> [!IMPORTANT]
> Utilize a versão mais recente do SQL Server Management Studio para permanecer sincronizado com atualizações para a Base de Dados Azure e SQL. As versões anteriores do SQL Server Management Studio não podem configurar a Base de Dados SQL como um subscritor. [Atualize o SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).

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
  - Fazê-lo causa um impacto de desempenho no servidor.
  - Se o impacto no desempenho for inaceitável pode utilizar outro servidor, mas aumenta a complexidade na gestão e na administração.
- Quando seleciona uma pasta de instantâneos, certifique-se de que a pasta que seleciona é suficientemente grande para conter um BCP de cada tabela que pretende replicar.
- A criação do instantâneo bloqueia as tabelas associadas até estar concluída, por isso, agende adequadamente o instantâneo.
- Apenas são suportadas subscrições push na Base de Dados SQL do Azure. Apenas pode adicionar subscritores da base de dados de origem.

## <a name="resolving-database-migration-compatibility-issues"></a>Corrigir problemas de compatibilidade de migração de bases de dados

Existe uma ampla variedade de problemas de compatibilidade que pode encontrar, dependendo da versão do SQL Server na base de dados de origem e da complexidade da base de dados que está a migrar. As versões mais antigas do SQL Server têm mais problemas de compatibilidade. Utilize os seguintes recursos, para além de uma pesquisa na Internet direcionada com o seu motor de busca preferido:

- [Funcionalidades da base de dados do SQL Server não suportadas na Base de Dados SQL do Azure](transact-sql-tsql-differences-sql-server.md)
- [Funcionalidade do Motor da Base de Dados Descontinuada no SQL Server 2016](https://msdn.microsoft.com/library/ms144262%28v=sql.130%29)
- [Funcionalidade do Motor da Base de Dados Descontinuada no SQL Server 2014](https://msdn.microsoft.com/library/ms144262%28v=sql.120%29)
- [Funcionalidade do Motor da Base de Dados Descontinuada no SQL Server 2012](https://msdn.microsoft.com/library/ms144262%28v=sql.110%29)
- [Funcionalidade do Motor da Base de Dados Descontinuada no SQL Server 2008 R2](https://msdn.microsoft.com/library/ms144262%28v=sql.105%29)
- [Funcionalidade do Motor da Base de Dados Descontinuada no SQL Server 2005](https://msdn.microsoft.com/library/ms144262%28v=sql.90%29)

Além de pesquisar na Internet e utilizar estes recursos, utilize o Microsoft Q&Uma página de perguntas para A Base de [Dados Azure SQL](https://docs.microsoft.com/answers/topics/azure-sql-database.html) ou [StackOverflow](https://stackoverflow.com/).

> [!IMPORTANT]
> A Instância Gerida pelo Azure SQL permite-lhe migrar uma instância de Servidor SQL existente e as suas bases de dados com problemas mínimos ou sem compatibilidade. Veja [o que é uma instância gerida Azure SQL](../managed-instance/sql-managed-instance-paas-overview.md).

## <a name="next-steps"></a>Próximos passos

- Utilize o script no blogue de Engenheiros do Azure SQL EMEA para [Monitorizar a utilização de tempdb durante a migração](https://blogs.msdn.microsoft.com/azuresqlemea/2016/12/28/lesson-learned-10-monitoring-tempdb-usage/).
- Utilize o script no blogue de Engenheiros do Azure SQL EMEA para [Monitorizar o espaço do registo de transações da base de dados enquanto a migração está a ocorrer](https://docs.microsoft.com/archive/blogs/azuresqlemea/lesson-learned-7-monitoring-the-transaction-log-space-of-my-database).
- Para saber mais sobre a migração com ficheiros BACPAC num blogue da Equipa de Aconselhamento ao Cliente do SQL Server, consulte [Migrating from SQL Server to Azure SQL Database using BACPAC Files (Migrar a partir do SQL Server para a Base de Dados SQL do Azure com Ficheiros BACPAC)](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).
- Para obter informações sobre como trabalhar com a hora UTC após a migração, consulte [Modifying the default time zone for your local time zone (Modificar o fuso horário predefinido para o fuso horário local)](https://blogs.msdn.microsoft.com/azuresqlemea/2016/07/27/lesson-learned-4-modifying-the-default-time-zone-for-your-local-time-zone/).
- Para obter informações sobre como alterar o idioma predefinido de uma base de dados após a migração, consulte [How to change the default language of Azure SQL Database (Como alterar o idioma predefinido da Base de Dados SQL do Azure)](https://blogs.msdn.microsoft.com/azuresqlemea/2017/01/13/lesson-learned-16-how-to-change-the-default-language-of-azure-sql-database/).
