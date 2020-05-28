---
title: Réplica do Servidor SQL para base de dados Azure SQL
description: Pode configurar uma Base de Dados Azure SQL como subscritor de push numa topologia de replicação transacional ou instantânea de sentido único.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: mathoma
ms.date: 04/28/2020
ms.openlocfilehash: eebf0bb2a5f2a813ff282854b62f10957475e3b1
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84046440"
---
# <a name="replication-to-azure-sql-database"></a>Replicação à Base de Dados Azure SQL
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Pode configurar uma Base de Dados Azure SQL como subscritor de push numa topologia de replicação transacional ou instantânea de sentido único.

> [!NOTE]
> Este artigo descreve a utilização da [replicação transacional](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication) na Base de Dados Azure SQL. Não está relacionado com a [geo-replicação ativa,](https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication)uma funcionalidade de Base de Dados Azure SQL que lhe permite criar réplicas legíveis completas de bases de dados individuais.

## <a name="supported-configurations"></a>Configurações Suportadas
  
- A Base de Dados Azure SQL só pode ser o subscritor push de um editor e distribuidor SQL Server.  
- O Servidor SQL que atua como editor e/ou distribuidor pode ser uma instância de [SQL Server em execução no local,](https://www.microsoft.com/sql-server/sql-server-downloads)uma [Instância Gerida Azure SQL,](../managed-instance/instance-create-quickstart.md)ou uma instância de [SQL Server funcionando numa máquina virtual Azure na nuvem](../virtual-machines/windows/sql-vm-create-portal-quickstart.md). 
- A base de dados de distribuição e os agentes de replicação não podem ser colocados numa base de dados Azure SQL.  
- A replicação transacional [instantânea](/sql/relational-databases/replication/snapshot-replication) e de [ida](/sql/relational-databases/replication/transactional/transactional-replication) são suportadas. A replicação transacional entre pares e a replicação da fusão não são suportadas.

### <a name="versions"></a>Versões  

Para replicar com sucesso uma base de dados Azure SQL, os editores e distribuidores do SQL Server devem utilizar (pelo menos) uma das seguintes versões: 

A publicação em qualquer Base de Dados Azure SQL a partir de um Servidor SQL no local é suportada pelas seguintes versões do Servidor SQL:

- SQL Server 2016 e maior
- SQL Server 2014 [RTM CU10 (12.0.4427.24)](https://support.microsoft.com/help/3094220/cumulative-update-10-for-sql-server-2014) ou [SP1 CU3 (12.0.2556.4)](https://support.microsoft.com/help/3094221/cumulative-update-3-for-sql-server-2014-service-pack-1)
- SQL Server 2012 [SP2 CU8 (11.0.5634.1)](https://support.microsoft.com/help/3082561/cumulative-update-8-for-sql-server-2012-sp2) ou [SP3 (11.0.6020.0)](https://www.microsoft.com/download/details.aspx?id=49996)

> [!NOTE]
> Tentar configurar a replicação utilizando uma versão não suportada pode resultar num número de erro MSSQL_REPL20084 (O processo não conseguiu ligar-se ao Assinante.) e MSSQL_REPL40532 (Não pode abrir o servidor \<name> solicitado pelo login. O login falhou.).  

Para utilizar todas as funcionalidades da Base de Dados Azure SQL, deve utilizar as versões mais recentes do Estúdio de Gestão de [Servidores SQL](/sql/ssms/download-sql-server-management-studio-ssms) e das Ferramentas de Dados do [Servidor SQL](/sql/ssdt/download-sql-server-data-tools-ssdt).  

### <a name="types-of-replication"></a>Tipos de replicação

Existem diferentes [tipos de replicação:](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)

| Replicação | Base de Dados SQL do Azure | Instância Gerida do Azure SQL |
| :----| :------------- | :--------------- |
| [**Transação Padrão**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication) | Sim (apenas como assinante) | Sim | 
| [**Instantâneo**](https://docs.microsoft.com/sql/relational-databases/replication/snapshot-replication) | Sim (apenas como assinante) | Sim|
| [**Fusão de replicação**](https://docs.microsoft.com/sql/relational-databases/replication/merge/merge-replication) | Não | Não|
| [**Peer-to-peer**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | Não | Não|
| [**Bidirecional**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | Não | Sim|
| [**Subscrições updatable**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | Não | Não|
| &nbsp; | &nbsp; | &nbsp; |

  
## <a name="remarks"></a>Observações

- Apenas são suportadas as subscrições push para a Base de Dados Azure SQL.  
- A replicação pode ser configurada utilizando o [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) ou executando declarações da Transact-SQL na editora. Não é possível configurar a replicação utilizando o portal Azure.  
- A replicação só pode utilizar logins de autenticação do Servidor SQL para ligar à Base de Dados Azure SQL.
- As mesas replicadas devem ter uma chave primária.  
- Deve ter uma assinatura Azure existente.  
- O assinante da Base de Dados Azure SQL pode estar em qualquer região.  
- Uma única publicação no SQL Server pode suportar tanto os subscritores da Base de Dados SQL e do SQL Server (no local e do SQL Server numa máquina virtual Azure).  
- A gestão da replicação, monitorização e resolução de problemas devem ser realizadas a partir do SQL Server em vez da Base de Dados Azure SQL.  
- Apenas `@subscriber_type = 0` é suportado em **sp_addsubscription** para a Base de Dados SQL.  
- A Base de Dados Azure SQL não suporta replicação bidirecional, imediata, atualizável ou peer-to-peer.

## <a name="replication-architecture"></a>Arquitetura de Replicação  

![replicação-a-sql-base de dados](./media/replication-to-sql-database/replication-to-sql-database.png)  

## <a name="scenarios"></a>Cenários  

### <a name="typical-replication-scenario"></a>Cenário típico de replicação  

1. Crie uma publicação de replicação transacional numa base de dados do SQL Server no local.  
2. No sql server no local, utilize o **Novo Assistente de Subscrição** ou declarações Transact-SQL para criar um impulso para a subscrição da Base de Dados Azure SQL.  
3. Com bases de dados únicas e agr0nadas na Base de Dados Azure SQL, o conjunto de dados inicial é um instantâneo que é criado pelo Agente Snapshot e distribuído e aplicado pelo Agente de Distribuição. Com um editor SQL Managed Instance, também pode utilizar uma cópia de dados de base de dados para sementeir o assinante da Base de Dados Azure SQL.

### <a name="data-migration-scenario"></a>Cenário de migração de dados  

1. Utilize a replicação transacional para replicar dados de uma base de dados do Servidor SQL no local para a Base de Dados Azure SQL.  
2. Redirecione as aplicações do cliente ou de nível médio para atualizar a cópia da base de dados.  
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
- [Crie uma Subscrição Push](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/) utilizando o nome do servidor como assinante (por exemplo **N'azuresqldbdns.database.windows.net'**) e o nome de base de dados Azure SQL como base de dados de destino (por **exemplo, AdventureWorks**).  

## <a name="see-also"></a>Veja também  

- [Replicação transacional](../managed-instance/replication-transactional-overview.md)
- [Criar uma Publicação](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Criar uma subscrição push](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Types of Replication](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication) (Tipos de Replicação)
- [Monitorização (Replicação)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Inicializar uma Subscrição](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
