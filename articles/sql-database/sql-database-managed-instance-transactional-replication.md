---
title: Replicação transacional
description: Saiba mais sobre o uso de SQL Server replicação transacional com bancos de dados únicos, em pool e de instância no Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 02/08/2019
ms.openlocfilehash: a57d1c85384204c26e75f7138b9514f2b3297bef
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823300"
---
# <a name="transactional-replication-with-single-pooled-and-instance-databases-in-azure-sql-database"></a>Replicação transacional com bancos de dados únicos, em pool e de instância no banco de dados SQL do Azure

A replicação transacional é um recurso do banco de dados SQL do Azure e SQL Server que permite replicar dados de uma tabela no banco de dados SQL do Azure ou uma SQL Server para as tabelas colocadas em bancos de dados remotos. Esse recurso permite sincronizar várias tabelas em bancos de dados diferentes.

## <a name="when-to-use-transactional-replication"></a>Quando usar a replicação transacional

A replicação transacional é útil nos seguintes cenários:
- Publique alterações feitas em uma ou mais tabelas em um banco de dados e distribua-as para um ou vários bancos de dados SQL Server ou do Azure SQL que se inscreveram para as alterações.
- Mantenha vários bancos de dados distribuídos em estado sincronizado.
- Migre bancos de dados de uma instância SQL Server ou gerenciada para outro banco de dados publicando as alterações continuamente.

## <a name="overview"></a>Descrição geral

Os principais componentes na replicação transacional são mostrados na figura a seguir:  

![replicação com o banco de dados SQL](media/replication-to-sql-database/replication-to-sql-database.png)

O **Publicador** é uma instância ou um servidor que publica as alterações feitas em algumas tabelas (artigos) enviando as atualizações para o distribuidor. A publicação em qualquer banco de dados SQL do Azure a partir de um SQL Server local tem suporte nas seguintes versões do SQL Server:

- SQL Server 2019 (versão prévia)
- SQL Server 2016 para o SQL 2017
- SQL Server 2014 SP1 CU3 ou superior (12.00.4427)
- SQL Server 2014 RTM CU10 (12.00.2556)
- SQL Server 2012 SP3 ou superior (11.0.6020)
- SQL Server 2012 SP2 CU8 (11.0.5634.0)
- Para outras versões do SQL Server que não dão suporte à publicação em objetos no Azure, é possível utilizar o método de [republicação de dados](https://docs.microsoft.com/sql/relational-databases/replication/republish-data) para mover dados para versões mais recentes do SQL Server. 

O **distribuidor** é uma instância ou um servidor que coleta alterações nos artigos de um Publicador e os distribui aos assinantes. O distribuidor pode ser uma instância gerenciada do banco de dados SQL do Azure ou SQL Server (qualquer versão, desde que seja igual ou superior à versão do editor). 

O **assinante** é uma instância ou um servidor que está recebendo as alterações feitas no Publicador. Os assinantes podem ser de bancos de dados únicos, em pool e de instância no banco de dados SQL do Azure ou em bancos de SQL Server. Um assinante em um banco de dados único ou em pool deve ser configurado como Push-Subscriber. 

| Função | Bancos de dados individuais e em pool | Bancos de dados de instância |
| :----| :------------- | :--------------- |
| **Publicador** | Não | Sim | 
| **Distribuidor** | Não | Sim|
| **Assinante de pull** | Não | Sim|
| **Assinante push**| Sim | Sim|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > Não há suporte para uma assinatura pull quando o distribuidor é um banco de dados de instância e o Assinante não é. 

Há diferentes [tipos de replicação](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication):


| Replicação | Bancos de dados individuais e em pool | Bancos de dados de instância|
| :----| :------------- | :--------------- |
| [**Transacional padrão**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication) | Sim (somente como assinante) | Sim | 
| [**Instantânea**](https://docs.microsoft.com/sql/relational-databases/replication/snapshot-replication) | Sim (somente como assinante) | Sim|
| [**Replicação de mesclagem**](https://docs.microsoft.com/sql/relational-databases/replication/merge/merge-replication) | Não | Não|
| [**Ponto a ponto**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | Não | Não|
| [**Direciona**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | Não | Sim|
| [**Assinaturas atualizáveis**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | Não | Não|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > - A tentativa de configurar a replicação usando uma versão mais antiga pode resultar em um número de erro MSSQL_REPL20084 (o processo não pôde se conectar ao Assinante.) e MSSQ_REPL40532 (não é possível abrir o servidor \<nome > solicitado pelo logon. Falha no logon.)
  > - Para usar todos os recursos do banco de dados SQL do Azure, você deve usar as versões mais recentes do [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) e [SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt).
  
  ### <a name="supportability-matrix-for-instance-databases-and-on-premises-systems"></a>Matriz de suporte para bancos de dados de instância e sistemas locais
  A matriz de suporte de replicação para bancos de dados de instância é igual à do SQL Server local. 
  
  | **Publicador**   | **Distribuidor** | **Farão** |
| :------------   | :-------------- | :------------- |
| SQL Server 2017 | SQL Server 2017 | SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 |
| SQL Server 2016 | SQL Server 2017 <br/> SQL Server 2016 | SQL Server 2017 <br/>SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 |
| SQL Server 2014 | SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>| SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |
| SQL Server 2012 | SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> | SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 | 
| SQL Server 2008 R2 <br/> SQL Server 2008 | SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 | SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 <br/>  |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="requirements"></a>Requisitos

- A conectividade utiliza a Autenticação SQL entre os participantes da replicação. 
- Um compartilhamento de conta de armazenamento do Azure para o diretório de trabalho usado pela replicação. 
- A porta 445 (TCP de saída) precisa ser aberta nas regras de segurança da sub-rede da instância gerenciada para acessar o compartilhamento de arquivos do Azure. 
- A porta 1433 (TCP de saída) precisa ser aberta se o Publicador/distribuidor estiver em uma instância gerenciada e o Assinante estiver no local.


>[!NOTE]
> - Você poderá encontrar o erro 53 ao se conectar a um arquivo de armazenamento do Azure se a porta 445 de saída do grupo de segurança de rede (NSG) for bloqueada quando o distribuidor for um banco de dados de instância e o assinante for local. [Atualize a VNET NSG](/azure/storage/files/storage-troubleshoot-windows-file-connection-problems) para resolver esse problema. 
> - Se os bancos de dados do Publicador e do distribuidor em uma instância gerenciada usarem [grupos de failover automático](sql-database-auto-failover-group.md), o administrador da instância gerenciada deverá [excluir todas as publicações no antigo primário e reconfigurá-las no novo primário após a ocorrência de um failover](sql-database-managed-instance-transact-sql-information.md#replication).

### <a name="compare-data-sync-with-transactional-replication"></a>Comparar a sincronização de dados com a replicação transacional

| | Sincronização de Dados | Replicação Transacional |
|---|---|---|
| Principais | -Suporte ativo-ativo<br/>-Bidirecional entre o local e o banco de dados SQL do Azure | -Latência mais baixa<br/>-Consistência transacional<br/>-Reutilizar a topologia existente após a migração |
| Desvantagens | -5 min ou mais latência<br/>-Sem consistência transacional<br/>-Maior impacto no desempenho | -Não é possível publicar no banco de dados de banco de dados SQL ou banco de dados em pool<br/>-Custo de manutenção alta |
| | | |

## <a name="common-configurations"></a>Configurações comuns

Em geral, o Publicador e o distribuidor devem estar na nuvem ou no local. Há suporte para as seguintes configurações: 

### <a name="publisher-with-local-distributor-on-a-managed-instance"></a>Publicador com distribuidor local em uma instância gerenciada

![Instância única como Publicador e distribuidor](media/replication-with-sql-database-managed-instance/01-single-instance-asdbmi-pubdist.png)

O Publicador e o distribuidor são configurados em uma única instância gerenciada e a distribuição de alterações para outra instância gerenciada, banco de dados individual, banco de dados em pool ou SQL Server local. 

### <a name="publisher-with-remote-distributor-on-a-managed-instance"></a>Publicador com distribuidor remoto em uma instância gerenciada

Nessa configuração, uma instância gerenciada publica as alterações no distribuidor colocadas em outra instância gerenciada que pode atender a muitas instâncias gerenciadas por fonte e distribuir as alterações para um ou vários destinos na instância gerenciada, banco de dados individual, banco de dados em pool ou SQL Server.

![Instâncias separadas para o Publicador e o distribuidor](media/replication-with-sql-database-managed-instance/02-separate-instances-asdbmi-pubdist.png)

O Publicador e o distribuidor são configurados em duas instâncias gerenciadas. Há algumas restrições com essa configuração: 

- Ambas as instâncias gerenciadas estão na mesma vNet.
- Ambas as instâncias gerenciadas estão no mesmo local.


### <a name="publisher-and-distributor-on-premises-with-a-subscriber-on-a-single-pooled-and-instance-database"></a>Publicador e distribuidor local com um assinante em um banco de dados único, em pool e de instância 

![Banco de BD SQL do Azure como assinante](media/replication-with-sql-database-managed-instance/03-azure-sql-db-subscriber.png)
 
Nessa configuração, um banco de dados SQL do Azure (banco de dados único, em pool e de instância) é um assinante. Essa configuração dá suporte à migração do local para o Azure. Se um Assinante estiver em um banco de dados único ou em pool, ele deverá estar no modo de push.  


## <a name="next-steps"></a>Passos seguintes

1. [Configure a replicação entre duas instâncias gerenciadas](replication-with-sql-database-managed-instance.md). 
1. [Crie uma publicação](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication).
1. [Crie uma assinatura push](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription) usando o nome do servidor do banco de dados SQL do Azure como o assinante (por exemplo `N'azuresqldbdns.database.windows.net` e o nome do banco de dados SQL do Azure como o banco de dados de destino (por exemplo, **AdventureWorks**. )
1. Saiba mais sobre as [limitações da replicação transacional para uma instância gerenciada](sql-database-managed-instance-transact-sql-information.md#replication)



## <a name="see-also"></a>Veja também  

- [Replicação com um MI e um grupo de failover](sql-database-managed-instance-transact-sql-information.md#replication)
- [Replicação para a Base de Dados SQL](replication-to-sql-database.md)
- [Replicação para instância gerenciada](replication-with-sql-database-managed-instance.md)
- [Criar uma publicação](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Criar uma assinatura push](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Types of Replication](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication) (Tipos de Replicação)
- [Monitoramento (replicação)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Inicializar uma assinatura](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
