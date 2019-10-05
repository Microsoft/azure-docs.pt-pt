---
title: Solucionando problemas de conectividade com o Banco de Dados SQL do Microsoft Azure | Microsoft Docs
description: Solucionando problemas de conectividade com o Banco de Dados SQL do Microsoft Azure
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
author: v-miegge
ms.author: ramakoni
ms.reviewer: ''
ms.date: 09/27/2019
ms.openlocfilehash: 3717b36e37f7d9cca206b5a9e8e3cded50393748
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2019
ms.locfileid: "71974425"
---
# <a name="troubleshooting-connectivity-issues-with-microsoft-azure-sql-database"></a>Solucionando problemas de conectividade com o Banco de Dados SQL do Microsoft Azure

Recebe mensagens de erro quando a ligação à base de dados SQL do Azure falha. Os problemas de ligação podem ser provocados pela reconfiguração da base de dados SQL do Azure, por definições de firewall, por tempo limite das ligações excedido ou por informações de início de sessão incorretas. Além disso, se o limite máximo de alguns recursos do banco de dados SQL do Azure for atingido, você não poderá se conectar ao banco de dados SQL do Azure.

## <a name="error-40613-database--x--on-server--y--is-not-currently-available"></a>Erro 40613: O banco de dados < x > no servidor < y > não está disponível no momento

**Erro Detalhado**

``40613: Database <DBname> on server <server name> is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of '<Tracing ID>'.``

Para resolver este problema:

1. Verifique o [painel de serviço do Microsoft Azure](https://status.azure.com/status) em busca de interrupções conhecidas. 
2. Se não houver interrupções conhecidas, vá para o [site de suporte da Microsoft Azure](http://azure.microsoft.com/support/options) para abrir um caso de suporte.

Para obter mais informações, consulte [solucionar problemas do erro "o banco de dados no servidor não está disponível no momento"](https://docs.microsoft.com/azure/sql-database/sql-database-troubleshoot-common-connection-issues#troubleshoot-transient-errors).

## <a name="a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server"></a>Ocorreu um erro relacionado à rede ou específico à instância ao estabelecer uma conexão com SQL Server

O problema ocorre porque o aplicativo não é capaz de se conectar ao servidor.

Para resolver esse problema, tente as etapas (em ordem) na seção abaixo, intituladas **etapas para corrigir problemas comuns de conexão**.

## <a name="the-server-was-not-found-or-was-not-accessible-errors-26-40-10053"></a>O servidor não foi encontrado ou não estava acessível (erros 26, 40, 10053)

### <a name="error-26-error-locating-serverinstance-specified"></a>Erro 26: Erro ao localizar o servidor/instância especificado

**Erro Detalhado**

``System.Data.SqlClient.SqlException: A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections.(provider: SQL Network Interfaces, error: 26 – Error Locating Server/Instance Specified)``

### <a name="error-40-could-not-open-a-connection-to-sql-server"></a>Erro 40: Não foi possível abrir uma conexão com o SQL Server

**Erro Detalhado**

``A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections. (provider: Named Pipes Provider, error: 40 - Could not open a connection to SQL Server)``

### <a name="error-10053-a-transport-level-error-has-occurred-when-receiving-results-from-the-server"></a>Erro 10053: Ocorreu um erro no nível de transporte ao receber os resultados do servidor.

**Erro Detalhado**

``10053: A transport-level error has occurred when receiving results from the server. (Provider: TCP Provider, error: 0 - An established connection was aborted by the software in your host machine)``

Esses problemas ocorrem porque o aplicativo não é capaz de se conectar ao servidor.

Para resolver esse problema, tente as etapas (em ordem) na seção abaixo, intituladas **etapas para corrigir problemas comuns de conexão**.

## <a name="cannot-connect-to--servername--due-to-firewall-issues"></a>Não é possível se conectar a < ServerName > devido a problemas de firewall

### <a name="error-40615-cannot-connect-to--servername-"></a>Erro 40615: Não é possível conectar-se ao < ServerName >

Para resolver esse problema, [defina as configurações de firewall no banco de dados SQL usando o portal do Azure.](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)

### <a name="error-5-cannot-connect-to--servername-"></a>Erro 5: Não é possível conectar-se ao < ServerName >

Para resolver esse problema, verifique se a porta 1433 está aberta para conexões de saída em todos os firewalls entre o cliente e a Internet. 

Para obter mais informações, consulte [Configurar o Firewall do Windows para permitir acesso SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).

## <a name="unable-to-login-to-the-server-errors-18456-40531"></a>Não é possível fazer logon no servidor (erros 18456, 40531)

### <a name="login-failed-for-user--user-name-"></a>Falha de logon do usuário ' < nome de usuário > '

**Erro Detalhado**

``Login failed for user '<User name>'.This session has been assigned a tracing ID of '<Tracing ID>'. Provide this tracing ID to customer support when you need assistance. (Microsoft SQL Server, Error: 18456)``

Para resolver esse problema, entre em contato com o administrador de serviços para fornecer um nome de usuário e uma senha válidos do SQL.

Regra geral, o administrador do serviço pode utilizar os passos abaixo para adicionar o início de sessão:

1. Faça logon no servidor usando SQL Server Management Studio (SSMS).
2. Verificar se o nome de início de sessão está desativado, com a consulta SQL seguinte:

   ```
   SELECT name, is_disabled FROM sys.sql_logins
   ```

3. Se o nome correspondente estiver desativado, ative-o através da instrução seguinte: 

   ```
   Alter login <User name> enable
   ```

4. Se o nome de usuário de logon do SQL não existir, crie-o usando o SSMS:

   1. Faça duplo clique em **Segurança**, para expandi-la. 
   2. Clique com botão direito do rato em **Inícios de sessão** e, em seguida, selecione **Novo início de sessão**. 
   3. No script gerado com marcadores de posição, pode editar e executar a consulta SQL abaixo:
 
   ```
   CREATE LOGIN <SQL_login_name, sysname, login_name>
   WITH PASSWORD = ‘<password, sysname, Change_Password>’
   GO
   ```       
5. Faça duplo clique em **Base de dados**. 
6. Selecione a base de dados para a qual pretende conceder a permissão ao utilizador.
7. Faça duplo clique em **Segurança**. 
8. Clique com botão direito do rato em **Utilizadores** e, em seguida, selecione **Novo utilizador**. 
9. No script gerado com marcadores de posição, pode editar e executar a consulta SQL abaixo: 

   ```
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

Para obter mais informações, veja [Gerir Bases de Dados e Inícios de Sessão na Base de Dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins).

## <a name="connection-timeout-expired-errors"></a>Erros de tempo limite de conexão expirados

### <a name="systemdatasqlclientsqlexception-0x80131904-connection-timeout-expired"></a>System. Data. SqlClient. SqlException (0x80131904): Tempo limite de conexão expirado.

**Erro Detalhado**

``System.Data.SqlClient.SqlException (0x80131904): Connection Timeout Expired. The timeout period elapsed while attempting to consume the pre-login handshake acknowledgement. This could be because the pre-login handshake failed or the server was unable to respond back in time. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=3; handshake=29995;``

### <a name="systemdatasqlclientsqlexception-0x80131904-timeout-expired"></a>System. Data. SqlClient. SqlException (0x80131904): Tempo limite expirado.

**Erro Detalhado**

``System.Data.SqlClient.SqlException (0x80131904): Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding.``

### <a name="systemdataentitycoreentityexception-the-underlying-provider-failed-on-open"></a>System. Data. entidade. Core. EntityException: O provedor subjacente falhou ao abrir.

**Erro Detalhado**

``System.Data.Entity.Core.EntityException: The underlying provider failed on Open. -> System.Data.SqlClient.SqlException: Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding. -> System.ComponentModel.Win32Exception: The wait operation timed out``

### <a name="cannot-connect-to--server-name-"></a>Não é possível se conectar ao nome do servidor < >. ' '

**Erro Detalhado**

``Cannot connect to <server name>.ADDITIONAL INFORMATION:Connection Timeout Expired. The timeout period elapsed during the post-login phase. The connection could have timed out while waiting for server to complete the login process and respond; Or it could have timed out while attempting to create multiple active connections. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=231; handshake=983; [Login] initialization=0; authentication=0; [Post-Login] complete=13000; (Microsoft SQL Server, Error: -2) For help, click: http://go.microsoft.com/fwlink?ProdName=Microsoft%20SQL%20Server&EvtSrc=MSSQLServer&EvtID=-2&LinkId=20476 The wait operation timed out``

Essas exceções podem ocorrer devido a problemas de conexão ou de consulta. Para confirmar se esse erro ocorre devido a problemas de conectividade, verifique a seção abaixo intitulada **confirmar se o erro se deve a um problema de conectividade**:

Os tempos limite de conexão ocorrem porque o aplicativo não é capaz de se conectar ao servidor. Para resolver esse problema, tente as etapas (em ordem) na seção abaixo, intituladas **etapas para corrigir problemas comuns de conexão**.

## <a name="transient-errors-errors-40197-40545"></a>Erros transitórios (erros 40197, 40545)

### <a name="error-40197-the-service-has-encountered-an-error-processing-your-request-please-try-again-error-code--code-"></a>Erro 40197: O serviço encontrou um erro ao processar sua solicitação. Tente novamente. Código de erro < código >

Esse problema ocorre devido a um erro transitório encontrado durante uma reconfiguração/failover no back-end.

Para resolver esse problema, aguarde um curto período e tente novamente. Nenhum caso de suporte é necessário a menos que o problema permaneça persistente.

Como prática recomendada, verifique se a lógica de repetição está em vigor. Para obter mais informações sobre a lógica de repetição, consulte [solucionar falhas transitórias e erros de conexão no banco de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues).

## <a name="connection-terminated-due-to-hitting-some-system-defined-limit"></a>A conexão foi encerrada devido a um limite definido pelo sistema

### <a name="error-10928-resource-id-d"></a>Erro 10928: ID do recurso:% d.

**Erro Detalhado**

``10928: Resource ID: %d. The %s limit for the database is %d and has been reached. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. The Resource ID value in error message indicates the resource for which limit has been reached. For sessions, Resource ID = 2.``

Para contornar esse problema, tente um dos seguintes métodos:

* Verifique se há consultas de execução longa:

  > [!NOTE]
  > Essa é uma abordagem insignificante que pode não resolver necessariamente o problema.

  1. Verifique a exibição [Sys. dm _exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) para ver as solicitações de bloqueio, executando a seguinte consulta SQL:

     ```
     SELECT * FROM dm_exec_requests
     ```

  2. Determine o **buffer de entrada** para o bloqueador de cabeçalho.
  3. Ajuste a consulta do bloqueador de cabeçalho.

    Para obter um procedimento detalhado de solução de problemas, consulte a [minha consulta está funcionando bem na nuvem?](http://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).

* Se o banco de dados atingir consistentemente seu limite, apesar de lidar com o bloqueio e consultas de longa execução, considere atualizar para uma das novas edições de visualização (como as [edições Standard ou Premium](https://azure.microsoft.com/pricing/details/sql-database/)).

Para obter mais informações sobre as opções de preços do banco de dados SQL, consulte [preços do banco de dados SQL](https://azure.microsoft.com/pricing/details/sql-database/single/)

Para obter mais informações sobre exibições de gerenciamento dinâmico, consulte [exibições de gerenciamento dinâmico do sistema](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views).

Para obter mais informações sobre essa mensagem de erro, consulte [limites de recursos do banco de dados SQL para o servidor de banco de dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server).

### <a name="error-10929-resource-id-1"></a>Erro 10929: ID do Recurso: 1.

**Erro Detalhado**

``10929: Resource ID: 1. The %s minimum guarantee is %d, maximum limit is %d and the current usage for the database is %d. However, the server is currently too busy to support requests greater than %d for this database. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. Otherwise, please try again later.``

Para obter mais informações sobre esse erro, consulte [mensagens de erro para programas cliente do banco de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-develop-error-messages)

### <a name="error-40501-the-service-is-currently-busy"></a>Erro 40501: O serviço está ocupado no momento

**Erro Detalhado**

``40501: The service is currently busy. Retry the request after 10 seconds. Incident ID: %ls. Code: %d.``

Esse é um erro de limitação do mecanismo, uma indicação de que os limites de recursos estão sendo excedidos.

Para obter mais informações sobre limites de recursos, consulte [limites de recursos do servidor de banco de dados](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server).

### <a name="error-40544--the-database-has-reached-its-size-quota"></a>Erro 40544: O banco de dados atingiu sua cota de tamanho

**Erro Detalhado**

``40544: The database has reached its size quota. Partition or delete data, drop indexes, or consult the documentation for possible resolutions. Incident ID: <ID>. Code: <code>.``

Esse erro ocorre quando o banco de dados atingiu sua cota de tamanho.

As etapas a seguir podem ajudá-lo a trabalhar em volta do problema ou fornecer opções adicionais que você pode considerar.

1. Verifique o tamanho atual do banco de dados usando o painel no portal do Azure.

   > [!NOTE]
   > Para identificar quais tabelas estão consumindo mais espaço e candidatos potenciais para limpeza, você pode usar a seguinte consulta SQL:

   ```
   SELECT o.name,
    a.SUM(p.row_count) AS 'Row Count',
    b.SUM(p.reserved_page_count) * 8.0 / 1024 AS 'Table Size (MB)'
   FROM sys.objects o
   JOIN sys.dm_db_partition_stats p on p.object_id = o.object_id
   GROUP BY o.name
   ORDER BY [Table Size (MB)] DESC
   ```

2. Se o tamanho atual não exceder o tamanho máximo com suporte para sua edição, você poderá usar ALTER DATABASE para aumentar a configuração MAXSIZE. 
3. Se o tamanho do banco de dados já tiver passado do tamanho máximo com suporte para sua edição, você poderá executar uma das seguintes etapas:
   1. Executar atividades normais de limpeza do banco de dados (limpando-os por meio de truncar/excluir etc. ou mover dados usando SSIS, bcp, etc.)
   2. Particione ou exclua dados, remova índices ou consulte a documentação para obter possíveis resoluções. 
   
   *  Para o dimensionamento de banco de dados, consulte [dimensionar recursos de banco de dados individual](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-scale) e [dimensionar recursos de pool elástico](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool-scale).

### <a name="error-40549-session-is-terminated-because-you-have-a-long-running-transaction"></a>Erro 40549: A sessão foi encerrada porque você tem uma transação de longa execução.

**Erro Detalhado**

``40549: Session is terminated because you have a long-running transaction. Try shortening your transaction.``

Se você encontrar repetidamente essa mensagem de erro, tente estas etapas para resolver esse problema: 

1. Verifique a exibição sys. dm _exec_requests para ver as sessões abertas que têm um valor alto para a coluna total_elapsed_time, executando o seguinte script SQL:

   ```
   SELECT * FROM dm_exec_requests
   ```
2. Determine o buffer de entrada para a consulta que está de longa execução. 
3. Ajuste a consulta.

Considere também o envio em lote de suas consultas. Para obter informações sobre o envio em lote, consulte [como usar o envio em lote para melhorar o desempenho do aplicativo do banco de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-use-batching-to-improve-performance).

Para obter um procedimento detalhado de solução de problemas, consulte a [minha consulta está funcionando bem na nuvem?](http://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).

### <a name="error-40551-the-session-has-been-terminated-because-of-excessive-tempdb-usage"></a>Erro 40551: A sessão foi encerrada devido ao uso excessivo de TEMPDB.

**Erro Detalhado**

``40551: The session has been terminated because of excessive TEMPDB usage. Try modifying your query to reduce the temporary table space usage.``

Para contornar esse problema, siga estas etapas:

1. Altere as consultas para reduzir o uso do espaço de tabela temporário. 
2. Descartar objetos temporários depois que eles não forem mais necessários. 
3. Truncar tabelas ou remover tabelas não usadas.

### <a name="error-40552-the-session-has-been-terminated-because-of-excessive-transaction-log-space-usage"></a>Erro 40552: A sessão foi encerrada devido ao uso excessivo de espaço no log de transações.

**Erro Detalhado**

``40552: The session has been terminated because of excessive transaction log space usage. Try modifying fewer rows in a single transaction.``

Para resolver esse problema, siga estes métodos: 

* O problema ocorre devido a operações de inserção, atualização ou exclusão. Tente reduzir o número de linhas que são operadas imediatamente implementando em lote ou dividindo em várias transações menores.
* O problema ocorre devido às operações de recompilação de índice. Certifique-se de seguir a seguinte fórmula: número de linhas que são afetadas na tabela * (tamanho médio do campo que é atualizado em bytes + 80) < 2 GB

  > [!NOTE]
  > Para a recompilação de índice, o tamanho médio do campo que é atualizado deve ser substituído pelo tamanho médio do índice.

### <a name="error-40553-the-session-has-been-terminated-because-of-excessive-memory-usage"></a>Erro 40553: A sessão foi encerrada devido ao uso excessivo de memória.

**Erro Detalhado**

``40553 : The session has been terminated because of excessive memory usage. Try modifying your query to process fewer rows.``

Para contornar esse problema, tente otimizar a consulta.

Para obter um procedimento detalhado de solução de problemas, consulte a [minha consulta está funcionando bem na nuvem?](http://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).


### <a name="cannot-open-database-master-requested-by-the-login-the-login-failed"></a>Não é possível abrir o banco de dados "mestre" solicitado pelo logon. Falha no logon.

Esse problema ocorre porque a conta não tem permissão de acesso ao banco de dados mestre. No entanto, por padrão, o SQL Server Management Studio (SSMS) tenta se conectar ao banco de dados mestre.

Para resolver este problema, siga estes passos:

1. Na tela de logon do SSMS, clique em **Opções**e em **Propriedades da conexão**. 
2. Em **conectar ao banco de dados**, digite o nome do banco de dados padrão do usuário como o banco de dados de logon padrão e clique em **conectar**.

   ![Cannot-Open-Database-Master. png](media/troubleshoot-connectivity-issues-microsoft-azure-sql-database/cannot-open-database-master.png)

## <a name="confirm-whether-an-error-is-due-to-a-connectivity-issue"></a>Confirmar se um erro é devido a um problema de conectividade

Para confirmar se um erro é devido a um problema de conectividade, examine o rastreamento de pilha para quadros que mostram chamadas para abrir uma conexão como as seguintes (Observe a referência à classe **SqlConnection** ):

```
System.Data.SqlClient.SqlConnection.TryOpen(TaskCompletionSource`1 retry)
 at System.Data.SqlClient.SqlConnection.Open()
 at AzureConnectionTest.Program.Main(String[] args)
ClientConnectionId:<Client connection ID>
```

Quando a exceção ocorrer devido a problemas de consulta, você notará uma pilha de chamadas semelhante às seguintes (Observe a referência à classe **SqlCommand** ). Nesses cenários, [ajuste suas consultas](http://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).

```
  at System.Data.SqlClient.SqlCommand.ExecuteReader()
  at AzureConnectionTest.Program.Main(String[] args)
  ClientConnectionId:<Client ID>
```
Para obter diretrizes adicionais sobre o desempenho de ajuste fino, consulte o seguinte:

* [Como manter índices e estatísticas do SQL Azure](https://techcommunity.microsoft.com/t5/Azure-Database-Support-Blog/How-to-maintain-Azure-SQL-Indexes-and-Statistics/ba-p/368787)
* [Ajuste manual de desempenho de consulta no banco de dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-performance-guidance)
* [Monitoramento de desempenho do banco de dados SQL do Azure usando exibições de gerenciamento dinâmico](https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-with-dmvs)
* [Operando o Repositório de Consultas no banco de dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-operate-query-store)


## <a name="steps-to-fix-common-connection-issues"></a>Etapas para corrigir problemas comuns de conexão

1. Verifique se o IP TCP está habilitado como um protocolo de cliente no servidor de aplicativos. Para obter mais informações, consulte [Configurar protocolos de cliente](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-client-protocols). Em servidores de aplicativos em que você não tem as ferramentas de SQL Server instaladas, verifique se o IP TCP está habilitado executando **cliconfg. exe** (SQL Server Client Network Utility). 
2. Verifique a cadeia de conexão do aplicativo para certificar-se de que ela está configurada corretamente. Por exemplo, verifique se a cadeia de conexão especifica a porta correta (1433) e o nome do servidor totalmente qualificado.
Consulte [obter SQL Server informações de conexão](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms#get-sql-server-connection-information).
3. Tente aumentar o **tempo limite**da conexão. A Microsoft recomenda usar um tempo limite de conexão de, pelo menos, 30 segundos. 
4. Teste a conectividade entre o servidor de aplicativos e o banco de dados SQL do Azure usando o [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms), um arquivo UDL, ping e Telnet. Para obter mais informações, consulte [Solucionando problemas de conectividade SQL Server](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server) e [diagnóstico para problemas de conectividade](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues#diagnostics).

   > [!NOTE]
   > Como uma etapa de solução de problemas, você também pode tentar testar a conectividade em um computador cliente diferente.

5. Como prática recomendada, verifique se a lógica de repetição está em vigor. Para obter mais informações sobre a lógica de repetição, consulte [solucionar falhas transitórias e erros de conexão no banco de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues).

Se as etapas anteriores não resolverem o problema, tente coletar mais dados e contate o suporte. Se seu aplicativo for um serviço de nuvem, habilite o registro em log. Esta etapa retorna um carimbo de data/hora UTC da falha. Além disso, SQL Azure retorna a ID de rastreamento. Os serviços de atendimento [ao cliente da Microsoft](http://azure.microsoft.com/support/options/) podem usar essas informações. 

Para obter mais informações sobre como habilitar o registro em log, consulte [habilitar o log de diagnóstico para aplicativos Web no serviço Azure app](https://azure.microsoft.com/documentation/articles/web-sites-enable-diagnostic-log/).

**Documentos relacionados**

* [Arquitetura de Conectividade do SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture)<br>
* [Controles de acesso à rede do banco de dados SQL do Azure e do data warehouse](https://docs.microsoft.com/azure/sql-database/sql-database-networkaccess-overview)