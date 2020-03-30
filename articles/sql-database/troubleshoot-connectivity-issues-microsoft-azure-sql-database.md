---
title: Problemas de ligação comum à base de dados SQL
description: Fornece passos para resolver problemas de ligação à base de dados Azure SQL e resolver outros problemas específicos da Base de Dados SQL
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
ms.custom: seo-lt-2019, OKR 11/2019
author: ramakoni1
ms.author: ramakoni
ms.reviewer: carlrab,vanto
ms.date: 01/14/2020
ms.openlocfilehash: 578d076dfc2fc383051c0c3a8528cdbb0fbcdb15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79208790"
---
# <a name="troubleshooting-connectivity-issues-and-other-errors-with-microsoft-azure-sql-database"></a>Resolver problemas de conectividade e outros erros com a Base de Dados SQL do Microsoft Azure

Recebe mensagens de erro quando a ligação à base de dados SQL do Azure falha. Estes problemas de ligação podem ser causados pela reconfiguração da Base de Dados Azure SQL, definições de firewall, um tempo de ligação, informações de login incorretas ou falta de aplicação das melhores práticas e orientações de design durante o processo de conceção da [aplicação.](sql-database-develop-overview.md) Além disso, se o limite máximo de alguns recursos da Base de Dados Azure SQL for atingido, não pode ligar-se à Base de Dados Azure SQL.

## <a name="transient-fault-error-messages-40197-40613-and-others"></a>Mensagens transitórias de erro de falha (40197, 40613 e outras)

A infraestrutura do Azure tem a capacidade de reconfigurar dinamicamente os serviços quando surgem cargas de trabalho pesadas no serviço Base de Dados SQL.  Este comportamento dinâmico poderá fazer com que o seu programa cliente perca a ligação à Base de Dados SQL. Este tipo de condição de erro é chamado de *falha transitória.* Os eventos de reconfiguração da base de dados ocorrem devido a um evento planeado (por exemplo, uma atualização de software) ou a um evento não planeado (por exemplo, um crash de processo ou um equilíbrio de carga). A maioria dos eventos de reconfiguração são geralmente de curta duração e devem ser concluídos em menos de 60 segundos no máximo. No entanto, estes eventos podem ocasionalmente demorar mais tempo a terminar, como quando uma grande transação causa uma recuperação a longo prazo. A tabela seguinte enumera vários erros transitórios que as aplicações podem receber ao ligar-se à Base de Dados SQL

### <a name="list-of-transient-fault-error-codes"></a>Lista de códigos transitórios de erro de falha


| Código de erro | Gravidade | Descrição |
| ---:| ---:|:--- |
| 4060 |16 |Não é possível abrir a base de dados "%.&#x2a;ls" solicitado pelo login. O login falhou. Para mais informações, consulte [Erros 4000 a 4999](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-4000-to-4999)|
| 40197 |17 |O serviço encontrou um erro processando o seu pedido. Tente novamente. Código de erro %d.<br/><br/>Recebe este erro quando o serviço está em baixo devido a atualizações de software ou hardware, falhas de hardware ou quaisquer outros problemas de falha. O código de erro (%d) incorporado na mensagem de erro 40197] fornece informações adicionais sobre o tipo de falha ou falha que ocorreu. Alguns exemplos dos códigos de erro estão incorporados na mensagem de erro 40197 são 40020, 40143, 40166 e 40540.<br/><br/>A religação ao seu servidor de base de dados SQL liga-o automaticamente a uma cópia saudável da sua base de dados. A sua aplicação deve capturar o erro 40197, registar o código de erro incorporado (%d) dentro da mensagem para resolução de problemas e tentar reconectar-se à Base de Dados SQL até que os recursos estejam disponíveis, e a sua ligação seja novamente estabelecida. Para mais informações, consulte [erros transitórios.](sql-database-connectivity-issues.md#transient-errors-transient-faults)|
| 40501 |20 |o serviço está ocupado neste momento. Tente o pedido após 10 segundos. Id incidente: %ls. Código: %d. Para obter mais informações, consulte: <br/>&bull;&nbsp; [Limites de recursos](sql-database-resource-limits-database-server.md) do servidor de dados<br/>&bull;&nbsp; [Limites baseados em DTU para bases de dados únicas](sql-database-service-tiers-dtu.md)<br/>&bull;&nbsp; [Limites baseados em DTU para piscinas elásticas](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [limites baseados em vCore para bases de dados únicas](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp; [limites baseados em vCore para piscinas elásticas](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [Limites de recursos de instância geridos.](sql-database-managed-instance-resource-limits.md)|
| 40613 |17 |A base de dados '%.&#x2a;ls' no servidor '%.&#x2a;ls' não está atualmente disponível. Por favor, tente a ligação mais tarde. Se o problema persistir, contacte o apoio ao cliente e forneça-lhes a identificação de rastreio da sessão de '%.&#x2a;ls'.<br/><br/> Este erro pode ocorrer se já existir uma ligação de administrador dedicado existente (DAC) estabelecida na base de dados. Para mais informações, consulte [erros transitórios.](sql-database-connectivity-issues.md#transient-errors-transient-faults)|
| 49918 |16 |Não é possível processar o pedido. Não há recursos suficientes para processar o pedido.<br/><br/>o serviço está ocupado neste momento. Por favor, tente o pedido mais tarde. Para obter mais informações, consulte: <br/>&bull;&nbsp; [Limites de recursos](sql-database-resource-limits-database-server.md) do servidor de dados<br/>&bull;&nbsp; [Limites baseados em DTU para bases de dados únicas](sql-database-service-tiers-dtu.md)<br/>&bull;&nbsp; [Limites baseados em DTU para piscinas elásticas](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [limites baseados em vCore para bases de dados únicas](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp; [limites baseados em vCore para piscinas elásticas](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [Limites de recursos de instância geridos.](sql-database-managed-instance-resource-limits.md) |
| 49919 |16 |Não é possível processar a criação ou o pedido de atualização. Demasiadas operações de criação ou atualização em curso para subscrição "%ld".<br/><br/>O serviço está ocupado processando vários pedidos de criação ou atualização para a sua subscrição ou servidor. Os pedidos estão atualmente bloqueados para otimização de recursos. Consultas [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) para operações pendentes. Aguarde até que os pedidos de criação ou atualização estejam completos ou apaguem um dos seus pedidos pendentes e voltem a tentar o seu pedido mais tarde. Para obter mais informações, consulte: <br/>&bull;&nbsp; [Limites de recursos](sql-database-resource-limits-database-server.md) do servidor de dados<br/>&bull;&nbsp; [Limites baseados em DTU para bases de dados únicas](sql-database-service-tiers-dtu.md)<br/>&bull;&nbsp; [Limites baseados em DTU para piscinas elásticas](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [limites baseados em vCore para bases de dados únicas](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp; [limites baseados em vCore para piscinas elásticas](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [Limites de recursos de instância geridos.](sql-database-managed-instance-resource-limits.md) |
| 49920 |16 |Não é possível processar o pedido. Demasiadas operações em curso para a subscrição "%ld".<br/><br/>O serviço está ocupado processando vários pedidos para esta subscrição. Os pedidos estão atualmente bloqueados para otimização de recursos. Consulta [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) para o estado de operação. Aguarde até que os pedidos pendentes estejam completos ou apague um dos seus pedidos pendentes e tente novamente o seu pedido mais tarde. Para obter mais informações, consulte: <br/>&bull;&nbsp; [Limites de recursos](sql-database-resource-limits-database-server.md) do servidor de dados<br/>&bull;&nbsp; [Limites baseados em DTU para bases de dados únicas](sql-database-service-tiers-dtu.md)<br/>&bull;&nbsp; [Limites baseados em DTU para piscinas elásticas](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [limites baseados em vCore para bases de dados únicas](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp; [limites baseados em vCore para piscinas elásticas](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [Limites de recursos de instância geridos.](sql-database-managed-instance-resource-limits.md) |
| 4221 |16 |O login para o secundário de leitura falhou devido à longa espera em 'HADR_DATABASE_WAIT_FOR_TRANSITION_TO_VERSIONING'. A réplica não está disponível para login porque faltam versões de linha para transações que estavam a bordo quando a réplica foi reciclada. A questão pode ser resolvida rebolando ou cometendo as transações ativas na réplica primária. As ocorrências desta condição podem ser minimizadas evitando transações de escrita prolongada nas primárias. |


### <a name="steps-to-resolve-transient-connectivity-issues"></a>Passos para resolver problemas transitórios de conectividade

1. Verifique se o [Microsoft Azure Service Dashboard](https://azure.microsoft.com/status) está a verificar quaisquer falhas conhecidas que ocorreram durante o período durante o qual os erros foram reportados pela aplicação.
2. As aplicações que se ligam a um serviço na nuvem, como a Base de Dados Azure SQL, devem esperar eventos de reconfiguração periódica e implementar a lógica de retry para lidar com estes erros em vez de os surcar como erros de aplicação para os utilizadores. 
3. À medida que uma base de dados se aproxima dos seus limites de recursos, pode parecer um problema transitório de conectividade. Ver [Limites de Recursos](sql-database-resource-limits-database-server.md#what-happens-when-database-resource-limits-are-reached).
4. Se os problemas de conectividade continuarem, ou se a duração para a qual a sua aplicação se encontra o erro exceder 60 segundos ou se vir múltiplas ocorrências do erro num determinado dia, faça um pedido de suporte Azure selecionando **o Suporte** get no site do [Suporte Azure.](https://azure.microsoft.com/support/options)

#### <a name="implementing-retry-logic"></a>Implementar a Lógica de Retry
Recomenda-se vivamente que o seu programa de cliente tenha uma lógica de retry para que possa restabelecer uma ligação depois de dar tempo de falha transitória para se corrigir.  Recomendamos que atrase por 5 segundos antes da primeira tentativa. Tentar depois de um atraso de menos de 5 segundos corre o risco de sobrecarregar o serviço de nuvem. Para cada tentativa subsequente, o atraso deve crescer exponencialmente, até um máximo de 60 segundos.

Para exemplos de código de lógica de retry, consulte:
- [Ligue-se resilientemente ao SQL com ADO.NET](https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net)
- [Ligue-se resilientemente à SQL com PHP](https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php)

Para obter informações adicionais sobre o tratamento de erros transitórios na sua revisão de aplicações
* [Erros de ligação transitórios de resolução de problemas à Base de Dados SQL](sql-database-connectivity-issues.md)

Uma discussão sobre o período de *bloqueio* para clientes que usam ADO.NET está disponível no [SQL Server Connection Pooling (ADO.NET)](https://msdn.microsoft.com/library/8xx3tyca.aspx).

## <a name="a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-database-server"></a>Ocorreu um erro relacionado com a rede ou por exemplo ao estabelecer uma ligação ao servidor de base de dados SQL

O problema ocorre se a aplicação não conseguir ligar-se ao servidor.

Para resolver este problema, experimente os passos (na ordem apresentada) nas Etapas para corrigir a secção de problemas de [ligação comuns.](#steps-to-fix-common-connection-issues)

## <a name="the-serverinstance-was-not-found-or-was-not-accessible-errors-26-40-10053"></a>O servidor/instância não foi encontrado ou não foi acessível (erros 26, 40, 10053)

#### <a name="error-26-error-locating-server-specified"></a>Erro 26: Servidor de localização de erros especificado

``System.Data.SqlClient.SqlException: A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections.(provider: SQL Network Interfaces, error: 26 – Error Locating Server/Instance Specified)``

#### <a name="error-40-could-not-open-a-connection-to-the-server"></a>Erro 40: Não conseguiu abrir uma ligação ao servidor

``A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections. (provider: Named Pipes Provider, error: 40 - Could not open a connection to SQL Server)``

#### <a name="error-10053-a-transport-level-error-has-occurred-when-receiving-results-from-the-server"></a>Erro 10053: Ocorreu um erro ao nível de transporte ao receber resultados do servidor

``10053: A transport-level error has occurred when receiving results from the server. (Provider: TCP Provider, error: 0 - An established connection was aborted by the software in your host machine)``

Estes problemas ocorrem se a aplicação não conseguir ligar-se ao servidor.

Para resolver estas questões, experimente os passos (na ordem apresentada) nas Etapas para corrigir a secção de problemas de [ligação comuns.](#steps-to-fix-common-connection-issues)

## <a name="cannot-connect-to-server-due-to-firewall-issues"></a>Não é possível ligar ao servidor devido a problemas de firewall

### <a name="error-40615-cannot-connect-to--servername-"></a>Erro 40615: Não é possível ligar-se ao > de servidor <

Para resolver este problema, configure as definições de firewall na Base de [Dados SQL através do portal Azure.](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)

### <a name="error-5-cannot-connect-to--servername-"></a>Erro 5: Não pode ligar-se a < nome de servidor >

Para resolver este problema, certifique-se de que a porta 1433 está aberta para ligações de saída em todas as firewalls entre o cliente e a internet.

Para mais informações, consulte Configurar o Firewall do Windows para permitir o [acesso ao Servidor SQL](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).

## <a name="unable-to-log-in-to-the-server-errors-18456-40531"></a>Incapaz de iniciar sessão no servidor (erros 18456, 40531)

### <a name="login-failed-for-user--user-name-"></a>O login falhou no utilizador '< nome de utilizador >'

``Login failed for user '<User name>'.This session has been assigned a tracing ID of '<Tracing ID>'. Provide this tracing ID to customer support when you need assistance. (Microsoft SQL Server, Error: 18456)``

Para resolver este problema, contacte o seu administrador de serviço para lhe fornecer um nome de utilizador e senha de utilizador sQL server válidos.

Normalmente, o administrador de serviço pode usar os seguintes passos para adicionar as credenciais de login:

1. Inicie sessão no servidor utilizando o Estúdio de Gestão de Servidores SQL (SSMS).
2. Faça a seguinte consulta SQL para verificar se o nome de login está desativado:

   ```sql
   SELECT name, is_disabled FROM sys.sql_logins
   ```

3. Se o nome correspondente estiver desativado, ative-o através da instrução seguinte:

   ```sql
   Alter login <User name> enable
   ```

4. Se o nome de utilizador de login SQL não existir, crie-o seguindo estes passos:

   1. No SSMS, clique duas vezes **na Segurança** para expandi-lo.
   2. Clique com botão direito do rato em **Inícios de sessão** e, em seguida, selecione **Novo início de sessão**.
   3. No script gerado com espaços reservados, edite e execute a seguinte consulta SQL:

   ```sql
   CREATE LOGIN <SQL_login_name, sysname, login_name>
   WITH PASSWORD = ‘<password, sysname, Change_Password>’
   GO
   ```

5. Faça duplo clique em **Base de dados**.
6. Selecione a base de dados a que pretende conceder permissão ao utilizador.
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
   > Também pode `sp_addrolemember` utilizar para mapear utilizadores específicos para funções específicas da base de dados.

Para mais informações, consulte A Gestão de bases de [dados e logins na Base de Dados Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins).

## <a name="connection-timeout-expired-errors"></a>Erros expirados de tempo de ligação

### <a name="systemdatasqlclientsqlexception-0x80131904-connection-timeout-expired"></a>System.Data.SqlClient.SqlException (0x80131904): Tempo de ligação expirado

``System.Data.SqlClient.SqlException (0x80131904): Connection Timeout Expired. The timeout period elapsed while attempting to consume the pre-login handshake acknowledgement. This could be because the pre-login handshake failed or the server was unable to respond back in time. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=3; handshake=29995;``

### <a name="systemdatasqlclientsqlexception-0x80131904-timeout-expired"></a>System.Data.SqlClient.SqlException (0x80131904): Tempo expirado

``System.Data.SqlClient.SqlException (0x80131904): Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding.``

### <a name="systemdataentitycoreentityexception-the-underlying-provider-failed-on-open"></a>System.Data.Entity.Core.EntityException: O fornecedor subjacente falhou no Open

``System.Data.Entity.Core.EntityException: The underlying provider failed on Open. -> System.Data.SqlClient.SqlException: Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding. -> System.ComponentModel.Win32Exception: The wait operation timed out``

### <a name="cannot-connect-to--server-name-"></a>Não é possível ligar-se ao < nome do servidor >

``Cannot connect to <server name>.ADDITIONAL INFORMATION:Connection Timeout Expired. The timeout period elapsed during the post-login phase. The connection could have timed out while waiting for server to complete the login process and respond; Or it could have timed out while attempting to create multiple active connections. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=231; handshake=983; [Login] initialization=0; authentication=0; [Post-Login] complete=13000; (Microsoft SQL Server, Error: -2) For help, click: http://go.microsoft.com/fwlink?ProdName=Microsoft%20SQL%20Server&EvtSrc=MSSQLServer&EvtID=-2&LinkId=20476 The wait operation timed out``

Estas exceções podem ocorrer quer por questões de ligação ou consulta. Para confirmar que este erro é causado por problemas de conectividade, consulte [Confirmar se um erro é causado por um problema](#confirm-whether-an-error-is-caused-by-a-connectivity-issue)de conectividade .

Os intervalos de ligação ocorrem porque a aplicação não pode ligar-se ao servidor. Para resolver este problema, experimente os passos (na ordem apresentada) nas Etapas para corrigir a secção de problemas de [ligação comuns.](#steps-to-fix-common-connection-issues)

## <a name="resource-governance-errors"></a>Erros de governação de recursos

### <a name="error-10928-resource-id-d"></a>Erro 10928: Id de recurso: %d

``10928: Resource ID: %d. The %s limit for the database is %d and has been reached. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. The Resource ID value in error message indicates the resource for which limit has been reached. For sessions, Resource ID = 2.``

Para contornar esta questão, experimente um dos seguintes métodos:

* Verifique se existem consultas de longa duração.

  > [!NOTE]
  > Esta é uma abordagem minimalista que pode não resolver a questão.

1. Faça a seguinte consulta SQL para verificar a visão [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) para ver quaisquer pedidos de bloqueio:

   ```sql
   SELECT * FROM dm_exec_requests
   ```

2. Determine o **tampão** de entrada para o bloqueador de cabeça.
3. Sintonize a consulta do bloqueador de cabeças.

   Para um procedimento aprofundado de resolução de problemas, vê se [a minha consulta está a correr bem na nuvem?](https://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx)

Se a base de dados atingir o seu limite de forma consistente, apesar de abordar consultas de bloqueio e de longa duração, considere atualizar para uma edição com [edições](https://azure.microsoft.com/pricing/details/sql-database/)mais recursos).

Para obter mais informações sobre pontos de vista dinâmicos de gestão, consulte as vistas dinâmicas de [gestão do Sistema.](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views)

Para obter mais informações sobre os limites da base de dados, consulte os limites de recursos de base de [dados SQL para o servidor de base de dados Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server).

### <a name="error-10929-resource-id-1"></a>Erro 10929: Id de recurso: 1

``10929: Resource ID: 1. The %s minimum guarantee is %d, maximum limit is %d and the current usage for the database is %d. However, the server is currently too busy to support requests greater than %d for this database. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. Otherwise, please try again later.``

### <a name="error-40501-the-service-is-currently-busy"></a>Erro 40501: O serviço está atualmente ocupado

``40501: The service is currently busy. Retry the request after 10 seconds. Incident ID: %ls. Code: %d.``

Trata-se de um erro de estrangulamento do motor, uma indicação de que os limites dos recursos estão a ser ultrapassados.

Para obter mais informações sobre os limites de recursos, consulte [os limites](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server)de recursos do servidor base de dados .

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
   ORDER BY [Table Size (MB)] DESC
   ```

2. Se o tamanho atual não exceder o tamanho máximo suportado para a sua edição, pode utilizar a BASE DE DADOS ALTER para aumentar a definição MAXSIZE.
3. Se a base de dados já tiver ultrapassado o tamanho máximo suportado para a sua edição, experimente um ou mais dos seguintes passos:

   * Realizar atividades normais de limpeza de bases de dados. Por exemplo, limpe os dados indesejados utilizando truncate/eliminação ou desenvolva dados utilizando o SQL Server Integration Services (SSIS) ou o utilitário do programa de cópias a granel (bcp).
   * Partição ou eliminação de dados, queda de índices ou consulte a documentação para eventuais resoluções.
   * Para a escala de base de dados, consulte os recursos de base de [dados individuais scale](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-scale) e [scale elásticos recursos](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool-scale)de piscina .

### <a name="error-40549-session-is-terminated-because-you-have-a-long-running-transaction"></a>Erro 40549: A sessão é terminada porque tem uma transação de longa duração

``40549: Session is terminated because you have a long-running transaction. Try shortening your transaction.``

Se encontrar repetidamente este erro, tente resolver o problema seguindo estes passos:

1. Verifique a vista sys.dm_exec_requests para ver quaisquer sessões abertas que tenham um valor elevado para a coluna total_elapsed_time. Execute esta verificação executando o seguinte script SQL:

   ```sql
   SELECT * FROM dm_exec_requests
   ```

2. Determine o tampão de entrada para a consulta de longa duração.
3. Sintonize a consulta.

Considere também a possibilidade de fazer as suas consultas. Para obter informações sobre o loteamento, consulte como utilizar o loteamento para melhorar o desempenho da aplicação da Base de [Dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-use-batching-to-improve-performance).

Para um procedimento aprofundado de resolução de problemas, vê se [a minha consulta está a correr bem na nuvem?](https://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx)

### <a name="error-40551-the-session-has-been-terminated-because-of-excessive-tempdb-usage"></a>Erro 40551: A sessão foi encerrada devido ao uso excessivo de TEMPDB

``40551: The session has been terminated because of excessive TEMPDB usage. Try modifying your query to reduce the temporary table space usage.``

Para contornar esta questão, siga estes passos:

1. Altere as consultas para reduzir o uso temporário do espaço da mesa.
2. Larguem objetos temporários depois de já não serem necessários.
3. Truncate tabelas ou remova mesas não utilizadas.

### <a name="error-40552-the-session-has-been-terminated-because-of-excessive-transaction-log-space-usage"></a>Erro 40552: A sessão foi encerrada devido ao uso excessivo do espaço de registo de transações

``40552: The session has been terminated because of excessive transaction log space usage. Try modifying fewer rows in a single transaction.``

Para resolver este problema, experimente os métodos seguintes:

* O problema pode ocorrer devido à inserção, atualização ou eliminação de operações.
Tente reduzir o número de linhas que são operadas imediatamente implementando lotação ou dividindo-se em várias transações menores.
* A questão pode ocorrer devido a operações de reconstrução de índices. Para contornar esta questão, certifique-se do número de linhas que são afetadas na tabela * (tamanho médio do campo atualizado em bytes + 80) < 2 gigabytes (GB).

  > [!NOTE]
  > Para uma reconstrução do índice, o tamanho médio do campo atualizado deve ser substituído pelo tamanho médio do índice.

### <a name="error-40553-the-session-has-been-terminated-because-of-excessive-memory-usage"></a>Erro 40553: A sessão foi encerrada devido ao uso excessivo da memória

``40553 : The session has been terminated because of excessive memory usage. Try modifying your query to process fewer rows.``

Para contornar esta questão, tente otimizar a consulta.

Para um procedimento aprofundado de resolução de problemas, vê se [a minha consulta está a correr bem na nuvem?](https://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx)

### <a name="table-of-additional-resource-governance-error-messages"></a>Tabela de mensagens adicionais de erro de governação de recursos

| Código de erro | Gravidade | Descrição |
| ---:| ---:|:--- |
| 10928 |20 |Id de recurso: %d. O limite %s para a base de dados é %d e foi atingido. Para mais informações, consulte os limites de recursos da Base de [Dados SQL para bases de dados únicas e reunidas](sql-database-resource-limits-database-server.md).<br/><br/>O ID de recurso indica o recurso que atingiu o limite. Para os fios dos trabalhadores, o ID de recurso = 1. Para sessões, o ID de recurso = 2.<br/><br/>Para obter mais informações sobre este erro e como resolvê-lo, consulte: <br/>&bull;&nbsp; [Limites de recursos](sql-database-resource-limits-database-server.md) do servidor de dados<br/>&bull;&nbsp; [Limites baseados em DTU para bases de dados únicas](sql-database-service-tiers-dtu.md)<br/>&bull;&nbsp; [Limites baseados em DTU para piscinas elásticas](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [limites baseados em vCore para bases de dados únicas](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp; [limites baseados em vCore para piscinas elásticas](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [Limites de recursos de instância geridos.](sql-database-managed-instance-resource-limits.md) |
| 10929 |20 |Id de recurso: %d. A garantia mínima %s é %d, o limite máximo é %d, e a utilização atual para a base de dados é %d. No entanto, o servidor está atualmente demasiado ocupado para suportar pedidos superiores a %d para esta base de dados. O ID de recurso indica o recurso que atingiu o limite. Para os fios dos trabalhadores, o ID de recurso = 1. Para sessões, o ID de recurso = 2. Para obter mais informações, consulte: <br/>&bull;&nbsp; [Limites de recursos](sql-database-resource-limits-database-server.md) do servidor de dados<br/>&bull;&nbsp; [Limites baseados em DTU para bases de dados únicas](sql-database-service-tiers-dtu.md)<br/>&bull;&nbsp; [Limites baseados em DTU para piscinas elásticas](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [limites baseados em vCore para bases de dados únicas](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp; [limites baseados em vCore para piscinas elásticas](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [Limites de recursos de instância geridos.](sql-database-managed-instance-resource-limits.md) <br/>Caso contrário, por favor, tente de novo mais tarde. |
| 40544 |20 |A base de dados atingiu a sua quota de tamanho. Partição ou eliminação de dados, queda de índices ou consulte a documentação para eventuais resoluções. Para a escala de base de dados, consulte os recursos de base de [dados individuais scale](sql-database-single-database-scale.md) e [scale elásticos recursos](sql-database-elastic-pool-scale.md)de piscina .|
| 40549 |16 |A sessão é encerrada porque tem uma transação de longa duração. Tente encurtar a sua transação. Para obter informações sobre o loteamento, consulte como utilizar o loteamento para melhorar o desempenho da aplicação da Base de [Dados SQL](sql-database-use-batching-to-improve-performance.md).|
| 40550 |16 |A sessão foi encerrada porque adquiriu demasiados cadeados. Tente ler ou modificar menos linhas numa única transação. Para obter informações sobre o loteamento, consulte como utilizar o loteamento para melhorar o desempenho da aplicação da Base de [Dados SQL](sql-database-use-batching-to-improve-performance.md).|
| 40551 |16 |A sessão foi encerrada `TEMPDB` devido ao uso excessivo. Tente modificar a sua consulta para reduzir o uso temporário do espaço da mesa.<br/><br/>Se estiver a utilizar objetos `TEMPDB` temporários, conserve espaço na base de dados, largando objetos temporários depois de já não serem necessários pela sessão. Para obter mais informações sobre o uso temporário na Base de Dados SQL, consulte a base de [dados Tempdb na Base de Dados SQL](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).|
| 40552 |16 |A sessão foi encerrada devido ao uso excessivo do espaço de registo de transações. Tente modificar menos linhas numa única transação. Para obter informações sobre o loteamento, consulte como utilizar o loteamento para melhorar o desempenho da aplicação da Base de [Dados SQL](sql-database-use-batching-to-improve-performance.md).<br/><br/>Se executar inserções `bcp.exe` a granel `System.Data.SqlClient.SqlBulkCopy` utilizando o `-b batchsize` utilitário `BatchSize` ou a classe, tente utilizar as ou opções para limitar o número de linhas copiadas para o servidor em cada transação. Se estiver a reconstruir um `ALTER INDEX` índice com `REBUILD WITH ONLINE = ON` a declaração, tente utilizar a opção. Para obter informações sobre os tamanhos de registo de transações para o modelo de compra vCore, consulte: <br/>&bull;&nbsp; [limites baseados em vCore para bases de dados únicas](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp; [limites baseados em vCore para piscinas elásticas](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [Limites de recursos de instância geridos.](sql-database-managed-instance-resource-limits.md)|
| 40553 |16 |A sessão foi encerrada devido ao uso excessivo da memória. Tente modificar a sua consulta para processar menos linhas.<br/><br/>A redução `ORDER BY` do `GROUP BY` número de operações e operações no seu código Transact-SQL reduz os requisitos de memória da sua consulta. Para a escala de base de dados, consulte os recursos de base de [dados individuais scale](sql-database-single-database-scale.md) e [scale elásticos recursos](sql-database-elastic-pool-scale.md)de piscina .|

## <a name="elastic-pool-errors"></a>Erros na piscina elástica

Os seguintes erros estão relacionados com a criação e utilização de piscinas elásticas:

| Código de erro | Gravidade | Descrição | Ação corretiva |
|:--- |:--- |:--- |:--- |
| 1132 | 17 |A piscina elástica atingiu o seu limite de armazenamento. O uso de armazenamento para a piscina elástica não pode exceder (%d) MBs. Tentando escrever dados numa base de dados quando o limite de armazenamento da piscina elástica tiver sido atingido. Para obter informações sobre os limites de recursos, consulte: <br/>&bull;&nbsp; [Limites baseados em DTU para piscinas elásticas](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [limites baseados em vCore para piscinas elásticas.](sql-database-vcore-resource-limits-elastic-pools.md) <br/> |Considere aumentar as DTUs e/ou adicionar armazenamento ao pool elástico, se possível, para aumentar o seu limite de armazenamento, reduzir o armazenamento utilizado por bases de dados individuais dentro da piscina elástica ou remover bases de dados da piscina elástica. Para a escala elástica da piscina, consulte [os recursos da piscina elástica scale](sql-database-elastic-pool-scale.md).|
| 10929 | 16 |A garantia mínima %s é %d, o limite máximo é %d, e a utilização atual para a base de dados é %d. No entanto, o servidor está atualmente demasiado ocupado para suportar pedidos superiores a %d para esta base de dados. Para obter informações sobre os limites de recursos, consulte: <br/>&bull;&nbsp; [Limites baseados em DTU para piscinas elásticas](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [limites baseados em vCore para piscinas elásticas.](sql-database-vcore-resource-limits-elastic-pools.md) <br/> Caso contrário, por favor, tente de novo mais tarde. DTU / vCore min por base de dados; DTU / vCore max por base de dados. O número total de trabalhadores simultâneos (pedidos) em todas as bases de dados da piscina elástica tentou ultrapassar o limite da piscina. |Considere aumentar as DTUs ou vCores do pool elástico, se possível, para aumentar o seu limite de trabalhador, ou remover bases de dados da piscina elástica. |
| 40844 | 16 |A base de dados '%ls' no Server '%ls' é uma base de dados de edição '%ls' numa piscina elástica e não pode ter uma relação de cópia contínua.  |N/D |
| 40857 | 16 |Piscina elástica não encontrada para servidor: '%ls', nome de piscina elástica: '%ls'. O conjunto elástico especificado não existe no servidor especificado. | Forneça um nome de piscina elástica válido. |
| 40858 | 16 |A piscina elástica '%ls' já existe no servidor: '%ls'. O conjunto elástico especificado já existe no servidor de base de dados SQL especificado. | Forneça um novo nome de piscina elástica. |
| 40859 | 16 |A piscina elástica não suporta o nível de serviço '%ls'. O nível de serviço especificado não é suportado para o fornecimento de piscina elástica. |Forneça a edição correta ou deixe o nível de serviço em branco para utilizar o nível de serviço predefinido. |
| 40860 | 16 |A combinação elástica '%ls' e o objetivo de serviço '%ls' é inválida. A piscina elástica e o nível de serviço só podem ser especificados em conjunto se o tipo de recurso for especificado como 'ElasticPool'. |Especifique a combinação correta de piscina elástica e nível de serviço. |
| 40861 | 16 |A edição da base de dados '. *Ls' não pode ser diferente do elástico nível* de serviço de piscina que é '%. Não, não, não. A edição da base de dados é diferente do elástico do serviço de piscina. |Não especifique uma edição de base de dados diferente do nível de serviço de piscina elástica.  Note que a edição da base de dados não precisa de ser especificada. |
| 40862 | 16 |O nome da piscina elástica deve ser especificado se o objetivo de serviço de piscina elástica for especificado. O objetivo de serviço de piscina elástica não identifica exclusivamente uma piscina elástica. |Especifique o nome da piscina elástica se utilizar o objetivo de serviço de piscina elástica. |
| 40864 | 16 |As DTUs para a piscina elástica devem ser pelo menos (%d) DTUs para o nível de serviço '%.*ls'. Tentando definir as DTUs para a piscina elástica abaixo do limite mínimo. |Retry definindo as DTUs para a piscina elástica para pelo menos o limite mínimo. |
| 40865 | 16 |As DTUs para o pool elástico não podem exceder (%d) DTUs para o nível de serviço '%.*ls'. Tentando definir as DTUs para a piscina elástica acima do limite máximo. |Retry definindo as DTUs para a piscina elástica não maior do que o limite máximo. |
| 40867 | 16 |O DTU max por base de dados deve ser pelo menos (%d) para o nível de serviço '%.*ls'. Tentando definir o DTU max por base de dados abaixo do limite suportado. | Considere utilizar o nível de serviço de piscina elástica que suporta a configuração desejada. |
| 40868 | 16 |O DTU max por base de dados não pode exceder (%d) para o nível de serviço '%.*ls'. Tentando definir o DTU max por base de dados para além do limite suportado. | Considere utilizar o nível de serviço de piscina elástica que suporta a configuração desejada. |
| 40870 | 16 |O DTU min por base de dados não pode exceder (%d) para o nível de serviço '%.*ls'. Tentando definir o DTU min por base de dados para além do limite suportado. | Considere utilizar o nível de serviço de piscina elástica que suporta a configuração desejada. |
| 40873 | 16 |O número de bases de dados (%d) e DTU min por base de dados (%d) não pode exceder as DTUs da piscina elástica (%d). Tentando especificar DTU min para bases de dados na piscina elástica que excede as DTUs da piscina elástica. | Considere aumentar as DTUs da piscina elástica, ou diminuir o DTU min por base de dados, ou diminuir o número de bases de dados no pool elástico. |
| 40877 | 16 |Uma piscina elástica não pode ser eliminada a menos que não contenha quaisquer bases de dados. O conjunto elástico contém uma ou mais bases de dados e, portanto, não pode ser eliminado. |Retire as bases de dados da piscina elástica para eliminá-la. |
| 40881 | 16 |O pool elástico '%.*ls' atingiu o limite de contagem de bases de dados.  O limite de contagem de dados para o pool elástico não pode exceder (%d) para uma piscina elástica com DTUs (%d). Tentar criar ou adicionar base de dados a um pool elástico quando o limite de contagem de bases de dados do pool elástico tiver sido atingido. | Considere aumentar as DTUs do pool elástico, se possível, para aumentar o seu limite de base de dados, ou remover bases de dados do pool elástico. |
| 40889 | 16 |As DTUs ou o limite de armazenamento para a piscina elástica '%.*ls' não podem ser diminuídos, uma vez que isso não forneceria espaço de armazenamento suficiente para as suas bases de dados. Tentando diminuir o limite de armazenamento da piscina elástica abaixo do seu uso de armazenamento. | Considere reduzir o uso de armazenamento de bases de dados individuais na piscina elástica ou remover bases de dados da piscina de forma a reduzir as suas DTUs ou o seu limite de armazenamento. |
| 40891 | 16 |O DTU min por base de dados (%d) não pode exceder o DTU max por base de dados (%d). Tentando definir o DTU min por base de dados superior ao DTU max por base de dados. |Certifique-se de que o DTU min por base de dados não excede o dTU max por base de dados. |
| TBD | 16 |O tamanho do armazenamento de uma base de dados individual numa piscina elástica não pode exceder o tamanho máximo permitido por uma piscina elástica de nível de serviço '%.*ls'. O tamanho máximo da base de dados excede o tamanho máximo permitido pelo nível de serviço de piscina elástica. |Descoloque o tamanho máximo da base de dados dentro dos limites do tamanho máximo permitido pelo nível de serviço de piscina elástica. |

## <a name="cannot-open-database-master-requested-by-the-login-the-login-failed"></a>Não é possível abrir a base de dados "mestre" solicitada pelo login. O login falhou

Este problema ocorre porque a conta não tem permissão para aceder à base de dados principal. Mas, por padrão, o SQL Server Management Studio (SSMS) tenta ligar-se à base de dados principal.

Para resolver este problema, siga estes passos:

1. No ecrã de login do SSMS, selecione **Opções**, e, em seguida, selecione Propriedades de **Ligação**.
2. No campo **Connect to database,** introduza o nome de base de dados predefinido do utilizador como base de dados de login predefinido e, em seguida, selecione **Connect**.

   ![Propriedades de ligação](media/troubleshoot-connectivity-issues-microsoft-azure-sql-database/cannot-open-database-master.png)

## <a name="confirm-whether-an-error-is-caused-by-a-connectivity-issue"></a>Confirme se um erro é causado por um problema de conectividade

Para confirmar se um erro é causado por um problema de conectividade, reveja o traço da pilha para quadros que mostrem chamadas para abrir uma ligação como as seguintes (note a referência à classe **SqlConnection):**

```
System.Data.SqlClient.SqlConnection.TryOpen(TaskCompletionSource`1 retry)
 at System.Data.SqlClient.SqlConnection.Open()
 at AzureConnectionTest.Program.Main(String[] args)
ClientConnectionId:<Client connection ID>
```

Quando a exceção for desencadeada por problemas de consulta, irá notar uma pilha de chamadas semelhante à seguinte (note a referência à classe **SqlCommand).** Nesta situação, [sintonize as suas consultas.](https://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx)

```
  at System.Data.SqlClient.SqlCommand.ExecuteReader()
  at AzureConnectionTest.Program.Main(String[] args)
  ClientConnectionId:<Client ID>
```

Para obter orientações adicionais sobre o desempenho da afinação, consulte os seguintes recursos:

* [Como manter os índices e estatísticas Azure SQL](https://techcommunity.microsoft.com/t5/Azure-Database-Support-Blog/How-to-maintain-Azure-SQL-Indexes-and-Statistics/ba-p/368787)
* [Desempenho de consulta de melodia manual na Base de Dados Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-performance-guidance)
* [Monitorizar o desempenho Azure SQL Database utilizando pontos de vista dinâmicos de gestão](https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-with-dmvs)
* [Operando a Loja de Consultas na Base de Dados Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-operate-query-store)

## <a name="steps-to-fix-common-connection-issues"></a>Steps to fix common connection issues (Passos para corrigir problemas de ligação comuns)

1. Certifique-se de que o TCP/IP está ativado como um protocolo de cliente no servidor de aplicações. Para mais informações, consulte os protocolos do [cliente Configure](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-client-protocols). Nos servidores de aplicações onde não tem ferramentas SQL Server instaladas, verifique se o TCP/IP está ativado através da execução de **cliconfg.exe** (utilitário de rede de clientes do Servidor SQL).
2. Verifique a cadeia de ligação da aplicação para se certificar de que está configurada corretamente. Por exemplo, certifique-se de que a cadeia de ligação especifica a porta correta (1433) e o nome do servidor totalmente qualificado.
Consulte [obter informações de ligação do Servidor SQL](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms#get-sql-server-connection-information).
3. Tente aumentar o valor do tempo de ligação. Recomendamos a utilização de um tempo de ligação de pelo menos 30 segundos.
4. Teste a conectividade entre o servidor de aplicações e a base de dados Azure SQL utilizando o Estúdio de [gestão do Servidor SQL (SSMS)](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms), um ficheiro UDL, ping ou telnet. Para mais informações, consulte problemas de [conectividade sQL Server](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server) e [diagnósticos para problemas](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues#diagnostics)de conectividade .

   > [!NOTE]
   > Como um passo de resolução de problemas, você também pode testar conectividade em um computador cliente diferente.

5. Como uma boa prática, certifique-se de que a lógica de retry está no lugar. Para obter mais informações sobre a lógica de retry, consulte [falhas transitórias de Troubleshoot e erros](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues)de ligação à Base de Dados SQL .

Se estes passos não resolverem o seu problema, tente recolher mais dados e, em seguida, contacte o suporte. Se a sua aplicação for um serviço na nuvem, ative o registo. Este passo devolve um carimbo de tempo UTC da falha. Além disso, o SQL Azure devolve o ID de rastreio. Os [Serviços](https://azure.microsoft.com/support/options/) de Apoio ao Cliente da Microsoft podem utilizar estas informações.

Para obter mais informações sobre como ativar o registo de registos, consulte [O registo de diagnósticos para aplicações no Serviço de Aplicações Azure](https://azure.microsoft.com/documentation/articles/web-sites-enable-diagnostic-log/).

## <a name="next-steps"></a>Passos seguintes

* [Arquitetura de conectividade Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture)
* [Controlos de acesso à rede Azure SQL e Data Warehouse](https://docs.microsoft.com/azure/sql-database/sql-database-networkaccess-overview)
