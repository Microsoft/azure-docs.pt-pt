---
title: Replicação transacional
description: Saiba utilizar a replicação transacional do SQL Server com bases de dados únicas, pooled e por exemplo na Base de Dados Azure SQL.
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
ms.openlocfilehash: 2a048ddefbcd76193436da13cd3ba68b8b6ffb0a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80607598"
---
# <a name="transactional-replication-with-single-pooled-and-instance-databases-in-azure-sql-database"></a>Replicação transacional com bases de dados individuais, reunidas e por exemplo na Base de Dados Azure SQL

A replicação transacional é uma característica da Base de Dados Azure SQL e do Servidor SQL que lhe permite replicar dados de uma tabela na Base de Dados Azure SQL ou de um Servidor SQL para as tabelas colocadas em bases de dados remotas. Esta funcionalidade permite sincronizar várias tabelas em diferentes bases de dados.

## <a name="when-to-use-transactional-replication"></a>Quando utilizar a replicação transacional

A replicação transacional é útil nos seguintes cenários:
- Publique alterações feitas numa ou mais tabelas numa base de dados e distribua-as para uma ou muitas bases de dados SQL Server ou Azure SQL que subscreveram as alterações.
- Mantenha várias bases de dados distribuídas em estado sincronizado.
- Migrar bases de dados de um Servidor SQL ou gerir a instância para outra base de dados publicando continuamente as alterações.

## <a name="overview"></a>Descrição geral

Os componentes-chave na replicação transacional são mostrados na imagem seguinte:  

![replicação com Base de Dados SQL](media/replication-to-sql-database/replication-to-sql-database.png)

O **Publisher** é uma instância ou servidor que publica alterações feitas em algumas tabelas (artigos) enviando as atualizações para o Distribuidor. A publicação em qualquer base de dados Azure SQL a partir de um Servidor SQL no local é suportada pelas seguintes versões do Servidor SQL:

- SQL Server 2019 (pré-visualização)
- SQL Server 2016 para SQL 2017
- SQL Server 2014 SP1 CU3 ou superior (12.00.4427)
- SQL Server 2014 RTM CU10 (12.00.2556)
- SQL Server 2012 SP3 ou superior (11.0.6020)
- SQL Server 2012 SP2 CU8 (11.0.5634.0)
- Para outras versões do SQL Server que não suportam a publicação de objetos no Azure, é possível utilizar o método de [republicação](https://docs.microsoft.com/sql/relational-databases/replication/republish-data) de dados para mover dados para versões mais recentes do SQL Server. 

O **Distribuidor** é uma instância ou servidor que recolhe alterações nos artigos de um Editor e distribui-as aos Assinantes. O Distribuidor pode ser uma instância gerida pela Azure SQL Database ou o SQL Server (qualquer versão, desde que seja igual ou superior à versão Publisher). 

O **Assinante** é uma instância ou servidor que está a receber as alterações efetuadas na Editora. Os subscritores podem ser bases de dados individuais, reunidas e por exemplo nas bases de dados Do Azure SQL ou do SQL Server. Um Assinante numa única base de dados ou agrinado deve ser configurado como subscritor de push. 

| Função | Bases de dados individuais e agréus | Bases de dados de instâncias |
| :----| :------------- | :--------------- |
| **Editora** | Não | Sim | 
| **Distribuidor** | Não | Sim|
| **Puxar assinante** | Não | Sim|
| **Flexão Push**| Sim | Sim|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > Uma subscrição de pull não é suportada quando o distribuidor é uma base de dados da Instância e o assinante não é. 

Existem diferentes [tipos de replicação:](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)


| Replicação | Bases de dados individuais e agréus | Bases de dados de instâncias|
| :----| :------------- | :--------------- |
| [**Transação Padrão**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication) | Sim (apenas como assinante) | Sim | 
| [**Instantâneo**](https://docs.microsoft.com/sql/relational-databases/replication/snapshot-replication) | Sim (apenas como assinante) | Sim|
| [**Fusão de replicação**](https://docs.microsoft.com/sql/relational-databases/replication/merge/merge-replication) | Não | Não|
| [**Peer-to-peer**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | Não | Não|
| [**Bidirecional**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | Não | Sim|
| [**Subscrições updatable**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | Não | Não|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > - Tentar configurar a replicação utilizando uma versão mais antiga pode resultar num número de erro MSSQL_REPL20084 \<(O processo não conseguiu ligar-se ao Assinante.) e MSSQ_REPL40532 (Não pode abrir o nome do servidor> solicitado pelo login. O login falhou.)
  > - Para utilizar todas as funcionalidades da Base de Dados Azure SQL, deve utilizar as versões mais recentes do [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) e das Ferramentas de Dados do [Servidor SQL (SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt).
  
  ### <a name="supportability-matrix-for-instance-databases-and-on-premises-systems"></a>Matriz de apoio para bases de dados por exemplo e sistemas no local
  A matriz de suporte de replicação, por exemplo, bases de dados é a mesma do SQL Server no local. 
  
| **Editora**   | **Distribuidor** | **Assinante** |
| :------------   | :-------------- | :------------- |
| SQL Server 2019 | SQL Server 2019 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/>  |
| SQL Server 2017 | SQL Server 2019 <br/>SQL Server 2017 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 |
| SQL Server 2016 | SQL Server 2019 <br/>SQL Server 2017 <br/> SQL Server 2016 | SQL Server 2019 <br/> SQL Server 2017 <br/>SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 |
| SQL Server 2014 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>| SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |
| SQL Server 2012 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> | SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 | 
| SQL Server 2008 R2 <br/> SQL Server 2008 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |  SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 <br/>  |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="requirements"></a>Requisitos

- A conectividade utiliza a Autenticação SQL entre os participantes da replicação. 
- Uma conta de armazenamento Azure partilhada para o diretório de trabalho utilizado por replicação. 
- A porta 445 (saída do TCP) tem de ser aberta nas regras de segurança da subnet de instância gerida para aceder à parte do ficheiro Azure. 
- A porta 1433 (saída de TCP) tem de ser aberta se o Editor/Distribuidor estiver numa instância gerida e o assinante não o fizer. Também pode ser necessário alterar a regra de segurança `allow_linkedserver_outbound` de saída da NSG para `virtualnetwork` `internet`a etiqueta de serviço de **destino** porta 1433 de . 
- Todos os tipos de participantes de replicação (Editor, Distribuidor, Pull Subscriber e Push Subscriber) podem ser colocados em instâncias geridas, mas o editor e o distribuidor devem estar na nuvem ou ambos no local.
- Se o editor, o distribuidor e/ou o assinante existirem em diferentes redes virtuais, então o peering VPN deve ser estabelecido entre cada entidade, de modo a que haja VPN a espreitar entre o editor e o distribuidor, e/ou existe VPN a espreitar entre o distribuidor e o assinante. 


>[!NOTE]
> - Pode encontrar erro 53 ao ligar-se a um Ficheiro de Armazenamento Azure se a porta 445 do grupo de segurança de rede de saída (NSG) estiver bloqueada quando o distribuidor for uma base de dados de instâncias e o assinante estiver no local. [Atualize o vNet NSG](/azure/storage/files/storage-troubleshoot-windows-file-connection-problems) para resolver este problema. 


### <a name="compare-data-sync-with-transactional-replication"></a>Comparar sincronização de dados com replicação transacional

| | Sincronização de Dados | Replicação Transacional |
|---|---|---|
| Vantagens | - Apoio ativo<br/>- Bidirecional entre as instalações e a Base de Dados SQL Azure | - Latência mais baixa<br/>- Consistência transacional<br/>- Reutilizar a topologia existente após a migração |
| Desvantagens | - 5 min ou mais latência<br/>- Sem consistência transacional<br/>- Impacto de desempenho mais elevado | - Não pode publicar a partir da base de dados azure SQL única base de dados ou base de dados pooled<br/>- Elevado custo de manutenção |
| | | |

## <a name="common-configurations"></a>Configurações comuns

Em geral, o editor e o distribuidor devem estar na nuvem ou no local. As configurações a seguir são suportadas: 

### <a name="publisher-with-local-distributor-on-a-managed-instance"></a>Editor com Distribuidor local em instância gerida

![Instância única como Editor e Distribuidor](media/replication-with-sql-database-managed-instance/01-single-instance-asdbmi-pubdist.png)

O editor e o distribuidor estão configurados numa única instância gerida e distribuem alterações para outras instâncias geridas, base de dados única, base de dados pooled ou SQL Server no local. 

### <a name="publisher-with-remote-distributor-on-a-managed-instance"></a>Editor com distribuidor remoto em instância gerida

Nesta configuração, uma instância gerida publica alterações ao distribuidor colocadas em outra instância gerida que pode servir muitas instâncias geridas por fontee distribuir alterações em um ou muitos alvos em instância gerida, base de dados única, base de dados pooled ou SQL Server.

![Casos separados para Editor e Distribuidor](media/replication-with-sql-database-managed-instance/02-separate-instances-asdbmi-pubdist.png)

Editor e distribuidor estão configurados em duas instâncias geridas. Existem alguns constrangimentos com esta configuração: 

- Ambos os casos geridos estão no mesmo vNet.
- Ambos os casos geridos estão no mesmo local.


### <a name="publisher-and-distributor-on-premises-with-a-subscriber-on-a-single-pooled-and-instance-database"></a>Editor e distribuidor no local com um assinante numa única base de dados, pooled e instância 

![Azure SQL DB como subscritor](media/replication-with-sql-database-managed-instance/03-azure-sql-db-subscriber.png)
 
Nesta configuração, uma Base de Dados Azure SQL (base de dados única, pooled e instância) é um assinante. Esta configuração suporta a migração do local para o Azure. Se um assinante estiver numa única base de dados ou agrinado, deve estar em modo de impulso.  

## <a name="with-failover-groups"></a>Com grupos failover

Se a geo-replicação for ativada numa **instância de editor** ou **distribuidor** num grupo de [failover,](sql-database-auto-failover-group.md)o administrador de instância gerido deve limpar todas as publicações na antiga primária e reconfigurá-las nas novas primárias após a ocorrência de uma falha. Neste cenário são necessárias as seguintes atividades:

1. Pare todos os trabalhos de replicação na base de dados, se houver.
2. Deixe cair os metadados de subscrição da editora executando o seguinte script na base de dados do editor:

   ```sql
   EXEC sp_dropsubscription @publication='<name of publication>', @article='all',@subscriber='<name of subscriber>'
   ```             
 
1. Deixe cair os metadados de subscrição do assinante. Executar o seguinte script na base de dados de subscrição na instância de assinante:

   ```sql
   EXEC sp_subscription_cleanup
      @publisher = N'<full DNS of publisher, e.g. example.ac2d23028af5.database.windows.net>', 
      @publisher_db = N'<publisher database>', 
      @publication = N'<name of publication>'; 
   ```                

1. Larga veementemente todos os objetos de replicação da editora executando o seguinte script na base de dados publicada:

   ```sql
   EXEC sp_removedbreplication
   ```

1. Larga o distribuidor antigo do caso primário original (se não voltar a uma antiga primária que costumava ter um distribuidor). Executar o seguinte script na base de dados principal em instância gerida por distribuidores antigos:

   ```sql
   EXEC sp_dropdistributor 1,1
   ```

Se a geo-replicação for ativada numa instância **de subscrição** num grupo de failover, a publicação deve ser configurada para se ligar ao ponto final do ouvinte do grupo failover para a instância gerida pelo assinante. Em caso de failover, a ação subsequente do administrador de instância gerida depende do tipo de falha que ocorreu: 

- Para uma falha sem perda de dados, a replicação continuará a funcionar após a falha. 
- Para uma falha com perda de dados, a replicação também funcionará. Vai replicar as mudanças perdidas novamente. 
- Para uma falha com perda de dados, mas a perda de dados está fora do período de retenção da base de dados de distribuição, o administrador de instância gerido terá de reinicializar a base de dados de subscrição. 

## <a name="next-steps"></a>Passos seguintes

- [Configure a replicação entre um editor do MI e um assinante](replication-with-sql-database-managed-instance.md)
- [Configure a replicação entre um editor do MI, distribuidor MI e subscritor do SQL Server](sql-database-managed-instance-configure-replication-tutorial.md)
- [Criar uma publicação.](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Crie uma subscrição push](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription) utilizando o nome do servidor de `N'azuresqldbdns.database.windows.net` base de dados Azure SQL como assinante (por exemplo, e o nome de Base de Dados Azure SQL como base de dados de destino (por **exemplo, Adventureworks**. )


Para obter mais informações sobre a configuração da replicação transacional, consulte os seguintes tutoriais:



## <a name="see-also"></a>Veja também  

- [Replicação com um MI e um grupo failover](sql-database-managed-instance-transact-sql-information.md#replication)
- [Replicação para a Base de Dados SQL](replication-to-sql-database.md)
- [Replicação para instância gerida](replication-with-sql-database-managed-instance.md)
- [Criar uma Publicação](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Criar uma subscrição push](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Types of Replication](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication) (Tipos de Replicação)
- [Monitorização (Replicação)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Inicializar uma Subscrição](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
