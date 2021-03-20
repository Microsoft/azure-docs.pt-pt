---
title: Manuseie os procedimentos armazenados de longa duração no conector SQL
description: Como lidar com os procedimentos armazenados que se apagam quando se utiliza o conector SQL em Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: camerost, logicappspm
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: f5b04c563dc81497f591788dc4890d379c0f898f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93103113"
---
# <a name="handle-stored-procedure-timeouts-in-the-sql-connector-for-azure-logic-apps"></a>Manuseie os intervalos de tempo do procedimento armazenado no conector SQL para apps Azure Logic

Quando a sua aplicação lógica funciona com conjuntos de resultados tão grandes que o [conector SQL](../connectors/connectors-create-api-sqlazure.md) não devolve todos os resultados ao mesmo tempo, ou se quiser mais controlo sobre o tamanho e estrutura para os seus conjuntos de resultados, pode criar um [procedimento armazenado](/sql/relational-databases/stored-procedures/stored-procedures-database-engine) que organize os resultados da forma que deseja. O conector SQL fornece muitas funcionalidades de backend a que pode aceder utilizando [aplicações Azure Logic](../logic-apps/logic-apps-overview.md) para que possa automatizar mais facilmente tarefas de negócio que funcionam com tabelas de base de dados SQL.

Por exemplo, ao obter ou inserir várias linhas, a sua aplicação lógica pode iterar através destas linhas utilizando um loop [ **Until**](../logic-apps/logic-apps-control-flow-loops.md#until-loop) dentro [destes limites](../logic-apps/logic-apps-limits-and-config.md). No entanto, quando a sua aplicação lógica tem de funcionar com milhares ou milhões de linhas, pretende minimizar os custos resultantes das chamadas para a base de dados. Para obter mais informações, consulte [lidar com os dados a granel utilizando o conector SQL](../connectors/connectors-create-api-sqlazure.md#handle-bulk-data).

<a name="timeout-limit"></a>

## <a name="timeout-limit-on-stored-procedure-execution"></a>Limite de prazo para execução de procedimentos armazenados

O conector SQL tem um prazo de tempo de procedimento armazenado inferior a [2 minutos.](/connectors/sql/#known-issues-and-limitations) Alguns procedimentos armazenados podem demorar mais tempo do que este limite a ser concluído, causando um `504 Timeout` erro. Por vezes, estes processos de longa duração são codificados como procedimentos armazenados explicitamente para este fim. Devido ao limite de tempo, chamar estes procedimentos de Azure Logic Apps pode criar problemas. Embora o conector SQL não suporte de forma nativa um modo assíncronos, pode trabalhar em torno deste problema e simular este modo usando um gatilho de conclusão SQL, consulta de passagem SQL nativa, uma tabela de estado e trabalhos do lado do servidor. Para esta tarefa, pode utilizar o [Agente De Trabalho Elástico Azure](../azure-sql/database/elastic-jobs-overview.md) para [a Base de Dados Azure SQL](../azure-sql/database/sql-database-paas-overview.md). Para [o SQL Server nas instalações](/sql/sql-server/sql-server-technical-documentation) e [para o Azure SQL Managed Instance,](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)pode utilizar o [SqL Server Agent](/sql/ssms/agent/sql-server-agent).

Por exemplo, suponha que tem o seguinte procedimento armazenado de longa duração, que demora mais do que o limite de tempo para terminar de correr. Se executar este procedimento armazenado a partir de uma aplicação lógica utilizando o conector SQL, obtém-se um `HTTP 504 Gateway Timeout` erro como resultado.

```sql
CREATE PROCEDURE [dbo].[WaitForIt]
   @delay char(8) = '00:03:00'
AS
BEGIN
   SET NOCOUNT ON;
   WAITFOR DELAY @delay
END
```

Em vez de ligar diretamente para o procedimento armazenado, pode executar o procedimento de forma assíncronea em segundo plano utilizando um *agente de trabalho*. Pode armazenar as entradas e saídas numa tabela de estado com a quais pode interagir através da sua aplicação lógica. Se não precisar das entradas e saídas, ou se já estiver a escrever os resultados para uma tabela no procedimento armazenado, pode simplificar esta abordagem.

> [!IMPORTANT]
> Certifique-se de que o seu procedimento armazenado e todos os trabalhos são *idempotentes*, o que significa que podem correr várias vezes sem afetar os resultados. Se o processamento assíncronos falhar ou esgotar o tempo, o agente de trabalho pode voltar a tentar o passo, e assim o seu procedimento armazenado, várias vezes. Para evitar a duplicação de saídas, antes de criar quaisquer objetos, reveja estas [boas práticas e abordagens](../azure-sql/database/elastic-jobs-overview.md#idempotent-scripts).

A secção seguinte descreve como pode utilizar o Agente De Trabalho Elástico Azure para a Base de Dados Azure SQL. Para o SQL Server e Azure SQL Managed Instance, pode utilizar o Agente do Servidor SQL. Alguns detalhes de gestão serão diferentes, mas os passos fundamentais permanecem os mesmos que a criação de um agente de trabalho para a Azure SQL Database.

<a name="azure-sql-database"></a>

## <a name="job-agent-for-azure-sql-database"></a>Agente de trabalho para Azure SQL Database

Para criar um trabalho que possa executar o procedimento armazenado para [Azure SQL Database,](../azure-sql/database/sql-database-paas-overview.md)utilize o [Agente De Trabalho Elástico Azure](../azure-sql/database/elastic-jobs-overview.md). Crie o seu agente de trabalho no portal Azure. Esta abordagem irá adicionar vários procedimentos armazenados à base de dados que é usada pelo agente, também conhecido como base *de dados* de agente. Em seguida, pode criar um trabalho que executa o seu procedimento armazenado na base de dados-alvo e captura a saída quando terminada.

Antes de poder criar o trabalho, tem de configurar permissões, grupos e alvos, conforme descrito pela [documentação completa do Agente De Trabalho Elástico Azure](../azure-sql/database/elastic-jobs-overview.md). Também é necessário criar uma tabela de suporte na base de dados-alvo, tal como descrito nas seguintes secções.

<a name="create-state-table"></a>

### <a name="create-state-table-for-registering-parameters-and-storing-inputs"></a>Criar tabela de estado para registar parâmetros e armazenar entradas

O SqL Agent Jobs não aceita parâmetros de entrada. Em vez disso, na base de dados-alvo, crie uma tabela de estado onde registe os parâmetros e guarde as entradas para utilizar para ligar para os seus procedimentos armazenados. Todas as diligências de trabalho do agente vão contra a base de dados do alvo, mas os procedimentos armazenados do trabalho vão contra a base de dados do agente. 

Para criar a tabela do Estado, use este esquema:

```sql
CREATE TABLE [dbo].[LongRunningState](
   [jobid] [uniqueidentifier] NOT NULL,
   [rowversion] [timestamp] NULL,
   [parameters] [nvarchar](max) NULL,
   [start] [datetimeoffset](7) NULL,
   [complete] [datetimeoffset](7) NULL,
   [code] [int] NULL,
   [result] [nvarchar](max) NULL,
   CONSTRAINT [PK_LongRunningState] PRIMARY KEY CLUSTERED
      (   [jobid] ASC
      )WITH (STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF) ON [PRIMARY]
      ) ON [PRIMARY] TEXTIMAGE_ON [PRIMARY]
```

Eis como a tabela resultante fica no [SQL Server Management Studio (SMSS)](/sql/ssms/download-sql-server-management-studio-ssms):

![Screenshot que mostra tabela estatal criada que armazena entradas para procedimento armazenado.](media/handle-long-running-stored-procedures-sql-connector/state-table-input-parameters.png)

Para garantir um bom desempenho e certificar-se de que o trabalho do agente pode encontrar o registo associado, a tabela utiliza o ID de execução de trabalho `jobid` () como a chave primária. Se quiser, também pode adicionar colunas individuais para os parâmetros de entrada. O esquema previamente descrito pode lidar mais geralmente com vários parâmetros, mas limita-se ao tamanho calculado por `NVARCHAR(MAX)` .

<a name="create-top-level-job"></a>

### <a name="create-a-top-level-job-to-run-the-stored-procedure"></a>Criar um trabalho de alto nível para executar o procedimento armazenado

Para executar o procedimento armazenado de longa duração, crie este agente de trabalho de alto nível na base de dados do agente:

```sql
EXEC jobs.sp_add_job 
   @job_name='LongRunningJob',
   @description='Execute Long-Running Stored Proc',
   @enabled = 1
```

Agora, adicione passos ao trabalho que parametrize, corra e complete o procedimento armazenado. Por defeito, um passo de trabalho acaba depois de 12 horas. Se o seu procedimento armazenado necessitar de mais tempo, ou se pretender que o procedimento aussa mais cedo, pode alterar o `step_timeout_seconds` parâmetro para outro valor especificado em segundos. Por predefinição, um passo tem 10 retries incorporados com um intervalo de tempo entre cada reagem, que pode usar a seu favor.

Aqui estão os passos a acrescentar:

1. Aguarde que os parâmetros apareçam na `LongRunningState` tabela.

   Este primeiro passo aguarda que os parâmetros sejam adicionados na `LongRunningState` tabela, o que acontece logo após o início do trabalho. Se o ID de execução de trabalho `jobid` não for adicionado à `LongRunningState` tabela, o passo simplesmente falha, e o tempo de retenção padrão ou tempo de atraso faz a espera:

   ```sql
   EXEC jobs.sp_add_jobstep
      @job_name='LongRunningJob',
      @step_name= 'Parameterize WaitForIt',
      @step_timeout_seconds = 30,
      @command= N'
         IF NOT EXISTS(SELECT [jobid] FROM [dbo].[LongRunningState]
            WHERE jobid = $(job_execution_id))
            THROW 50400, ''Failed to locate call parameters (Step1)'', 1',
      @credential_name='JobRun',
      @target_group_name='DatabaseGroupLongRunning'
   ```

1. Consultar os parâmetros da tabela do estado e passá-los para o procedimento armazenado. Este passo também executa o procedimento em segundo plano. 

   Se o seu procedimento armazenado não precisar de parâmetros, basta ligar diretamente para o procedimento armazenado. Caso contrário, para passar o `@timespan` parâmetro, use o `@callparams` , que também pode estender para passar parâmetros adicionais.

   ```sql
   EXEC jobs.sp_add_jobstep
      @job_name='LongRunningJob',
      @step_name='Execute WaitForIt',
      @command=N'
         DECLARE @timespan char(8)
         DECLARE @callparams NVARCHAR(MAX)
         SELECT @callparams = [parameters] FROM [dbo].[LongRunningState]
            WHERE jobid = $(job_execution_id))
         SET @timespan = @callparams
         EXECUTE [dbo].[WaitForIt] @delay = @timespan', 
      @credential_name='JobRun',
      @target_group_name='DatabaseGroupLongRunning'
   ```

1. Complete o trabalho e grave os resultados.

   ```sql
   EXEC jobs.sp_add_jobstep
      @job_name='LongRunningJob',
      @step_name='Complete WaitForIt',
      @command=N'
         UPDATE [dbo].[LongRunningState]
            SET [complete] = GETUTCDATE(),
               [code] = 200,
               [result] = ''Success''
            WHERE jobid = $(job_execution_id)',
      @credential_name='JobRun',
      @target_group_name='DatabaseGroupLongRunning'
   ```

<a name="start-job-pass-parameters"></a>

### <a name="start-the-job-and-pass-the-parameters"></a>Comece o trabalho e passe os parâmetros

Para iniciar o trabalho, utilize uma consulta nativa passthrough com a [ **ação de consulta DO SQL**](/connectors/sql/#execute-a-sql-query-(v2)) e empurre imediatamente os parâmetros do trabalho para a mesa do estado. Para fornecer entrada ao `jobid` atributo na tabela-alvo, a Logic Apps adiciona um **Para cada** laço que itera através da saída da tabela a partir da ação anterior. Para cada ID de execução de trabalho, executa uma ação **de inserir linha** que utiliza a saída dinâmica de `ResultSets JobExecutionId` dados, para adicionar os parâmetros para a tarefa desempacotar e passar para o procedimento armazenado do alvo.

![Screenshot que mostra ações a usar para iniciar o trabalho e passar parâmetros para o procedimento armazenado.](media/handle-long-running-stored-procedures-sql-connector/start-job-actions.png)

Quando o trabalho termina, o trabalho atualiza a tabela de modo a `LongRunningState` poder ativar facilmente o resultado utilizando o gatilho [ **Quando um item é modificado** .](/connectors/sql/#when-an-item-is-modified-(v2)) Se não precisar da saída, ou se já tiver um gatilho que monitorize uma tabela de saída, pode saltar esta peça.

![Screenshot que mostra o gatilho SQL para quando um item é modificado.](media/handle-long-running-stored-procedures-sql-connector/trigger-on-results-after-job-completes.png)

<a name="sql-on-premises-or-managed-instance"></a>

## <a name="job-agent-for-sql-server-or-azure-sql-managed-instance"></a>Agente de trabalho para SQL Server ou Azure SQL Managed Instance

Para o mesmo cenário, pode utilizar o [SqL Server Agent](/sql/ssms/agent/sql-server-agent) para [o SQL Server nas instalações](/sql/sql-server/sql-server-technical-documentation) e [a Azure SQL Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md). Embora alguns detalhes de gestão diferam, os passos fundamentais permanecem os mesmos que a criação de um agente de trabalho para a Azure SQL Database.

## <a name="next-steps"></a>Passos seguintes

[Ligue ao SQL Server, Azure SQL Database ou Azure SQL Managed Instance](../connectors/connectors-create-api-sqlazure.md)

