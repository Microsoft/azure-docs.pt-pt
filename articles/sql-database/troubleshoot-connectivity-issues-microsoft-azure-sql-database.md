---
title: Solucionar problemas comuns de conexão com o banco de dados SQL
description: Fornece etapas para solucionar problemas de conexão do banco de dados SQL do Azure e resolver outros problemas específicos do banco de dados SQL
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
ms.custom: seo-lt-2019, OKR 11/2019
author: ramakoni1
ms.author: ramakoni
ms.reviewer: carlrab,vanto
ms.date: 11/14/2019
ms.openlocfilehash: 445048531826861afb13c5fff6af407348aa9c2e
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75940793"
---
# <a name="troubleshooting-connectivity-issues-and-other-errors-with-microsoft-azure-sql-database"></a>Solucionar problemas de conectividade e outros erros com o Banco de Dados SQL do Microsoft Azure

Recebe mensagens de erro quando a ligação à base de dados SQL do Azure falha. Esses problemas de conexão podem ser causados pela reconfiguração do banco de dados SQL do Azure, configurações de firewall, tempo limite de conexão, informações de logon incorretas ou falha na aplicação de práticas recomendadas e diretrizes de design durante o [design do aplicativo] ( sql-database-develop-overview.md) processo. Além disso, se o limite máximo de alguns recursos do banco de dados SQL do Azure for atingido, você não poderá se conectar ao banco de dados SQL do Azure.

## <a name="transient-fault-error-messages-40197-40613-and-others"></a>Mensagens transitórias de erro de falha (40197, 40613 e outras)

A infraestrutura do Azure tem a capacidade de reconfigurar dinamicamente os serviços quando surgem cargas de trabalho pesadas no serviço Base de Dados SQL.  Este comportamento dinâmico poderá fazer com que o seu programa cliente perca a ligação à Base de Dados SQL. Esse tipo de condição de erro é chamado de *falha transitória*. Os eventos de reconfiguração de banco de dados ocorrem devido a um evento planejado (por exemplo, uma atualização de software) ou a um evento não planejado (por exemplo, uma falha de processo ou balanceamento de carga). A maioria dos eventos de reconfiguração geralmente é de curta duração e deve ser concluída em menos de 60 segundos. No entanto, esses eventos podem levar mais tempo para terminar, como quando uma transação grande causa uma recuperação de execução longa. A tabela a seguir lista vários erros transitórios que os aplicativos podem receber ao se conectar ao banco de dados SQL

### <a name="list-of-transient-fault-error-codes"></a>Lista de códigos de erro transitórios


| Código de erro | Gravidade | Descrição |
| ---:| ---:|:--- |
| 4060 |16 |Não é possível abrir a base de dados "%.&#x2a;ls" pedida pelo início de sessão. Falha no logon. Para obter mais informações, consulte [erros 4000 a 4999](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-4000-to-4999)|
| 40197 |17 |O serviço encontrou um erro ao processar sua solicitação. Tente novamente. Código de erro% d.<br/><br/>Você recebe esse erro quando o serviço está inativo devido a atualizações de software ou hardware, falhas de hardware ou quaisquer outros problemas de failover. O código de erro (% d) incorporado na mensagem de erro 40197] fornece informações adicionais sobre o tipo de falha ou failover ocorrido. Alguns exemplos dos códigos de erro são inseridos dentro da mensagem de erro 40197 são 40020, 40143, 40166 e 40540.<br/><br/>Reconectar-se ao servidor do banco de dados SQL automaticamente o conecta a uma cópia íntegra do seu banco de dados. Seu aplicativo deve capturar o erro 40197, registrar o código de erro inserido (% d) na mensagem para solução de problemas e tentar reconectar-se ao banco de dados SQL até que os recursos estejam disponíveis e sua conexão seja estabelecida novamente. Para obter mais informações, consulte [erros transitórios](sql-database-connectivity-issues.md#transient-errors-transient-faults).|
| 40501 |20 |o serviço está ocupado neste momento. Repita a solicitação após 10 segundos. ID do incidente:% ls. Código:% d. Para obter mais informações, veja: <br/>limites de [recursos de servidor de banco de dados](sql-database-resource-limits-database-server.md) &bull; &nbsp;<br/>&bull; &nbsp;[limites com base em DTU para bancos de dados individuais](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[limites com base em DTU para pools elásticos](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[limites baseados em vCore para bancos de dados individuais](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[limites baseados em vCore para pools elásticos](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[limites de recursos de instância gerenciada](sql-database-managed-instance-resource-limits.md).|
| 40613 |17 |Base de dados '%.&#x2a;ls' no servidor '%.&#x2a;ls' não está atualmente disponível. Repita a conexão mais tarde. Se o problema persistir, contacte o suporte ao cliente e forneça o ID de rastreio de sessão de '%.&#x2a;ls'.<br/><br/> Esse erro pode ocorrer se já houver uma conexão de administrador dedicada (DAC) existente estabelecida com o banco de dados. Para obter mais informações, consulte [erros transitórios](sql-database-connectivity-issues.md#transient-errors-transient-faults).|
| 49918 |16 |Não é possível processar o pedido. Não há recursos suficientes para processar a solicitação.<br/><br/>o serviço está ocupado neste momento. Repita a solicitação mais tarde. Para obter mais informações, veja: <br/>limites de [recursos de servidor de banco de dados](sql-database-resource-limits-database-server.md) &bull; &nbsp;<br/>&bull; &nbsp;[limites com base em DTU para bancos de dados individuais](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[limites com base em DTU para pools elásticos](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[limites baseados em vCore para bancos de dados individuais](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[limites baseados em vCore para pools elásticos](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[limites de recursos de instância gerenciada](sql-database-managed-instance-resource-limits.md). |
| 49919 |16 |Não é possível processar a solicitação de criação ou atualização. Muitas operações de criação ou atualização em andamento para a assinatura "% ld".<br/><br/>O serviço está ocupado processando várias solicitações de criação ou atualização para sua assinatura ou servidor. Atualmente, as solicitações estão bloqueadas para otimização de recursos. Consulte [Sys. dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) para operações pendentes. Aguarde até que as solicitações de criação ou atualização pendentes sejam concluídas ou exclua uma de suas solicitações pendentes e repita a solicitação mais tarde. Para obter mais informações, veja: <br/>limites de [recursos de servidor de banco de dados](sql-database-resource-limits-database-server.md) &bull; &nbsp;<br/>&bull; &nbsp;[limites com base em DTU para bancos de dados individuais](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[limites com base em DTU para pools elásticos](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[limites baseados em vCore para bancos de dados individuais](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[limites baseados em vCore para pools elásticos](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[limites de recursos de instância gerenciada](sql-database-managed-instance-resource-limits.md). |
| 49920 |16 |Não é possível processar o pedido. Muitas operações em andamento para a assinatura "% ld".<br/><br/>O serviço está ocupado processando várias solicitações para esta assinatura. Atualmente, as solicitações estão bloqueadas para otimização de recursos. Consulte [Sys. dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) para obter o status da operação. Aguarde até que as solicitações pendentes sejam concluídas ou exclua uma de suas solicitações pendentes e repita a solicitação mais tarde. Para obter mais informações, veja: <br/>limites de [recursos de servidor de banco de dados](sql-database-resource-limits-database-server.md) &bull; &nbsp;<br/>&bull; &nbsp;[limites com base em DTU para bancos de dados individuais](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[limites com base em DTU para pools elásticos](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[limites baseados em vCore para bancos de dados individuais](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[limites baseados em vCore para pools elásticos](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[limites de recursos de instância gerenciada](sql-database-managed-instance-resource-limits.md). |
| 4221 |16 |Falha de logon para leitura-secundária devido a longa espera em ' HADR_DATABASE_WAIT_FOR_TRANSITION_TO_VERSIONING '. A réplica não está disponível para logon porque as versões de linha estão ausentes para transações que estavam em andamento quando a réplica foi reciclada. O problema pode ser resolvido recuperando ou confirmando as transações ativas na réplica primária. As ocorrências dessa condição podem ser minimizadas, evitando transações de gravação longas no primário. |


### <a name="steps-to-resolve-transient-connectivity-issues"></a>Etapas para resolver problemas de conectividade transitórios

1. Verifique o [painel de serviço do Microsoft Azure](https://azure.microsoft.com/status) para quaisquer interrupções conhecidas que ocorreram durante o tempo durante o qual os erros foram relatados pelo aplicativo.
2. Os aplicativos que se conectam a um serviço de nuvem como o banco de dados SQL do Azure devem esperar eventos de reconfiguração periódicos e implementar a lógica de repetição para lidar com esses erros em vez de identificando-los como erros de aplicativo para os usuários 
3. Como um banco de dados se aproxima de seus limites de recursos, pode parecer ser um problema de conectividade transitório. Consulte [limites de recursos](sql-database-resource-limits-database-server.md#what-happens-when-database-resource-limits-are-reached).
4. Se os problemas de conectividade continuarem, ou se a duração para a qual seu aplicativo encontra o erro exceder 60 segundos ou se você vir várias ocorrências do erro em um determinado dia, faça uma solicitação de suporte do Azure selecionando **obter suporte** no site de [suporte do Azure](https://azure.microsoft.com/support/options) .

#### <a name="implementing-retry-logic"></a>Implementando lógica de repetição
É altamente recomendável que o programa cliente tenha a lógica de repetição para que possa restabelecer uma conexão depois de dar o tempo de falha transitória para corrigir a si mesmo.  É recomendável que você adie por 5 segundos antes da primeira tentativa. Repetir após um atraso menor que 5 segundos riscos sobrecarregam o serviço de nuvem. Para cada repetição subsequente, o atraso deve aumentar exponencialmente, até um máximo de 60 segundos.

Para obter exemplos de código de lógica de repetição, consulte:
- [Conectar-se de forma resiliente ao SQL com ADO.NET](https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net)
- [Conectar-se de forma resiliente ao SQL com PHP](https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php)

Para obter informações adicionais sobre como lidar com erros transitórios na revisão do seu aplicativo
* [Solucionando problemas de erros transitórios de conexão para o banco de dados SQL](sql-database-connectivity-issues.md)

Uma discussão sobre o *período de bloqueio* para clientes que usam o ADO.net está disponível no [ADO.net (pooling de conexão SQL Server)](https://msdn.microsoft.com/library/8xx3tyca.aspx).

## <a name="a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-database-server"></a>Ocorreu um erro relacionado à rede ou específico da instância ao estabelecer uma conexão com o servidor do banco de dados SQL

O problema ocorre se o aplicativo não puder se conectar ao servidor.

Para resolver esse problema, tente as etapas (na ordem apresentada) na seção [etapas para corrigir problemas comuns de conexão](#steps-to-fix-common-connection-issues) .

## <a name="the-serverinstance-was-not-found-or-was-not-accessible-errors-26-40-10053"></a>O servidor/instância não foi encontrado ou não estava acessível (erros 26, 40, 10053)

#### <a name="error-26-error-locating-server-specified"></a>Erro 26: erro ao localizar o servidor especificado

``System.Data.SqlClient.SqlException: A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections.(provider: SQL Network Interfaces, error: 26 – Error Locating Server/Instance Specified)``

#### <a name="error-40-could-not-open-a-connection-to-the-server"></a>Erro 40: não foi possível abrir uma conexão com o servidor

``A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections. (provider: Named Pipes Provider, error: 40 - Could not open a connection to SQL Server)``

#### <a name="error-10053-a-transport-level-error-has-occurred-when-receiving-results-from-the-server"></a>Erro 10053: ocorreu um erro no nível de transporte ao receber os resultados do servidor

``10053: A transport-level error has occurred when receiving results from the server. (Provider: TCP Provider, error: 0 - An established connection was aborted by the software in your host machine)``

Esses problemas ocorrem se o aplicativo não puder se conectar ao servidor.

Para resolver esses problemas, tente as etapas (na ordem apresentada) na seção [etapas para corrigir problemas comuns de conexão](#steps-to-fix-common-connection-issues) .

## <a name="cannot-connect-to-server-due-to-firewall-issues"></a>Não é possível se conectar ao servidor devido a problemas de firewall

### <a name="error-40615-cannot-connect-to--servername-"></a>Erro 40615: não é possível conectar-se ao < ServerName >

Para resolver esse problema, [defina as configurações de firewall no banco de dados SQL por meio do portal do Azure.](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)

### <a name="error-5-cannot-connect-to--servername-"></a>Erro 5: não é possível conectar-se ao < ServerName >

Para resolver esse problema, verifique se a porta 1433 está aberta para conexões de saída em todos os firewalls entre o cliente e a Internet.

Para obter mais informações, consulte [Configurar o Firewall do Windows para permitir acesso SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).

## <a name="unable-to-log-in-to-the-server-errors-18456-40531"></a>Não é possível fazer logon no servidor (erros 18456, 40531)

### <a name="login-failed-for-user--user-name-"></a>Falha de logon do usuário ' < nome de usuário > '

``Login failed for user '<User name>'.This session has been assigned a tracing ID of '<Tracing ID>'. Provide this tracing ID to customer support when you need assistance. (Microsoft SQL Server, Error: 18456)``

Para resolver esse problema, entre em contato com o administrador do serviço para fornecer um nome de usuário e uma senha de SQL Server válidos.

Normalmente, o administrador de serviços pode usar as seguintes etapas para adicionar as credenciais de logon:

1. Faça logon no servidor usando SQL Server Management Studio (SSMS).
2. Execute a seguinte consulta SQL para verificar se o nome de logon está desabilitado:

   ```sql
   SELECT name, is_disabled FROM sys.sql_logins
   ```

3. Se o nome correspondente estiver desativado, ative-o através da instrução seguinte:

   ```sql
   Alter login <User name> enable
   ```

4. Se o nome de usuário de logon do SQL não existir, crie-o seguindo estas etapas:

   1. No SSMS, clique duas vezes em **segurança** para expandi-la.
   2. Clique com botão direito do rato em **Inícios de sessão** e, em seguida, selecione **Novo início de sessão**.
   3. No script gerado com espaços reservados, edite e execute a seguinte consulta SQL:

   ```sql
   CREATE LOGIN <SQL_login_name, sysname, login_name>
   WITH PASSWORD = ‘<password, sysname, Change_Password>’
   GO
   ```

5. Faça duplo clique em **Base de dados**.
6. Selecione o banco de dados ao qual você deseja conceder a permissão de usuário.
7. Faça duplo clique em **Segurança**.
8. Clique com botão direito do rato em **Utilizadores** e, em seguida, selecione **Novo utilizador**.
9. No script gerado com espaços reservados, edite e execute a seguinte consulta SQL:

   ```sql
   CREATE USER <user_name, sysname, user_name>
   FOR LOGIN <login_name, sysname, login_name>
   WITH DEFAULT_SCHEMA = <default_schema, sysname, dbo>
   GO
   -- Add user to the database owner role

   EXEC sp_addrolemember N’db_owner’, N’<user_name, sysname, user_name>’
   GO
   ```

   > [!NOTE]
   > Você também pode usar `sp_addrolemember` para mapear usuários específicos para funções de banco de dados específicas.

Para obter mais informações, consulte [Gerenciando bancos de dados e logons no Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins).

## <a name="connection-timeout-expired-errors"></a>Erros de tempo limite de conexão expirados

### <a name="systemdatasqlclientsqlexception-0x80131904-connection-timeout-expired"></a>System. Data. SqlClient. SqlException (0x80131904): tempo limite de conexão expirado

``System.Data.SqlClient.SqlException (0x80131904): Connection Timeout Expired. The timeout period elapsed while attempting to consume the pre-login handshake acknowledgement. This could be because the pre-login handshake failed or the server was unable to respond back in time. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=3; handshake=29995;``

### <a name="systemdatasqlclientsqlexception-0x80131904-timeout-expired"></a>System. Data. SqlClient. SqlException (0x80131904): tempo limite expirado

``System.Data.SqlClient.SqlException (0x80131904): Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding.``

### <a name="systemdataentitycoreentityexception-the-underlying-provider-failed-on-open"></a>System. Data. entidade. Core. EntityException: o provedor subjacente falhou ao abrir

``System.Data.Entity.Core.EntityException: The underlying provider failed on Open. -> System.Data.SqlClient.SqlException: Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding. -> System.ComponentModel.Win32Exception: The wait operation timed out``

### <a name="cannot-connect-to--server-name-"></a>Não é possível se conectar ao nome do servidor < >

``Cannot connect to <server name>.ADDITIONAL INFORMATION:Connection Timeout Expired. The timeout period elapsed during the post-login phase. The connection could have timed out while waiting for server to complete the login process and respond; Or it could have timed out while attempting to create multiple active connections. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=231; handshake=983; [Login] initialization=0; authentication=0; [Post-Login] complete=13000; (Microsoft SQL Server, Error: -2) For help, click: http://go.microsoft.com/fwlink?ProdName=Microsoft%20SQL%20Server&EvtSrc=MSSQLServer&EvtID=-2&LinkId=20476 The wait operation timed out``

Essas exceções podem ocorrer devido a problemas de conexão ou de consulta. Para confirmar que esse erro é causado por problemas de conectividade, consulte [confirmar se um erro é causado por um problema de conectividade](#confirm-whether-an-error-is-caused-by-a-connectivity-issue).

Os tempos limite de conexão ocorrem porque o aplicativo não pode se conectar ao servidor. Para resolver esse problema, tente as etapas (na ordem apresentada) na seção [etapas para corrigir problemas comuns de conexão](#steps-to-fix-common-connection-issues) .

## <a name="resource-governance-errors"></a>Erros de governança de recursos

### <a name="error-10928-resource-id-d"></a>Erro 10928: ID do recurso:% d

``10928: Resource ID: %d. The %s limit for the database is %d and has been reached. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. The Resource ID value in error message indicates the resource for which limit has been reached. For sessions, Resource ID = 2.``

Para contornar esse problema, tente um dos seguintes métodos:

* Verifique se há consultas de execução longa.

  > [!NOTE]
  > Essa é uma abordagem insuficiente que pode não resolver o problema.

1. Execute a seguinte consulta SQL para verificar a exibição [Sys. dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) para ver todas as solicitações de bloqueio:

   ```sql
   SELECT * FROM dm_exec_requests
   ```

2. Determine o **buffer de entrada** para o bloqueador de cabeçalho.
3. Ajuste a consulta do bloqueador de cabeçalho.

   Para obter um procedimento detalhado de solução de problemas, consulte a [minha consulta está funcionando bem na nuvem?](https://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).

Se o banco de dados atingir consistentemente seu limite, apesar de lidar com o bloqueio e consultas de longa execução, considere atualizar para uma edição com mais [edições](https://azure.microsoft.com/pricing/details/sql-database/)de recursos).

Para obter mais informações sobre exibições de gerenciamento dinâmico, consulte [exibições de gerenciamento dinâmico do sistema](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views).

Para obter mais informações sobre limites de banco de dados, consulte [limites de recursos do banco de dados SQL para o servidor do banco de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server)

### <a name="error-10929-resource-id-1"></a>Erro 10929: ID do recurso: 1

``10929: Resource ID: 1. The %s minimum guarantee is %d, maximum limit is %d and the current usage for the database is %d. However, the server is currently too busy to support requests greater than %d for this database. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. Otherwise, please try again later.``

### <a name="error-40501-the-service-is-currently-busy"></a>Erro 40501: o serviço está ocupado no momento

``40501: The service is currently busy. Retry the request after 10 seconds. Incident ID: %ls. Code: %d.``

Esse é um erro de limitação do mecanismo, uma indicação de que os limites de recursos estão sendo excedidos.

Para obter mais informações sobre limites de recursos, consulte [limites de recursos do servidor de banco de dados](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server).

### <a name="error-40544-the-database-has-reached-its-size-quota"></a>Erro 40544: o banco de dados atingiu sua cota de tamanho

``40544: The database has reached its size quota. Partition or delete data, drop indexes, or consult the documentation for possible resolutions. Incident ID: <ID>. Code: <code>.``

Esse erro ocorre quando o banco de dados atingiu sua cota de tamanho.

As etapas a seguir podem ajudá-lo a solucionar o problema ou fornecer opções adicionais:

1. Verifique o tamanho atual do banco de dados usando o painel no portal do Azure.

   > [!NOTE]
   > Para identificar quais tabelas estão consumindo mais espaço e, portanto, candidatos potenciais para limpeza, execute a seguinte consulta SQL:

   ```sql
   SELECT o.name,
    a.SUM(p.row_count) AS 'Row Count',
    b.SUM(p.reserved_page_count) * 8.0 / 1024 AS 'Table Size (MB)'
   FROM sys.objects o
   JOIN sys.dm_db_partition_stats p on p.object_id = o.object_id
   GROUP BY o.name
   ORDER BY [Table Size (MB)] DESC
   ```

2. Se o tamanho atual não exceder o tamanho máximo com suporte para sua edição, você poderá usar ALTER DATABASE para aumentar a configuração MAXSIZE.
3. Se o banco de dados já estiver além do tamanho máximo com suporte para sua edição, tente uma ou mais das seguintes etapas:

   * Executar atividades normais de limpeza do banco de dados. Por exemplo, limpe os dados indesejados usando truncar/excluir ou mover dados para fora usando SQL Server Integration Services (SSIS) ou o utilitário bcp (programa de cópia em massa).
   * Particione ou exclua dados, remova índices ou consulte a documentação para obter possíveis resoluções.
   * Para o dimensionamento de banco de dados, consulte [dimensionar recursos de banco de dados individual](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-scale) e [dimensionar recursos de pool elástico](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool-scale).

### <a name="error-40549-session-is-terminated-because-you-have-a-long-running-transaction"></a>Erro 40549: a sessão foi encerrada porque você tem uma transação de execução longa

``40549: Session is terminated because you have a long-running transaction. Try shortening your transaction.``

Se você encontrar esse erro repetidamente, tente resolver o problema seguindo estas etapas:

1. Verifique a exibição sys. dm_exec_requests para ver as sessões abertas que têm um valor alto para a coluna total_elapsed_time. Execute esta verificação executando o seguinte script SQL:

   ```sql
   SELECT * FROM dm_exec_requests
   ```

2. Determine o buffer de entrada para a consulta de execução longa.
3. Ajuste a consulta.

Considere também o envio em lote de suas consultas. Para obter informações sobre o envio em lote, consulte [como usar o envio em lote para melhorar o desempenho do aplicativo do banco de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-use-batching-to-improve-performance).

Para obter um procedimento detalhado de solução de problemas, consulte a [minha consulta está funcionando bem na nuvem?](https://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).

### <a name="error-40551-the-session-has-been-terminated-because-of-excessive-tempdb-usage"></a>Erro 40551: a sessão foi encerrada devido ao uso excessivo de TEMPDB

``40551: The session has been terminated because of excessive TEMPDB usage. Try modifying your query to reduce the temporary table space usage.``

Para contornar esse problema, siga estas etapas:

1. Altere as consultas para reduzir o uso de espaço de tabela temporário.
2. Descartar objetos temporários depois que eles não forem mais necessários.
3. Truncar tabelas ou remover tabelas não usadas.

### <a name="error-40552-the-session-has-been-terminated-because-of-excessive-transaction-log-space-usage"></a>Erro 40552: a sessão foi encerrada devido ao uso excessivo de espaço do log de transações

``40552: The session has been terminated because of excessive transaction log space usage. Try modifying fewer rows in a single transaction.``

Para resolver este problema, experimente os métodos seguintes:

* O problema pode ocorrer devido a operações de inserção, atualização ou exclusão.
Tente reduzir o número de linhas que são operadas imediatamente implementando em lote ou dividindo em várias transações menores.
* O problema pode ocorrer devido a operações de recompilação de índice. Para solucionar esse problema, verifique se o número de linhas afetadas na tabela * (tamanho médio do campo atualizado em bytes + 80) < 2 gigabytes (GB).

  > [!NOTE]
  > Para uma recompilação de índice, o tamanho médio do campo que é atualizado deve ser substituído pelo tamanho médio do índice.

### <a name="error-40553-the-session-has-been-terminated-because-of-excessive-memory-usage"></a>Erro 40553: a sessão foi encerrada devido ao uso excessivo de memória

``40553 : The session has been terminated because of excessive memory usage. Try modifying your query to process fewer rows.``

Para contornar esse problema, tente otimizar a consulta.

Para obter um procedimento detalhado de solução de problemas, consulte a [minha consulta está funcionando bem na nuvem?](https://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).

### <a name="table-of-additional-resource-governance-error-messages"></a>Tabela de mensagens de erro adicionais de governança de recursos

| Código de erro | Gravidade | Descrição |
| ---:| ---:|:--- |
| 10928 |20 |ID do recurso:% d. O limite de% s para o banco de dados é% d e foi atingido. Para obter mais informações, consulte [limites de recursos do banco de dados SQL para bancos de dados individuais e em pool](sql-database-resource-limits-database-server.md).<br/><br/>A ID do recurso indica o recurso que atingiu o limite. Para threads de trabalho, a ID de recurso = 1. Para sessões, a ID do recurso = 2.<br/><br/>Para obter mais informações sobre esse erro e como resolvê-lo, consulte: <br/>limites de [recursos de servidor de banco de dados](sql-database-resource-limits-database-server.md) &bull; &nbsp;<br/>&bull; &nbsp;[limites com base em DTU para bancos de dados individuais](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[limites com base em DTU para pools elásticos](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[limites baseados em vCore para bancos de dados individuais](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[limites baseados em vCore para pools elásticos](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[limites de recursos de instância gerenciada](sql-database-managed-instance-resource-limits.md). |
| 10929 |20 |ID do recurso:% d. A garantia mínima de% s é% d, o limite máximo é% d, e o uso atual do banco de dados é% d. No entanto, o servidor está muito ocupado no momento para dar suporte a solicitações maiores que% d para este banco de dados. A ID do recurso indica o recurso que atingiu o limite. Para threads de trabalho, a ID de recurso = 1. Para sessões, a ID do recurso = 2. Para obter mais informações, veja: <br/>limites de [recursos de servidor de banco de dados](sql-database-resource-limits-database-server.md) &bull; &nbsp;<br/>&bull; &nbsp;[limites com base em DTU para bancos de dados individuais](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[limites com base em DTU para pools elásticos](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[limites baseados em vCore para bancos de dados individuais](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[limites baseados em vCore para pools elásticos](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[limites de recursos de instância gerenciada](sql-database-managed-instance-resource-limits.md). <br/>Caso contrário, tente novamente mais tarde. |
| 40544 |20 |O banco de dados atingiu sua cota de tamanho. Particione ou exclua dados, remova índices ou consulte a documentação para obter possíveis resoluções. Para o dimensionamento de banco de dados, consulte [dimensionar recursos de banco de dados individual](sql-database-single-database-scale.md) e [dimensionar recursos de pool elástico](sql-database-elastic-pool-scale.md).|
| 40549 |16 |A sessão foi encerrada porque você tem uma transação de longa execução. Tente encurtar sua transação. Para obter informações sobre o envio em lote, consulte [como usar o envio em lote para melhorar o desempenho do aplicativo do banco de dados SQL](sql-database-use-batching-to-improve-performance.md).|
| 40550 |16 |A sessão foi encerrada porque adquiriu muitos bloqueios. Tente ler ou modificar menos linhas em uma única transação. Para obter informações sobre o envio em lote, consulte [como usar o envio em lote para melhorar o desempenho do aplicativo do banco de dados SQL](sql-database-use-batching-to-improve-performance.md).|
| 40551 |16 |A sessão foi encerrada devido ao excesso de uso de `TEMPDB`. Tente modificar sua consulta para reduzir o uso de espaço de tabela temporário.<br/><br/>Se você estiver usando objetos temporários, conservar espaço no banco de dados `TEMPDB` removendo objetos temporários depois que eles não forem mais necessários para a sessão. Para obter mais informações sobre o uso de tempdb no banco de dados SQL, consulte [banco de dados tempdb no banco de dados SQL](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).|
| 40552 |16 |A sessão foi encerrada devido ao uso excessivo de espaço no log de transações. Tente modificar menos linhas em uma única transação. Para obter informações sobre o envio em lote, consulte [como usar o envio em lote para melhorar o desempenho do aplicativo do banco de dados SQL](sql-database-use-batching-to-improve-performance.md).<br/><br/>Se você executar inserções em massa usando o utilitário `bcp.exe` ou a classe `System.Data.SqlClient.SqlBulkCopy`, tente usar as opções `-b batchsize` ou `BatchSize` para limitar o número de linhas copiadas para o servidor em cada transação. Se você estiver recriando um índice com a instrução `ALTER INDEX`, tente usar a opção `REBUILD WITH ONLINE = ON`. Para obter informações sobre tamanhos de log de transações para o modelo de compra vCore, consulte: <br/>&bull; &nbsp;[limites baseados em vCore para bancos de dados individuais](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[limites baseados em vCore para pools elásticos](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[limites de recursos de instância gerenciada](sql-database-managed-instance-resource-limits.md).|
| 40553 |16 |A sessão foi encerrada devido ao uso excessivo de memória. Tente modificar a consulta para processar menos linhas.<br/><br/>A redução do número de operações de `ORDER BY` e `GROUP BY` em seu código Transact-SQL reduz os requisitos de memória de sua consulta. Para o dimensionamento de banco de dados, consulte [dimensionar recursos de banco de dados individual](sql-database-single-database-scale.md) e [dimensionar recursos de pool elástico](sql-database-elastic-pool-scale.md).|

## <a name="elastic-pool-errors"></a>Erros de pool elástico

Os seguintes erros estão relacionados à criação e ao uso de pools elásticos:

| Código de erro | Gravidade | Descrição | Ação corretiva |
|:--- |:--- |:--- |:--- |
| 1132 | 17 |O pool elástico atingiu seu limite de armazenamento. O uso de armazenamento para o pool elástico não pode exceder (% d) MBs. Tentativa de gravar dados em um banco de dado quando o limite de armazenamento do pool elástico foi atingido. Para obter informações sobre limites de recursos, consulte: <br/>&bull; &nbsp;[limites com base em DTU para pools elásticos](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[limites baseados em vCore para pools elásticos](sql-database-vcore-resource-limits-elastic-pools.md). <br/> |Considere aumentar as DTUs de e/ou adicionar armazenamento ao pool elástico, se possível, para aumentar seu limite de armazenamento, reduzir o armazenamento usado por bancos de dados individuais dentro do pool elástico ou Remover bancos de dados do pool elástico. Para o dimensionamento do pool elástico, consulte [dimensionar recursos do pool elástico](sql-database-elastic-pool-scale.md).|
| 10929 | 16 |A garantia mínima de% s é% d, o limite máximo é% d, e o uso atual do banco de dados é% d. No entanto, o servidor está muito ocupado no momento para dar suporte a solicitações maiores que% d para este banco de dados. Para obter informações sobre limites de recursos, consulte: <br/>&bull; &nbsp;[limites com base em DTU para pools elásticos](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[limites baseados em vCore para pools elásticos](sql-database-vcore-resource-limits-elastic-pools.md). <br/> Caso contrário, tente novamente mais tarde. DTU/mínimo de vCore por banco de dados; DTU/vCore máximo por banco de dados. O número total de trabalhos simultâneos (solicitações) em todos os bancos de dados no pool elástico tentou exceder o limite do pool. |Considere aumentar as DTUs ou vCores do pool elástico, se possível, para aumentar seu limite de trabalho ou Remover bancos de dados do pool elástico. |
| 40844 | 16 |O banco de dados '% ls ' no servidor '% ls ' é um banco de dados de edição '% ls ' em um pool elástico e não pode ter uma relação de cópia contínua.  |N/A |
| 40857 | 16 |Pool elástico não encontrado para o servidor: '% ls ', nome do pool elástico: '% ls '. O pool elástico especificado não existe no servidor especificado. | Forneça um nome de pool elástico válido. |
| 40858 | 16 |O pool elástico '% ls ' já existe no servidor: '% ls '. O pool elástico especificado já existe no servidor de banco de dados SQL especificado. | Forneça o novo nome do pool elástico. |
| 40859 | 16 |O pool elástico não dá suporte à camada de serviço '% ls '. A camada de serviço especificada não tem suporte para provisionamento de pool elástico. |Forneça a edição correta ou deixe a camada de serviço em branco para usar a camada de serviço padrão. |
| 40860 | 16 |A combinação do pool elástico '% ls ' e do objetivo de serviço '% ls ' é inválida. O pool elástico e a camada de serviço podem ser especificados juntos somente se o tipo de recurso for especificado como ' ElasticPool '. |Especifique a combinação correta de pool elástico e camada de serviço. |
| 40861 | 16 |A edição do banco de dados '%. *ls ' não pode ser diferente da camada de serviço do pool elástico, que é '%.* ls '. A edição do banco de dados é diferente da camada de serviço do pool elástico. |Não especifique uma edição de banco de dados que seja diferente da camada de serviço do pool elástico.  Observe que a edição do banco de dados não precisa ser especificada. |
| 40862 | 16 |O nome do pool elástico deve ser especificado se o objetivo do serviço do pool elástico for especificado. O objetivo do serviço de pool elástico não identifica exclusivamente um pool elástico. |Especifique o nome do pool elástico se estiver usando o objetivo de serviço do pool elástico. |
| 40864 | 16 |As DTUs para o pool elástico devem ser de pelo menos (% d) DTUs para a camada de serviço '%. * ls '. Tentando definir as DTUs para o pool elástico abaixo do limite mínimo. |Repita a configuração das DTUs para o pool elástico para pelo menos o limite mínimo. |
| 40865 | 16 |As DTUs para o pool elástico não podem exceder (% d) DTUs para a camada de serviço '%. * ls '. Tentando definir DTUs para o pool elástico acima do limite máximo. |Repita a configuração das DTUs para o pool elástico para não maior que o limite máximo. |
| 40867 | 16 |O máximo de DTU por banco de dados deve ser pelo menos (% d) para a camada de serviço '%. * ls '. Tentando definir o máximo de DTU por banco de dados abaixo do limite com suporte. | Considere usar a camada de serviço do pool elástico que dá suporte à configuração desejada. |
| 40868 | 16 |O máximo de DTU por banco de dados não pode exceder (% d) para a camada de serviço '%. * ls '. Tentando definir o máximo de DTU por banco de dados além do limite com suporte. | Considere usar a camada de serviço do pool elástico que dá suporte à configuração desejada. |
| 40870 | 16 |O mínimo de DTU por banco de dados não pode exceder (% d) para a camada de serviço '%. * ls '. Tentando definir o mínimo de DTU por banco de dados além do limite com suporte. | Considere usar a camada de serviço do pool elástico que dá suporte à configuração desejada. |
| 40873 | 16 |O número de bancos de dados (% d) e o mínimo de DTU por banco (% d) não podem exceder as DTUs do pool elástico (% d). Tentativa de especificar o mínimo de DTU para bancos de dados no pool elástico que excede as DTUs do pool elástico. | Considere aumentar as DTUs do pool elástico, ou diminuir o mínimo de DTU por banco de dados, ou diminuir o número de banco de dados no pool elástico. |
| 40877 | 16 |Um pool elástico não pode ser excluído, a menos que não contenha nenhum banco de dados. O pool elástico contém um ou mais bancos de dados e, portanto, não pode ser excluído. |Remova os bancos de dados do pool elástico para excluí-los. |
| 40881 | 16 |O pool elástico '%. * ls ' atingiu seu limite de contagem de banco de dados.  O limite de contagem de banco de dados para o pool elástico não pode exceder (% d) para um pool elástico com DTUs (% d). Tentativa de criar ou adicionar banco de dados ao pool elástico quando o limite de contagem de banco de dados do pool elástico foi atingido. | Considere aumentar as DTUs do pool elástico, se possível, para aumentar seu limite de banco de dados ou Remover bancos de dados do pool elástico. |
| 40889 | 16 |O limite de DTUs ou de armazenamento para o pool elástico '%. * ls ' não pode ser diminuído, pois isso não forneceria espaço de armazenamento suficiente para seus bancos de dados. Tentando diminuir o limite de armazenamento do pool elástico abaixo de seu uso de armazenamento. | Considere reduzir o uso de armazenamento de bancos de dados individuais no pool elástico ou Remover bancos de dados do pool para reduzir seu limite de DTUs ou de armazenamento. |
| 40891 | 16 |O mínimo de DTU por banco de dados (% d) não pode exceder o máximo de DTU por banco de dados (% d). Tentativa de definir o mínimo de DTU por banco de dados acima do máximo de DTU por banco de dados. |Certifique-se de que o mínimo de DTU por bancos de dados não exceda o máximo de DTU por Database. |
| TBD | 16 |O tamanho do armazenamento de um banco de dados individual em um pool elástico não pode exceder o tamanho máximo permitido pelo pool elástico da camada de serviço '%. * ls '. O tamanho máximo do banco de dados excede o tamanho máximo permitido pela camada de serviço do pool elástico. |Defina o tamanho máximo do banco de dados dentro dos limites do tamanho máximo permitido pela camada de serviço do pool elástico. |

## <a name="cannot-open-database-master-requested-by-the-login-the-login-failed"></a>Não é possível abrir o banco de dados "mestre" solicitado pelo logon. Falha no logon

Esse problema ocorre porque a conta não tem permissão para acessar o banco de dados mestre. Mas, por padrão, o SQL Server Management Studio (SSMS) tenta se conectar ao banco de dados mestre.

Para resolver este problema, siga estes passos:

1. Na tela de logon do SSMS, selecione **Opções**e, em seguida, selecione **Propriedades da conexão**.
2. No campo **conectar ao banco de dados** , insira o nome do banco de dados padrão do usuário como o banco de dados de logon padrão e, em seguida, selecione **conectar**.

   ![Propriedades de ligação](media/troubleshoot-connectivity-issues-microsoft-azure-sql-database/cannot-open-database-master.png)

## <a name="confirm-whether-an-error-is-caused-by-a-connectivity-issue"></a>Confirmar se um erro é causado por um problema de conectividade

Para confirmar se um erro é causado por um problema de conectividade, examine o rastreamento de pilha para quadros que mostram chamadas para abrir uma conexão como as seguintes (Observe a referência à classe **SqlConnection** ):

```
System.Data.SqlClient.SqlConnection.TryOpen(TaskCompletionSource`1 retry)
 at System.Data.SqlClient.SqlConnection.Open()
 at AzureConnectionTest.Program.Main(String[] args)
ClientConnectionId:<Client connection ID>
```

Quando a exceção for disparada por problemas de consulta, você notará uma pilha de chamadas semelhante à seguinte (Observe a referência à classe **SqlCommand** ). Nessa situação, [ajuste suas consultas](https://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).

```
  at System.Data.SqlClient.SqlCommand.ExecuteReader()
  at AzureConnectionTest.Program.Main(String[] args)
  ClientConnectionId:<Client ID>
```

Para obter diretrizes adicionais sobre o desempenho de ajuste fino, consulte os seguintes recursos:

* [Como manter índices e estatísticas do SQL Azure](https://techcommunity.microsoft.com/t5/Azure-Database-Support-Blog/How-to-maintain-Azure-SQL-Indexes-and-Statistics/ba-p/368787)
* [Ajuste manual de desempenho de consulta no banco de dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-performance-guidance)
* [Monitorando o desempenho do banco de dados SQL do Azure usando exibições de gerenciamento dinâmico](https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-with-dmvs)
* [Operando o Repositório de Consultas no banco de dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-operate-query-store)

## <a name="steps-to-fix-common-connection-issues"></a>Etapas para corrigir problemas comuns de conexão

1. Verifique se o TCP/IP está habilitado como um protocolo de cliente no servidor de aplicativos. Para obter mais informações, consulte [Configurar protocolos de cliente](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-client-protocols). Em servidores de aplicativos em que você não tem as ferramentas de SQL Server instaladas, verifique se o TCP/IP está habilitado executando **cliconfg. exe** (SQL Server Client Network Utility).
2. Verifique a cadeia de conexão do aplicativo para certificar-se de que ela está configurada corretamente. Por exemplo, verifique se a cadeia de conexão especifica a porta correta (1433) e o nome do servidor totalmente qualificado.
Consulte [obter SQL Server informações de conexão](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms#get-sql-server-connection-information).
3. Tente aumentar o valor de tempo limite da conexão. É recomendável usar um tempo limite de conexão de, pelo menos, 30 segundos.
4. Teste a conectividade entre o servidor de aplicativos e o banco de dados SQL do Azure usando o [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms), um arquivo UDL, ping ou telnet. Para obter mais informações, consulte [Solucionando problemas de conectividade SQL Server](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server) e [diagnóstico para problemas de conectividade](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues#diagnostics).

   > [!NOTE]
   > Como uma etapa de solução de problemas, você também pode testar a conectividade em um computador cliente diferente.

5. Como prática recomendada, verifique se a lógica de repetição está em vigor. Para obter mais informações sobre a lógica de repetição, consulte [solucionar falhas transitórias e erros de conexão no banco de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues).

Se essas etapas não resolverem o problema, tente coletar mais dados e entre em contato com o suporte. Se seu aplicativo for um serviço de nuvem, habilite o registro em log. Esta etapa retorna um carimbo de data/hora UTC da falha. Além disso, SQL Azure retorna a ID de rastreamento. Os serviços de atendimento [ao cliente da Microsoft](https://azure.microsoft.com/support/options/) podem usar essas informações.

Para obter mais informações sobre como habilitar o registro em log, consulte [habilitar o log de diagnóstico para aplicativos no serviço Azure app](https://azure.microsoft.com/documentation/articles/web-sites-enable-diagnostic-log/).

## <a name="next-steps"></a>Passos seguintes

* [Arquitetura de conectividade do SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture)
* [Controles de acesso à rede do banco de dados SQL do Azure e do data warehouse](https://docs.microsoft.com/azure/sql-database/sql-database-networkaccess-overview)
