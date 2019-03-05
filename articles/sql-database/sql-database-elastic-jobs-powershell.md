---
title: Criar e gerir conjuntos elásticos com o PowerShell | Documentos da Microsoft
description: PowerShell utilizado para gerir os conjuntos de base de dados do Azure SQL
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: powershell
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: dc9b58402ed0fcc48d60b51d30f891ffcadddbe0
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57315627"
---
# <a name="create-and-manage-sql-database-elastic-jobs-using-powershell-preview"></a>Criar e gerir conjuntos elásticos de base de dados SQL com o PowerShell (pré-visualização)

As APIs do PowerShell para o **tarefas de bases de dados elásticas** (em pré-visualização), permitem-lhe definir um grupo de bases de dados em relação aos quais os scripts serão executados. Este artigo mostra como criar e gerir **tarefas de bases de dados elásticas** utilizando cmdlets do PowerShell. Ver [descrição geral das tarefas elásticas](sql-database-elastic-jobs-overview.md). 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos
* Uma subscrição do Azure. Para uma avaliação gratuita, consulte [durante um mês avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Um conjunto de bases de dados criadas com as ferramentas de bases de dados elásticas. Ver [introdução às ferramentas de bases de dados elásticas](sql-database-elastic-scale-get-started.md).
* Azure PowerShell. Para informações detalhadas, consulte [Como instalar e configurar o Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
* **Tarefas de base de dados elásticas** pacotes do PowerShell: Consulte [tarefas de instalação de bases de dados elásticas](sql-database-elastic-jobs-service-installation.md)

### <a name="select-your-azure-subscription"></a>Selecione a sua subscrição do Azure
Para selecionar a subscrição tem o Id de subscrição (**- SubscriptionId**) ou o nome da subscrição (**- SubscriptionName**). Se tiver várias subscrições podem executar o **Get-AzSubscription** cmdlet e copie as informações de subscrição pretendida do resultado definido. Depois de ter as informações da sua subscrição, execute o cmdlet seguinte para definir esta subscrição como predefinição, ou seja o destino para a criação e gestão de tarefas:

    Select-AzSubscription -SubscriptionId {SubscriptionID}

O [ISE do PowerShell](https://technet.microsoft.com/library/dd315244.aspx) é recomendado para utilização desenvolver e executar scripts do PowerShell contra as tarefas de bases de dados elásticas.

## <a name="elastic-database-jobs-objects"></a>Objetos de tarefas de base de dados elásticos
A tabela seguinte lista as todos os tipos de objeto de **tarefas de bases de dados elásticas** juntamente com a respetiva descrição e as APIs relevantes do PowerShell.

<table style="width:100%">
  <tr>
    <th>Tipo de Objeto</th>
    <th>Descrição</th>
    <th>APIs do PowerShell relacionados</th>
  </tr>
  <tr>
    <td>Credencial</td>
    <td>Nome de utilizador e palavra-passe para utilizar ao ligar a bases de dados para a execução de scripts ou aplicações de DACPACs. <p>A palavra-passe é encriptada antes de enviar para e armazenar na base de dados de tarefas elásticas da base de dados.  A palavra-passe é desencriptada pelo serviço de tarefas elásticas da base de dados por meio de credencial criado e carregado a partir do script de instalação.</td>
    <td><p>Get-AzureSqlJobCredential</p>
    <p>New-AzureSqlJobCredential</p><p>Set-AzureSqlJobCredential</p></td></td>
  </tr>

  <tr>
    <td>Script</td>
    <td>Script de Transact-SQL a ser utilizado para execução em bases de dados.  O script deve ser criado para serem idempotentes, uma vez que o serviço irá tentar novamente a execução do script após falhas.
    </td>
    <td>
    <p>Get-AzureSqlJobContent</p>
    <p>Get-AzureSqlJobContentDefinition</p>
    <p>New-AzureSqlJobContent</p>
    <p>Set-AzureSqlJobContentDefinition</p>
    </td>
  </tr>

  <tr>
    <td>DACPAC</td>
    <td><a href="https://msdn.microsoft.com/library/ee210546.aspx">Aplicação de camada de dados </a> pacote a ser aplicado em bancos de dados.
    </td>
    <td>
    <p>Get-AzureSqlJobContent</p>
    <p>New-AzureSqlJobContent</p>
    <p>Set-AzureSqlJobContentDefinition</p>
    </td>
  </tr>
  <tr>
    <td>Destino da base de dados</td>
    <td>Nome de base de dados e o servidor que aponta para uma base de dados do SQL do Azure.
    </td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>New-AzureSqlJobTarget</p>
    </td>
  </tr>
  <tr>
    <td>Destino de mapa de partições horizontais</td>
    <td>Combinação de um destino de base de dados e uma credencial que deve ser utilizado para determinar as informações armazenadas dentro de um mapa de partições horizontais de bases de dados elásticas.
    </td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>New-AzureSqlJobTarget</p>
    <p>Set-AzureSqlJobTarget</p>
    </td>
  </tr>
<tr>
    <td>Destino de coleção personalizada</td>
    <td>Grupo definido de bases de dados a utilizar coletivamente para execução.</td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>New-AzureSqlJobTarget</p>
    </td>
  </tr>
<tr>
    <td>Destino de subordinado de coleção personalizada</td>
    <td>Destino de base de dados que é referenciado a partir de uma coleção personalizada.</td>
    <td>
    <p>Add-AzureSqlJobChildTarget</p>
    <p>Remove-AzureSqlJobChildTarget</p>
    </td>
  </tr>

<tr>
    <td>Tarefa</td>
    <td>
    <p>Definição de parâmetros para uma tarefa que pode ser utilizado para acionar a execução ou para atender a uma agenda.</p>
    </td>
    <td>
    <p>Get-AzureSqlJob</p>
    <p>New-AzureSqlJob</p>
    <p>Set-AzureSqlJob</p>
    </td>
  </tr>

<tr>
    <td>Execução da tarefa</td>
    <td>
    <p>Contentor de tarefas necessárias para cumprir a executar um script ou aplicar uma DACPAC para um destino com as credenciais para ligações de base de dados com falhas é processada de acordo com uma diretiva de execução.</p>
    </td>
    <td>
    <p>Get-AzureSqlJobExecution</p>
    <p>Start-AzureSqlJobExecution</p>
    <p>Stop-AzureSqlJobExecution</p>
    <p>Wait-AzureSqlJobExecution</p>
  </tr>

<tr>
    <td>Execução de tarefas da tarefa</td>
    <td>
    <p>Única unidade de trabalho para atender a uma tarefa.</p>
    <p>Se uma tarefa com êxito não é capaz de executar, a mensagem de exceção resultante será registada e uma nova tarefa de trabalho correspondente será criada e executada de acordo com a política de execução especificado.</p></p>
    </td>
    <td>
    <p>Get-AzureSqlJobExecution</p>
    <p>Start-AzureSqlJobExecution</p>
    <p>Stop-AzureSqlJobExecution</p>
    <p>Wait-AzureSqlJobExecution</p>
  </tr>

<tr>
    <td>Política de execução de tarefa</td>
    <td>
    <p>Controles de tarefas tempos limite de execução, limites de tentativas e intervalos entre tentativas.</p>
    <p>Tarefas elásticas da base de dados inclui uma política de execução de tarefa predefinida que fazer com que essencialmente infinitas repetições de falhas de tarefas do trabalho com um término exponencial de intervalos entre cada tentativa.</p>
    </td>
    <td>
    <p>Get-AzureSqlJobExecutionPolicy</p>
    <p>New-AzureSqlJobExecutionPolicy</p>
    <p>Set-AzureSqlJobExecutionPolicy</p>
    </td>
  </tr>

<tr>
    <td>Agenda</td>
    <td>
    <p>Hora baseada especificação para execução ter lugar num intervalo recorrente ou de uma só vez.</p>
    </td>
    <td>
    <p>Get-AzureSqlJobSchedule</p>
    <p>New-AzureSqlJobSchedule</p>
    <p>Set-AzureSqlJobSchedule</p>
    </td>
  </tr>

<tr>
    <td>Aciona a tarefa</td>
    <td>
    <p>Um mapeamento entre uma tarefa e uma agenda para acionar a execução de tarefa, de acordo com a agenda.</p>
    </td>
    <td>
    <p>New-AzureSqlJobTrigger</p>
    <p>Remove-AzureSqlJobTrigger</p>
    </td>
  </tr>
</table>

## <a name="supported-elastic-database-jobs-group-types"></a>Tipos de grupo de tarefas de bases de dados elásticas suportadas
A tarefa executa scripts do Transact-SQL (T-SQL) ou a aplicação de DACPACs longo de um grupo de bases de dados. Quando uma tarefa é submetida a ser executado num grupo de bases de dados, a tarefa "expande" o em tarefas subordinadas, onde cada executa a execução do pedido em relação a uma base de dados individual no grupo. 

Existem dois tipos de grupos que podem ser criados: 

* [Mapa de partições horizontais](sql-database-elastic-scale-shard-map-management.md) grupo: Quando uma tarefa é submetida para um mapa de partições horizontais de destino, a tarefa consulta o mapa de partições horizontais para determinar o conjunto atual de partições horizontais e, em seguida, cria filho tarefas para cada partição horizontal no mapa de partições horizontais.
* Grupo de recolha de personalizado: Um conjunto definido de personalizado de bases de dados. Quando uma tarefa destina-se uma coleção personalizada, ele cria filho tarefas para cada base de dados atualmente na coleção personalizada.

## <a name="to-set-the-elastic-database-jobs-connection"></a>Para definir a base de dados elásticas tarefas de ligação
Uma ligação tem de ser definido como as tarefas *base de dados do controle* antes de utilizar as APIs de tarefas. Executar este cmdlet aciona uma janela de credencial para pop-up solicitando o nome de utilizador e palavra-passe criado ao instalar tarefas elásticas da base de dados. Todos os exemplos fornecidos neste tópico partem do princípio de que este primeiro passo já foi executado.

Abra uma ligação para as tarefas de bases de dados elásticas:

    Use-AzureSqlJobConnection -CurrentAzureSubscription 

## <a name="encrypted-credentials-within-the-elastic-database-jobs"></a>Credenciais encriptadas dentro as tarefas de bases de dados elásticas
Credenciais da base de dados podem ser inseridas nas tarefas *base de dados do controle* com a respetiva palavra-passe encriptada. É necessário armazenar credenciais para ativar tarefas a ser executado num momento posterior, (usando as agendas de tarefas).

Encriptação funciona por meio de um certificado criado como parte do script de instalação. O script de instalação cria e carrega o certificado para o serviço de Cloud do Azure para a desencriptação das palavras-passe armazenadas encriptadas. O serviço de nuvem do Azure mais tarde armazena a chave pública dentro das tarefas *base de dados do controle* que permite que a interface de portal do Azure ou da API do PowerShell encriptar uma palavra-passe fornecida sem a necessidade do certificado a ser instalado localmente .

As palavras-passe de credencial são encriptadas e seguro dos utilizadores com acesso só de leitura aos objetos de tarefas de bases de dados elásticas. Mas é possível que um utilizador mal intencionado com acesso de leitura e escrita aos objetos de tarefas elásticas da base de dados extrair uma palavra-passe. As credenciais são projetadas para ser reutilizadas em execuções de tarefas. As credenciais são transmitidas para bases de dados de destino durante o estabelecimento de conexões. Atualmente não há restrições sobre as bases de dados de destino utilizadas para cada credencial, o usuário mal-intencionado poderia adicionar um destino de base de dados para uma base de dados sob o controle do usuário mal-intencionado. O utilizador, em seguida, foi possível iniciar uma tarefa de direcionamento esta base de dados para obter a palavra-passe da credencial.

Melhores práticas de segurança para as tarefas de bases de dados elásticas incluem:

* Limite a utilização das APIs a pessoas de confiança.
* As credenciais devem ter o mínimo de privilégios necessários para executar a tarefa de tarefa.  Obter mais informações podem ser vistas dentro desta [autorização e as permissões](https://msdn.microsoft.com/library/bb669084.aspx) artigo do MSDN do SQL Server.

### <a name="to-create-an-encrypted-credential-for-job-execution-across-databases"></a>Para criar uma credencial encriptada para execução de tarefas entre bases de dados
Para criar uma nova credencial encriptada, o [ **cmdlet Get-Credential** ](/powershell/module/microsoft.powershell.security/get-credential) pede-lhe um nome de utilizador e palavra-passe que pode ser passado para o [ **cmdlet New-AzureSqlJobCredential** ](/powershell/module/elasticdatabasejobs/new-azuresqljobcredential).

    $credentialName = "{Credential Name}"
    $databaseCredential = Get-Credential
    $credential = New-AzureSqlJobCredential -Credential $databaseCredential -CredentialName $credentialName
    Write-Output $credential

### <a name="to-update-credentials"></a>Ao atualizar as credenciais
Quando alterar as palavras-passe, utilize o [ **cmdlet Set-AzureSqlJobCredential** ](/powershell/module/elasticdatabasejobs/set-azuresqljobcredential) e defina o **CredentialName** parâmetro.

    $credentialName = "{Credential Name}"
    Set-AzureSqlJobCredential -CredentialName $credentialName -Credential $credential 

## <a name="to-define-an-elastic-database-shard-map-target"></a>Para definir um destino de mapa de partições horizontais de bases de dados elásticas
Para executar uma tarefa em todas as bases de dados num conjunto de partições horizontais (criado usando [biblioteca de clientes de bases de dados elásticas](sql-database-elastic-database-client-library.md)), utilizar um mapa de partições horizontais como o destino de base de dados. Este exemplo requer uma aplicação fragmentada criada usando a biblioteca de clientes de bases de dados elásticas. Ver [introdução ao exemplo de ferramentas de bases de dados elásticas](sql-database-elastic-scale-get-started.md).

A base de dados de Gestor de mapas de partições horizontais tem de ser definido como um destino de base de dados e, em seguida, o mapa de partições horizontais específico deve ser especificado como destino.

    $shardMapCredentialName = "{Credential Name}"
    $shardMapDatabaseName = "{ShardMapDatabaseName}" #example: ElasticScaleStarterKit_ShardMapManagerDb
    $shardMapDatabaseServerName = "{ShardMapServerName}"
    $shardMapName = "{MyShardMap}" #example: CustomerIDShardMap
    $shardMapDatabaseTarget = New-AzureSqlJobTarget -DatabaseName $shardMapDatabaseName -ServerName $shardMapDatabaseServerName
    $shardMapTarget = New-AzureSqlJobTarget -ShardMapManagerCredentialName $shardMapCredentialName -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapDatabaseServerName -ShardMapName $shardMapName
    Write-Output $shardMapTarget

## <a name="create-a-t-sql-script-for-execution-across-databases"></a>Criar um Script T-SQL para execução em bases de dados
Durante a criação de scripts T-SQL para execução, é altamente recomendável para criá-los para ser [idempotentes](https://en.wikipedia.org/wiki/Idempotence) e resilientes contra falhas. Tarefas elásticas da base de dados tentará a execução de um script sempre que a execução detetar uma falha, independentemente da classificação da falha.

Utilize o [ **cmdlet New-AzureSqlJobContent** ](/powershell/module/elasticdatabasejobs/new-azuresqljobcontent) para criar e guardar um script para execução e defina o **- ContentName** e **- CommandText** parâmetros.

    $scriptName = "Create a TestTable"

    $scriptCommandText = "
    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
    BEGIN
        CREATE TABLE TestTable(
            TestTableId INT PRIMARY KEY IDENTITY,
            InsertionTime DATETIME2
        );
    END
    GO
    INSERT INTO TestTable(InsertionTime) VALUES (sysutcdatetime());
    GO"

    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script

### <a name="create-a-new-script-from-a-file"></a>Criar um novo script a partir de um ficheiro
Se o script de T-SQL é definido dentro de um ficheiro, utilize isto para importar o script:

    $scriptName = "My Script Imported from a File"
    $scriptPath = "{Path to SQL File}"
    $scriptCommandText = Get-Content -Path $scriptPath
    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script

### <a name="to-update-a-t-sql-script-for-execution-across-databases"></a>Para atualizar um script T-SQL para execução em bases de dados
Este script do PowerShell atualiza o texto do comando T-SQL para um script existente.

Defina as variáveis seguintes para refletir a definição de script desejado seja definido:

    $scriptName = "Create a TestTable"
    $scriptUpdateComment = "Adding AdditionalInformation column to TestTable"
    $scriptCommandText = "
    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
    BEGIN
    CREATE TABLE TestTable(
        TestTableId INT PRIMARY KEY IDENTITY,
        InsertionTime DATETIME2
    );
    END
    GO

    IF NOT EXISTS (SELECT columns.name FROM sys.columns INNER JOIN sys.tables on columns.object_id = tables.object_id WHERE tables.name = 'TestTable' AND columns.name = 'AdditionalInformation')
    BEGIN
    ALTER TABLE TestTable
    ADD AdditionalInformation NVARCHAR(400);
    END
    GO

    INSERT INTO TestTable(InsertionTime, AdditionalInformation) VALUES (sysutcdatetime(), 'test');
    GO"

### <a name="to-update-the-definition-to-an-existing-script"></a>Para atualizar a definição para um script existente
    Set-AzureSqlJobContentDefinition -ContentName $scriptName -CommandText $scriptCommandText -Comment $scriptUpdateComment 

## <a name="to-create-a-job-to-execute-a-script-across-a-shard-map"></a>Para criar uma tarefa para executar um script num mapa de partições horizontais
Este script do PowerShell inicia uma tarefa para execução de um script em cada partição horizontal num mapa de partições horizontais de escala elástica.

Defina as variáveis seguintes para refletir o script desejado e de destino:

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $shardMapServerName = "{Shard Map Server Name}"
    $shardMapDatabaseName = "{Shard Map Database Name}"
    $shardMapName = "{Shard Map Name}"
    $credentialName = "{Credential Name}"
    $shardMapTarget = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName 
    $job = New-AzureSqlJob -ContentName $scriptName -CredentialName $credentialName -JobName $jobName -TargetId $shardMapTarget.TargetId
    Write-Output $job

## <a name="to-execute-a-job"></a>Para executar uma tarefa
Este script do PowerShell executa uma tarefa existente:

Atualize a seguinte variável para refletir o nome da tarefa pretendida foram executados:

    $jobName = "{Job Name}"
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName 
    Write-Output $jobExecution

## <a name="to-retrieve-the-state-of-a-single-job-execution"></a>Para obter o estado de uma execução de tarefa individual
Utilize o [ **cmdlet Get-AzureSqlJobExecution** ](/powershell/module/elasticdatabasejobs/get-azuresqljobexecution) e defina o **JobExecutionId** parâmetro para ver o estado de execução da tarefa.

    $jobExecutionId = "{Job Execution Id}"
    $jobExecution = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId
    Write-Output $jobExecution

Utilizar o mesmo **Get-AzureSqlJobExecution** cmdlet com o **IncludeChildren** parâmetro para ver o estado de execuções de tarefas de subordinado, ou seja, o estado específico para cada execução de tarefa em relação a cada base de dados visada pela tarefa.

    $jobExecutionId = "{Job Execution Id}"
    $jobExecutions = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId -IncludeChildren
    Write-Output $jobExecutions 

## <a name="to-view-the-state-across-multiple-job-executions"></a>Para ver o estado entre várias execuções de tarefas
O [ **cmdlet Get-AzureSqlJobExecution** ](/powershell/module/elasticdatabasejobs/new-azuresqljob) tem vários parâmetros opcionais que podem ser usados para exibir várias execuções de tarefas, filtradas por meio dos parâmetros fornecidos. O seguinte exemplo demonstra Algumas das formas possíveis para utilizar Get-AzureSqlJobExecution:

Obter todas as execuções de tarefas ativas de nível superior:

    Get-AzureSqlJobExecution

Obter todas as execuções de tarefas de nível superior, incluindo as execuções de tarefas inativa:

    Get-AzureSqlJobExecution -IncludeInactive

Obter todas as execuções de tarefas de subordinado de um ID de execução de tarefa fornecida, incluindo as execuções de tarefas inativa:

    $parentJobExecutionId = "{Job Execution Id}"
    Get-AzureSqlJobExecution -AzureSqlJobExecution -JobExecutionId $parentJobExecutionId -IncludeInactive -IncludeChildren

Obter todas as execuções de tarefas criadas com base numa agenda / combinação, incluindo Inativas tarefas da tarefa:

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Get-AzureSqlJobExecution -JobName $jobName -ScheduleName $scheduleName -IncludeInactive

Obter todas as tarefas de direcionamento um mapa de fragmentos especificado, incluindo tarefas Inativas:

    $shardMapServerName = "{Shard Map Server Name}"
    $shardMapDatabaseName = "{Shard Map Database Name}"
    $shardMapName = "{Shard Map Name}"
    $target = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName
    Get-AzureSqlJobExecution -TargetId $target.TargetId -IncludeInactive

Obter todas as tarefas de direcionamento de uma coleção personalizada especificada, incluindo tarefas Inativas:

    $customCollectionName = "{Custom Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    Get-AzureSqlJobExecution -TargetId $target.TargetId -IncludeInactive

Obter a lista de execuções de tarefas de tarefas dentro de uma execução de tarefa específica:

    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Write-Output $jobTaskExecutions 

Obter detalhes de execução da tarefa de tarefa:

O seguinte script do PowerShell pode ser utilizado para ver os detalhes de uma execução de tarefas da tarefa, que é particularmente útil na depuração de falhas de execução.

    $jobTaskExecutionId = "{Job Task Execution Id}"
    $jobTaskExecution = Get-AzureSqlJobTaskExecution -JobTaskExecutionId $jobTaskExecutionId
    Write-Output $jobTaskExecution

## <a name="to-retrieve-failures-within-job-task-executions"></a>Para recuperar de falhas dentro de execuções de tarefas de tarefas
O **JobTaskExecution objeto** inclui uma propriedade para o ciclo de vida da tarefa, juntamente com uma propriedade da mensagem. Se uma execução de tarefas da tarefa falhou, a propriedade de ciclo de vida será definida como *falhada* e a propriedade message será definida como a mensagem de exceção resultante e sua pilha. Se uma tarefa não foi concluída com êxito, é importante ver os detalhes das tarefas de tarefa que falhou para uma determinada tarefa.

    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Foreach($jobTaskExecution in $jobTaskExecutions) 
        {
        if($jobTaskExecution.Lifecycle -ne 'Succeeded')
            {
            Write-Output $jobTaskExecution
            }
        }

## <a name="to-wait-for-a-job-execution-to-complete"></a>A aguardar pela conclusão de uma execução de tarefa
O seguinte script do PowerShell pode ser utilizado para aguardar pela conclusão de uma tarefa de tarefa:

    $jobExecutionId = "{Job Execution Id}"
    Wait-AzureSqlJobExecution -JobExecutionId $jobExecutionId 

## <a name="create-a-custom-execution-policy"></a>Criar uma política de execução personalizado
Tarefas elásticas da base de dados suporta a criação de políticas de execução personalizado que podem ser aplicadas quando a partir de tarefas.

Atualmente permitem definir políticas de execução:

* Nome: Identificador para a política de execução.
* Tempo limite da tarefa: Tempo total antes de uma tarefa será cancelada pelo tarefas elásticas da base de dados.
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

    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 10
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $executionPolicy = New-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval 
    -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $executionPolicy

### <a name="update-a-custom-execution-policy"></a>Atualizar uma política de execução personalizado
Atualize a política de execução desejado para atualizar:

    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 15
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $updatedExecutionPolicy = Set-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $updatedExecutionPolicy

## <a name="cancel-a-job"></a>Cancelar uma tarefa
Tarefas elásticas da base de dados oferece suporte a solicitações de cancelamento de tarefas.  Se as tarefas de base de dados elásticas detectar uma solicitação de cancelamento de uma tarefa a ser executada, atualmente, ele irá tentar parar a tarefa.

Existem duas formas diferentes, que as tarefas elásticas da base de dados pode efetuar um cancelamento:

1. Cancelar tarefas atualmente em execução: Se um cancelamento é detetado enquanto uma tarefa está atualmente em execução, um cancelamento ocorrerá dentro o aspecto atualmente em execução da tarefa.  Por exemplo: Se houver uma consulta de execução longa atualmente a ser realizada quando um cancelamento é tentado, haverá uma tentativa de cancelar a consulta.
2. A cancelar as repetições de tarefas: Se um cancelamento é detetado pelo thread controlo antes de uma tarefa é iniciada para execução, o thread de controle será evitar iniciar a tarefa e declarar o pedido como canceladas.

Se é pedido um cancelamento de tarefa para uma tarefa principal, a solicitação de cancelamento será cumprida para a tarefa principal e para todas as tarefas de subordinado.

Para submeter um pedido de cancelamento, utilize o [ **cmdlet Stop-AzureSqlJobExecution** ](/powershell/module/elasticdatabasejobs/stop-azuresqljobexecution) e defina o **JobExecutionId** parâmetro.

    $jobExecutionId = "{Job Execution Id}"
    Stop-AzureSqlJobExecution -JobExecutionId $jobExecutionId

## <a name="to-delete-a-job-and-job-history-asynchronously"></a>Para eliminar uma tarefa e de forma assíncrona o histórico de tarefas
Tarefas elásticas da base de dados suporta a eliminação assíncrona de tarefas. Uma tarefa pode ser marcada para eliminação e o sistema irá eliminar a tarefa e o respetivo histórico de tarefas depois de concluíram todas as execuções de tarefas para a tarefa. O sistema não cancelará automaticamente as execuções de tarefas do Active Directory.  

Invocar [ **Stop-AzureSqlJobExecution** ](/powershell/module/elasticdatabasejobs/stop-azuresqljobexecution) para cancelar as execuções de tarefas do Active Directory.

Para acionar a eliminação da tarefa, utilize o [ **cmdlet Remove-AzureSqlJob** ](/powershell/module/elasticdatabasejobs/remove-azuresqljob) e defina o **JobName** parâmetro.

    $jobName = "{Job Name}"
    Remove-AzureSqlJob -JobName $jobName

## <a name="to-create-a-custom-database-target"></a>Para criar um destino de base de dados personalizada
Pode definir os destinos de base de dados personalizada para execução direta ou para inclusão dentro de um grupo de base de dados personalizada. Por exemplo, uma vez **conjuntos elásticos** são ainda não suportados diretamente usando as APIs do PowerShell, pode criar um destino de banco de dados personalizado e o destino de coleção de banco de dados personalizado que abrange todas as bases de dados no conjunto.

Defina as variáveis seguintes para refletir as informações de banco de dados desejado:

    $databaseName = "{Database Name}"
    $databaseServerName = "{Server Name}"
    New-AzureSqlJobTarget -DatabaseName $databaseName -ServerName $databaseServerName 

## <a name="to-create-a-custom-database-collection-target"></a>Para criar um destino de coleção de base de dados personalizada
Utilize o [ **New-AzureSqlJobTarget** ](/powershell/module/elasticdatabasejobs/new-azuresqljobtarget) cmdlet para definir um destino de coleção de base de dados personalizada para permitir a execução em vários destinos de bases de dados definidos. Depois de criar um grupo de bases de dados, bases de dados podem ser associados ao destino de coleção personalizada.

Defina as variáveis seguintes para refletir a configuração de destino pretendido de coleção personalizada:

    $customCollectionName = "{Custom Database Collection Name}"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName 

### <a name="to-add-databases-to-a-custom-database-collection-target"></a>Para adicionar bases de dados para um destino de coleção de base de dados personalizada
Para adicionar uma base de dados a numa coleção personalizada específica, utilize o [ **Add-AzureSqlJobChildTarget** ](/powershell/module/elasticdatabasejobs/add-azuresqljobchildtarget) cmdlet.

    $databaseServerName = "{Database Server Name}"
    $databaseName = "{Database Name}"
    $customCollectionName = "{Custom Database Collection Name}"
    Add-AzureSqlJobChildTarget -CustomCollectionName $customCollectionName -DatabaseName $databaseName -ServerName $databaseServerName 

#### <a name="review-the-databases-within-a-custom-database-collection-target"></a>Reveja as bases de dados dentro de um destino de coleção de base de dados personalizada
Utilize o [ **Get-AzureSqlJobTarget** ](/powershell/module/elasticdatabasejobs/new-azuresqljobtarget) cmdlet para obter as bases de dados do filho dentro de um destino de coleção de base de dados personalizada. 

    $customCollectionName = "{Custom Database Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $childTargets = Get-AzureSqlJobTarget -ParentTargetId $target.TargetId
    Write-Output $childTargets

### <a name="create-a-job-to-execute-a-script-across-a-custom-database-collection-target"></a>Criar uma tarefa para executar um script num destino de coleção de base de dados personalizada
Utilize o [ **New-AzureSqlJob** ](/powershell/module/elasticdatabasejobs/new-azuresqljob) cmdlet para criar uma tarefa em relação a um grupo de bases de dados definidos por um destino de coleção de base de dados personalizada. Tarefas elásticas da base de dados irão expandir a tarefa para várias tarefas de subordinado cada um correspondendo a um banco de dados associado com o destino de coleção de base de dados personalizados e certifique-se de que o script é executado em cada banco de dados. Novamente, é importante que os scripts são idempotentes para que seja resiliente para repetições.

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job

## <a name="data-collection-across-databases"></a>Recolha de dados entre bases de dados
Pode utilizar uma tarefa para executar uma consulta num grupo de bases de dados e enviar os resultados para uma tabela específica. A tabela pode ser consultada após o facto para ver os resultados da consulta de cada base de dados. Isso fornece um método assíncrono para executar uma consulta em muitas bases de dados. Tentativas falhadas são processadas automaticamente por meio de novas tentativas.

A tabela de destino especificado será criada automaticamente se ainda não existir. A nova tabela corresponde ao esquema do conjunto de resultados devolvidos. Se um script devolver vários conjuntos de resultados, tarefas de bases de dados elásticas enviar apenas o primeiro para a tabela de destino.

O script do PowerShell seguinte executa um script e recolhe os resultados numa tabela especificada. Este script presume que foi criado um script T-SQL que produz um conjunto de resultados único e que foi criado um destino de coleção de base de dados personalizada.

Este script utiliza o [ **Get-AzureSqlJobTarget** ](/powershell/module/elasticdatabasejobs/new-azuresqljobtarget) cmdlet. Defina os parâmetros de script, credenciais e o destino de execução:

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

### <a name="to-create-and-start-a-job-for-data-collection-scenarios"></a>Para criar e iniciar uma tarefa para cenários de recolha de dados
Este script utiliza o [ **início AzureSqlJobExecution** ](/powershell/module/elasticdatabasejobs/start-azuresqljobexecution) cmdlet.

    $job = New-AzureSqlJob -JobName $jobName 
    -CredentialName $executionCredentialName 
    -ContentName $scriptName 
    -ResultSetDestinationServerName $destinationServerName 
    -ResultSetDestinationDatabaseName $destinationDatabaseName 
    -ResultSetDestinationSchemaName $destinationSchemaName 
    -ResultSetDestinationTableName $destinationTableName 
    -ResultSetDestinationCredentialName $destinationCredentialName 
    -TargetId $target.TargetId
    Write-Output $job
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName
    Write-Output $jobExecution

## <a name="to-schedule-a-job-execution-trigger"></a>Para agendar um acionador de execução de tarefa
O seguinte script do PowerShell pode ser utilizado para criar uma agenda periódica. Este script utiliza um intervalo de minuto, mas [ **New-AzureSqlJobSchedule** ](/powershell/module/elasticdatabasejobs/new-azuresqljobschedule) também suporta o DayInterval-, - HourInterval, - MonthInterval e - WeekInterval parâmetros. Agendas que execute apenas uma vez que podem ser criadas por passando - OneTime.

Crie uma nova agenda:

    $scheduleName = "Every one minute"
    $minuteInterval = 1
    $startTime = (Get-Date).ToUniversalTime()
    $schedule = New-AzureSqlJobSchedule 
    -MinuteInterval $minuteInterval 
    -ScheduleName $scheduleName 
    -StartTime $startTime 
    Write-Output $schedule

### <a name="to-trigger-a-job-executed-on-a-time-schedule"></a>Para acionar uma tarefa executada com base numa agenda de tempo
Um acionador de tarefa pode ser definido para que uma tarefa seja executada de acordo com uma agenda de tempo. O seguinte script do PowerShell pode ser utilizado para criar um acionador de tarefa.

Uso [New-AzureSqlJobTrigger](/powershell/module/elasticdatabasejobs/new-azuresqljobtrigger) e defina as variáveis seguintes para corresponder para a tarefa pretendida e a agenda:

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    $jobTrigger = New-AzureSqlJobTrigger
    -ScheduleName $scheduleName
    -JobName $jobName
    Write-Output $jobTrigger

### <a name="to-remove-a-scheduled-association-to-stop-job-from-executing-on-schedule"></a>Para remover uma associação de agendada para parar a tarefa a execução numa agenda
Para descontinuar a execução da tarefa recorrente por meio de um acionador de tarefa, pode ser removido o acionador de tarefa. Remover um acionador de tarefa para parar uma tarefa de que está sendo executada, de acordo com um agendamento a utilizar o [ **cmdlet Remove-AzureSqlJobTrigger**](/powershell/module/elasticdatabasejobs/remove-azuresqljobtrigger).

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Remove-AzureSqlJobTrigger 
    -ScheduleName $scheduleName 
    -JobName $jobName

### <a name="retrieve-job-triggers-bound-to-a-time-schedule"></a>Obter os acionadores de tarefa vinculados a uma agenda de tempo
O seguinte script do PowerShell pode ser utilizado para obter e exibir os disparadores de tarefa registrados para uma agenda de tempo específico.

    $scheduleName = "{Schedule Name}"
    $jobTriggers = Get-AzureSqlJobTrigger -ScheduleName $scheduleName
    Write-Output $jobTriggers

### <a name="to-retrieve-job-triggers-bound-to-a-job"></a>Para obter os acionadores de tarefa vinculado a uma tarefa
Uso [Get-AzureSqlJobTrigger](/powershell/module/elasticdatabasejobs/get-azuresqljobtrigger) para obter e exibir agendas que contém um trabalho registado.

    $jobName = "{Job Name}"
    $jobTriggers = Get-AzureSqlJobTrigger -JobName $jobName
    Write-Output $jobTriggers

## <a name="to-create-a-data-tier-application-dacpac-for-execution-across-databases"></a>Para criar uma aplicação de camada de dados (DACPAC) para execução entre bases de dados
Para criar um DACPAC, veja [aplicações de camada de dados](https://msdn.microsoft.com/library/ee210546.aspx). Para implementar um DACPAC, utilize o [cmdlet New-AzureSqlJobContent](/powershell/module/elasticdatabasejobs/new-azuresqljobcontent). O DACPAC deve ser acessível para o serviço. É recomendado para carregar um DACPAC criado para o armazenamento do Azure e criar um [assinatura de acesso partilhado](../storage/common/storage-dotnet-shared-access-signature-part-1.md) para o DACPAC.

    $dacpacUri = "{Uri}"
    $dacpacName = "{Dacpac Name}"
    $dacpac = New-AzureSqlJobContent -DacpacUri $dacpacUri -ContentName $dacpacName 
    Write-Output $dacpac

### <a name="to-update-a-data-tier-application-dacpac-for-execution-across-databases"></a>Para atualizar uma aplicação de camada de dados (DACPAC) para execução em bases de dados
DACPACs existentes registados num tarefas elásticas da base de dados podem ser atualizados para apontar para URIs de novo. Utilize o [ **cmdlet Set-AzureSqlJobContentDefinition** ](/powershell/module/elasticdatabasejobs/set-azuresqljobcontentdefinition) atualizar o URI de DACPAC na existente registado DACPAC:

    $dacpacName = "{Dacpac Name}"
    $newDacpacUri = "{Uri}"
    $updatedDacpac = Set-AzureSqlJobDacpacDefinition -ContentName $dacpacName -DacpacUri $newDacpacUri
    Write-Output $updatedDacpac

## <a name="to-create-a-job-to-apply-a-data-tier-application-dacpac-across-databases"></a>Para criar uma tarefa para aplicar uma aplicação de camada de dados (DACPAC) em bases de dados
Após um DACPAC tiver sido criada nas tarefas de base de dados elásticas, pode ser criada uma tarefa para aplicar o DACPAC num grupo de bases de dados. O seguinte script do PowerShell pode ser utilizado para criar uma tarefa DACPAC numa coleção personalizada de bases de dados:

    $jobName = "{Job Name}"
    $dacpacName = "{Dacpac Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget 
    -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob 
    -JobName $jobName 
    -CredentialName $credentialName 
    -ContentName $dacpacName -TargetId $target.TargetId
    Write-Output $job 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-powershell/cmd-prompt.png
[2]: ./media/sql-database-elastic-jobs-powershell/portal.png
<!--anchors-->
