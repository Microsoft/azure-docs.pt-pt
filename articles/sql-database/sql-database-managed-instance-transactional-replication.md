---
title: Replicação transacional com SQL Database do Azure | Documentos da Microsoft"
description: Aprenda a usar replicação transacional do SQL Server com o único, agrupados, e a instância de bases de dados na base de dados do Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
manager: craigg
ms.date: 02/08/2019
ms.openlocfilehash: 08ec654ecdfe9764aefdde287c5a4c78022c108c
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57551699"
---
# <a name="transactional-replication-with-single-pooled-and-instance-databases-in-azure-sql-database"></a>Replicação transacional com único, em pool e a instância de bases de dados na base de dados do Azure SQL

Replicação transacional é uma funcionalidade de base de dados do Azure SQL e SQL Server que permite-lhe replicar dados de uma tabela numa base de dados do Azure SQL ou SQL Server para as tabelas colocadas em bancos de dados remotos. Esta funcionalidade permite-lhe sincronizar várias tabelas nas bases de dados diferentes.

## <a name="overview"></a>Descrição geral

Os principais componentes na replicação transacional são mostrados na imagem seguinte:  

![replicação de base de dados SQL](media/replication-to-sql-database/replication-to-sql-database.png)

O **publicador** é uma instância ou um servidor que publica as alterações feitas no algumas tabelas (artigos) ao enviar as atualizações para o distribuidor. Base de dados de um servidor de SQL no local de publicação para qualquer SQL do Azure é suportada pelas seguintes versões do SQL Server:

- SQL Server 2019 (pré-visualização)
- SQL Server 2016 para o SQL 2017
- SQL Server 2014 SP1 CU3 ou superior (12.00.4427)
- SQL Server 2014 RTM CU10 (12.00.2556)
- SQL Server 2012 SP3 ou superior (11.0.6020)
- SQL Server 2012 SP2 CU8 (11.0.5634.0)
- Para outras versões do SQL Server que não suportam a publicação de objetos no Azure, é possível utilizar o [republicar dados](https://docs.microsoft.com/sql/relational-databases/replication/republish-data) método para mover dados para as versões mais recentes do SQL Server. 

O **distribuidor** é uma instância ou um servidor que recolhe as alterações nos artigos de um editor e distribui-los para os subscritores. O distribuidor pode ser a instância gerida da base de dados SQL do Azure ou o SQL Server (qualquer versão desde que é igual ou superior à versão do publicador). 

O **assinante** é uma instância ou um servidor que está a receber as alterações feitas no publicador. Os assinantes podem ser único, agrupados, e instância bases de dados em bancos de dados de base de dados do Azure SQL ou SQL Server. Um subscritor numa base de dados individual ou agrupada deve ser configurado como assinante do push. 

| Função | Bases de dados únicos e em pool | Bases de dados de instância |
| :----| :------------- | :--------------- |
| **Publicador** | Não | Sim | 
| **Distribuidor** | Não | Sim|
| **Subscritor de extração** | Não | Sim|
| **Subscritor de push**| Sim | Sim|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > Subscrição de solicitação não é suportada quando o distribuidor é uma base de dados de instância e o subscritor não é. 

Existem diferentes [tipos de replicação](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication?view=sql-server-2017):


| Replicação | Bases de dados únicos e em pool | Bases de dados de instância|
| :----| :------------- | :--------------- |
| [**Transactional**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication) | Sim (apenas como um assinante) | Sim | 
| [**instantâneo**](https://docs.microsoft.com/sql/relational-databases/replication/snapshot-replication) | Sim (apenas como um assinante) | Sim|
| [**Replicação de mesclagem**](https://docs.microsoft.com/sql/relational-databases/replication/merge/merge-replication) | Não | Não|
| [**Ponto a ponto**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | Não | Não|
| **One-way** | Sim | Sim|
| [**Bidirecional**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | Não | Sim|
| [**Subscrições atualizáveis**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | Não | Não|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > - A tentar configurar a replicação com uma versão mais antiga pode resultar em erro número MSSQL_REPL20084 (o processo não foi possível ligar ao subscritor.) e MSSQ_REPL40532 (não é possível abrir o servidor \<nome > pedido pelo início de sessão. O início de sessão falhou.)
  > - Para utilizar todos os recursos do Azure SQL Database, tem de utilizar as versões mais recentes [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) e [SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017).
  
  ### <a name="supportabilty-matrix-for-instance-databases-and-on-premises-systems"></a>Matriz de Supportabilty para sistemas de bancos de dados de instância e no local
  A matriz de suporte de replicação por exemplo, bancos de dados é o mesmo que o SQL Server no local. 
  
  | **Publicador**   | **Distribuidor** | **Para assinantes** |
| :------------   | :-------------- | :------------- |
| SQL Server 2017 | SQL Server 2017 | SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 |
| SQL Server 2016 | SQL Server 2017 <br/> SQL Server 2016 | SQL Server 2017 <br/>SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 |
| SQL Server 2014 | SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>| SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |
| SQL Server 2012 | SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> | SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 | 
| SQL Server 2008 R2 <br/> SQL Server 2008 | SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 | SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 <br/>  |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="requirements"></a>Requisitos

- Conectividade utiliza a autenticação do SQL entre os participantes de replicação. 
- Um compartilhamento de conta de armazenamento do Azure para o diretório de trabalho utilizado pela replicação. 
- Porta 445 (saída de TCP) tem de estar abertas nas regras de segurança de sub-rede da instância gerida para aceder à partilha de ficheiros do Azure. 
- A porta 1433 (saída de TCP) tem de ser aberta se o publicador/distribuidor estão numa instância gerida e o assinante está no local.

  >[!NOTE]
  > Pode encontrar o erro 53 ao ligar a um ficheiro de armazenamento do Azure, se a porta de grupo (NSG) de segurança de rede de saída 445 estiver bloqueada quando o distribuidor é uma base de dados de instância e o assinante está no local. [Atualizar a vNet NSG](/azure/storage/files/storage-troubleshoot-windows-file-connection-problems) para resolver este problema. 

## <a name="when-to-use-transactional-replication"></a>Quando utilizar a replicação transacional

Replicação transacional é útil nos seguintes cenários:

- Publicar as alterações feitas numa ou mais tabelas numa base de dados e distribuí-los para as bases de dados uma ou mais o SQL Server ou o SQL do Azure que subscrito para que as alterações.
- Tenha várias bases de dados distribuídas em estado sincronizadas.
- Migre bases de dados de um SQL Server ou a instância gerida para outra base de dados publicando continuamente as alterações.

### <a name="compare-data-sync-with-transactional-replication"></a>Comparar a sincronização de dados com a replicação transacional

| | Sincronização de Dados | Replicação Transacional |
|---|---|---|
| Vantagens | -Suporte de ativo-ativo<br/>Bidirecional entre o local e a base de dados SQL do Azure | -Menor latência<br/>-Consistência transacional<br/>-Reutilizar topologia existente após a migração |
| Desvantagens | -5 min ou mais latência<br/>-Sem uma consistência transacional<br/>-Maior impacto no desempenho | -Não é possível publicar a partir da base de dados única base de dados do Azure SQL ou base de dados agrupada<br/>-Custo de manutenção elevada |
| | | |

## <a name="common-configurations"></a>Configurações comuns

Em geral, o publicador e distribuidor tem de estar na cloud ou no local. São suportadas as seguintes configurações: 

### <a name="publisher-with-local-distributor-on-a-managed-instance"></a>Publicador com o distribuidor local numa instância gerida

![Instância única, como o publicador e distribuidor](media/replication-with-sql-database-managed-instance/01-single-instance-asdbmi-pubdist.png)

Editor e distribuidor estão configurados numa única instância gerida e distribuir as alterações para outros instância gerida, base de dados, bases de dados agrupadas ou SQL Server no local. Nesta configuração, o publicador/distribuidor a instância gerida não pode ser configurado com [grupos de ativação pós-falha georreplicação e automática](sql-database-auto-failover-group.md).

### <a name="publisher-with-remote-distributor-on-a-managed-instance"></a>Publicador com o distribuidor remoto numa instância gerida

Nesta configuração, uma instância gerida publica as alterações para o distribuidor colocado na outra instância gerida que possa servir várias instâncias de gerido de origem e distribuir as alterações para um ou muitos destinos na instância gerida, base de dados, bases de dados agrupadas, ou SQL Server.

![Instâncias separadas para o publicador e distribuidor](media/replication-with-sql-database-managed-instance/02-separate-instances-asdbmi-pubdist.png)

Editor e distribuidor estão configurados em duas instâncias geridas. Nesta configuração

- Ambas as instâncias geridas estão na mesma vNet.
- Ambas as instâncias geridas estão na mesma localização.
- Instâncias geridas que alojam publicado e bases de dados do distribuidor não podem ser [georreplicado com grupos de ativação pós-falha automática](sql-database-auto-failover-group.md).

### <a name="publisher-and-distributor-on-premises-with-a-subscriber-on-a-single-pooled-and-instance-database"></a>Editor e distribuidor no local com um subscritor numa única, agrupada e a instância da base de dados 

![O Azure SQL DB como subscritor](media/replication-with-sql-database-managed-instance/03-azure-sql-db-subscriber.png)
 
Nesta configuração, um Azure SQL Database (único, em pool e a instância da base de dados) é um subscritor. Esta configuração suporta a migração no local para o Azure. Se for um subscritor numa base de dados individual ou agrupada, tem de estar no modo push.  


## <a name="next-steps"></a>Passos Seguintes

1. [Configurar a replicação transacional para uma instância gerida](replication-with-sql-database-managed-instance.md#configure-publishing-and-distribution-example). 
1. [Criar uma publicação](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication).
1. [Criar uma subscrição de emissão](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription) ao utilizar o nome do servidor de base de dados do Azure SQL como o subscritor (por exemplo `N'azuresqldbdns.database.windows.net` e o nome de base de dados do Azure SQL como a base de dados de destino (por exemplo **Adventureworks**. )



## <a name="see-also"></a>Consultar Também  

- [Replicação para a Base de Dados SQL](replication-to-sql-database.md)
- [Replicação para a instância gerida](replication-with-sql-database-managed-instance.md)
- [Criar uma publicação](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Criar uma subscrição de emissão](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Tipos de replicação](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Monitorização (replicação)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Inicializar uma subscrição](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
