---
title: Replicação do Servidor Azure SQL para Azure SQL Database
description: Pode configurar uma base de dados na Base de Dados Azure SQL como assinante de um topologia de replicação transacional ou instantânea de ida.
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
ms.openlocfilehash: 6ff1d485ab4c0662ae8a9d754ce67b1446b76fcc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92780959"
---
# <a name="replication-to-azure-sql-database"></a>Replicação para Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Pode configurar uma Base de Dados Azure SQL como assinante de push numa topologia de replicação transacional ou instantânea de ida.

> [!NOTE]
> Este artigo descreve a utilização de [replicação transacional](/sql/relational-databases/replication/transactional/transactional-replication) na Base de Dados Azure SQL. Não está relacionado com a [geo-replicação ativa,](./active-geo-replication-overview.md)uma funcionalidade de Base de Dados Azure SQL que permite criar réplicas legíveis completas de bases de dados individuais.

## <a name="supported-configurations"></a>Configurações suportadas
  
- A Azure SQL Database só pode ser o assinante push de um editor e distribuidor SQL Server.  
- A instância do SQL Server agindo como editora e/ou distribuidor pode ser um exemplo do [SQL Server a funcionar no local](https://www.microsoft.com/sql-server/sql-server-downloads), um [Azure SQL Managed Instance](../managed-instance/instance-create-quickstart.md), ou um exemplo de [SQL Server a funcionar numa máquina virtual Azure na nuvem](../virtual-machines/windows/sql-vm-create-portal-quickstart.md). 
- A base de dados de distribuição e os agentes de replicação não podem ser colocados numa base de dados na Base de Dados Azure SQL.  
- [O instantâneo](/sql/relational-databases/replication/snapshot-replication) e a replicação [transacional unidirecional](/sql/relational-databases/replication/transactional/transactional-replication) são suportados. A replicação transacional entre pares e a replicação da fusão não são suportadas.

### <a name="versions"></a>Versões  

Para replicar com sucesso uma base de dados na Base de Dados Azure SQL, os editores e distribuidores do SQL Server devem estar a utilizar (pelo menos) uma das seguintes versões:

A publicação de qualquer Base de Dados Azure SQL a partir de uma base de dados do SQL Server é suportada pelas seguintes versões do SQL Server:

- SQL Server 2016 e maior
- SQL Server 2014 [RTM CU10 (12.0.4427.24)](https://support.microsoft.com/help/3094220/cumulative-update-10-for-sql-server-2014) ou [SP1 CU3 (12.0.2556.4)](https://support.microsoft.com/help/3094221/cumulative-update-3-for-sql-server-2014-service-pack-1)
- SQL Server 2012 [SP2 CU8 (11.0.5634.1)](https://support.microsoft.com/help/3082561/cumulative-update-8-for-sql-server-2012-sp2) ou [SP3 (11.0.6020.0)](https://www.microsoft.com/download/details.aspx?id=49996)

> [!NOTE]
> Tentar configurar a replicação utilizando uma versão não apoiada pode resultar em número de erro MSSQL_REPL20084 (O processo não conseguiu ligar-se ao Assinante.) e MSSQL_REPL40532 (Não pode abrir o servidor \<name> solicitado pelo login. O login falhou.).  

Para utilizar todas as funcionalidades da Base de Dados Azure SQL, deve utilizar as versões mais recentes do [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) e das Ferramentas de [Dados do SqL Server](/sql/ssdt/download-sql-server-data-tools-ssdt).  

### <a name="types-of-replication"></a>Tipos de replicação

Existem diferentes [tipos de replicação:](/sql/relational-databases/replication/types-of-replication)

| Replicação | Base de Dados SQL do Azure | Instância Gerida do Azure SQL |
| :----| :------------- | :--------------- |
| [**Transação Padrão**](/sql/relational-databases/replication/transactional/transactional-replication) | Sim (apenas como assinante) | Yes | 
| [**Instantâneo**](/sql/relational-databases/replication/snapshot-replication) | Sim (apenas como assinante) | Yes|
| [**Fusão de replicação**](/sql/relational-databases/replication/merge/merge-replication) | No | No|
| [**Ponto a ponto**](/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | No | No|
| [**Bidirecional**](/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | No | Yes|
| [**Assinaturas updatable**](/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | No | No|
| &nbsp; | &nbsp; | &nbsp; |

  
## <a name="remarks"></a>Observações

- Apenas são suportadas as subscrições push para a Base de Dados Azure SQL.  
- A replicação pode ser configurada utilizando [o SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) ou executando declarações Transact-SQL na editora. Não é possível configurar a replicação utilizando o portal Azure.  
- A replicação só pode utilizar logins de autenticação do SQL Server para ligar à Base de Dados Azure SQL.
- As mesas replicadas devem ter uma chave primária.  
- Deve ter uma assinatura Azure existente.  
- O assinante da Base de Dados Azure SQL pode estar em qualquer região.  
- Uma única publicação no SQL Server pode suportar tanto a Base de Dados SQL Azure como o SQL Server (no local e o SQL Server numa máquina virtual Azure).  
- A gestão, monitorização e resolução de problemas de replicação devem ser realizadas a partir do SQL Server em vez da Base de Dados Azure SQL.  
- Apenas `@subscriber_type = 0` é suportado em **sp_addsubscription** para a Base de Dados SQL.  
- A Azure SQL Database não suporta replicação bidirecional, imediata, updatable ou peer-to-peer.

## <a name="replication-architecture"></a>Arquitetura de Replicação  

![O diagrama mostra a arquitetura de replicação com a Base de Dados Azure SQL, que contém vários clusters de assinantes em diferentes regiões, e máquinas virtuais Azure no local, que contém um Editor, Logread executável e executáveis distribuidores que se conectam a clusters remotos.](./media/replication-to-sql-database/replication-to-sql-database.png)  

## <a name="scenarios"></a>Cenários  

### <a name="typical-replication-scenario"></a>Cenário típico de replicação  

1. Crie uma publicação de replicação transacional numa base de dados do SQL Server.  
2. No SQL Server utilize as declarações **do Novo Assistente de Subscrição** ou Transact-SQL para criar um impulso para a subscrição da Base de Dados Azure SQL.  
3. Com bases de dados únicas e agrárias na Base de Dados Azure SQL, o conjunto de dados iniciais é um instantâneo que é criado pelo Agente Snapshot e distribuído e aplicado pelo Agente de Distribuição. Com um editor SQL Managed Instance, também pode utilizar uma cópia de segurança de base de dados para semear o assinante da Base de Dados Azure SQL.

### <a name="data-migration-scenario"></a>Cenário de migração de dados  

1. Utilize a replicação transacional para replicar dados de uma base de dados do SQL Server para a Base de Dados Azure SQL.  
2. Redirecione as aplicações de nível médio ou cliente para atualizar a cópia da base de dados.  
3. Pare de atualizar a versão SQL Server da tabela e remova a publicação.  

## <a name="limitations"></a>Limitações

As seguintes opções não são suportadas para as subscrições da Base de Dados Azure SQL:

- Associação de grupos de ficheiros de cópia  
- Esquemas de partição de mesa de cópia  
- Esquemas de partição de índices de cópia  
- Copiar estatísticas definidas pelo utilizador  
- Vinculações padrão de cópia  
- Vinculações de regras de cópia  
- Copiar índices de texto completo  
- Cópia XML XSD  
- Copiar índices XML  
- Permissões de cópia  
- Copiar índices espaciais  
- Copiar índices filtrados  
- Atributo de compressão de dados de cópia  
- Atributo de coluna de cópias esparsa  
- Converter fluxo de ficheiros para tipos de dados MAX  
- Converter hierarquias para tipos de dados MAX  
- Converter o espaço para os tipos de dados MAX  
- Copiar propriedades estendidas  

### <a name="limitations-to-be-determined"></a>Limitações a determinar

- Coligação de cópia  
- Execução numa transação serializada do PS  

## <a name="examples"></a>Exemplos

Crie uma publicação e uma subscrição push. Para obter mais informações, consulte:
  
- [Criar uma Publicação](/sql/relational-databases/replication/publish/create-a-publication)
- [Crie uma Subscrição push](/sql/relational-databases/replication/create-a-push-subscription/) utilizando o nome do servidor como assinante (por **exemplo, N'azuresqldbdns.database.windows.net'**) e o nome Azure SQL Database como base de dados de destino (por **exemplo, AdventureWorks).**  

## <a name="see-also"></a>Consulte também  

- [Replicação transacional](../managed-instance/replication-transactional-overview.md)
- [Criar uma Publicação](/sql/relational-databases/replication/publish/create-a-publication)
- [Criar uma subscrição push](/sql/relational-databases/replication/create-a-push-subscription/)
- [Types of Replication](/sql/relational-databases/replication/types-of-replication) (Tipos de Replicação)
- [Monitorização (Replicação)](/sql/relational-databases/replication/monitor/monitoring-replication)
- [Inicializar uma Subscrição](/sql/relational-databases/replication/initialize-a-subscription)