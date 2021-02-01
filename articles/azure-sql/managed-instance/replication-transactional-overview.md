---
title: Replicação transacional
titleSuffix: Azure SQL Managed Instance
description: Saiba como utilizar a replicação transacional do SQL Server com Azure SQL Managed Instance (Preview).
services: sql-database
ms.service: sql-managed-instance
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein
ms.date: 04/20/2020
ms.openlocfilehash: 3e4b4fc3d4a6c9529c7c0ac0daef8a28173e0bf3
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2021
ms.locfileid: "99225348"
---
# <a name="transactional-replication-with-azure-sql-managed-instance-preview"></a>Replicação transacional com Azure SQL Gestded Instance (Preview)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

A replicação transacional é uma característica do Azure SQL Managed Instance e do SQL Server que lhe permite replicar dados de uma tabela em Azure SQL Managed Instance ou uma instância do SQL Server para tabelas colocadas em bases de dados remotas. Esta funcionalidade permite sincronizar várias tabelas em diferentes bases de dados. 

A replicação transacional está atualmente em pré-visualização pública para SQL Managed Instance. 

## <a name="overview"></a>Descrição Geral

Pode utilizar a replicação transacional para impulsionar as alterações efetuadas numa Instância Gerida Azure SQL para:

- Uma base de dados SQL Server - no local ou no Azure VM
- Uma base de dados na Base de Dados Azure SQL
- Uma base de dados de casos em Azure SQL GestdEd Instance

  > [!NOTE]
  > Para utilizar todas as funcionalidades do Azure SQL Managed Instance, deve utilizar as versões mais recentes do [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) e das Ferramentas de [Dados do Servidor SQL (SSDT)](/sql/ssdt/download-sql-server-data-tools-ssdt).

### <a name="components"></a>Componentes

Os componentes-chave na replicação transacional são o **Editor,** **Distribuidor** e **Assinante,** como mostra a seguinte imagem:  

![replicação com Base de Dados SQL](./media/replication-transactional-overview/replication-to-sql-database.png)

| Função | Base de Dados SQL do Azure | Instância Gerida do Azure SQL |
| :----| :------------- | :--------------- |
| **Publisher** | Não | Sim |
| **Distribuidor** | Não | Sim|
| **Puxe o assinante** | Não | Sim|
| **Empurre o assinante**| Sim | Sim|
| &nbsp; | &nbsp; | &nbsp; |

A **Editora** publica alterações feitas em algumas tabelas (artigos) enviando as atualizações para o Distribuidor. O editor pode ser um Azure SQL Managed Instance ou uma instância sql Server.

O **Distribuidor** recolhe alterações nos artigos de um Editor e distribui-os aos Assinantes. O Distribuidor pode ser um Exemplo Gerido Azure SQL ou uma instância SQL Server (qualquer versão desde que seja igual ou superior à versão Editor).

O **Assinante** recebe alterações es feitas na Editora. Uma instância do SQL Server e a Azure SQL Managed Instance podem ser simultaneamente assinantes de empurrar e puxar, embora uma subscrição de pull não seja suportada quando o distribuidor é uma Instância Gerida Azure SQL e o assinante não é. Uma base de dados na Base de Dados Azure SQL só pode ser um assinante.

Azure SQL Managed Instance pode suportar ser assinante a partir das seguintes versões do SQL Server:

- SQL Server 2016 e mais tarde
- SQL Server 2014 [RTM CU10 (12.0.4427.24)](https://support.microsoft.com/help/3094220/cumulative-update-10-for-sql-server-2014) ou [SP1 CU3 (12.0.2556.4)](https://support.microsoft.com/help/3094221/cumulative-update-3-for-sql-server-2014-service-pack-1)
- SQL Server 2012 [SP2 CU8 (11.0.5634.1)](https://support.microsoft.com/help/3082561/cumulative-update-8-for-sql-server-2012-sp2) ou [SP3 (11.0.6020.0)](https://www.microsoft.com/download/details.aspx?id=49996)

   > [!NOTE]
   >
   > - Para outras versões do SQL Server que não suportam a publicação de objetos em Azure, é possível utilizar o método de [dados de republicação](/sql/relational-databases/replication/republish-data) para mover dados para versões mais recentes do SQL Server.
   > - Tentar configurar a replicação utilizando uma versão mais antiga pode resultar em número de erro MSSQL_REPL20084 (O processo não conseguiu ligar-se ao Assinante.) e MSSQ_REPL40532 (Não pode abrir o servidor \<name> solicitado pelo login. O login falhou.)

### <a name="types-of-replication"></a>Tipos de replicação

Existem diferentes [tipos de replicação:](/sql/relational-databases/replication/types-of-replication)

| Replicação | Base de Dados SQL do Azure | Instância Gerida do Azure SQL |
| :----| :------------- | :--------------- |
| [**Transação Padrão**](/sql/relational-databases/replication/transactional/transactional-replication) | Sim (apenas como assinante) | Sim |
| [**Instantâneo**](/sql/relational-databases/replication/snapshot-replication) | Sim (apenas como assinante) | Sim|
| [**Fusão de replicação**](/sql/relational-databases/replication/merge/merge-replication) | Não | Não|
| [**Ponto a ponto**](/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | Não | Não|
| [**Bidirecional**](/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | Não | Sim|
| [**Assinaturas updatable**](/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | Não | Não|
| &nbsp; | &nbsp; | &nbsp; |

### <a name="supportability-matrix"></a>Matriz de Apoio

  A matriz de suporte de replicação transacional para Azure SQL Managed Instance é a mesma para o SQL Server.
  
| **Publisher**   | **Distribuidor** | **Assinante** |
| :------------   | :-------------- | :------------- |
| SQL Server 2019 | SQL Server 2019 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/>  |
| SQL Server 2017 | SQL Server 2019 <br/>SQL Server 2017 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 |
| SQL Server 2016 | SQL Server 2019 <br/>SQL Server 2017 <br/> SQL Server 2016 | SQL Server 2019 <br/> SQL Server 2017 <br/>SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 |
| SQL Server 2014 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>| SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |
| SQL Server 2012 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> | SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |
| SQL Server 2008 R2 <br/> SQL Server 2008 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |  SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 <br/>  |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="when-to-use"></a>Quando utilizar

A replicação transacional é útil nos seguintes cenários:

- Publique alterações es feitas numa ou mais tabelas numa base de dados e distribua-as para uma ou muitas bases de dados numa instância do SQL Server ou na Base de Dados Azure SQL que subscreveu as alterações.
- Mantenha várias bases de dados distribuídas em estado sincronizado.
- Migrar bases de dados de uma instância do SQL Server ou Azure SQL Managed Instance para outra base de dados publicando continuamente as alterações.

### <a name="compare-data-sync-with-transactional-replication"></a>Compare o Sincronização de Dados com a Replicação Transacional

| Categoria | Sincronização de Dados | Replicação Transacional |
|---|---|---|
| Vantagens | - Suporte ativo<br/>- Biducional entre as instalações e a Base de Dados Azure SQL | - Menor latência<br/>- Consistência transacional<br/>- Reutilização da topologia existente após a migração |
| Desvantagens | - Sem consistência transacional<br/>- Maior impacto no desempenho | - Não pode publicar na Base de Dados Azure SQL <br/>- Alto custo de manutenção |

## <a name="common-configurations"></a>Configurações comuns

Em geral, o editor e o distribuidor devem estar na nuvem ou no local. As configurações a seguir são suportadas:

### <a name="publisher-with-local-distributor-on-sql-managed-instance"></a>Editor com distribuidor local em SQL Managed Instance

![Instância única como Editor e Distribuidor](./media/replication-transactional-overview/01-single-instance-asdbmi-pubdist.png)

O editor e o distribuidor estão configurados dentro de uma única ocorrência gerida sql e distribuindo alterações para outra instância gerida sql, base de dados SQL ou exemplo de servidor SQL.

### <a name="publisher-with-remote-distributor-on-sql-managed-instance"></a>Editor com distribuidor remoto em SQL Managed Instance

Nesta configuração, um caso gerido publica alterações a um distribuidor colocado em outra SQL Managed Instance que pode servir muitas fontes SQL Managed Instances e distribuir alterações a um ou muitos alvos na Base de Dados Azure SQL, Azure SQL Managed Instance, ou SQL Server.

![Instâncias separadas para Editor e Distribuidor](./media/replication-transactional-overview/02-separate-instances-asdbmi-pubdist.png)

Editor e distribuidor são configurados em duas instâncias geridas. Existem alguns constrangimentos com esta configuração:

- Ambas as instâncias geridas estão no mesmo vNet.
- Ambas as instâncias geridas estão no mesmo local.

### <a name="on-premises-publisherdistributor-with-remote-subscriber"></a>No local Editor/Distribuidor com assinante remoto

![Azure SQL Database como assinante](./media/replication-transactional-overview/03-azure-sql-db-subscriber.png)

Nesta configuração, uma base de dados na Base de Dados Azure SQL ou Azure SQL Managed Instance é um assinante. Esta configuração suporta a migração de instalações para Azure. Se um assinante for uma base de dados na Base de Dados Azure SQL, deve estar no modo de pressão.  

## <a name="requirements"></a>Requisitos

- Utilize a autenticação SQL para conectividade entre os participantes da replicação.
- Utilize uma parte da Conta de Armazenamento Azure para o diretório de trabalho utilizado por replicação.
- Abra a porta de saída TCP 445 nas regras de segurança da sub-rede para aceder à partilha de ficheiros Azure.
- Abra a porta de saída TCP 1433 quando a SQL Managed Instance é a Editora/Distribuidor, e o Assinante não é. Também poderá ter de alterar a regra de segurança de saída NSG de instância gerida sql para `allow_linkedserver_outbound` a etiqueta de serviço de **destino** do porto 1433 de `virtualnetwork` `internet` .
- Coloque tanto o editor como o distribuidor na nuvem, ou ambos no local.
- Configure a VPN a espreitar entre as redes virtuais de participantes de replicação se as redes virtuais forem diferentes.

> [!NOTE]
> Pode encontrar o erro 53 ao ligar-se a um Ficheiro de Armazenamento Azure se a porta 445 do grupo de segurança da rede de saída (NSG) estiver bloqueada quando o distribuidor for uma base de dados de instância gerida Azure SQL e o assinante estiver no local. [Atualize o vNet NSG](../../storage/files/storage-troubleshoot-windows-file-connection-problems.md) para resolver este problema.

## <a name="with-failover-groups"></a>Com grupos de failover

[A geo-replicação ativa](../database/active-geo-replication-overview.md) não é suportada com uma SQL Managed Instance utilizando a replicação transacional. Em vez de geo-replicação ativa, utilize [grupos auto-failover](../database/auto-failover-group-overview.md), mas note que a publicação tem de ser [eliminada manualmente](transact-sql-tsql-differences-sql-server.md#replication) da instância gerida primária e recriada na segunda SQL Managed Instance após o failover.

Se a geo-replicação estiver ativada num **editor** ou **distribuidor** SQL Managed Instance num grupo de [failover,](../database/auto-failover-group-overview.md)o administrador da SQL Managed Instance deve limpar todas as publicações sobre a antiga primária e reconfigurá-las na nova primária após a ocorrência de uma falha. Neste cenário são necessárias as seguintes atividades:

1. Parem com todos os trabalhos de replicação na base de dados, se houver.
1. Largue os metadados de subscrição do editor executando o seguinte script na base de dados dos editores:

   ```sql
   EXEC sp_dropsubscription @publication='<name of publication>', @article='all',@subscriber='<name of subscriber>'
   ```

1. Largue os metadados de subscrição do assinante. Execute o seguinte script na base de dados de subscrição no assinante SQL Managed Instance:

   ```sql
   EXEC sp_subscription_cleanup
      @publisher = N'<full DNS of publisher, e.g. example.ac2d23028af5.database.windows.net>',
      @publisher_db = N'<publisher database>',
      @publication = N'<name of publication>';
   ```

1. Largando vigorosamente todos os objetos de replicação do editor executando o seguinte script na base de dados publicada:

   ```sql
   EXEC sp_removedbreplication
   ```

1. Largar vigorosamente o antigo distribuidor do sql managed instance primário original (se falhar de volta a uma antiga primária que costumava ter um distribuidor). Execute o seguinte script na base de dados principal do antigo distribuidor SQL Managed Instance:

   ```sql
   EXEC sp_dropdistributor 1,1
   ```

Se a geo-replicação for ativada numa instância **de assinante** num grupo de failover, a publicação deve ser configurada para ligar ao ponto final do grupo de failover para a instância gerida pelo assinante. Em caso de falha, a ação subsequente do administrador de instâncias gerida depende do tipo de falha que ocorreu:

- Para uma falha sem perda de dados, a replicação continuará a funcionar após o fracasso.
- Para uma falha com a perda de dados, a replicação também funcionará. Vai replicar as mudanças perdidas novamente.
- Para uma falha com a perda de dados, mas a perda de dados está fora do período de retenção da base de dados de distribuição, o administrador da SQL Managed Instance terá de reinitializar a base de dados de subscrição.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a configuração da replicação transacional, consulte os seguintes tutoriais:

- [Configure a replicação entre um editor de exemplos geridos sql e assinante](../managed-instance/replication-between-two-instances-configure-tutorial.md)
- [Configure a replicação entre um editor de instância gerida SQL, distribuidor de instância gerida SQL e subscritor do SQL Server](../managed-instance/replication-two-instances-and-sql-server-configure-tutorial.md)
- [Criar uma publicação.](/sql/relational-databases/replication/publish/create-a-publication)
- [Crie uma subscrição push](/sql/relational-databases/replication/create-a-push-subscription) utilizando o nome do servidor como assinante (por `N'azuresqldbdns.database.windows.net` exemplo, e a base de dados no nome Azure SQL Database como base de dados de destino (por exemplo, **Adventureworks**. )

## <a name="see-also"></a>Ver também  

- [Replicação com uma SQL Managed Instance e um grupo de failover](transact-sql-tsql-differences-sql-server.md#replication)
- [Replicação para a Base de Dados SQL](../database/replication-to-sql-database.md)
- [Replicação para instância gerida](../managed-instance/replication-between-two-instances-configure-tutorial.md)
- [Criar uma Publicação](/sql/relational-databases/replication/publish/create-a-publication)
- [Criar uma subscrição push](/sql/relational-databases/replication/create-a-push-subscription/)
- [Types of Replication](/sql/relational-databases/replication/types-of-replication) (Tipos de Replicação)
- [Monitorização (Replicação)](/sql/relational-databases/replication/monitor/monitoring-replication)
- [Inicializar uma Subscrição](/sql/relational-databases/replication/initialize-a-subscription)
