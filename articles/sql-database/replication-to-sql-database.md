---
title: Replicação de base de dados SQL do Azure | Documentos da Microsoft"
description: Saiba mais sobre como utilizar a replicação do SQL Server com bancos de dados nos conjuntos elásticos e bases de dados individuais do banco de dados do Azure SQL
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: mathoma
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: b9d6569504b5352c6187afe12d903c986019c517
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57860708"
---
# <a name="replication-to-sql-database-single-and-pooled-databases"></a>Replicação de bases de dados de base de dados SQL, únicas e em pool

Replicação do SQL Server pode ser configurada para bases de dados únicos e em pool numa [servidor de base de dados SQL](sql-database-servers.md) na base de dados do Azure SQL.  

## <a name="supported-configurations"></a>**Configurações suportadas:**
  
- O SQL Server pode ser uma instância do SQL Server em execução no local ou uma instância do SQL Server em execução numa máquina virtual do Azure na cloud. Para obter mais informações, consulte [SQL Server em Descrição geral de máquinas virtuais do Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-infrastructure-services/).  
- A base de dados SQL do Azure tem de ser um subscritor de push de um publicador do SQL Server.  
- A base de dados de distribuição e os agentes de replicação não não possível colocar numa base de dados SQL do Azure.  
- São suportados instantâneos e replicação transacional unidirecional. Replicação transacional ponto a ponto a ponto e replicação de intercalação não são suportadas.
- A replicação está disponível para pré-visualização pública na instância gerida da base de dados SQL do Azure. Instância gerida pode alojar as bases de dados do publicador, o distribuidor e o subscritor. Para obter mais informações, consulte [replicação com o SQL Database Managed Instance](replication-with-sql-database-managed-instance.md).

## <a name="versions"></a>Versões  

- O publicador e distribuidor tem de ser pelo menos, uma das seguintes versões:  
- SQL Server 2017 (14.x)
- SQL Server 2016 (13.x)
- SQL Server 2014 (12.x) SP1 CU3
- SQL Server 2014 (12.x) RTM CU10
- CU8 do SQL Server 2012 (11.x) SP2 ou SP3
- A tentar configurar a replicação com uma versão mais antiga pode resultar em erro número MSSQL_REPL20084 (o processo não foi possível ligar ao subscritor.) e MSSQL_REPL40532 (não é possível abrir o servidor \<nome > pedido pelo início de sessão. O início de sessão falhou.).  
- Para utilizar todos os recursos do Azure SQL Database, tem de utilizar as versões mais recentes [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) e [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt).  
  
## <a name="remarks"></a>Observações

- A replicação pode ser configurada usando [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) ou executando instruções Transact-SQL no publicador. Não é possível configurar a replicação com o portal do Azure.  
- A replicação só pode utilizar inícios de sessão de autenticação de SQL Server para ligar a uma base de dados SQL do Azure.
- Tabelas replicadas tem de ter uma chave primária.  
- Tem de ter uma subscrição do Azure existente.  
- O subscritor de base de dados SQL do Azure pode estar em qualquer região.  
- Uma única publicação no SQL Server pode suportar a base de dados do Azure SQL e os subscritores do SQL Server (no local e o SQL Server numa máquina virtual do Azure).  
- Gestão da replicação, monitorização, e deve ser executada de resolução de problemas do SQL Server no local.  
- São suportadas apenas as subscrições de emissão para a base de dados do Azure SQL.  
- Apenas `@subscriber_type = 0` é suportado no **sp_addsubscription** para base de dados SQL.  
- Base de dados SQL do Azure não suporta a replicação bidirecional, imediata, atualizável ou ponto a ponto.

## <a name="replication-architecture"></a>Arquitetura da replicação  

![replication-to-sql-database](./media/replication-to-sql-database/replication-to-sql-database.png)  

## <a name="scenarios"></a>Cenários  

### <a name="typical-replication-scenario"></a>Cenário de típico de replicação  

1. Crie uma publicação de replicação transacional num banco de dados do SQL Server no local.  
2. No SQL Server no local, utilize o **Assistente de nova subscrição** ou instruções de Transact-SQL para criar um esforço para subscrição para a base de dados do Azure SQL.  
3. Com o únicos e em pool bases de dados na base de dados do Azure SQL, o conjunto de dados inicial é um instantâneo que é criado pelo agente de instantâneo e distribuído e aplicado pelo agente de distribuição. Com uma instância gerida da base de dados, também pode utilizar uma cópia de segurança da base de dados para efetuar o seeding da base de dados do subscritor.

### <a name="data-migration-scenario"></a>Cenário de migração de dados  

1. Utilize a replicação transacional para replicar dados de uma base de dados do SQL Server no local para a base de dados do Azure SQL.  
2. Redirecione o cliente ou aplicativos de camada intermediária para atualizar a cópia de base de dados SQL do Azure.  
3. Param de atualizar a versão do SQL Server da tabela e remover a publicação.  

## <a name="limitations"></a>Limitações

As seguintes opções não são suportadas para as subscrições do Azure SQL Database:

- Copie a associação de grupos de ficheiros  
- Copie os esquemas de particionamento de tabelas  
- Copie os esquemas de criação de partições do índice  
- Copie as estatísticas de definidas pelo utilizador  
- Enlaces de padrão de cópia  
- Enlaces de regra de cópia  
- Copie os índices de texto completo  
- Copiar XSD de XML  
- Índices XML de cópia  
- Copiar permissões  
- Copie os índices espaciais  
- Copie os índices filtrados  
- Atributo de compressão de dados de cópia  
- Atributo de colunas dispersas de cópia  
- Converter o filestream para tipos de dados de máx.  
- Converter hierarchyid para tipos de dados de máx.  
- Converter geográficos para tipos de dados de máx.  
- Copiar as propriedades expandidas  
- Copiar permissões  

### <a name="limitations-to-be-determined"></a>Limitações ser determinada

- Agrupamento de cópia  
- Execução de uma transação serializada de SP  

## <a name="examples"></a>Exemplos

Crie uma publicação e uma subscrição de emissão. Para obter mais informações, consulte:
  
- [Criar uma publicação](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Criar uma subscrição de Push](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/) ao utilizar o nome do servidor de base de dados do Azure SQL como o subscritor (por exemplo **N'azuresqldbdns.database.windows.net'**) e o nome de base de dados SQL do Azure como a base de dados de destino (para exemplo **AdventureWorks**).  

## <a name="see-also"></a>Consultar Também  

- [Replicação transacional](sql-database-managed-instance-transactional-replication.md)
- [Criar uma publicação](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Criar uma subscrição de emissão](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Tipos de replicação](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Monitorização (replicação)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Inicializar uma subscrição](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
