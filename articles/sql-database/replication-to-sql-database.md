---
title: Replicação para o banco de dados SQL do Azure "
description: Saiba mais sobre como usar a replicação SQL Server com bancos de dados individuais do banco de dados SQL do Azure em pools elásticos
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: mathoma
ms.date: 01/25/2019
ms.openlocfilehash: ae564a98b77b1ac564fa9aae152b27c491187d0c
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692217"
---
# <a name="replication-to-sql-database-single-and-pooled-databases"></a>Replicação em bancos de dados individuais e em pool

A replicação do SQL Server pode ser configurada para bancos de dados individuais e em pool em um [servidor de banco de dados SQL](sql-database-servers.md) no banco de dados SQL do Azure.  

## <a name="supported-configurations"></a>**Configurações com suporte:**
  
- O SQL Server pode ser uma instância do SQL Server em execução local ou uma instância do SQL Server em execução em uma máquina virtual do Azure na nuvem. Para obter mais informações, consulte a [visão geral de SQL Server em máquinas virtuais do Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-infrastructure-services/).  
- O banco de dados SQL do Azure deve ser um assinante de envio por push de um SQL Server editor.  
- O banco de dados de distribuição e os agentes de replicação não podem ser colocados em um banco de dados SQL do Azure.  
- Há suporte para o instantâneo e a replicação transacional unidirecional. Não há suporte para replicação transacional ponto a ponto e replicação de mesclagem.
- A replicação está disponível para visualização pública no Instância Gerenciada do Banco de Dados SQL do Azure. Instância Gerenciada pode hospedar bancos de dados de Publicador, distribuidor e Assinante. Para obter mais informações, consulte [replicação com instância gerenciada do banco de dados SQL](replication-with-sql-database-managed-instance.md).

## <a name="versions"></a>Versões  

- O Publicador e o distribuidor devem ser pelo menos uma das seguintes versões:  
- SQL Server 2017 (14. x)
- SQL Server 2016 (13. x)
- SQL Server 2014 (12. x) SP1 CU3
- SQL Server 2014 (12. x) CU10 RTM
- SQL Server 2012 (11. x) SP2 CU8 ou SP3
- A tentativa de configurar a replicação usando uma versão mais antiga pode resultar em um número de erro MSSQL_REPL20084 (o processo não pôde se conectar ao Assinante.) e MSSQL_REPL40532 (não é possível abrir o servidor \<nome > solicitado pelo logon. Falha no logon.).  
- Para usar todos os recursos do banco de dados SQL do Azure, você deve usar as versões mais recentes do [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) e [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt).  
  
## <a name="remarks"></a>Observações

- A replicação pode ser configurada usando [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) ou executando instruções TRANSACT-SQL no Publicador. Não é possível configurar a replicação usando o portal do Azure.  
- A replicação só pode usar SQL Server logons de autenticação para se conectar a um banco de dados SQL do Azure.
- As tabelas replicadas devem ter uma chave primária.  
- Você deve ter uma assinatura do Azure existente.  
- O assinante do banco de dados SQL do Azure pode estar em qualquer região.  
- Uma única publicação no SQL Server pode dar suporte aos assinantes do banco de dados SQL do Azure e SQL Server (local e SQL Server em uma máquina virtual do Azure).  
- O gerenciamento de replicação, o monitoramento e a solução de problemas devem ser executados a partir do SQL Server local.  
- Há suporte apenas para assinaturas push no banco de dados SQL do Azure.  
- Há suporte apenas para `@subscriber_type = 0` no **sp_addsubscription** para o banco de dados SQL.  
- O banco de dados SQL do Azure não dá suporte à replicação bidirecional, imediata, atualizável ou ponto a ponto.

## <a name="replication-architecture"></a>Arquitetura de replicação  

![replicação-para-SQL-banco de dados](./media/replication-to-sql-database/replication-to-sql-database.png)  

## <a name="scenarios"></a>Cenários  

### <a name="typical-replication-scenario"></a>Cenário de replicação típico  

1. Crie uma publicação de replicação transacional em um banco de dados SQL Server local.  
2. No local SQL Server use o **Assistente para nova assinatura** ou instruções TRANSACT-SQL para criar um push para assinatura no banco de dados SQL do Azure.  
3. Com bancos de dados únicos e em pool no Azure SQL Database, o conjunto inicial é um instantâneo criado pelo Agente de Instantâneo e distribuído e aplicado pelo Agente de Distribuição. Com um banco de dados de instância gerenciado, você também pode usar um backup de banco de dados para propagar o banco de dados do Assinante.

### <a name="data-migration-scenario"></a>Cenário de migração de dados  

1. Use a replicação transacional para replicar dados de um SQL Server local de um banco de dado para o Azure SQL Database.  
2. Redirecione os aplicativos cliente ou de camada intermediária para atualizar a cópia do banco de dados SQL do Azure.  
3. Pare de atualizar a versão SQL Server da tabela e remova a publicação.  

## <a name="limitations"></a>Limitações

As seguintes opções não têm suporte para assinaturas do banco de dados SQL do Azure:

- Copiar Associação de grupos de arquivos  
- Copiar esquemas de particionamento de tabela  
- Copiar esquemas de particionamento de índice  
- Copiar estatísticas definidas pelo usuário  
- Copiar associações padrão  
- Copiar associações de regra  
- Copiar índices de texto completo  
- Copiar XSD XML  
- Copiar índices XML  
- Copiar permissões  
- Copiar índices espaciais  
- Copiar índices filtrados  
- Copiar atributo de compactação de dados  
- Copiar atributo de coluna esparsa  
- Converter FILESTREAM em tipos de dados MAX  
- Converter hierarchyid em tipos de dados MAX  
- Converter Spatial em tipos de dados MAX  
- Copiar propriedades estendidas  
- Copiar permissões  

### <a name="limitations-to-be-determined"></a>Limitações a serem determinadas

- Copiar agrupamento  
- Execução em uma transação serializada do SP  

## <a name="examples"></a>Exemplos

Crie uma publicação e uma assinatura push. Para obter mais informações, consulte:
  
- [Criar uma publicação](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Crie uma assinatura push](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/) usando o nome do servidor do banco de dados SQL do Azure como o assinante (por exemplo **n' azuresqldbdns. Database. Windows. net '** ) e o nome do banco de dados SQL do Azure como o banco de dados de destino (por exemplo, **AdventureWorks** ).  

## <a name="see-also"></a>Veja também  

- [Replicação transacional](sql-database-managed-instance-transactional-replication.md)
- [Criar uma publicação](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Criar uma assinatura push](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Types of Replication](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication) (Tipos de Replicação)
- [Monitoramento (replicação)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Inicializar uma assinatura](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
