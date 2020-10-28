---
title: Automatização de tarefas
description: Utilize a Automação de Emprego para executar scripts Transact-SQL (T-SQL) através de um conjunto de uma ou mais bases de dados
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/10/2020
ms.openlocfilehash: 7ecd7e847a91847db8f57c640a374dc329fce7ea
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92782948"
---
# <a name="automate-management-tasks-using-database-jobs"></a>Automatizar tarefas de gestão utilizando trabalhos de base de dados
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Você pode criar e agendar trabalhos que podem ser executados periodicamente contra uma ou muitas bases de dados para executar consultas Transact-SQL (T-SQL) e executar tarefas de manutenção.

Pode definir uma base de dados ou grupos de bases de dados de destino onde a tarefa será executada e, além disso, definir horários para executar uma tarefa.
Uma tarefa processa a tarefa de início de sessão na base de dados de destino. Também pode definir, manter e persistir os scripts Transact-SQL para serem executados num grupo de bases de dados.

Cada tarefa regista o estado de execução e, além disso, repete as operações automaticamente caso ocorra alguma falha.

## <a name="when-to-use-automated-jobs"></a>Quando utilizar trabalhos automatizados

Existem vários cenários em que se pode utilizar a automação de emprego:

- Automatizar tarefas de gestão e agendar-lhes para correr todos os dias da semana, fora de horas, etc.
  - Implemente alterações de esquema, gestão de credenciais, recolha de dados de desempenho ou recolha de telemetria do inquilino (cliente).
  - Atualizar dados de referência (informações comuns em todas as bases de dados), carregar dados do armazenamento de Azure Blob.
  - Reconstrua índices para melhorar o desempenho das consultas. Configure tarefas para serem executadas numa coleção de bases de dados recorrentemente, como, por exemplo, fora das horas de ponta.
  - Recolha os resultados da consulta a partir de um conjunto de bases de dados numa tabela central de forma contínua. As consultas de desempenho podem ser continuamente executadas e configuradas para acionar tarefas adicionais para serem executadas.
- Recolher dados para relatórios
  - Dados agregados de uma recolha de bases de dados numa única tabela de destino.
  - Execute consultas de processamento de dados de execução mais longa num grande conjunto de bases de dados, por exemplo, a coleção de telemetria de cliente. Os resultados são recolhidos para uma tabela de destino única para análise adicional.
- Movimentos de dados
  - Crie empregos que reproduzam alterações escontes feitas nas suas bases de dados para outras bases de dados ou recolha de atualizações es feitas em bases de dados remotas e aplique alterações na base de dados.
  - Crie empregos que carreguem dados de ou para as suas bases de dados utilizando serviços de integração de servidores SQL (SSIS).

## <a name="overview"></a>Descrição geral

Estão disponíveis as seguintes tecnologias de agendamento de emprego:

- **SQL Agent Jobs** são componentes de agendamento de trabalho sql server clássicos e testados em batalha que estão disponíveis em Azure SQL Managed Instance. Sql Agent Jobs não estão disponíveis na Base de Dados Azure SQL.
- **Elastic Database Jobs (pré-visualização)** são serviços de Agendamento de Emprego que executam trabalhos personalizados em uma ou muitas bases de dados na Base de Dados Azure SQL.

Vale a pena notar algumas diferenças entre o Agente SQL (disponível no local e como parte do SQL Managed Instance) e o agente de trabalho elástico da base de dados (disponível para bases de dados únicas na Base de Dados Azure SQL e bases de dados em Azure Synapse Analytics).

| |Tarefas Elásticas |Agente SQL |
|---------|---------|---------|
|**Âmbito** | Qualquer número de bases de dados na Base de Dados Azure SQL e/ou armazéns de dados na mesma nuvem Azure que o agente de trabalho. Os alvos podem estar em diferentes servidores, subscrições e/ou regiões. <br><br>Os grupos de destino podem ser compostos por bases de dados individuais ou armazéns de dados, ou por todas as bases de dados num servidor, conjunto ou por shardmaps (enumerados dinamicamente durante a execução da tarefa). | Qualquer base de dados individual no mesmo caso que o agente SQL. |
|**APIs e Ferramentas suportadas** | Portal, PowerShell, T-SQL, Azure Resource Manager | T-SQL, SQL Server Management Studio (SSMS) |

## <a name="sql-agent-jobs"></a>Tarefas de SQL Agent

SQL Agent Jobs é uma série especificada de scripts T-SQL na sua base de dados. Utilize postos de trabalho para definir uma tarefa administrativa que possa ser executada uma ou mais vezes e monitorizada para o sucesso ou insucesso.
Um trabalho pode ser executado num servidor local ou em vários servidores remotos. SQL Agent Jobs é um componente interno do Motor de Base de Dados que é executado dentro do serviço De Instância Gerida.
Existem vários conceitos-chave no SQL Agent Jobs:

- **Passos** de trabalho definidos de um ou muitos passos que devem ser executados dentro do trabalho. Para cada passo de trabalho pode definir a estratégia de retíria e a ação que deve acontecer se o passo de trabalho for bem sucedido ou falhado.
- **Os horários** definem quando o trabalho deve ser executado.
- **As notificações** permitem definir regras que serão usadas para notificar os operadores por e-mail assim que o trabalho estiver concluído.

### <a name="job-steps"></a>Passos de trabalho

Os passos de trabalho do agente SQL são sequências de ações que o Agente SQL deve executar. Cada passo tem o passo seguinte que deve ser executado se o passo for bem sucedido ou falhar, número de retrações em caso de falha.

O SqL Agent permite-lhe criar diferentes tipos de etapas de trabalho, tais como etapas de trabalho Transact-SQL que executam um único lote Transact-SQL contra a base de dados, ou passos de comando/PowerShell que podem executar scripts de trabalho personalizados, passos de trabalho SSIS que lhe permitem carregar dados usando o tempo de execução SSIS, ou passos de [replicação](../managed-instance/replication-transactional-overview.md) que podem publicar alterações da sua base de dados para outras bases de dados.

[A replicação transacional](../managed-instance/replication-transactional-overview.md) é uma funcionalidade do Motor de Base de Dados que permite publicar as alterações efetivas feitas numa ou várias tabelas numa base de dados e publicá-las/distribuí-las por um conjunto de bases de dados de assinantes. A publicação das alterações é implementada utilizando os seguintes tipos de etapas de trabalho do Agente SQL:

- Leitor de registo de transações.
- Uma foto.
- O distribuidor.

Outros tipos de passos de trabalho não são atualmente suportados, incluindo:

- O passo de trabalho de replicação de fusão não é suportado.
- O Leitor de Fila não é suportado.
- Os Serviços de Análise não são suportados

### <a name="job-schedules"></a>Agendas de tarefas

Um horário especifica quando um trabalho funciona. Mais de um trabalho pode funcionar no mesmo horário, e mais de um horário pode aplicar-se ao mesmo trabalho.
Um horário pode definir as seguintes condições para o momento em que um trabalho funciona:

- Sempre que a instância for reiniciada (ou quando o Agente do Servidor SQL começa). O trabalho é ativado depois de cada falha.
- Uma vez, numa data e hora específicas, o que é útil para atrasar a execução de algum trabalho.
- Num horário recorrente.

> [!Note]
> A sql Managed Instance não lhe permite iniciar um trabalho quando a instância é "ocioso".

### <a name="job-notifications"></a>Notificações de emprego

Sql Agent Jobs permite-lhe receber notificações quando o trabalho termina com sucesso ou falha. Pode receber notificações por e-mail.

Em primeiro lugar, deverá configurar a conta de e-mail que será utilizada para enviar as notificações de e-mail e atribuir a conta ao perfil de e-mail chamado `AzureManagedInstance_dbmail_profile` , como mostra a seguinte amostra:

```sql
-- Create a Database Mail account
EXECUTE msdb.dbo.sysmail_add_account_sp
    @account_name = 'SQL Agent Account',
    @description = 'Mail account for Azure SQL Managed Instance SQL Agent system.',
    @email_address = '$(loginEmail)',
    @display_name = 'SQL Agent Account',
    @mailserver_name = '$(mailserver)' ,
    @username = '$(loginEmail)' ,
    @password = '$(password)'

-- Create a Database Mail profile
EXECUTE msdb.dbo.sysmail_add_profile_sp
    @profile_name = 'AzureManagedInstance_dbmail_profile',
    @description = 'E-mail profile used for messages sent by Managed Instance SQL Agent.' ;

-- Add the account to the profile
EXECUTE msdb.dbo.sysmail_add_profileaccount_sp
    @profile_name = 'AzureManagedInstance_dbmail_profile',
    @account_name = 'SQL Agent Account',
    @sequence_number = 1;
```

Também necessitaria de ativar o Correio da Base de Dados em Casos Geridos:

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

Pode notificar o operador de que algo aconteceu com os seus trabalhos de Agente SQL. Um operador define informações de contacto para um indivíduo responsável pela manutenção de um ou mais casos em SQL Managed Instance. Por vezes, as responsabilidades dos operadores são atribuídas a um indivíduo.
Em sistemas com múltiplas instâncias em SQL Managed Instance ou SQL Server, muitos indivíduos podem partilhar responsabilidades do operador. Um operador não contém informações de segurança e não define um princípio de segurança.

Pode criar operadores utilizando SSMS ou o script Transact-SQL mostrado no seguinte exemplo:

```sql
EXEC msdb.dbo.sp_add_operator
    @name=N'Mihajlo Pupun',
    @enabled=1,
    @email_address=N'mihajlo.pupin@contoso.com'
```

Pode modificar qualquer trabalho e atribuir operadores que serão notificados por e-mail se o trabalho completar, falhar ou tiver sucesso usando SSMS ou o seguinte script Transact-SQL:

```sql
EXEC msdb.dbo.sp_update_job @job_name=N'Load data using SSIS',
    @notify_level_email=3, -- Options are: 1 on succeed, 2 on failure, 3 on complete
    @notify_email_operator_name=N'Mihajlo Pupun'
```

### <a name="sql-agent-job-limitations"></a>Limitações de emprego do agente SQL

Algumas das funcionalidades do Agente SQL que estão disponíveis no SQL Server não são suportadas em Instância Gerida:

- As definições do SqL Agent são lidas apenas. O procedimento `sp_set_agent_properties` não é suportado em Instância Gerida.
- Ativar/desativar o Agente SQL não é atualmente suportado em Instância Gerida. O SQL Agent está sempre em execução.
- As notificações são parcialmente suportadas
  - Pager não é suportado.
  - NetSend não é suportado.
  - Os alertas não são suportados.
- Os proxies não são apoiados.
- O eventlog não é suportado.

Para obter informações sobre o Agente do Servidor [SQL,](/sql/ssms/agent/sql-server-agent)consulte o Agente do Servidor SQL .

## <a name="elastic-database-jobs-preview"></a>Trabalhos de base de dados elásticos (pré-visualização)

As **Tarefas de Base de Dados Elástica** permitem executar um ou mais scripts T-SQL em paralelo, num grande número de bases de dados, com base num agendamento ou a pedido.

**Executar tarefas em qualquer combinação de bases de dados** : uma ou mais bases de dados individuais, todas as bases de dados num servidor, todas as bases de dados num conjunto elástico, ou shardmap, com a flexibilidade adicional para incluir ou excluir qualquer base de dados específica. **As tarefas podem ser executadas em vários servidores, vários conjuntos e podem até ser executadas em bases de dados em subscrições diferentes.** Os servidores e os conjuntos são dinamicamente enumerados em runtime, para que as tarefas sejam executadas em todas as bases de dados que existem no grupo de destino no momento da execução.

A imagem seguinte mostra um agente de tarefa a executar tarefas nos diferentes tipos de grupos de destino:

![Modelo conceptual de agente de Tarefa Elástica](./media/job-automation-overview/conceptual-diagram.png)

### <a name="elastic-job-components"></a>Componentes de Tarefa Elástica

|Componente | Descrição (abaixo da tabela encontram-se detalhes adicionais) |
|---------|---------|
|[**Agente de Tarefa Elástica**](#elastic-job-agent) | O recurso do Azure que cria para executar e gerir tarefas. |
|[**Base de dados da tarefa**](#job-database) | Uma base de dados na Base de Dados Azure SQL que o agente de trabalho utiliza para armazenar dados relacionados com o trabalho, definições de emprego, etc. |
|[**Grupo de destino**](#target-group) | O conjunto de servidores, conjuntos, bases de dados e mapas de shard no qual executar uma tarefa. |
|[**Tarefa**](#job) | Um trabalho é uma unidade de trabalho que é composta por uma ou mais etapas de [trabalho.](#job-step) Os passos de tarefa especificam o script T-SQL a executar, bem como outros detalhes necessários para executar o script. |

#### <a name="elastic-job-agent"></a>Agente de Tarefa Elástica

Um agente de Tarefa Elástica é o recurso do Azure para criar, executar e gerir tarefas. O agente de Tarefa Elástica é um recurso do Azure que o utilizador cria no portal (o [PowerShell](elastic-jobs-powershell-create.md) e o REST também são suportados).

A criação de um **agente de trabalho elástico** requer uma base de dados existente na Base de Dados Azure SQL. O agente configura esta base de dados existente como a [*Base de dados da tarefa*](#job-database).

O agente de Tarefa Elástica é gratuito. A base de dados de emprego é faturada ao mesmo ritmo que qualquer base de dados na Base de Dados Azure SQL.

#### <a name="job-database"></a>Base de dados da tarefa

A *Base de dados da tarefa* serve para definir tarefas e controlar o estado e o histórico de execuções de tarefas. A *base de dados Job* também é usada para armazenar metadados de agentes, registos, resultados, definições de emprego, e também contém muitos procedimentos armazenados úteis e outros objetos de base de dados para criar, executar e gerir empregos usando T-SQL.

Para a pré-visualização atual, é necessária uma base de dados existente na Base de Dados Azure SQL (S0 ou superior) para criar um agente De serviço elástico.

A *base de dados job* não precisa literalmente de ser nova, mas deve ser um objetivo de serviço limpo, vazio, S0 ou superior. O objetivo de serviço recomendado da base de *dados job* é S1 ou superior, mas a escolha ideal depende das necessidades de desempenho do seu(s): o número de passos de emprego, o número de alvos de emprego e a frequência com que os empregos são executados. Por exemplo, uma base de dados S0 pode ser suficiente para um agente de trabalho que gere poucos empregos por hora direcionando menos de dez bases de dados, mas gerir um trabalho a cada minuto pode não ser rápido o suficiente com uma base de dados S0, e um nível de serviço mais elevado pode ser melhor.

Se as operações contra a base de dados de trabalho forem mais lentas do que o esperado, [monitorize](monitor-tune-overview.md#azure-sql-database-and-azure-sql-managed-instance-resource-monitoring) o desempenho da base de dados e a utilização do recurso na base de dados de trabalho durante períodos de lentidão utilizando o portal Azure ou o [DMV sys.dm_db_resource_stats.](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) Se a utilização de um recurso, como CPU, Data IO ou Log Write se aproximar a 100% e se correlacionar com períodos de lentidão, considere aumentar gradualmente a base de dados para objetivos de serviço mais elevados (quer no [modelo DTU,](service-tiers-dtu.md) quer no [modelo vCore)](service-tiers-vcore.md)até que o desempenho da base de dados de emprego seja suficientemente melhorado.

##### <a name="job-database-permissions"></a>Permissões da base de dados da tarefa

Durante a criação do agente de tarefa, um esquema, tabelas e uma função chamada *jobs_reader* são criados na *Base de dados da tarefa* . A função é criada com as seguintes permissões e tem como objetivo dar aos administradores um melhor controlo de acesso para a monitorização de tarefas:

|Nome da função |permissões de esquema "jobs" |permissões de esquema "jobs_internal" |
|---------|---------|---------|
|**jobs_reader** | SELECIONAR | Nenhum |

> [!IMPORTANT]
> Considere as implicações de segurança antes de conceder acesso à *Base de dados da tarefa* como um administrador da base de dados. Um utilizador mal intencionado com permissões para criar ou editar tarefas podia criar ou editar uma tarefa que utilize uma credencial armazenada para ligar a uma base de dados sob o controlo do utilizador mal intencionado, o que podia permitir que o utilizador mal intencionado determinasse a palavra-passe da credencial.

#### <a name="target-group"></a>Grupo de destino

Um *grupo de destino* define o conjunto de bases de dados onde será executado um passo de tarefa. Um grupo de destino pode conter qualquer número e combinação dos seguintes elementos:

- **Servidor lógico DO SQL** - se um servidor for especificado, todas as bases de dados existentes no servidor no momento da execução do trabalho fazem parte do grupo. A credencial da base de dados mestra tem de ser fornecida para que o grupo possa ser enumerado e atualizado antes da execução da tarefa.
- **Conjunto elástico** - se for especificado um conjunto elástico, todas as bases de dados que estão no conjunto elástico no momento da execução da tarefa fazem parte do grupo. Tal como acontece para um servidor, a credencial da base de dados mestra tem de ser fornecida para que o grupo possa ser atualizado antes da execução da tarefa.
- **Base de dados individual** - especifique uma ou mais bases de dados individuais para fazerem parte do grupo.
- **Shardmap** - bases de dados de um shardmap.

> [!TIP]
> No momento da execução da tarefa, a *enumeração dinâmica* reavalia o conjunto de bases de dados nos grupos de destino que incluem servidores ou conjuntos. A enumeração dinâmica garante que as **tarefas são executadas em todas as bases de dados que existem no servidor ou conjunto no momento da execução da tarefa** . A reavaliação da lista de bases de dados em runtime é especificamente útil para cenários em que a associação do conjunto ou do servidor muda com frequência.

Os conjuntos e as bases de dados individuais podem ser especificados como estando incluídos ou excluídos do grupo. Isto permite criar um grupo de destino com qualquer combinação de bases de dados. Por exemplo, pode adicionar um servidor a um grupo de destino, mas excluir bases de dados específicas num conjunto elástico (ou excluir um conjunto completo).

Um grupo de destino pode incluir bases de dados em várias subscrições e em várias regiões. Lembre-se que as execuções em várias regiões têm uma latência superior do que as execuções na mesma região.

Os seguintes exemplos mostram como as diferentes definições de grupo de destino são enumeradas dinamicamente no momento da execução do trabalho, de modo a determinar as bases de dados que o trabalho vai executar:

![Exemplos de grupos de destino](./media/job-automation-overview/targetgroup-examples1.png)

O **Exemplo 1** mostra um grupo de destino que consiste numa lista de bases de dados individuais. Quando é executado um passo do trabalho com este grupo de destino, a ação desse passo será executada em cada uma dessas bases de dados.<br>
**O exemplo 2** mostra um grupo alvo que contém um servidor como alvo. Quando é executado um passo do trabalho com este grupo de destino, o servidor é enumerado dinamicamente para determinar a lista de bases de dados que estão, atualmente, no servidor. A ação desse passo será executada em cada uma dessas bases de dados.<br>
O **Exemplo 3** mostra um grupo de destino semelhante ao do *Exemplo 2* , mas é excluída especificamente uma base de dados individual. A ação do passo deste trabalho *não* será executada na base de dados excluída.<br>
O **Exemplo 4** mostra um grupo de destino que contém um conjunto elástico como o destino. Tal como no *Exemplo 2* , o conjunto será enumerado dinamicamente no momento de execução do trabalho para determinar a lista de bases de dados contidas no mesmo.
<br><br>

![Exemplos adicionais do grupo alvo](./media/job-automation-overview/targetgroup-examples2.png)

**Os exemplos 5** e **exemplo 6** mostram cenários avançados onde servidores, piscinas elásticas e bases de dados podem ser combinados usando incluir e excluir regras.<br>
O **Exemplo 7** mostra que as partições num mapa de partições também podem ser avaliadas no momento de execução do trabalho.

> [!NOTE]
> A base de dados job em si pode ser o alvo de um trabalho. Neste cenário, a base de dados Job é tratada como qualquer outra base de dados-alvo. O utilizador de emprego deve ser criado e concedido permissões suficientes na base de dados Job, e a credencial de pesquisa da base de dados para o utilizador de emprego também deve existir na base de dados job, tal como acontece com qualquer outra base de dados-alvo.
>

#### <a name="job"></a>Tarefa

Uma *tarefa* é uma unidade de trabalho que é executada com base num agendamento ou como uma tarefa ocasional. Uma tarefa é composta por um ou mais *passos de tarefa* .

##### <a name="job-step"></a>Passo de tarefa

Cada passo de tarefa especifica um script T-SQL a executar, um ou mais grupos de destino onde executar o script T-SQL e as credenciais de que o agente de tarefa precisa para ligar à base de dados de destino. Cada passo de tarefa tem políticas de repetição e de tempo limite personalizáveis e, opcionalmente, pode especificar parâmetros de saída.

#### <a name="job-output"></a>Saída da tarefa

O resultado dos passos de uma tarefa em cada base de dados de destino é registado pormenorizadamente e a saída do script pode ser capturada numa tabela especificada. Pode especificar uma base de dados para guardar os dados devolvidos por uma tarefa.

#### <a name="job-history"></a>Histórico de tarefas

Histórico de execuções da tarefa é armazenado na *Base de dados da tarefa* . Uma tarefa de limpeza do sistema remove o histórico de execuções com mais de 45 dias. Para remover o histórico com menos de 45 dias, chame o procedimento armazenado **sp_purge_history** na *Base de dados da tarefa* .

### <a name="agent-performance-capacity-and-limitations"></a>Desempenho, capacidade e limitações do agente

As Tarefas Elásticas utilizam recursos de computação mínimos enquanto aguardam pela conclusão de tarefas de execução longa.

Dependendo do tamanho do grupo de destino de bases de dados e do tempo de execução pretendido para uma tarefa (número de trabalhos em simultâneo), o agente requer diferentes quantidades de computação e desempenho da *Base de dados da tarefa* (quando mais destinos houver e quanto maior for o número de tarefas, maior é a quantidade de computação necessária).

Atualmente, a pré-visualização está limitada a 100 tarefas simultâneas.

#### <a name="prevent-jobs-from-reducing-target-database-performance"></a>Impedir que as tarefas reduzam o desempenho da base de dados de destino

Para garantir que os recursos não são sobrecarregados quando executar tarefas nas bases de dados num conjunto elástico de SQL, as tarefas podem ser configuradas para limitar o número de bases de dados nas quais uma tarefa pode ser executada ao mesmo tempo.

## <a name="next-steps"></a>Passos seguintes

- [O que é o Agente de Servidor SQL](/sql/ssms/agent/sql-server-agent)
- [Como criar e gerir trabalhos elásticos](elastic-jobs-overview.md)
- [Criar e gerir Tarefas Elásticas com o PowerShell](elastic-jobs-powershell-create.md)
- [Criar e gerir Tarefas Elásticas com o Transact-SQL (T-SQL)](elastic-jobs-tsql-create-manage.md)