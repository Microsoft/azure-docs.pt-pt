---
title: Introdução às tarefas de bases de dados elásticas | Documentos da Microsoft
description: Utilize tarefas de bases de dados elásticas para executar scripts T-SQL que abrangem várias bases de dados.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 6c77bb1f17f93e25605d251c717c2a4d61f940dd
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57313972"
---
# <a name="getting-started-with-elastic-database-jobs"></a>Introdução às tarefas de bases de dados elásticas

Tarefas de base de dados elástica (pré-visualização) para a base de dados do Azure SQL permite que execute com fiabilidade scripts T-SQL que abrangem várias bases de dados ao repetir automaticamente e o fornecimento de garantias de sua eventual conclusão. Para obter mais informações sobre a funcionalidade de trabalho de bases de dados elásticas, consulte [tarefas elásticas](sql-database-elastic-jobs-overview.md).

Este artigo estende a amostra encontrada na [introdução às ferramentas de bases de dados elásticas](sql-database-elastic-scale-get-started.md). Quando concluído, irá aprender a criar e gerir tarefas que gerem um grupo de bases de dados relacionados. Não é necessário utilizar as ferramentas de escala elástica para tirar o máximo partido dos benefícios das tarefas elásticas.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Transferir e executar o [introdução ao exemplo de ferramentas de bases de dados elásticas](sql-database-elastic-scale-get-started.md).

## <a name="create-a-shard-map-manager-using-the-sample-app"></a>Criar uma partição horizontal Gestor de mapas de utilizar a aplicação de exemplo

Aqui é criar um mapa de partições horizontais manager juntamente com vários fragmentos, seguido de inserção de dados em partições horizontais. Se já tiver partições horizontais que configurar com dados em partição horizontal nos mesmos, pode ignorar os passos seguintes e mover para a secção seguinte.

1. Criar e executar o **introdução às ferramentas de bases de dados elásticas** aplicação de exemplo. Siga os passos até o passo 7 na secção [transferir e executar a aplicação de exemplo](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app). No final do 7 passo, verá a seguinte linha de comandos:

   ![linha de comandos](./media/sql-database-elastic-query-getting-started/cmd-prompt.png)

2. Na janela de comandos, escreva "1" e prima **Enter**. Esta ação cria a partição horizontal Gestor de mapas e adiciona as partições dois horizontais para o servidor. Em seguida, escreva "3" e prima **Enter**; Repita esta ação quatro vezes. Isso insere as linhas de dados de exemplo em suas partições horizontais.
3. O [portal do Azure](https://portal.azure.com) deve mostrar três novas bases de dados:

   ![Confirmação do Visual Studio](./media/sql-database-elastic-query-getting-started/portal.png)

   Neste ponto, podemos criar uma coleção de banco de dados personalizado que reflete todas as bases de dados no mapa de partições horizontais. Isso permite criar e executar uma tarefa que adiciona uma nova tabela em partições horizontais.

Aqui estamos normalmente criaria um mapa de partições horizontais de destino, utilizando o **New-AzureSqlJobTarget** cmdlet. A base de dados de Gestor de mapas de partições horizontais tem de ser definido como um destino de base de dados e, em seguida, o mapa de partições horizontais específico é especificado como destino. Em vez disso, vamos enumerar todas as bases de dados no servidor e adicionar as bases de dados para a nova coleção personalizada, com exceção de base de dados mestra.

## <a name="creates-a-custom-collection-and-add-all-databases-in-the-server-to-the-custom-collection-target-with-the-exception-of-master"></a>Cria uma coleção personalizada e adicionar todas as bases de dados no servidor para o destino de coleção personalizada com a exceção principal

   ```Powershell
    $customCollectionName = "dbs_in_server"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $ResourceGroupName = "ddove_samples"
    $ServerName = "samples"
    $dbsinserver = Get-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName
    $dbsinserver | %{
    $currentdb = $_.DatabaseName
    $ErrorActionPreference = "Stop"
    Write-Output ""

    Try
    {
       New-AzureSqlJobTarget -ServerName $ServerName -DatabaseName $currentdb | Write-Output
    }
    Catch
    {
        $ErrorMessage = $_.Exception.Message
        $ErrorCategory = $_.CategoryInfo.Reason

        if ($ErrorCategory -eq 'UniqueConstraintViolatedException')
        {
             Write-Host $currentdb "is already a database target."
        }

        else
        {
            throw $_
        }

    }

    Try
    {
        if ($currentdb -eq "master")
        {
            Write-Host $currentdb "will not be added custom collection target" $CustomCollectionName "."
        }

        else
        {
            Add-AzureSqlJobChildTarget -CustomCollectionName $CustomCollectionName -ServerName $ServerName -DatabaseName $currentdb
            Write-Host $currentdb "was added to" $CustomCollectionName "."
        }

    }
    Catch
    {
        $ErrorMessage = $_.Exception.Message
        $ErrorCategory = $_.CategoryInfo.Reason

        if ($ErrorCategory -eq 'UniqueConstraintViolatedException')
        {
             Write-Host $currentdb "is already in the custom collection target" $CustomCollectionName"."
        }

        else
        {
            throw $_
        }
    }
    $ErrorActionPreference = "Continue"
   }
   ```
## <a name="create-a-t-sql-script-for-execution-across-databases"></a>Criar um Script T-SQL para execução em bases de dados
   ```
    $scriptName = "NewTable"
    $scriptCommandText = "
    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'Test')
    BEGIN
        CREATE TABLE Test(
            TestId INT PRIMARY KEY IDENTITY,
            InsertionTime DATETIME2
        );
    END
    GO
    INSERT INTO Test(InsertionTime) VALUES (sysutcdatetime());
    GO"

    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script
   ```

## <a name="create-the-job-to-execute-a-script-across-the-custom-group-of-databases"></a>Criar a tarefa para executar um script entre o grupo personalizado de bases de dados

   ```
    $jobName = "create on server dbs"
    $scriptName = "NewTable"
    $customCollectionName = "dbs_in_server"
    $credentialName = "ddove66"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job
   ```

## <a name="execute-the-job"></a>Executar a tarefa
O seguinte script do PowerShell pode ser utilizado para executar uma tarefa existente:

Atualize a seguinte variável para refletir o nome da tarefa pretendida foram executados:

   ```
    $jobName = "create on server dbs"
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName
    Write-Output $jobExecution
   ```

## <a name="retrieve-the-state-of-a-single-job-execution"></a>Obter o estado de uma execução de tarefa individual
Utilizar o mesmo **Get-AzureSqlJobExecution** cmdlet com o **IncludeChildren** parâmetro para ver o estado de execuções de tarefas de subordinado, ou seja, o estado específico para cada execução de tarefa em relação a cada base de dados visada pela tarefa.

   ```
    $jobExecutionId = "{Job Execution Id}"
    $jobExecutions = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId -IncludeChildren
    Write-Output $jobExecutions
   ```

## <a name="view-the-state-across-multiple-job-executions"></a>Ver o estado entre várias execuções de tarefas
O **Get-AzureSqlJobExecution** cmdlet tem vários parâmetros opcionais que podem ser usados para exibir várias execuções de tarefas, filtradas por meio dos parâmetros fornecidos. O seguinte exemplo demonstra Algumas das formas possíveis para utilizar Get-AzureSqlJobExecution:

Obter todas as execuções de tarefas ativas de nível superior:

   ```
    Get-AzureSqlJobExecution
   ```

Obter todas as execuções de tarefas de nível superior, incluindo as execuções de tarefas inativa:

   ```
    Get-AzureSqlJobExecution -IncludeInactive
   ```

Obter todas as execuções de tarefas de subordinado de um ID de execução de tarefa fornecida, incluindo as execuções de tarefas inativa:

   ```
    $parentJobExecutionId = "{Job Execution Id}"
    Get-AzureSqlJobExecution -AzureSqlJobExecution -JobExecutionId $parentJobExecutionId -IncludeInactive -IncludeChildren
   ```

Obter todas as execuções de tarefas criadas com base numa agenda / combinação, incluindo Inativas tarefas da tarefa:

   ```
    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Get-AzureSqlJobExecution -JobName $jobName -ScheduleName $scheduleName -IncludeInactive
   ```

Obter todas as tarefas de direcionamento um mapa de fragmentos especificado, incluindo tarefas Inativas:

   ```
    $shardMapServerName = "{Shard Map Server Name}"
    $shardMapDatabaseName = "{Shard Map Database Name}"
    $shardMapName = "{Shard Map Name}"
    $target = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName
    Get-AzureSqlJobExecution -TargetId $target.TargetId -IncludeInactive
   ```

Obter todas as tarefas de direcionamento de uma coleção personalizada especificada, incluindo tarefas Inativas:

   ```
    $customCollectionName = "{Custom Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    Get-AzureSqlJobExecution -TargetId $target.TargetId -IncludeInactive
   ```

Obter a lista de execuções de tarefas de tarefas dentro de uma execução de tarefa específica:

   ```
    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Write-Output $jobTaskExecutions
   ```

Obter detalhes de execução da tarefa de tarefa:

O seguinte script do PowerShell pode ser utilizado para ver os detalhes de uma execução de tarefas da tarefa, que é particularmente útil na depuração de falhas de execução.
   ```
    $jobTaskExecutionId = "{Job Task Execution Id}"
    $jobTaskExecution = Get-AzureSqlJobTaskExecution -JobTaskExecutionId $jobTaskExecutionId
    Write-Output $jobTaskExecution
   ```

## <a name="retrieve-failures-within-job-task-executions"></a>Recuperar de falhas dentro de execuções de tarefas de tarefas
O objeto de JobTaskExecution inclui uma propriedade para o ciclo de vida da tarefa, juntamente com uma propriedade da mensagem. Se uma execução de tarefas da tarefa falhou, a propriedade de ciclo de vida é definida como *falhada* e a propriedade Message é definida como a mensagem de exceção resultante e sua pilha. Se uma tarefa não foi concluída com êxito, é importante ver os detalhes das tarefas de tarefa que falhou para uma determinada tarefa.

   ```
    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Foreach($jobTaskExecution in $jobTaskExecutions)
        {
        if($jobTaskExecution.Lifecycle -ne 'Succeeded')
            {
            Write-Output $jobTaskExecution
            }
        }
   ```

## <a name="waiting-for-a-job-execution-to-complete"></a>A aguardar pela conclusão de uma execução de tarefa
O seguinte script do PowerShell pode ser utilizado para aguardar pela conclusão de uma tarefa de tarefa:

   ```
    $jobExecutionId = "{Job Execution Id}"
    Wait-AzureSqlJobExecution -JobExecutionId $jobExecutionId
   ```

## <a name="create-a-custom-execution-policy"></a>Criar uma política de execução personalizado
Tarefas elásticas da base de dados suporta a criação de políticas de execução personalizado que podem ser aplicadas quando a partir de tarefas.

Atualmente permitem definir políticas de execução:

* Nome: Identificador para a política de execução.
* Tempo limite da tarefa: Tempo total antes de uma tarefa é cancelada por tarefas elásticas da base de dados.
* Intervalo de repetição inicial: Intervalo de espera antes de primeira repetição.
* Intervalo de repetições máxima: Limite de intervalos de repetição a utilizar.
* Coeficiente de término do intervalo de repetição: Coeficiente utilizado para calcular o próximo intervalo entre repetições.  É utilizada a seguinte fórmula: (A inicial do intervalo entre tentativas) * Math.pow ((coeficiente de término de intervalo), (número de repetições) - 2).
* Máximo de tentativas: O número máximo de repetições tentou fazer dentro de uma tarefa.

A política de execução predefinido utiliza os seguintes valores:

* Nome: Política de execução predefinida
* Tempo limite da tarefa: uma semana
* Intervalo de repetição inicial:  100 milissegundos
* Intervalo de repetições máxima: 30 minutos
* Coeficiente de intervalo de repetição: 2
* Máximo de tentativas: 2,147,483,647

Crie a política de execução desejado:

   ```
    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 10
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $executionPolicy = New-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $executionPolicy
   ```

### <a name="update-a-custom-execution-policy"></a>Atualizar uma política de execução personalizado
Atualize a política de execução desejado para atualizar:

   ```
    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 15
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $updatedExecutionPolicy = Set-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $updatedExecutionPolicy
   ```

## <a name="cancel-a-job"></a>Cancelar uma tarefa

Tarefas elásticas da base de dados oferece suporte a solicitações de cancelamento de tarefas.  Se as tarefas de base de dados elásticas detectar uma solicitação de cancelamento de uma tarefa atualmente a ser executada, tenta parar a tarefa.

Existem duas formas diferentes, que as tarefas elásticas da base de dados pode efetuar um cancelamento:

1. Cancelando tarefas atualmente em execução: Se um cancelamento é detetado enquanto uma tarefa está atualmente em execução, um cancelamento for tentado no aspecto atualmente em execução da tarefa.  Por exemplo: Se houver uma consulta de execução longa atualmente a ser realizada quando um cancelamento é tentado, há uma tentativa de cancelar a consulta.
2. A cancelar a tarefa de repetições: Se um cancelamento é detetado pelo thread controlo antes de uma tarefa é iniciada para execução, o thread de controle evita a iniciar a tarefa e declara o pedido como canceladas.

Se é pedido um cancelamento de tarefa para uma tarefa principal, a solicitação de cancelamento é honrada para a tarefa principal e para todas as tarefas de subordinado.

Para submeter um pedido de cancelamento, utilize o **Stop-AzureSqlJobExecution** cmdlet e defina a **JobExecutionId** parâmetro.

   ```Powershell
    $jobExecutionId = "{Job Execution Id}"
    Stop-AzureSqlJobExecution -JobExecutionId $jobExecutionId
   ```

## <a name="delete-a-job-by-name-and-the-jobs-history"></a>Eliminar uma tarefa por nome e o histórico da tarefa

Tarefas elásticas da base de dados suporta a eliminação assíncrona de tarefas. Uma tarefa pode ser marcada para eliminação e o sistema elimina a tarefa e o respetivo histórico de tarefas depois de concluíram todas as execuções de tarefas para a tarefa. O sistema não cancela automaticamente as execuções de tarefas do Active Directory.  

Em vez disso, Stop-AzureSqlJobExecution tem de ser invocado para cancelar as execuções de tarefas do Active Directory.

Para acionar a eliminação da tarefa, utilize o **Remove-AzureSqlJob** cmdlet e defina a **JobName** parâmetro.

   ```
    $jobName = "{Job Name}"
    Remove-AzureSqlJob -JobName $jobName
   ```

## <a name="create-a-custom-database-target"></a>Criar um destino de base de dados personalizada
Destinos de banco de dados personalizados podem ser definidos nas tarefas de bases de dados elásticas, que podem ser utilizadas para a execução diretamente ou para inclusão dentro de um grupo de base de dados personalizada. Uma vez que **conjuntos elásticos** não são ainda diretamente suportadas por meio das APIs do PowerShell, basta criar um destino de banco de dados personalizado e o destino de coleção de banco de dados personalizado que abrange todas as bases de dados no conjunto.

Defina as variáveis seguintes para refletir as informações de banco de dados desejado:

   ```
    $databaseName = "{Database Name}"
    $databaseServerName = "{Server Name}"
    New-AzureSqlJobDatabaseTarget -DatabaseName $databaseName -ServerName $databaseServerName
   ```

## <a name="create-a-custom-database-collection-target"></a>Criar um destino de coleção de base de dados personalizada
Um destino de coleção de base de dados personalizada pode ser definido para permitir a execução em vários destinos de bases de dados definidos. Depois de criar um grupo de base de dados, bases de dados podem ser associados para o destino de coleção personalizada.

Defina as variáveis seguintes para refletir a configuração de destino pretendido de coleção personalizada:

   ```
    $customCollectionName = "{Custom Database Collection Name}"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName
   ```

### <a name="add-databases-to-a-custom-database-collection-target"></a>Adicionar bases de dados para um destino de coleção de base de dados personalizada
Destinos de base de dados podem ser associados com destinos de coleção de base de dados personalizada para criar um grupo de bases de dados. Sempre que é criada uma tarefa que tenha como destino um destino de coleção de base de dados personalizada, expandir-se para as bases de dados associados ao grupo de no momento da execução de destino.

Adicione a base de dados desejado para uma coleção personalizada específica:

   ```
    $serverName = "{Database Server Name}"
    $databaseName = "{Database Name}"
    $customCollectionName = "{Custom Database Collection Name}"
    Add-AzureSqlJobChildTarget -CustomCollectionName $customCollectionName -DatabaseName $databaseName -ServerName $databaseServerName
   ```

#### <a name="review-the-databases-within-a-custom-database-collection-target"></a>Reveja as bases de dados dentro de um destino de coleção de base de dados personalizada
Utilize o **Get-AzureSqlJobTarget** cmdlet para obter as bases de dados do filho dentro de um destino de coleção de base de dados personalizada.

   ```
    $customCollectionName = "{Custom Database Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $childTargets = Get-AzureSqlJobTarget -ParentTargetId $target.TargetId
    Write-Output $childTargets
   ```

### <a name="create-a-job-to-execute-a-script-across-a-custom-database-collection-target"></a>Criar uma tarefa para executar um script num destino de coleção de base de dados personalizada
Utilize o **New-AzureSqlJob** cmdlet para criar uma tarefa em relação a um grupo de bases de dados definidos por um destino de coleção de base de dados personalizada. Tarefas de base de dados elásticas expande a tarefa em várias tarefas de subordinado cada um correspondendo a um banco de dados associado com o destino de coleção de base de dados personalizados e certifique-se de que o script é executado em cada banco de dados. Novamente, é importante que os scripts são idempotentes para que seja resiliente para repetições.

   ```
    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job
   ```

## <a name="data-collection-across-databases"></a>Recolha de dados entre bases de dados
**Tarefas de base de dados elásticas** suporta a execução de uma consulta num grupo de bases de dados e envia os resultados à tabela de um especificado da base de dados. A tabela pode ser consultada após o facto para ver os resultados da consulta de cada base de dados. Esta opção fornece um mecanismo assíncrono para executar uma consulta em muitas bases de dados. Casos de falha como uma das bases de dados a ser temporariamente indisponível são processados automaticamente por meio de novas tentativas.

A tabela de destino especificado é criada automaticamente se ele ainda não existir, o esquema do conjunto de resultados retornados de correspondência. Se uma execução de script devolver vários conjuntos de resultados, tarefas de bases de dados elásticas envia apenas aquele primeiro para a tabela de destino fornecido.

O seguinte script do PowerShell pode ser utilizado para executar um script coletando os resultados numa tabela especificada. Este script presume que foi criado um script T-SQL que produz um conjunto de resultados único e um destino de coleção de banco de dados personalizado foi criado.

Defina o seguinte para refletir o script desejado, credenciais e o destino de execução:

   ```
    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $executionCredentialName = "{Execution Credential Name}"
    $customCollectionName = "{Custom Collection Name}"
    $destinationCredentialName = "{Destination Credential Name}"
    $destinationServerName = "{Destination Server Name}"
    $destinationDatabaseName = "{Destination Database Name}"
    $destinationSchemaName = "{Destination Schema Name}"
    $destinationTableName = "{Destination Table Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
   ```

### <a name="create-and-start-a-job-for-data-collection-scenarios"></a>Criar e iniciar uma tarefa para cenários de recolha de dados
   ```
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $executionCredentialName -ContentName $scriptName -ResultSetDestinationServerName $destinationServerName -ResultSetDestinationDatabaseName $destinationDatabaseName -ResultSetDestinationSchemaName $destinationSchemaName -ResultSetDestinationTableName $destinationTableName -ResultSetDestinationCredentialName $destinationCredentialName -TargetId $target.TargetId
    Write-Output $job
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName
    Write-Output $jobExecution
   ```

## <a name="create-a-schedule-for-job-execution-using-a-job-trigger"></a>Criar um agendamento para execução da tarefa com um acionador de tarefa
O seguinte script do PowerShell pode ser utilizado para criar um agendamento recorrente. Este script utiliza um intervalo de um minuto, mas New-AzureSqlJobSchedule também suporta o DayInterval-, - HourInterval, - MonthInterval e - WeekInterval parâmetros. Agendas que execute apenas uma vez que podem ser criadas por passando - OneTime.

Crie uma nova agenda:
   ```
    $scheduleName = "Every one minute"
    $minuteInterval = 1
    $startTime = (Get-Date).ToUniversalTime()
    $schedule = New-AzureSqlJobSchedule -MinuteInterval $minuteInterval -ScheduleName $scheduleName -StartTime $startTime
    Write-Output $schedule
   ```

### <a name="create-a-job-trigger-to-have-a-job-executed-on-a-time-schedule"></a>Criar um acionador de tarefa para que uma tarefa seja executada com base numa agenda de tempo
Um acionador de tarefa pode ser definido para que uma tarefa seja executada de acordo com uma agenda de tempo. O seguinte script do PowerShell pode ser utilizado para criar um acionador de tarefa.

Defina as variáveis seguintes para corresponder para a tarefa pretendida e a agenda:

   ```
    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    $jobTrigger = New-AzureSqlJobTrigger -ScheduleName $scheduleName -JobName $jobName
    Write-Output $jobTrigger
   ```

### <a name="remove-a-scheduled-association-to-stop-job-from-executing-on-schedule"></a>Remover uma associação agendada para parar a tarefa a execução numa agenda
Para descontinuar a execução da tarefa recorrente por meio de um acionador de tarefa, pode ser removido o acionador de tarefa.
Remover um acionador de tarefa para parar uma tarefa de que está sendo executada, de acordo com um agendamento a utilizar o **Remove-AzureSqlJobTrigger** cmdlet.

   ```
    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Remove-AzureSqlJobTrigger -ScheduleName $scheduleName -JobName $jobName
   ```

## <a name="import-elastic-database-query-results-to-excel"></a>Importar os resultados da consulta de base de dados elástica para Excel
 Pode importar os resultados de uma consulta para um ficheiro do Excel.

1. Inicie o Excel 2013.
2. Navegue para o **dados** faixa de opções.
3. Clique em **de outras origens** e clique em **do SQL Server**.

   ![Importação de Excel a partir de outras origens](./media/sql-database-elastic-query-getting-started/exel-sources.png)

4. Na **Assistente de ligação de dados** escreva as credenciais de início de sessão e o nome do servidor. Clique depois em **Seguinte**.
5. Na caixa de diálogo **selecione a base de dados que contém os dados que pretende**, selecione a **ElasticDBQuery** base de dados.
6. Selecione o **os clientes** da tabela na vista de lista e clique em **próxima**. Em seguida, clique em **concluir**.
7. Na **importar dados** formam, em **Selecione como pretende ver estes dados no seu livro**, selecione **tabela** e clique em **OK**.

Todas as linhas da **clientes** tabela, armazenada em partições horizontais diferentes preencher a folha do Excel.

## <a name="next-steps"></a>Passos Seguintes
Agora, pode utilizar as funções de dados do Excel. Utilize a cadeia de ligação com o nome do servidor, nome de base de dados e as credenciais para ligar as ferramentas de integração BI e os dados na base de dados de consulta elástica. Certifique-se de que o SQL Server é suportado como uma origem de dados para a sua ferramenta. Consulte a base de dados de consulta elástica e tabelas externas, assim como qualquer outro banco de dados do SQL Server e tabelas do SQL Server que iria a estabelecer ligação com a sua ferramenta.

### <a name="cost"></a>Custo
Não é sem custos adicionais para utilizar a funcionalidade de consulta de base de dados elástica. No entanto, neste momento esta funcionalidade está disponível apenas nos conjuntos elásticos e bases de dados Premium e crítico para a empresa como um ponto final, mas as partições horizontais podem ser de qualquer camada de serviços.

Para obter informações sobre preços, consulte [detalhes de preços de base de dados do SQL](https://azure.microsoft.com/pricing/details/sql-database/).

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-getting-started/cmd-prompt.png
[2]: ./media/sql-database-elastic-query-getting-started/portal.png
[3]: ./media/sql-database-elastic-query-getting-started/tiers.png
[4]: ./media/sql-database-elastic-query-getting-started/details.png
[5]: ./media/sql-database-elastic-query-getting-started/exel-sources.png
<!--anchors-->
