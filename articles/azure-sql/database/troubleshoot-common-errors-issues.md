---
title: Resolver problemas comuns de ligação para a Base de Dados SQL do Azure
description: Fornece medidas para resolver problemas de conexão Azure SQL Database e resolver outros problemas específicos da Base de Dados Azure SQL ou da Azure SQL
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.topic: troubleshooting
ms.custom: seo-lt-2019, OKR 11/2019, sqldbrb=1
author: ramakoni1
ms.author: ramakoni
ms.reviewer: sstein,vanto
ms.date: 01/14/2021
ms.openlocfilehash: 7c797c7e002f40a28e4be674c125c6ea5d60a13f
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98219067"
---
# <a name="troubleshooting-connectivity-issues-and-other-errors-with-azure-sql-database-and-azure-sql-managed-instance"></a>Problemas de conectividade de resolução de problemas e outros erros com Azure SQL Database e Azure SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Recebe mensagens de erro quando a ligação à Base de Dados Azure SQL ou à Instância Gerida Azure SQL falha. Estes problemas de ligação podem ser causados por reconfiguração, definições de firewall, tempo limite de ligação, informações de login incorretas ou falha na aplicação de boas práticas e orientações de design durante o processo de conceção da [aplicação.](develop-overview.md) Além disso, se o limite máximo em alguns recursos Azure SQL Database ou SQL Managed Instance for atingido, já não é possível ligar.

## <a name="transient-fault-error-messages-40197-40613-and-others"></a>Mensagens de erro transitórias (40197, 40613 e outras)

A infraestrutura do Azure tem a capacidade de reconfigurar dinamicamente os serviços quando surgem cargas de trabalho pesadas no serviço Base de Dados SQL.  Este comportamento dinâmico pode fazer com que o seu programa de clientes perca a sua ligação à base de dados ou instância. Este tipo de condição de erro é chamado de *falha transitória*. Os eventos de reconfiguração da base de dados ocorrem devido a um evento planeado (por exemplo, uma atualização de software) ou a um evento não planeado (por exemplo, uma falha de processo ou equilíbrio de carga). A maioria dos eventos de reconfiguração são geralmente de curta duração e devem ser concluídos em menos de 60 segundos no máximo. No entanto, estes eventos podem ocasionalmente demorar mais tempo a terminar, como quando uma grande transação causa uma recuperação prolongada. A tabela que se segue lista vários erros transitórios que as aplicações podem receber ao ligar-se à Base de Dados SQL

### <a name="list-of-transient-fault-error-codes"></a>Lista de códigos de erro transitórios

| Código de erro | Gravidade | Descrição |
| ---:| ---:|:--- |
| 4060 |16 |Não é possível abrir a base de dados "%&#x2a;ls" solicitada pelo login. O início de sessão falhou. Para mais informações, consulte [Errors 4000 a 4999](/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-4000-to-4999)|
| 40197 |17 |O serviço encontrou um erro no processamento do seu pedido. Tente novamente. Código de erro %d.<br/><br/>Recebe este erro quando o serviço está em baixo devido a atualizações de software ou hardware, falhas de hardware ou quaisquer outros problemas de falha. O código de erro (%d) incorporado na mensagem de erro 40197 fornece informações adicionais sobre o tipo de falha ou falha que ocorreu. Alguns exemplos dos códigos de erro estão incorporados na mensagem de erro 40197 são 40020, 40143, 40166 e 40540.<br/><br/>A reconexão liga-o automaticamente a uma cópia saudável da sua base de dados. A sua aplicação deve apanhar o erro 40197, registar o código de erro incorporado (%d) dentro da mensagem para resolução de problemas e tentar reconectar-se à Base de Dados SQL até que os recursos estejam disponíveis e a sua ligação seja novamente estabelecida. Para obter mais informações, consulte [erros transitórios.](troubleshoot-common-connectivity-issues.md#transient-errors-transient-faults)|
| 40501 |20 |o serviço está ocupado neste momento. Reda o pedido após 10 segundos. Identificação do incidente: %ls. Código: %d. Para obter mais informações, veja: <br/>&bull;&nbsp; [Limites de recursos de servidor SQL lógicos](resource-limits-logical-server.md)<br/>&bull;&nbsp; [Limites baseados em DTU para bases de dados individuais](service-tiers-dtu.md)<br/>&bull;&nbsp; [Limites à base de DTU para piscinas elásticas](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [limites baseados em vCore para bases de dados individuais](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [limites baseados em vCore para piscinas elásticas](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Limites de recursos de instância gerida Azure SQL](../managed-instance/resource-limits.md).|
| 40613 |17 |A base de dados "%.&#x2a;ls" no servidor '%.&#x2a;ls' não está disponível atualmente. Por favor, re-teste a ligação mais tarde. Se o problema persistir, contacte o apoio ao cliente e forneça-lhes o ID de '%.&#x2a;ls'.<br/><br/> Este erro pode ocorrer se já existir uma ligação de administrador dedicada existente (DAC) estabelecida na base de dados. Para obter mais informações, consulte [erros transitórios.](troubleshoot-common-connectivity-issues.md#transient-errors-transient-faults)|
| 49918 |16 |Não é possível processar o pedido. Não há recursos suficientes para processar o pedido.<br/><br/>o serviço está ocupado neste momento. Por favor, re-tentar o pedido mais tarde. Para obter mais informações, veja: <br/>&bull;&nbsp; [Limites de recursos de servidor SQL lógicos](resource-limits-logical-server.md)<br/>&bull;&nbsp; [Limites baseados em DTU para bases de dados individuais](service-tiers-dtu.md)<br/>&bull;&nbsp; [Limites à base de DTU para piscinas elásticas](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [limites baseados em vCore para bases de dados individuais](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [limites baseados em vCore para piscinas elásticas](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Limites de recursos de instância gerida Azure SQL](../managed-instance/resource-limits.md). |
| 49919 |16 |Não é possível processar o pedido de criação ou atualização. Muitas operações de criação ou atualização em curso para subscrição "%ld".<br/><br/>O serviço está ocupado a processar vários pedidos de criação ou atualização para a sua subscrição ou servidor. Os pedidos estão atualmente bloqueados para a otimização de recursos. Consulta [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) para operações pendentes. Aguarde até que os pedidos de criação ou atualização estejam completos ou elimine um dos seus pedidos pendentes e revendo o seu pedido mais tarde. Para obter mais informações, veja: <br/>&bull;&nbsp; [Limites de recursos de servidor SQL lógicos](resource-limits-logical-server.md)<br/>&bull;&nbsp; [Limites baseados em DTU para bases de dados individuais](service-tiers-dtu.md)<br/>&bull;&nbsp; [Limites à base de DTU para piscinas elásticas](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [limites baseados em vCore para bases de dados individuais](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [limites baseados em vCore para piscinas elásticas](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Limites de recursos de instância gerida Azure SQL](../managed-instance/resource-limits.md). |
| 49920 |16 |Não é possível processar o pedido. Demasiadas operações em curso para a subscrição "%ld".<br/><br/>O serviço está ocupado a processar vários pedidos para esta subscrição. Os pedidos estão atualmente bloqueados para a otimização de recursos. Consulta [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) para o estado de funcionamento. Aguarde até que os pedidos pendentes estejam completos ou elimine um dos seus pedidos pendentes e relemisse o seu pedido mais tarde. Para obter mais informações, veja: <br/>&bull;&nbsp; [Limites de recursos de servidor SQL lógicos](resource-limits-logical-server.md)<br/>&bull;&nbsp; [Limites baseados em DTU para bases de dados individuais](service-tiers-dtu.md)<br/>&bull;&nbsp; [Limites à base de DTU para piscinas elásticas](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [limites baseados em vCore para bases de dados individuais](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [limites baseados em vCore para piscinas elásticas](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Limites de recursos de instância gerida Azure SQL](../managed-instance/resource-limits.md). |
| 4221 |16 |O login para ler secundário falhou devido à longa espera em 'HADR_DATABASE_WAIT_FOR_TRANSITION_TO_VERSIONING'. A réplica não está disponível para login porque faltam versões de linha para transações que estavam a bordo quando a réplica foi reciclada. A questão pode ser resolvida recuando ou cometendo as transações ativas na réplica primária. As ocorrências desta condição podem ser minimizadas evitando transações de escrita longas nas primárias. |

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Medidas para resolver problemas transitórios de conectividade

1. Verifique o Painel de Serviços do [Microsoft Azure](https://azure.microsoft.com/status) para saber se as falhas conhecidas ocorreram durante o período durante o qual os erros foram reportados pela aplicação.
2. As aplicações que se ligam a um serviço de cloud, como o Azure SQL Database, devem esperar eventos de reconfiguração periódica e implementar lógica de repetição para lidar com estes erros em vez de os surgir como erros de aplicação para os utilizadores.
3. À medida que uma base de dados se aproxima dos seus limites de recursos, pode parecer uma questão de conectividade transitória. Ver [limites de recursos.](resource-limits-logical-server.md#what-happens-when-database-resource-limits-are-reached)
4. Se os problemas de conectividade continuarem, ou se a duração durante a qual a sua aplicação se encontra exceder 60 segundos ou se vir múltiplas ocorrências do erro num determinado dia, arquive um pedido de suporte Azure selecionando **Obter Suporte** no site de [Suporte Azure.](https://azure.microsoft.com/support/options)

#### <a name="implementing-retry-logic"></a>Implementação da Lógica de Retry

Recomenda-se vivamente que o seu programa de clientes tenha uma lógica de retíria para que possa restabelecer uma ligação depois de dar tempo de falha transitória para se corrigir.  Recomendamos que atrase por 5 segundos antes da primeira repetição. Tentar novamente depois de um atraso inferior a 5 segundos corre o risco de sobrecarregar o serviço de nuvem. Para cada tentativa subsequente, o atraso deve crescer exponencialmente, até um máximo de 60 segundos.

Para exemplos de código de lógica de retíria, consulte:

- [Conecte-se resilientemente ao SQL com ADO.NET](/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net)
- [Conecte-se resilientemente ao SQL com PHP](/sql/connect/php/step-4-connect-resiliently-to-sql-with-php)

Para informações adicionais sobre o manuseamento de erros transitórios na sua revisão de [resolução de problemas erros de ligação transitórios à Base de Dados SQL](troubleshoot-common-connectivity-issues.md)

Uma discussão sobre o período de *bloqueio* para clientes que usam ADO.NET está disponível em [Connection Pooling (ADO.NET)](/dotnet/framework/data/adonet/sql-server-connection-pooling).

## <a name="a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-your-server"></a>Ocorreu um erro específico relacionado com a rede ou de instância ao estabelecer uma ligação ao seu servidor

O problema ocorre se a aplicação não conseguir ligar-se ao servidor.

Para resolver esta questão, experimente os passos (na ordem apresentada) na secção [Etapas para corrigir problemas de conexão comuns.](#steps-to-fix-common-connection-issues)

## <a name="the-serverinstance-was-not-found-or-was-not-accessible-errors-26-40-10053"></a>O servidor/instância não foi encontrado ou não foi acessível (erros 26, 40, 10053)

### <a name="error-26-error-locating-server-specified"></a>Erro 26: Servidor de localização de erros especificado

``System.Data.SqlClient.SqlException: A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections.(provider: SQL Network Interfaces, error: 26 – Error Locating Server/Instance Specified)``

#### <a name="error-40-could-not-open-a-connection-to-the-server"></a>Erro 40: Não foi possível abrir uma ligação ao servidor

``A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections. (provider: Named Pipes Provider, error: 40 - Could not open a connection to SQL Server)``

#### <a name="error-10053-a-transport-level-error-has-occurred-when-receiving-results-from-the-server"></a>Erro 10053: Ocorreu um erro de nível de transporte ao receber resultados do servidor

``10053: A transport-level error has occurred when receiving results from the server. (Provider: TCP Provider, error: 0 - An established connection was aborted by the software in your host machine)``

Estes problemas ocorrem se a aplicação não puder ligar-se ao servidor.

Para resolver estas questões, experimente os passos (na ordem apresentada) na secção [Etapas para corrigir problemas de conexão comuns.](#steps-to-fix-common-connection-issues)

## <a name="cannot-connect-to-server-due-to-firewall-issues"></a>Não é possível ligar ao servidor devido a problemas de firewall

### <a name="error-40615-cannot-connect-to--servername-"></a>Erro 40615: Não é possível ligar-se ao < nome de servidor >

Para resolver este problema, [configure as definições de firewall na Base de Dados SQL através do portal Azure](firewall-configure.md).

### <a name="error-5-cannot-connect-to--servername-"></a>Erro 5: Não é possível ligar-se ao < nome de servidor >

Para resolver este problema, certifique-se de que a porta 1433 está aberta para ligações de saída em todas as firewalls entre o cliente e a internet.

## <a name="unable-to-log-in-to-the-server-errors-18456-40531"></a>Não é possível iniciar sessão no servidor (erros 18456, 40531)

### <a name="login-failed-for-user--user-name-"></a>O login falhou para o utilizador '< > de nome de utilizador'

``Login failed for user '<User name>'.This session has been assigned a tracing ID of '<Tracing ID>'. Provide this tracing ID to customer support when you need assistance. (Microsoft SQL Server, Error: 18456)``

Para resolver este problema, contacte o seu administrador de serviço para lhe fornecer um nome de utilizador válido e uma palavra-passe.

Normalmente, o administrador de serviço pode usar os seguintes passos para adicionar as credenciais de login:

1. Inicie sessão no servidor utilizando o SQL Server Management Studio (SSMS).
2. Executar a seguinte consulta SQL para verificar se o nome de login está desativado:

   ```sql
   SELECT name, is_disabled FROM sys.sql_logins
   ```

3. Se o nome correspondente estiver desativado, ative-o através da instrução seguinte:

   ```sql
   Alter login <User name> enable
   ```

4. Se o nome de utilizador de login SQL não existir, crie-o seguindo estes passos:

   1. Na SSMS, clique duas vezes **em Segurança** para expandi-lo.
   2. Clique em **Logins** com direito e, em seguida, **selecione Novo login**.
   3. No script gerado com espaços reservados, edite e execute a seguinte consulta SQL:

   ```sql
   CREATE LOGIN <SQL_login_name, sysname, login_name>
   WITH PASSWORD = '<password, sysname, Change_Password>'
   GO
   ```

5. Base de **dados** de duplo clique .
6. Selecione a base de dados a que pretende conceder a permissão do utilizador.
7. **Segurança** de duplo clique.
8. Clique com o botão direito **dos Utilizadores** e, em seguida, selecione **Novo Utilizador**.
9. No script gerado com espaços reservados, edite e execute a seguinte consulta SQL:

   ```sql
   CREATE USER <user_name, sysname, user_name>
   FOR LOGIN <login_name, sysname, login_name>
   WITH DEFAULT_SCHEMA = <default_schema, sysname, dbo>
   GO
   -- Add user to the database owner role

   EXEC sp_addrolemember N'db_owner', N'<user_name, sysname, user_name>'
   GO
   ```

   > [!NOTE]
   > Também pode usar `sp_addrolemember` para mapear utilizadores específicos para funções específicas de base de dados.

Para obter mais informações, consulte [bases de dados e logins em Azure SQL Database](./logins-create-manage.md).

## <a name="connection-timeout-expired-errors"></a>Erros de tempo de tempo de ligação expirados

### <a name="systemdatasqlclientsqlexception-0x80131904-connection-timeout-expired"></a>System.Data.SqlClient.SqlException (0x80131904): Intervalo de ligação expirado

``System.Data.SqlClient.SqlException (0x80131904): Connection Timeout Expired. The timeout period elapsed while attempting to consume the pre-login handshake acknowledgement. This could be because the pre-login handshake failed or the server was unable to respond back in time. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=3; handshake=29995;``

### <a name="systemdatasqlclientsqlexception-0x80131904-timeout-expired"></a>System.Data.SqlClient.SqlException (0x80131904): Timeout expirado

``System.Data.SqlClient.SqlException (0x80131904): Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding.``

### <a name="systemdataentitycoreentityexception-the-underlying-provider-failed-on-open"></a>System.Data.Entity.Core.EntityExcepção: O fornecedor subjacente falhou em Open

``System.Data.Entity.Core.EntityException: The underlying provider failed on Open. -> System.Data.SqlClient.SqlException: Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding. -> System.ComponentModel.Win32Exception: The wait operation timed out``

### <a name="cannot-connect-to--server-name-"></a>Não é possível ligar-se ao nome do servidor < >

``Cannot connect to <server name>.ADDITIONAL INFORMATION:Connection Timeout Expired. The timeout period elapsed during the post-login phase. The connection could have timed out while waiting for server to complete the login process and respond; Or it could have timed out while attempting to create multiple active connections. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=231; handshake=983; [Login] initialization=0; authentication=0; [Post-Login] complete=13000; (Microsoft SQL Server, Error: -2) For help, click: http://go.microsoft.com/fwlink?ProdName=Microsoft%20SQL%20Server&EvtSrc=MSSQLServer&EvtID=-2&LinkId=20476 The wait operation timed out``

Estas exceções podem ocorrer quer por questões de ligação quer por questões de consulta. Para confirmar que este erro é causado por problemas de conectividade, consulte [confirmar se um erro é causado por um problema de conectividade](#confirm-whether-an-error-is-caused-by-a-connectivity-issue).

Os intervalos de ligação ocorrem porque a aplicação não pode ligar-se ao servidor. Para resolver esta questão, experimente os passos (na ordem apresentada) na secção [Etapas para corrigir problemas de conexão comuns.](#steps-to-fix-common-connection-issues)

## <a name="resource-governance-errors"></a>Erros de governação de recursos

### <a name="error-10928-resource-id-d"></a>Erro 10928: ID de recurso: %d

``10928: Resource ID: %d. The %s limit for the database is %d and has been reached. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. The Resource ID value in error message indicates the resource for which limit has been reached. For sessions, Resource ID = 2.``

Para contornar esta questão, experimente um dos seguintes métodos:

- Verifique se existem consultas de longa duração.

  > [!NOTE]
  > Esta é uma abordagem minimalista que pode não resolver a questão. Para obter informações detalhadas sobre o bloqueio de consultas de resolução de [problemas, consulte compreender e resolver problemas de bloqueio do Azure SQL](understand-resolve-blocking.md).

1. Faça a seguinte consulta SQL para verificar a vista [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) para ver quaisquer pedidos de bloqueio:

   ```sql
   SELECT * FROM sys.dm_exec_requests;
   ```

2. Determine o **tampão de entrada** para o bloqueador de cabeça.
3. Sintonize a consulta do bloqueador de cabeça.

   Para um procedimento aprofundado de resolução de problemas, vê [a minha consulta está a correr bem na nuvem?](/archive/blogs/sqlblog/is-my-query-running-fine-in-the-cloud) 

Se a base de dados atingir consistentemente o seu limite apesar de abordar consultas de bloqueio e de longa duração, considere o upgrade para uma edição com mais [recursos Edições](https://azure.microsoft.com/pricing/details/sql-database/)).

Para obter mais informações sobre os limites da base de dados, consulte  [os limites de recursos da Base de Dados SQL para servidores](./resource-limits-logical-server.md).

### <a name="error-10929-resource-id-1"></a>Erro 10929: ID de recurso: 1

``10929: Resource ID: 1. The %s minimum guarantee is %d, maximum limit is %d and the current usage for the database is %d. However, the server is currently too busy to support requests greater than %d for this database. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. Otherwise, please try again later.``

### <a name="error-40501-the-service-is-currently-busy"></a>Erro 40501: O serviço está atualmente ocupado

``40501: The service is currently busy. Retry the request after 10 seconds. Incident ID: %ls. Code: %d.``

Trata-se de um erro de estrangulamento do motor, uma indicação de que os limites de recursos estão a ser ultrapassados.

Para obter mais informações sobre os limites de recursos, consulte [os limites de recursos do servidor Lógico SQL](./resource-limits-logical-server.md).

### <a name="error-40544-the-database-has-reached-its-size-quota"></a>Erro 40544: A base de dados atingiu a sua quota de tamanho

``40544: The database has reached its size quota. Partition or delete data, drop indexes, or consult the documentation for possible resolutions. Incident ID: <ID>. Code: <code>.``

Este erro ocorre quando a base de dados atingiu a sua quota de tamanho.

Os seguintes passos podem ajudá-lo a contornar o problema ou fornecer-lhe opções adicionais:

1. Verifique o tamanho atual da base de dados utilizando o painel de instrumentos no portal Azure.

   > [!NOTE]
   > Para identificar quais as tabelas que consomem mais espaço e, portanto, são potenciais candidatos à limpeza, execute a seguinte consulta SQL:

   ```sql
   SELECT o.name,
    SUM(p.row_count) AS 'Row Count',
    SUM(p.reserved_page_count) * 8.0 / 1024 AS 'Table Size (MB)'
   FROM sys.objects o
   JOIN sys.dm_db_partition_stats p on p.object_id = o.object_id
   GROUP BY o.name
   ORDER BY [Table Size (MB)] DESC;
   ```

2. Se o tamanho atual não exceder o tamanho máximo suportado para a sua edição, pode utilizar a BASE DE DADOS ALTER para aumentar a definição MAXSIZE.
3. Se a base de dados já tiver ultrapassado o tamanho máximo suportado para a sua edição, experimente um ou mais dos seguintes passos:

   - Realizar atividades normais de limpeza de bases de dados. Por exemplo, limpe os dados indesejados utilizando o truncato/apagar ou desloque os dados utilizando os Serviços de Integração de Servidores SQL (SSIS) ou o utilitário do programa de cópias a granel (bcp).
   - Dividir ou apagar dados, deixar cair índices ou consultar a documentação para possíveis resoluções.
   - Para escalar a base de dados, consulte [os recursos de base de dados únicos de escala](./single-database-scale.md) e recursos de piscina elástica em [escala.](./elastic-pool-scale.md)

### <a name="error-40549-session-is-terminated-because-you-have-a-long-running-transaction"></a>Erro 40549: A sessão termina porque tem uma transação de longa duração

``40549: Session is terminated because you have a long-running transaction. Try shortening your transaction.``

Se encontrar repetidamente este erro, tente resolver o problema seguindo estes passos:

1. Verifique a vista sys.dm_exec_requests para ver quaisquer sessões abertas com um valor elevado para a coluna total_elapsed_time. Execute esta verificação executando o seguinte script SQL:

   ```sql
   SELECT * FROM sys.dm_exec_requests;
   ```

2. Determine o tampão de entrada para a consulta de longa duração.
3. Afina a consulta.

Considere também a lotar as suas consultas. Para obter informações sobre o lote, consulte [como utilizar o lote para melhorar o desempenho da aplicação da base de dados SQL](../performance-improve-use-batching.md).

Para um procedimento aprofundado de resolução de problemas, vê [a minha consulta está a correr bem na nuvem?](/archive/blogs/sqlblog/is-my-query-running-fine-in-the-cloud)

### <a name="error-40551-the-session-has-been-terminated-because-of-excessive-tempdb-usage"></a>Erro 40551: A sessão foi encerrada devido a uma utilização excessiva do TEMPDB

``40551: The session has been terminated because of excessive TEMPDB usage. Try modifying your query to reduce the temporary table space usage.``

Para contornar esta questão, siga estes passos:

1. Altere as consultas para reduzir o uso temporário do espaço de mesa.
2. Largue objetos temporários depois de já não serem necessários.
3. Truncar as mesas ou remover mesas não ususadas.

### <a name="error-40552-the-session-has-been-terminated-because-of-excessive-transaction-log-space-usage"></a>Erro 40552: A sessão foi encerrada devido ao uso excessivo do espaço de registo de transações

``40552: The session has been terminated because of excessive transaction log space usage. Try modifying fewer rows in a single transaction.``

Para resolver este problema, experimente os métodos seguintes:

- O problema pode ocorrer devido à inserção, atualização ou eliminação de operações.
Tente reduzir o número de linhas que são operadas imediatamente, implementando lotes ou dividindo-se em várias transações menores.
- O problema pode ocorrer devido a operações de reconstrução de índices. Para contornar este problema, certifique-se de que o número de linhas que são afetadas na tabela * (tamanho médio do campo que é atualizado em bytes + 80) < 2 gigabytes (GB).

  > [!NOTE]
  > Para uma reconstrução de índice, o tamanho médio do campo que é atualizado deve ser substituído pelo tamanho médio do índice.

### <a name="error-40553-the-session-has-been-terminated-because-of-excessive-memory-usage"></a>Erro 40553: A sessão foi encerrada devido ao uso excessivo da memória

``40553 : The session has been terminated because of excessive memory usage. Try modifying your query to process fewer rows.``

Para contornar esta questão, tente otimizar a consulta.

Para um procedimento aprofundado de resolução de problemas, vê [a minha consulta está a correr bem na nuvem?](/archive/blogs/sqlblog/is-my-query-running-fine-in-the-cloud)

### <a name="table-of-additional-resource-governance-error-messages"></a>Tabela de mensagens de erro de governação de recursos adicionais

| Código de erro | Gravidade | Descrição |
| ---:| ---:|:--- |
| 10928 |20 |Identificação de recursos: %d. O limite de %s para a base de dados é %d e foi atingido. Para obter mais informações, consulte [os limites de recursos da Base de Dados SQL para bases de dados individuais e agrizadas.](resource-limits-logical-server.md)<br/><br/>O ID de recursos indica o recurso que atingiu o limite. Para os fios dos trabalhadores, o ID de recurso = 1. Para sessões, o ID de recurso = 2.<br/><br/>Para obter mais informações sobre este erro e como resolvê-lo, consulte: <br/>&bull;&nbsp; [Limites de recursos de servidor SQL lógicos](resource-limits-logical-server.md)<br/>&bull;&nbsp; [Limites baseados em DTU para bases de dados individuais](service-tiers-dtu.md)<br/>&bull;&nbsp; [Limites à base de DTU para piscinas elásticas](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [limites baseados em vCore para bases de dados individuais](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [limites baseados em vCore para piscinas elásticas](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Limites de recursos de instância gerida Azure SQL](../managed-instance/resource-limits.md). |
| 10929 |20 |Identificação de recursos: %d. A garantia mínima %s é %d, o limite máximo é de %d, e a utilização atual para a base de dados é de %d. No entanto, o servidor encontra-se atualmente demasiado ocupado para suportar pedidos superiores a %d para esta base de dados. O ID de recursos indica o recurso que atingiu o limite. Para os fios dos trabalhadores, o ID de recurso = 1. Para sessões, o ID de recurso = 2. Para obter mais informações, veja: <br/>&bull;&nbsp; [Limites de recursos de servidor SQL lógicos](resource-limits-logical-server.md)<br/>&bull;&nbsp; [Limites baseados em DTU para bases de dados individuais](service-tiers-dtu.md)<br/>&bull;&nbsp; [Limites à base de DTU para piscinas elásticas](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [limites baseados em vCore para bases de dados individuais](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [limites baseados em vCore para piscinas elásticas](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Limites de recursos de instância gerida Azure SQL](../managed-instance/resource-limits.md). <br/>Caso contrário, tente de novo mais tarde. |
| 40544 |20 |A base de dados atingiu a sua quota de tamanho. Dividir ou apagar dados, deixar cair índices ou consultar a documentação para possíveis resoluções. Para escalar a base de dados, consulte [os recursos de base de dados únicos de escala](single-database-scale.md) e recursos de piscina elástica em [escala.](elastic-pool-scale.md)|
| 40549 |16 |A sessão termina porque tem uma transação de longa duração. Tente encurtar a sua transação. Para obter informações sobre o lote, consulte [como utilizar o lote para melhorar o desempenho da aplicação da base de dados SQL](../performance-improve-use-batching.md).|
| 40550 |16 |A sessão foi encerrada porque adquiriu demasiadas fechaduras. Tente ler ou modificar menos linhas numa única transação. Para obter informações sobre o lote, consulte [como utilizar o lote para melhorar o desempenho da aplicação da base de dados SQL](../performance-improve-use-batching.md).|
| 40551 |16 |A sessão foi encerrada devido ao `TEMPDB` uso excessivo. Tente modificar a sua consulta para reduzir o uso temporário do espaço de mesa.<br/><br/>Se estiver a utilizar objetos temporários, conserva o espaço na `TEMPDB` base de dados, deixando cair objetos temporários depois de já não serem necessários pela sessão. Para obter mais informações sobre a utilização de temperaturas na Base de Dados SQL, consulte [a base de dados Tempdb na Base de Dados SQL](/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).|
| 40552 |16 |A sessão foi encerrada devido ao uso excessivo do espaço de registo de transações. Tente modificar menos linhas numa única transação. Para obter informações sobre o lote, consulte [como utilizar o lote para melhorar o desempenho da aplicação da base de dados SQL](../performance-improve-use-batching.md).<br/><br/>Se efetuar inserções a granel utilizando o `bcp.exe` utilitário ou a `System.Data.SqlClient.SqlBulkCopy` classe, tente utilizar o ou `-b batchsize` `BatchSize` opções para limitar o número de linhas copiadas para o servidor em cada transação. Se estiver a reconstruir um índice com a `ALTER INDEX` declaração, tente utilizar a `REBUILD WITH ONLINE = ON` opção. Para obter informações sobre os tamanhos dos registos de transações para o modelo de compra vCore, consulte: <br/>&bull;&nbsp; [limites baseados em vCore para bases de dados individuais](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [limites baseados em vCore para piscinas elásticas](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Limites de recursos de instância gerida Azure SQL](../managed-instance/resource-limits.md).|
| 40553 |16 |A sessão foi encerrada devido ao uso excessivo da memória. Tente modificar a sua consulta para processar menos linhas.<br/><br/>A redução do número `ORDER BY` e `GROUP BY` operações no seu código Transact-SQL reduz os requisitos de memória da sua consulta. Para escalar a base de dados, consulte [os recursos de base de dados únicos de escala](single-database-scale.md) e recursos de piscina elástica em [escala.](elastic-pool-scale.md)|

## <a name="elastic-pool-errors"></a>Erros elásticos da piscina

Os seguintes erros estão relacionados com a criação e utilização de piscinas elásticas:

| Código de erro | Gravidade | Descrição | Ação corretiva |
|:--- |:--- |:--- |:--- |
| 1132 | 17 |A piscina elástica atingiu o seu limite de armazenamento. A utilização do armazenamento para a piscina elástica não pode exceder (%d) MBs. Tentar escrever dados para uma base de dados quando o limite de armazenamento do pool elástico tiver sido atingido. Para obter informações sobre os limites de recursos, consulte: <br/>&bull;&nbsp; [Limites à base de DTU para piscinas elásticas](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [limites à base de vCore para piscinas elásticas](resource-limits-vcore-elastic-pools.md). <br/> |Considere aumentar os DTUs de e/ou adicionar armazenamento à piscina elástica, se possível, a fim de aumentar o seu limite de armazenamento, reduzir o armazenamento utilizado por bases de dados individuais dentro da piscina elástica ou remover bases de dados da piscina elástica. Para dimensionamento elástico da piscina, consulte [os recursos elásticos da piscina.](elastic-pool-scale.md)|
| 10929 | 16 |A garantia mínima %s é %d, o limite máximo é de %d, e a utilização atual para a base de dados é de %d. No entanto, o servidor encontra-se atualmente demasiado ocupado para suportar pedidos superiores a %d para esta base de dados. Para obter informações sobre os limites de recursos, consulte: <br/>&bull;&nbsp; [Limites à base de DTU para piscinas elásticas](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [limites à base de vCore para piscinas elásticas](resource-limits-vcore-elastic-pools.md). <br/> Caso contrário, tente de novo mais tarde. DTU /vCore min por base de dados; DTU /vCore max por base de dados. O número total de trabalhadores simultâneos (pedidos) em todas as bases de dados da piscina elástica tentou ultrapassar o limite da piscina. |Considere aumentar os DTUs ou vCores da piscina elástica, se possível, para aumentar o seu limite de trabalhadores, ou remover bases de dados da piscina elástica. |
| 40844 | 16 |A base de dados '%ls' no Servidor '%ls' é uma base de dados de edição '%ls' numa piscina elástica e não pode ter uma relação de cópia contínua.  |N/D |
| 40857 | 16 |Piscina elástica não encontrada para servidor: '%ls', nome de piscina elástica: '%ls'. A piscina elástica especificada não existe no servidor especificado. | Forneça um nome de piscina elástica válido. |
| 40858 | 16 |Piscina elástica '%ls' já existe no servidor: '%ls'. Piscina elástica especificada já existe no servidor especificado. | Forneça um novo nome elástico da piscina. |
| 40859 | 16 |A piscina elástica não suporta o nível de serviço '%ls'. O nível de serviço especificado não é suportado para o fornecimento de piscina elástica. |Forneça a edição correta ou deixe o nível de serviço em branco para utilizar o nível de serviço predefinido. |
| 40860 | 16 |A combinação "%ls" da piscina elástica e o objetivo de serviço '%ls' são inválidos. Piscina elástica e nível de serviço só podem ser especificados em conjunto se o tipo de recurso for especificado como 'ElasticPool'. |Especifique a combinação correta de piscina elástica e nível de serviço. |
| 40861 | 16 |A edição da base de dados.%. *Ls' não pode ser diferente do nível elástico de serviço da piscina que é '%.* É uma questão de questão. A edição da base de dados é diferente do nível elástico de serviço da piscina. |Não especifique uma edição de base de dados diferente do nível de serviço da piscina elástica.  Note que a edição da base de dados não precisa de ser especificada. |
| 40862 | 16 |O nome da piscina elástica deve ser especificado se o objetivo de serviço da piscina elástica for especificado. O objetivo de serviço de piscina elástica não identifica exclusivamente uma piscina elástica. |Especifique o nome da piscina elástica se utilizar o objetivo de serviço de piscina elástica. |
| 40864 | 16 |Os DTUs para a piscina elástica devem ser pelo menos (%d) DTUs para o nível de serviço '%.*ls'. Tentar definir os DTUs para a piscina elástica abaixo do limite mínimo. |Relemiste a definição dos DTUs para a piscina elástica para pelo menos o limite mínimo. |
| 40865 | 16 |Os DTUs para a piscina elástica não podem exceder (%d) DTUs para o nível de serviço '%.*ls'. Tentar definir os DTUs para a piscina elástica acima do limite máximo. |Relemisse a fixação dos DTUs para a piscina elástica não superior ao limite máximo. |
| 40867 | 16 |O DTU max por base de dados deve ser pelo menos (%d) para o nível de serviço '%.*ls'. Tentar definir o DTU max por base de dados abaixo do limite suportado. | Considere utilizar o nível de serviço de piscina elástico que suporta a regulação desejada. |
| 40868 | 16 |O DTU max por base de dados não pode exceder (%d) para o nível de serviço '%.*ls'. Tentar definir o DTU max por base de dados para além do limite suportado. | Considere utilizar o nível de serviço de piscina elástico que suporta a regulação desejada. |
| 40870 | 16 |O DTU min por base de dados não pode exceder (%d) para o nível de serviço '%.*ls'. Tentando definir o DTU min por base de dados para além do limite suportado. | Considere utilizar o nível de serviço de piscina elástico que suporta a regulação desejada. |
| 40873 | 16 |O número de bases de dados (%d) e DTU min por base de dados (%d) não pode exceder os DTUs do pool elástico (%d). Tentar especificar dTU min para bases de dados na piscina elástica que exceda os DTUs da piscina elástica. | Considere aumentar os DTUs da piscina elástica, ou diminuir o DTU min por base de dados, ou diminuir o número de bases de dados na piscina elástica. |
| 40877 | 16 |Uma piscina elástica não pode ser eliminada a menos que não contenha bases de dados. A piscina elástica contém uma ou mais bases de dados e, portanto, não pode ser eliminada. |Retire as bases de dados da piscina elástica para a eliminar. |
| 40881 | 16 |O conjunto elástico '%.*ls' atingiu o seu limite de contagem de bases de dados.  O limite de contagem de dados para a piscina elástica não pode exceder (%d) para uma piscina elástica com DTUs (%d). Tentar criar ou adicionar base de dados à piscina elástica quando o limite de contagem de dados do elástico tiver sido atingido. | Considere aumentar os DTUs do pool elástico, se possível, a fim de aumentar o seu limite de base de dados, ou remover bases de dados da piscina elástica. |
| 40889 | 16 |Os DTUs ou o limite de armazenamento para o pool elástico '%.*ls' não podem ser diminuídos, uma vez que isso não proporcionaria espaço de armazenamento suficiente para as suas bases de dados. Tentando diminuir o limite de armazenamento da piscina elástica abaixo do seu uso de armazenamento. | Considere reduzir o uso de armazenamento de bases de dados individuais na piscina elástica ou remover bases de dados da piscina de modo a reduzir o seu DTUs ou limite de armazenamento. |
| 40891 | 16 |O DTU min por base de dados (%d) não pode exceder o DTU max por base de dados (%d). Tentando definir o DTU min por base de dados mais alto que o DTU max por base de dados. |Certifique-se de que o DTU min por base de dados não excede o DTU max por base de dados. |
| TBD | 16 |O tamanho de armazenamento de uma base de dados individual numa piscina elástica não pode exceder o tamanho máximo permitido pela piscina elástica de nível de serviço '%.*ls'. O tamanho máximo da base de dados excede o tamanho máximo permitido pelo nível de serviço da piscina elástica. |Desa ajuste o tamanho máximo da base de dados dentro dos limites do tamanho máximo permitido pelo nível de serviço da piscina elástica. |

## <a name="cannot-open-database-master-requested-by-the-login-the-login-failed"></a>Não é possível abrir a base de dados "master" solicitada pelo login. O login falhou

Este problema ocorre porque a conta não tem permissão para aceder à base de dados principal. Mas, por padrão, o SQL Server Management Studio (SSMS) tenta ligar-se à base de dados principal.

Para resolver este problema, siga estes passos:

1. No ecrã de login do SSMS, selecione **Opções** e, em seguida, selecione **Connection Properties**.
2. No campo **'Ligar à base de dados',** insira o nome de base de dados predefinido do utilizador como base de dados de login predefinido e, em seguida, selecione **Connect**.

   ![Propriedades de ligação](./media/troubleshoot-common-errors-issues/cannot-open-database-master.png)

## <a name="confirm-whether-an-error-is-caused-by-a-connectivity-issue"></a>Confirme se um erro é causado por um problema de conectividade

Para confirmar se um erro é causado por um problema de conectividade, reveja o traço de pilha para quadros que mostrem chamadas para abrir uma ligação como as seguintes (note a referência à classe **SqlConnection):**

```
System.Data.SqlClient.SqlConnection.TryOpen(TaskCompletionSource`1 retry)
 at System.Data.SqlClient.SqlConnection.Open()
 at AzureConnectionTest.Program.Main(String[] args)
ClientConnectionId:<Client connection ID>
```

Quando a exceção for desencadeada por problemas de consulta, notará uma pilha de chamadas semelhante à seguinte (note a referência à classe **SqlCommand).** Nesta situação, [afina as suas consultas.](/archive/blogs/sqlblog/is-my-query-running-fine-in-the-cloud)

```
  at System.Data.SqlClient.SqlCommand.ExecuteReader()
  at AzureConnectionTest.Program.Main(String[] args)
  ClientConnectionId:<Client ID>
```

Para obter orientações adicionais sobre o desempenho de afinação, consulte os seguintes recursos:

- [Como manter índices e estatísticas do Azure SQL](https://techcommunity.microsoft.com/t5/Azure-Database-Support-Blog/How-to-maintain-Azure-SQL-Indexes-and-Statistics/ba-p/368787)
- [Desempenho da consulta de sintonia manual na Base de Dados Azure SQL](./performance-guidance.md)
- [Monitorização do desempenho Azure SQL Database utilizando vistas dinâmicas de gestão](./monitoring-with-dmvs.md)
- [Operar a Loja de Consultas em Base de Dados Azure SQL](/sql/relational-databases/performance/best-practice-with-the-query-store#Insight)

## <a name="steps-to-fix-common-connection-issues"></a>Steps to fix common connection issues (Passos para corrigir problemas de ligação comuns)

1. Certifique-se de que o TCP/IP está ativado como protocolo de cliente no servidor de aplicações. Para mais informações, consulte os protocolos de [clientes configurar.](/sql/database-engine/configure-windows/configure-client-protocols) Nos servidores de aplicações onde não tenha ferramentas SQL instaladas, verifique se o TCP/IP está ativado executando **cliconfg.exe** (utilitário sql Server Client Network).
2. Verifique o fio de ligação da aplicação para se certificar de que está configurado corretamente. Por exemplo, certifique-se de que a cadeia de ligação especifica a porta correta (1433) e o nome do servidor totalmente qualificado.
Consulte [informações de ligação](./connect-query-ssms.md#get-server-connection-information).
3. Tente aumentar o valor do tempo limite de ligação. Recomendamos a utilização de um tempo de ligação de pelo menos 30 segundos.
4. Teste a conectividade entre o servidor de aplicações e a Base de Dados Azure SQL utilizando [o SQL Server Management Studio (SSMS)](./connect-query-ssms.md), um ficheiro UDL, ping ou telnet. Para obter mais informações, consulte problemas de conectividade de [resolução de problemas](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server) e [diagnósticos para questões de conectividade.](./troubleshoot-common-connectivity-issues.md#diagnostics)

   > [!NOTE]
   > Como um passo de resolução de problemas, também pode testar conectividade em um computador cliente diferente.

5. Como uma boa prática, certifique-se de que a lógica de relícola está em vigor. Para obter mais informações sobre a lógica de relagem, consulte [falhas transitórias de resolução de problemas e erros de ligação à Base de Dados SQL](./troubleshoot-common-connectivity-issues.md).

Se estes passos não resolverem o seu problema, tente recolher mais dados e, em seguida, contacte o suporte. Se a sua aplicação for um serviço de cloud, ative o registo registado. Este passo devolve um carimbo de tempo UTC da falha. Além disso, a SQL Database devolve o ID de rastreio. [Os Serviços de Apoio ao Cliente da Microsoft](https://azure.microsoft.com/support/options/) podem utilizar esta informação.

Para obter mais informações sobre como permitir a sessão, consulte [Ativar o registo de diagnósticos de aplicações no Azure App Service](../../app-service/troubleshoot-diagnostic-logs.md).

## <a name="next-steps"></a>Passos seguintes

- [Arquitetura da conectividade da Base de Dados SQL do Azure](./connectivity-architecture.md)
- [Azure SQL Database e Azure Synapse Analytics controlos de acesso à rede](./network-access-controls-overview.md)