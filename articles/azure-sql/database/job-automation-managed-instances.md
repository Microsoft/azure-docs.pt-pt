---
title: Automação de emprego com empregos de agente SQL em Azure SQL Gestd Instance
description: Opções de automatização para executar scripts Transact-SQL (T-SQL) em Azure SQL Managed Instance
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=1
dev_langs:
- TSQL
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.date: 02/01/2021
ms.openlocfilehash: 3be01c304a40317e0d21baf6789ef1376cd89b80
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104608083"
---
# <a name="automate-management-tasks-using-sql-agent-jobs-in-azure-sql-managed-instance"></a>Automatizar tarefas de gestão utilizando empregos de agente SQL em Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Utilizando [o SqL Server Agent](/sql/ssms/agent/sql-server-agent) em SQL Server e [SQL Managed Instance,](../../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)pode criar e agendar trabalhos que podem ser executados periodicamente contra uma ou muitas bases de dados para executar consultas Transact-SQL (T-SQL) e executar tarefas de manutenção. Este artigo introduziu o SqL Agent for SQL Managed Instance.

> [!Note]
> O SqL Agent não está disponível na Base de Dados Azure SQL ou na Azure Synapse Analytics. Em vez disso, recomendamos [a automatização de empregos com trabalhos elásticos.](job-automation-overview.md)

### <a name="sql-agent-job-limitations-in-azure-sql-managed-instance"></a>Limitações de emprego do Agente SQL em instância gerida Azure SQL

Vale a pena notar as diferenças entre o Agente SQL disponível no SQL Server e como parte da SQL Managed Instance. Para obter mais informações sobre as diferenças de funcionalidades suportadas entre o SQL Server e o SQL Managed Instance, consulte [as diferenças de T-SQL de Instância Gerida Azure SQL do SQL Server](../../azure-sql/managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent). 

Algumas das funcionalidades do Agente SQL que estão disponíveis no SQL Server não são suportadas em SQL Managed Instance:

- As definições do SqL Agent são lidas apenas. 
    - O procedimento armazenado do sistema `sp_set_agent_properties` não é suportado em SQL Managed Instance.
- Ativar/desativar o Agente SQL não é atualmente suportado em SQL Managed Instance. O SQL Agent está sempre em execução.
- As notificações são parcialmente suportadas:
  - Pager não é suportado.
  - NetSend não é suportado.
  - Os alertas não são suportados.
- Os proxies não são apoiados.
- O eventlog não é suportado.
- O gatilho do horário de trabalho baseado num CPU ocioso não é suportado.

## <a name="when-to-use-sql-agent-jobs"></a>Quando usar empregos de agente SQL 

Existem vários cenários em que se pode usar empregos de Agente SQL:

- Automatizar tarefas de gestão e agendar-lhes para correr todos os dias da semana, fora de horas, etc.
  - Implemente alterações de esquema, gestão de credenciais, recolha de dados de desempenho ou recolha de telemetria do inquilino (cliente).
  - Atualizar dados de referência (informações comuns em todas as bases de dados), carregar dados do armazenamento de Azure Blob.
  - Tarefas de manutenção comuns, incluindo DBCC CHECKDB, para garantir a integridade dos dados ou a manutenção do índice para melhorar o desempenho da consulta. Configure tarefas para serem executadas numa coleção de bases de dados recorrentemente, como, por exemplo, fora das horas de ponta.
  - Recolha os resultados da consulta a partir de um conjunto de bases de dados numa tabela central de forma contínua. As consultas de desempenho podem ser continuamente executadas e configuradas para acionar tarefas adicionais para serem executadas.
- Recolher dados para relatórios
  - Dados agregados de uma recolha de bases de dados numa única tabela de destino.
  - Execute consultas de processamento de dados de execução mais longa num grande conjunto de bases de dados, por exemplo, a coleção de telemetria de cliente. Os resultados são recolhidos para uma tabela de destino única para análise adicional.
- Movimentos de dados
  - Crie empregos que reproduzam alterações escontes feitas nas suas bases de dados para outras bases de dados ou recolha de atualizações es feitas em bases de dados remotas e aplique alterações na base de dados.
  - Crie empregos que carreguem dados de ou para as suas bases de dados utilizando serviços de integração de servidores SQL (SSIS).

## <a name="sql-agent-jobs-in-azure-sql-managed-instance"></a>Empregos de agente SQL em Azure SQL geridos instância

Sql Agent Jobs são executados pelo serviço SQL Agent que continua a ser usado para automatização de tarefas em SQL Server e SQL Managed Instance. 

SQL Agent Jobs é uma série especificada de scripts T-SQL na sua base de dados. Utilize postos de trabalho para definir uma tarefa administrativa que possa ser executada uma ou mais vezes e monitorizada para o sucesso ou insucesso. 

Um trabalho pode ser executado num servidor local ou em vários servidores remotos. SQL Agent Jobs é um componente interno do Motor de Base de Dados que é executado dentro do serviço SQL Managed Instance.

Existem vários conceitos-chave no SQL Agent Jobs:

- **Passos** de trabalho definidos de um ou muitos passos que devem ser executados dentro do trabalho. Para cada passo de trabalho pode definir a estratégia de retíria e a ação que deve acontecer se o passo de trabalho for bem sucedido ou falhado.
- **Os horários** definem quando o trabalho deve ser executado.
- **As notificações** permitem definir regras que serão usadas para notificar os operadores por e-mail assim que o trabalho estiver concluído.

### <a name="sql-agent-job-steps"></a>Passos de trabalho do Agente SQL

Os passos de trabalho do agente SQL são sequências de ações que o Agente SQL deve executar. Cada passo tem o passo seguinte que deve ser executado se o passo for bem sucedido ou falhar, número de retrações em caso de falha.

O SqL Agent permite-lhe criar diferentes tipos de etapas de trabalho, tais como etapas de trabalho Transact-SQL que executam um único lote Transact-SQL contra a base de dados, ou passos de comando/PowerShell que podem executar scripts de [trabalho personalizados, passos de trabalho SSIS](../../data-factory/how-to-invoke-ssis-package-managed-instance-agent.md) que lhe permitem carregar dados usando o tempo de execução SSIS, ou passos de [replicação](../managed-instance/replication-transactional-overview.md) que podem publicar alterações da sua base de dados para outras bases de dados.

> [!Note]
> Para obter mais informações sobre a utilização do tempo de execução da integração do Azure SSIS com o SSISDB hospedado pela Azure SQL Managed Instance, consulte [Use Azure SQL Managed Instance with SQL Server Integration Services (SSIS) in Azure Data Factory](../../data-factory/how-to-use-sql-managed-instance-with-ir.md).

[A replicação transacional](../managed-instance/replication-transactional-overview.md) pode replicar as alterações das suas tabelas em outras bases de dados em Azure SQL Managed Instance, Azure SQL Database ou SQL Server. Para obter informações, consulte a replicação de [configuração em Azure SQL Managed Instance](../../azure-sql/managed-instance/replication-between-two-instances-configure-tutorial.md). 

Outros tipos de passos de trabalho não são atualmente suportados em SQL Managed Instance, incluindo:

- O passo de trabalho de replicação de fusão não é suportado.
- O Leitor de Fila não é suportado.
- Os Serviços de Análise não são suportados
 
### <a name="sql-agent-job-schedules"></a>Horários de trabalho do Agente SQL

Um horário especifica quando um trabalho funciona. Mais de um trabalho pode funcionar no mesmo horário, e mais de um horário pode aplicar-se ao mesmo trabalho.

Um horário pode definir as seguintes condições para o momento em que um trabalho funciona:

- Sempre que o SqL Server Agent começa. O trabalho é ativado depois de cada falha.
- Uma vez, numa data e hora específicas, o que é útil para atrasar a execução de algum trabalho.
- Num horário recorrente.

> [!Note]
> A SQL Managed Instance não lhe permite iniciar um trabalho quando a CPU está inativa.

### <a name="sql-agent-job-notifications"></a>Notificações de emprego do Agente SQL

Sql Agent Jobs permite-lhe receber notificações quando o trabalho termina com sucesso ou falha. Pode receber notificações por e-mail.

Se ainda não estiver ativado, primeiro terá de configurar a [funcionalidade De Correio de Base de Dados](/sql/relational-databases/database-mail/database-mail) em Azure SQL Managed Instance:

```sql
GO
EXEC sp_configure 'show advanced options', 1;
GO
RECONFIGURE;
GO
EXEC sp_configure 'Database Mail XPs', 1;
GO
RECONFIGURE
```

Como um exercício de exemplo, crie a conta de e-mail que será usada para enviar as notificações de e-mail. Atribua a conta ao perfil de e-mail chamado `AzureManagedInstance_dbmail_profile` . Para enviar e-mails utilizando trabalhos de agente SQL em SQL Managed Instance, deve haver um perfil que deve ser chamado `AzureManagedInstance_dbmail_profile` . Caso contrário, a SQL Managed Instance não poderá enviar e-mails através do SqL Agent. Consulte a seguinte amostra:

```sql
-- Create a Database Mail account
EXECUTE msdb.dbo.sysmail_add_account_sp
    @account_name = 'SQL Agent Account',
    @description = 'Mail account for Azure SQL Managed Instance SQL Agent system.',
    @email_address = '$(loginEmail)',
    @display_name = 'SQL Agent Account',
    @mailserver_name = '$(mailserver)' ,
    @username = '$(loginEmail)' ,
    @password = '$(password)';

-- Create a Database Mail profile
EXECUTE msdb.dbo.sysmail_add_profile_sp
    @profile_name = 'AzureManagedInstance_dbmail_profile',
    @description = 'E-mail profile used for messages sent by Managed Instance SQL Agent.';

-- Add the account to the profile
EXECUTE msdb.dbo.sysmail_add_profileaccount_sp
    @profile_name = 'AzureManagedInstance_dbmail_profile',
    @account_name = 'SQL Agent Account',
    @sequence_number = 1;
```

Teste a configuração do Correio da Base de Dados via T-SQL utilizando o procedimento armazenado [sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) do sistema:

```sql
DECLARE @body VARCHAR(4000) = 'The email is sent from ' + @@SERVERNAME;
EXEC msdb.dbo.sp_send_dbmail  
    @profile_name = 'AzureManagedInstance_dbmail_profile',  
    @recipients = 'ADD YOUR EMAIL HERE',  
    @body = 'Add some text',  
    @subject = 'Azure SQL Instance - test email';  
```

Pode notificar o operador de que algo aconteceu com os seus trabalhos de Agente SQL. Um operador define informações de contacto para um indivíduo responsável pela manutenção de um ou mais casos em SQL Managed Instance. Por vezes, as responsabilidades dos operadores são atribuídas a um indivíduo.

Em sistemas com múltiplas instâncias em SQL Managed Instance ou SQL Server, muitos indivíduos podem partilhar responsabilidades do operador. Um operador não contém informações de segurança e não define um princípio de segurança. Idealmente, um operador não é um indivíduo cujas responsabilidades podem mudar, mas um grupo de distribuição de e-mails.   

Pode [criar operadores](/sql/relational-databases/system-stored-procedures/sp-add-operator-transact-sql) utilizando o SQL Server Management Studio (SSMS) ou o script Transact-SQL mostrado no seguinte exemplo:

```sql
EXEC msdb.dbo.sp_add_operator
    @name=N'AzureSQLTeam',
    @enabled=1,
    @email_address=N'AzureSQLTeamn@contoso.com';
```

Confirme o sucesso ou falha do e-mail através do [Registo de Correio de Base de Dados](/sql/relational-databases/database-mail/database-mail-log-and-audits) em SSMS.

Em seguida, pode [modificar qualquer trabalho de Agente SQL](/sql/relational-databases/system-stored-procedures/sp-update-job-transact-sql) e atribuir operadores que serão notificados por e-mail se o trabalho completar, falhar ou tiver sucesso usando SSMS ou o seguinte script Transact-SQL:

```sql
EXEC msdb.dbo.sp_update_job @job_name=N'Load data using SSIS',
    @notify_level_email=3, -- Options are: 1 on succeed, 2 on failure, 3 on complete
    @notify_email_operator_name=N'AzureSQLTeam';
```

### <a name="sql-agent-job-history"></a>Histórico de trabalho do Agente SQL

A Azure SQL Managed Instance atualmente não permite alterar quaisquer propriedades do Agente SQL porque estão armazenadas nos valores de registo subjacentes. Isto significa que as opções de ajuste da política de retenção do Agente para registos de histórico de emprego são fixadas à padrão de 1000 registos totais e registos máximos de 100 registos de história por trabalho.

### <a name="sql-agent-fixed-database-role-membership"></a>Membro da função de papel de base de dados fixa do Agente SQL

Se os utilizadores ligados a logins não sysadmin forem adicionados a qualquer uma das três funções de base de dados fixas do SqL Agent na base de dados do sistema MSDB, existe um problema em que as permissões explícitas do EXECUTE devem ser concedidas aos procedimentos de armazenagem principal para que estes logins funcionem. Se este problema for encontrado, será mostrada a mensagem de erro "A permissão EXECUTE foi negada no <object_name> do objeto (Microsoft SQL Server, Error: 229)" será mostrada. 

Uma vez adicionados utilizadores a uma função de base de dados fixa SQL Agent (SQLAgentUserRole, SQLAgentReaderRole ou SQLAgentOperatorRole) em MSDB, para cada um dos logins adicionados a estas funções, execute o script abaixo do T-SQL para conceder explicitamente permissões EXECUTE aos procedimentos armazenados do sistema listados. Este exemplo pressupõe que o nome de utilizador e o nome de login são os mesmos. 

```sql
USE [master]
GO
CREATE USER [login_name] FOR LOGIN [login_name];
GO
GRANT EXECUTE ON master.dbo.xp_sqlagent_enum_jobs TO [login_name];
GRANT EXECUTE ON master.dbo.xp_sqlagent_is_starting TO [login_name];
GRANT EXECUTE ON master.dbo.xp_sqlagent_notify TO [login_name];
```

## <a name="learn-more"></a>Saiba mais

- [O que é Azure SQL Managed Instance?](../managed-instance/sql-managed-instance-paas-overview.md)
- [Quais as novidades na Base de Dados Azure SQL & SQL Managed Instance?](../../azure-sql/database/doc-changes-updates-release-notes.md?tabs=managed-instance)
- [Azure SQL Gestão DeSação de T-SQL diferenças do SQL Server](../../azure-sql/managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)
- [Comparação de características: Azure SQL Database e Azure SQL Managed Instance](../../azure-sql/database/features-comparison.md)
