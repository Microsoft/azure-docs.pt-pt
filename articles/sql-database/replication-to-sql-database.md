---
title: Replicação
description: Saiba sobre a utilização da replicação do SQL Server com bases de dados únicas da Base de Dados Azure SQL e bases de dados em piscinas elásticas
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: mathoma
ms.date: 01/25/2019
ms.openlocfilehash: f28269b067ee98d69a97799911fd2d84a7f91e34
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381154"
---
# <a name="replication-to-sql-database-single-and-pooled-databases"></a>Replicação para bases de dados individuais e reunidas da SQL

A replicação do Servidor SQL pode ser configurada para bases de dados individuais e agréns em um servidor de base de [dados SQL](sql-database-servers.md) na Base de Dados Azure SQL.  

## <a name="supported-configurations"></a>**Configurações suportadas:**
  
- O Servidor SQL pode ser uma instância de SQL Server que funciona no local ou uma instância de SQL Server a funcionar numa máquina virtual Azure na nuvem. Para mais informações, consulte [o SQL Server na visão geral das Máquinas Virtuais Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-infrastructure-services/).  
- A base de dados Azure SQL deve ser um subscritor push de uma editora SQL Server.  
- A base de dados de distribuição e os agentes de replicação não podem ser colocados numa base de dados Azure SQL.  
- A replicação transacional instantânea e de ida são suportadas. A replicação transacional entre pares e a replicação da fusão não são suportadas.
- A replicação está disponível para pré-visualização pública em Azure SQL Database Managed Instance. A Managed Instance pode hospedar bases de dados de editores, distribuidores e assinantes. Para mais informações, consulte Replicação com A Instância Gerida pela Base de [Dados SQL](replication-with-sql-database-managed-instance.md).

## <a name="versions"></a>Versões  

No local, os editores e distribuidores do SQL Server devem utilizar (pelo menos) uma das seguintes versões:  

- SQL Server 2016 e maior
- SQL Server 2014 [RTM CU10 (12.0.4427.24)](https://support.microsoft.com/help/3094220/cumulative-update-10-for-sql-server-2014) ou [SP1 CU3 (12.0.2556.4)](https://support.microsoft.com/help/3094221/cumulative-update-3-for-sql-server-2014-service-pack-1)
- SQL Server 2012 [SP2 CU8 (11.0.5634.1)](https://support.microsoft.com/help/3082561/cumulative-update-8-for-sql-server-2012-sp2) ou [SP3 (11.0.6020.0)](https://www.microsoft.com/download/details.aspx?id=49996)

> [!NOTE]
> Tentar configurar a replicação utilizando uma versão não suportada pode resultar num número de erro MSSQL_REPL20084 \<(O processo não conseguiu ligar-se ao Assinante.) e MSSQL_REPL40532 (Não pode abrir o nome do servidor> solicitado pelo login. O login falhou.).  

Para utilizar todas as funcionalidades da Base de Dados Azure SQL, deve utilizar as versões mais recentes do Estúdio de Gestão de [Servidores SQL](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) e das Ferramentas de Dados do [Servidor SQL](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt).  

  
## <a name="remarks"></a>Observações

- A replicação pode ser configurada utilizando o [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) ou executando declarações da Transact-SQL na editora. Não é possível configurar a replicação utilizando o portal Azure.  
- A replicação só pode utilizar logins de autenticação do Servidor SQL para se ligar a uma base de dados Azure SQL.
- As mesas replicadas devem ter uma chave primária.  
- Deve ter uma assinatura Azure existente.  
- O assinante da base de dados Azure SQL pode estar em qualquer região.  
- Uma única publicação no SQL Server pode suportar tanto os subscritores da Base de Dados SQL e do SQL Server (no local e do SQL Server numa máquina virtual Azure).  
- A gestão da replicação, monitorização e resolução de problemas devem ser realizadas a partir do Servidor SQL no local.  
- Apenas são suportadas as subscrições push para a Base de Dados Azure SQL.  
- Apenas `@subscriber_type = 0` é suportado em **sp_addsubscription** para a Base de Dados SQL.  
- A Base de Dados Azure SQL não suporta replicação bidirecional, imediata, atualizável ou peer to peer to peer.

## <a name="replication-architecture"></a>Arquitetura de Replicação  

![replicação-a-sql-base de dados](./media/replication-to-sql-database/replication-to-sql-database.png)  

## <a name="scenarios"></a>Cenários  

### <a name="typical-replication-scenario"></a>Cenário típico de replicação  

1. Crie uma publicação de replicação transacional numa base de dados do SQL Server no local.  
2. No sql server no local, utilize o **Novo Assistente de Subscrição** ou declarações Transact-SQL para criar um impulso para a subscrição da Base de Dados Azure SQL.  
3. Com bases de dados únicas e agr0nadas na Base de Dados Azure SQL, o conjunto de dados inicial é um instantâneo que é criado pelo Agente Snapshot e distribuído e aplicado pelo Agente de Distribuição. Com uma base de dados de instânciagerida, também pode utilizar uma base de dados de backup para semear a base de dados dos assinantes.

### <a name="data-migration-scenario"></a>Cenário de migração de dados  

1. Utilize a replicação transacional para replicar dados de uma base de dados do Servidor SQL no local para a Base de Dados Azure SQL.  
2. Redirecione as aplicações do cliente ou de nível médio para atualizar a cópia da base de dados Azure SQL.  
3. Pare de atualizar a versão SQL Server da tabela e remova a publicação.  

## <a name="limitations"></a>Limitações

As seguintes opções não são suportadas para subscrições de Base de Dados Azure SQL:

- Copy file groups association  
- Esquemas de partilha de tabelas de cópias  
- Esquemas de partilha de índices de cópia  
- Copiar estatísticas definidas pelo utilizador  
- Encadernações por predefinição de cópia  
- Encadernações de regras de cópia  
- Copiar índices de texto completo  
- Cópia XML XSD  
- Copiar índices XML  
- Cópia de permissões  
- Copiar índices espaciais  
- Copiar índices filtrados  
- Atribuir a compressão de dados de cópia  
- Copiar atributo de coluna escassa  
- Converter filestream para tipos de dados MAX  
- Converter hieráides em tipos de dados MAX  
- Converter tipos de dados espaciais para MAX  
- Copiar propriedades estendidas  
- Cópia de permissões  

### <a name="limitations-to-be-determined"></a>Limitações a determinar

- Colagem de cópias  
- Execução numa transação serializada do SP  

## <a name="examples"></a>Exemplos

Crie uma publicação e uma subscrição push. Para obter mais informações, consulte:
  
- [Criar uma Publicação](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Crie uma Subscrição Push](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/) utilizando o nome do servidor de base de dados Azure SQL como assinante (por exemplo **N'azuresqldbdns.database.windows.net'**) e o nome de base de dados Azure SQL como base de dados de destino (por **exemplo, AdventureWorks**).  

## <a name="see-also"></a>Veja também  

- [Replicação transacional](sql-database-managed-instance-transactional-replication.md)
- [Criar uma Publicação](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Criar uma subscrição push](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Types of Replication](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication) (Tipos de Replicação)
- [Monitorização (Replicação)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Inicializar uma Subscrição](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
