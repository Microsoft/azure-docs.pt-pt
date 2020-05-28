---
title: Replicação transacional
titleSuffix: Azure SQL Managed Instance
description: Saiba mais sobre a utilização da replicação transacional do SQL Server com a Instância Gerida azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 04/20/2020
ms.openlocfilehash: e55b75b26eec3112472d78c0c22c147a80177007
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84118709"
---
# <a name="transactional-replication-with-azure-sql-managed-instance"></a>Replicação transacional com instância gerida azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

A replicação transacional é uma característica do Azure SQL Managed Instance e do SQL Server que lhe permite replicar dados de uma tabela em Azure SQL Managed Instance ou um Servidor SQL para tabelas colocadas em bases de dados remotas. Esta funcionalidade permite sincronizar várias tabelas em diferentes bases de dados.

## <a name="overview"></a>Descrição geral

Pode utilizar a replicação transacional para impulsionar alterações feitas numa Instância Gerida Azure SQL para:

- Uma base de dados do SQL Server - no local ou num VM Azure
- Uma base de dados única ou agréfica na Base de Dados Azure SQL
- Uma base de dados única ou agréfica em Azure SQL Managed Instance

  > [!NOTE]
  > Para utilizar todas as funcionalidades do Caso Gerido Azure SQL, deve utilizar as versões mais recentes do [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) e das Ferramentas de Dados do [Servidor SQL (SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt).

### <a name="components"></a>Componentes

Os componentes-chave na replicação transacional são o **Editor,** **Distribuidor,** e **Assinante,** como mostra a seguinte imagem:  

![replicação com Base de Dados SQL](./media/replication-transactional-overview/replication-to-sql-database.png)

| Função | Base de Dados SQL do Azure | Instância Gerida do Azure SQL |
| :----| :------------- | :--------------- |
| **Publisher** | Não | Sim |
| **Distribuidor** | Não | Sim|
| **Puxar assinante** | Não | Sim|
| **Flexão Push**| Sim | Sim|
| &nbsp; | &nbsp; | &nbsp; |

A **Editora** publica alterações feitas em algumas tabelas (artigos) enviando as atualizações para o Distribuidor. A editora pode ser uma Instância Gerida Azure SQL ou um Servidor SQL.

O **Distribuidor** recolhe alterações nos artigos de uma Editora e distribui-as aos Assinantes. O Distribuidor pode ser o Azure SQL Managed Instance ou o SQL Server (qualquer versão desde que seja igual ou superior à versão Publisher).

O **Assinante** recebe alterações feitas na Editora. O SQL Server e o Azure SQL Managed Instance podem ser os subscritores push e pull, embora uma subscrição de pull não seja suportada quando o distribuidor é um Caso Gerido Azure SQL e o assinante não é. A Base de Dados Azure SQL só pode ser um subscritor push.

A Instância Gerida pelo Azure SQL pode suportar ser assinante a partir das seguintes versões do Servidor SQL:

- SQL Server 2016 e maior
- SQL Server 2014 [RTM CU10 (12.0.4427.24)](https://support.microsoft.com/help/3094220/cumulative-update-10-for-sql-server-2014) ou [SP1 CU3 (12.0.2556.4)](https://support.microsoft.com/help/3094221/cumulative-update-3-for-sql-server-2014-service-pack-1)
- SQL Server 2012 [SP2 CU8 (11.0.5634.1)](https://support.microsoft.com/help/3082561/cumulative-update-8-for-sql-server-2012-sp2) ou [SP3 (11.0.6020.0)](https://www.microsoft.com/download/details.aspx?id=49996)

   > [!NOTE]
   >
   > - Para outras versões do SQL Server que não suportam a publicação de objetos no Azure, é possível utilizar o método de [republicação](https://docs.microsoft.com/sql/relational-databases/replication/republish-data) de dados para mover dados para versões mais recentes do SQL Server.
   > - Tentar configurar a replicação utilizando uma versão mais antiga pode resultar num número de erro MSSQL_REPL20084 (O processo não conseguiu ligar-se ao Assinante.) e MSSQ_REPL40532 (Não pode abrir o servidor \<name> solicitado pelo login. O login falhou.)

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

### <a name="supportability-matrix"></a>Matriz de apoio

  A matriz de suporte à replicação transacional para a Instância Gerida Azure SQL é a mesma para o SQL Server no local.
  
| **Publisher**   | **Distribuidor** | **Assinante** |
| :------------   | :-------------- | :------------- |
| SQL Server 2019 | SQL Server 2019 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/>  |
| SQL Server 2017 | SQL Server 2019 <br/>SQL Server 2017 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 |
| SQL Server 2016 | SQL Server 2019 <br/>SQL Server 2017 <br/> SQL Server 2016 | SQL Server 2019 <br/> SQL Server 2017 <br/>SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 |
| SQL Server 2014 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>| SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |
| SQL Server 2012 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> | SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |
| SQL Server 2008 R2 <br/> SQL Server 2008 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |  SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 <br/>  |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="when-to-use"></a>Quando utilizar

A replicação transacional é útil nos seguintes cenários:

- Publique alterações feitas numa ou mais tabelas numa base de dados e distribua-as para uma ou muitas bases de dados numa instância do Servidor SQL ou na Base de Dados Azure SQL que subscreveu as alterações.
- Mantenha várias bases de dados distribuídas em estado sincronizado.
- Migrar bases de dados de uma instância do SQL Server ou do Azure SQL Managed Instance para outra base de dados publicando continuamente as alterações.

### <a name="compare-data-sync-with-transactional-replication"></a>Comparar sincronização de dados com replicação transacional

| | Sincronização de Dados | Replicação Transacional |
|---|---|---|
| Vantagens | - Apoio ativo<br/>- Bidirecional entre as instalações e a Base de Dados SQL Azure | - Latência mais baixa<br/>- Consistência transacional<br/>- Reutilizar a topologia existente após a migração |
| Desvantagens | - 5 min ou mais latência<br/>- Sem consistência transacional<br/>- Impacto de desempenho mais elevado | - Não pode ser publicado na Base de Dados Azure SQL <br/>- Elevado custo de manutenção |
| | | |

## <a name="common-configurations"></a>Configurações comuns

Em geral, o editor e o distribuidor devem estar na nuvem ou no local. As configurações a seguir são suportadas:

### <a name="publisher-with-local-distributor-on-sql-mi"></a>Editor com distribuidor local no SQL MI

![Instância única como Editor e Distribuidor](./media/replication-transactional-overview/01-single-instance-asdbmi-pubdist.png)

O editor e o distribuidor estão configurados dentro de uma única instância gerida e distribuem alterações para outros Servidores Azure SQL, Instância Gerida Azure SQL ou SQL Server no local ou num VM.

### <a name="publisher-with-remote-distributor-on-sql-mi"></a>Editor com distribuidor remoto no SQL MI

Nesta configuração, uma instância gerida publica alterações ao distribuidor colocadas em outra instância gerida que pode servir muitas instâncias geridas por fontee distribuir alterações em um ou muitos alvos em Azure SQL Server, Azure SQL Managed Instance, ou SQL Server no local ou num VM.

![Casos separados para Editor e Distribuidor](./media/replication-transactional-overview/02-separate-instances-asdbmi-pubdist.png)

Editor e distribuidor estão configurados em duas instâncias geridas. Existem alguns constrangimentos com esta configuração:

- Ambos os casos geridos estão no mesmo vNet.
- Ambos os casos geridos estão no mesmo local.

### <a name="on-prem-pubdist-with-remote-subscriber"></a>Pub on-prem/Dist com assinante remoto

![Base de Dados Azure SQL como assinante](./media/replication-transactional-overview/03-azure-sql-db-subscriber.png)

Nesta configuração, uma base de dados Azure SQL ou azure SQL Managed Instance é um assinante. Esta configuração suporta a migração do local para o Azure. Se um assinante for Azure SQL Database, deve estar em modo de impulso.  

## <a name="requirements"></a>Requisitos

- Utilize a Autenticação SQL para a conectividade entre os participantes da replicação.
- Utilize uma conta de armazenamento Azure para o diretório de trabalho utilizado por replicação.
- Abra a porta de saída do TCP 445 nas regras de segurança da subnet para aceder à parte do ficheiro Azure.
- Abra a porta de saída do TCP 1433 quando a Instância Gerida SQL é a Editora/Distribuidor, e o Assinante não é. Também pode ser necessário alterar a regra de segurança de saída sQL Managed Instance NSG para a etiqueta de `allow_linkedserver_outbound` Serviço de **Destino** 1433 da porta 1433 de `virtualnetwork` . `internet`
- Coloque tanto o editor como o distribuidor na nuvem, ou ambos no local.
- Configure o peering VPN entre as redes virtuais de participantes de replicação se as redes virtuais forem diferentes.

> [!NOTE]
> Pode encontrar erro 53 ao ligar-se a um Ficheiro de Armazenamento Azure se a porta 445 do grupo de segurança de rede de saída (NSG) estiver bloqueada quando o distribuidor for uma base de dados Azure SQL Managed Instance e o assinante estiver no local. [Atualize o vNet NSG](/azure/storage/files/storage-troubleshoot-windows-file-connection-problems) para resolver este problema.

## <a name="with-failover-groups"></a>Com grupos failover

[A geo-replicação ativa](../database/active-geo-replication-overview.md) não é suportada com uma Instância Gerida SQL utilizando replicação transacional. Em vez de geo-replicação ativa, utilize [grupos de auto-failover,](../database/auto-failover-group-overview.md)mas note que a publicação tem de ser [eliminada manualmente](transact-sql-tsql-differences-sql-server.md#replication) da instância gerida primária e recriada no Caso Gerido sql secundário após a falha.

Se a geo-replicação for ativada num **editor** ou **distribuidor** SQL Managed Instance num grupo de [failover,](../database/auto-failover-group-overview.md)o administrador SQL Managed Instance deve limpar todas as publicações na antiga primária e reconfigurá-las nas novas primárias após a ocorrência de uma falha. Neste cenário são necessárias as seguintes atividades:

1. Pare todos os trabalhos de replicação na base de dados, se houver.
1. Deixe cair os metadados de subscrição da editora executando o seguinte script na base de dados do editor:

   ```sql
   EXEC sp_dropsubscription @publication='<name of publication>', @article='all',@subscriber='<name of subscriber>'
   ```

1. Deixe cair os metadados de subscrição do assinante. Executar o seguinte script na base de dados de subscrição em Caso Gerido sQL assinante:

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

1. Larga veementemente o antigo distribuidor da Instância Gerida Primária Original (se não voltar a uma antiga primária que costumava ter um distribuidor). Executar o seguinte script na base de dados principal em antigo distribuidor SQL Managed Instance:

   ```sql
   EXEC sp_dropdistributor 1,1
   ```

Se a geo-replicação for ativada numa instância **de subscrição** num grupo de failover, a publicação deve ser configurada para se ligar ao ponto final do ouvinte do grupo failover para a instância gerida pelo assinante. Em caso de failover, a ação subsequente do administrador de instância gerida depende do tipo de falha que ocorreu:

- Para uma falha sem perda de dados, a replicação continuará a funcionar após a falha.
- Para uma falha com perda de dados, a replicação também funcionará. Vai replicar as mudanças perdidas novamente.
- Para uma falha com perda de dados, mas a perda de dados está fora do período de retenção da base de dados de distribuição, o administrador sQL Managed Instance terá de reinicializar a base de dados de subscrição.

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre a configuração da replicação transacional, consulte os seguintes tutoriais:

- [Configure a replicação entre um editor do MI e um assinante](../managed-instance/replication-between-two-instances-configure-tutorial.md)
- [Configure a replicação entre um editor do MI, distribuidor MI e subscritor do SQL Server](../managed-instance/replication-two-instances-and-sql-server-configure-tutorial.md)
- [Criar uma publicação.](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Crie uma subscrição push](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription) utilizando o nome do servidor como assinante (por exemplo, e o nome de `N'azuresqldbdns.database.windows.net` Base de Dados Azure SQL como base de dados de destino (por **exemplo, Adventureworks**. )

## <a name="see-also"></a>Veja também  

- [Replicação com um MI e um grupo failover](transact-sql-tsql-differences-sql-server.md#replication)
- [Replicação para a Base de Dados SQL](../database/replication-to-sql-database.md)
- [Replicação para instância gerida](../managed-instance/replication-between-two-instances-configure-tutorial.md)
- [Criar uma Publicação](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Criar uma subscrição push](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Types of Replication](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication) (Tipos de Replicação)
- [Monitorização (Replicação)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Inicializar uma Subscrição](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
