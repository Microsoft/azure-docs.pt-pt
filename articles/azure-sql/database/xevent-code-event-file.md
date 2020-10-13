---
title: Código de arquivo de eventos XEvent
description: Fornece PowerShell e Transact-SQL para uma amostra de código em duas fases que demonstra o alvo do Ficheiro de Eventos num evento alargado na Base de Dados Azure SQL. O Azure Storage é uma parte necessária deste cenário.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: MightyPen
ms.author: genemi
ms.reviewer: jrasnik
ms.date: 06/06/2020
ms.openlocfilehash: e9f3f1ca6005ff8c61211263944513d859d6d23e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91620193"
---
# <a name="event-file-target-code-for-extended-events-in-azure-sql-database"></a>Código-alvo do Ficheiro de Eventos para eventos alargados na Base de Dados Azure SQL
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[!INCLUDE [sql-database-xevents-selectors-1-include](../../../includes/sql-database-xevents-selectors-1-include.md)]

Você quer uma amostra de código completa para uma forma robusta de capturar e reportar informações para um evento prolongado.

No Microsoft SQL Server, o [alvo do Ficheiro de Eventos](https://msdn.microsoft.com/library/ff878115.aspx) é utilizado para armazenar as saídas do evento num ficheiro de disco rígido local. Mas estes ficheiros não estão disponíveis para a Base de Dados Azure SQL. Em vez disso, utilizamos o serviço de Armazenamento Azure para suportar o alvo do Ficheiro de Eventos.

Este tópico apresenta uma amostra de código em duas fases:

- PowerShell, para criar um recipiente de armazenamento Azure na nuvem.
- Transact-SQL:
  - Para atribuir o recipiente de armazenamento Azure a um alvo de Ficheiro de Evento.
  - Para criar e iniciar a sessão do evento, e assim por diante.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado pela Base de Dados Azure SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para estes cmdlets, consulte [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

- Uma conta e subscrição do Azure. Pode inscrever-se para obter uma [versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
- Qualquer base de dados pode criar uma tabela.
  
  - Opcionalmente, pode [criar uma base de dados de demonstração **AdventureWorksLT** ](single-database-create-quickstart.md) em minutos.

- SQL Server Management Studio (ssms.exe), idealmente a sua mais recente versão mensal de atualização.
  Você pode baixar as últimas ssms.exe a partir de:
  
  - Tópico intitulado [Download SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
  - [Um link direto para o download.](https://go.microsoft.com/fwlink/?linkid=616025)

- Tem de instalar os [módulos Azure PowerShell.](https://go.microsoft.com/?linkid=9811175)

  - Os módulos fornecem comandos como - **New-AzStorageAccount**.

## <a name="phase-1-powershell-code-for-azure-storage-container"></a>Fase 1: Código PowerShell para recipiente de armazenamento Azure

Este PowerShell é a fase 1 da amostra de código de duas fases.

O script começa com comandos para limpar após uma possível execução anterior, e é re-executado.

1. Cole o script PowerShell num simples editor de texto, como Notepad.exe, e guarde o script como um ficheiro com a extensão **.ps1**.
2. Iniciar o PowerShell ISE como administrador.
3. No momento, tipo<br/>`Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser`<br/>e, em seguida, pressione Enter.
4. No PowerShell ISE, abra o seu ficheiro **.ps1.** Execute o script.
5. O guião começa a iniciar uma nova janela na qual inicias o login no Azure.

   - Se repetir o script sem interromper a sua sessão, tem a opção conveniente de comentar o comando **Add-AzureAccount.**

![PowerShell ISE, com módulo Azure instalado, pronto para executar script.](./media/xevent-code-event-file/event-file-powershell-ise-b30.png)

### <a name="powershell-code"></a>Código PowerShell

Este script PowerShell pressupõe que já instalou o módulo Az. Para obter informações, consulte [instalar o módulo Azure PowerShell](/powershell/azure/install-Az-ps).

```powershell
## TODO: Before running, find all 'TODO' and make each edit!!

cls;

#--------------- 1 -----------------------

'Script assumes you have already logged your PowerShell session into Azure.
But if not, run  Connect-AzAccount (or  Connect-AzAccount), just one time.';
#Connect-AzAccount;   # Same as  Connect-AzAccount.

#-------------- 2 ------------------------

'
TODO: Edit the values assigned to these variables, especially the first few!
';

# Ensure the current date is between
# the Expiry and Start time values that you edit here.

$subscriptionName    = 'YOUR_SUBSCRIPTION_NAME';
$resourceGroupName   = 'YOUR_RESOURCE-GROUP-NAME';

$policySasExpiryTime = '2018-08-28T23:44:56Z';
$policySasStartTime  = '2017-10-01';

$storageAccountLocation = 'YOUR_STORAGE_ACCOUNT_LOCATION';
$storageAccountName     = 'YOUR_STORAGE_ACCOUNT_NAME';
$contextName            = 'YOUR_CONTEXT_NAME';
$containerName          = 'YOUR_CONTAINER_NAME';
$policySasToken         = ' ? ';

$policySasPermission = 'rwl';  # Leave this value alone, as 'rwl'.

#--------------- 3 -----------------------

# The ending display lists your Azure subscriptions.
# One should match the $subscriptionName value you assigned
#   earlier in this PowerShell script.

'Choose an existing subscription for the current PowerShell environment.';

Select-AzSubscription -Subscription $subscriptionName;

#-------------- 4 ------------------------

'
Clean up the old Azure Storage Account after any previous run,
before continuing this new run.';

if ($storageAccountName) {
    Remove-AzStorageAccount `
        -Name              $storageAccountName `
        -ResourceGroupName $resourceGroupName;
}

#--------------- 5 -----------------------

[System.DateTime]::Now.ToString();

'
Create a storage account.
This might take several minutes, will beep when ready.
  ...PLEASE WAIT...';

New-AzStorageAccount `
    -Name              $storageAccountName `
    -Location          $storageAccountLocation `
    -ResourceGroupName $resourceGroupName `
    -SkuName           'Standard_LRS';

[System.DateTime]::Now.ToString();
[System.Media.SystemSounds]::Beep.Play();

'
Get the access key for your storage account.
';

$accessKey_ForStorageAccount = `
    (Get-AzStorageAccountKey `
        -Name              $storageAccountName `
        -ResourceGroupName $resourceGroupName
        ).Value[0];

"`$accessKey_ForStorageAccount = $accessKey_ForStorageAccount";

'Azure Storage Account cmdlet completed.
Remainder of PowerShell .ps1 script continues.
';

#--------------- 6 -----------------------

# The context will be needed to create a container within the storage account.

'Create a context object from the storage account and its primary access key.
';

$context = New-AzStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey  $accessKey_ForStorageAccount;

'Create a container within the storage account.
';

$containerObjectInStorageAccount = New-AzStorageContainer `
    -Name    $containerName `
    -Context $context;

'Create a security policy to be applied to the SAS token.
';

New-AzStorageContainerStoredAccessPolicy `
    -Container  $containerName `
    -Context    $context `
    -Policy     $policySasToken `
    -Permission $policySasPermission `
    -ExpiryTime $policySasExpiryTime `
    -StartTime  $policySasStartTime;

'
Generate a SAS token for the container.
';
try {
    $sasTokenWithPolicy = New-AzStorageContainerSASToken `
        -Name    $containerName `
        -Context $context `
        -Policy  $policySasToken;
}
catch {
    $Error[0].Exception.ToString();
}

#-------------- 7 ------------------------

'Display the values that YOU must edit into the Transact-SQL script next!:
';

"storageAccountName: $storageAccountName";
"containerName:      $containerName";
"sasTokenWithPolicy: $sasTokenWithPolicy";

'
REMINDER: sasTokenWithPolicy here might start with "?" character, which you must exclude from Transact-SQL.
';

'
(Later, return here to delete your Azure Storage account. See the preceding  Remove-AzStorageAccount -Name $storageAccountName)';

'
Now shift to the Transact-SQL portion of the two-part code sample!';

# EOFile
```

Tome nota dos poucos valores nomeados que o script PowerShell imprime quando termina. Deve editar esses valores no script Transact-SQL que se segue como fase 2.

<!--
TODO:   Consider whether the preceding PowerShell code example deserves to be updated to the latest package (AzureRM.SQL?).
2020/June/06   Adding the !NOTE below about "ADLS Gen2 storage accounts".
Related to   https://github.com/MicrosoftDocs/azure-docs/issues/56520
-->

> [!NOTE]
> No exemplo do código PowerShell anterior, os eventos estendidos SQL não são compatíveis com as contas de armazenamento ADLS Gen2.

## <a name="phase-2-transact-sql-code-that-uses-azure-storage-container"></a>Fase 2: Código Transact-SQL que utiliza recipiente de armazenamento Azure

- Na fase 1 desta amostra de código, executou um script PowerShell para criar um recipiente de armazenamento Azure.
- Em seguida, na fase 2, o seguinte script Transact-SQL deve utilizar o recipiente.

O script começa com comandos para limpar após uma possível execução anterior, e é re-executado.

O guião PowerShell imprimiu alguns valores nomeados quando terminou. Tem de editar o script Transact-SQL para utilizar esses valores. Encontre **TODO** no script Transact-SQL para localizar os pontos de edição.

1. Open SQL Server Management Studio (ssms.exe).
2. Ligue à sua base de dados na Base de Dados Azure SQL.
3. Clique para abrir um novo painel de consultas.
4. Cole o seguinte script Transact-SQL no painel de consultas.
5. Encontre **todos os TODO** no script e faça as edições apropriadas.
6. Salve e, em seguida, executar o guião.

> [!WARNING]
> O valor-chave SAS gerado pelo script anterior do PowerShell pode começar com um '?' (ponto de interrogação). Quando utilizar a tecla SAS no seguinte script T-SQL, deve *remover o '?' de te principal.* Caso contrário, os seus esforços podem ser bloqueados pela segurança.

### <a name="transact-sql-code"></a>Código Transact-SQL

```sql
---- TODO: First, run the earlier PowerShell portion of this two-part code sample.
---- TODO: Second, find every 'TODO' in this Transact-SQL file, and edit each.

---- Transact-SQL code for Event File target on Azure SQL Database.

SET NOCOUNT ON;
GO

----  Step 1.  Establish one little table, and  ---------
----  insert one row of data.

IF EXISTS
    (SELECT * FROM sys.objects
        WHERE type = 'U' and name = 'gmTabEmployee')
BEGIN
    DROP TABLE gmTabEmployee;
END
GO

CREATE TABLE gmTabEmployee
(
    EmployeeGuid         uniqueIdentifier   not null  default newid()  primary key,
    EmployeeId           int                not null  identity(1,1),
    EmployeeKudosCount   int                not null  default 0,
    EmployeeDescr        nvarchar(256)          null
);
GO

INSERT INTO gmTabEmployee ( EmployeeDescr )
    VALUES ( 'Jane Doe' );
GO

------  Step 2.  Create key, and  ------------
------  Create credential (your Azure Storage container must already exist).

IF NOT EXISTS
    (SELECT * FROM sys.symmetric_keys
        WHERE symmetric_key_id = 101)
BEGIN
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '0C34C960-6621-4682-A123-C7EA08E3FC46' -- Or any newid().
END
GO

IF EXISTS
    (SELECT * FROM sys.database_scoped_credentials
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        WHERE name = 'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent')
BEGIN
    DROP DATABASE SCOPED CREDENTIAL
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent] ;
END
GO

CREATE
    DATABASE SCOPED
    CREDENTIAL
        -- use '.blob.',   and not '.queue.' or '.table.' etc.
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
    WITH
        IDENTITY = 'SHARED ACCESS SIGNATURE',  -- "SAS" token.
        -- TODO: Paste in the long SasToken string here for Secret, but exclude any leading '?'.
        SECRET = 'sv=2014-02-14&sr=c&si=gmpolicysastoken&sig=EjAqjo6Nu5xMLEZEkMkLbeF7TD9v1J8DNB2t8gOKTts%3D'
    ;
GO

------  Step 3.  Create (define) an event session.  --------
------  The event session has an event with an action,
------  and a has a target.

IF EXISTS
    (SELECT * from sys.database_event_sessions
        WHERE name = 'gmeventsessionname240b')
BEGIN
    DROP
        EVENT SESSION
            gmeventsessionname240b
        ON DATABASE;
END
GO

CREATE
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE

    ADD EVENT
        sqlserver.sql_statement_starting
            (
            ACTION (sqlserver.sql_text)
            WHERE statement LIKE 'UPDATE gmTabEmployee%'
            )
    ADD TARGET
        package0.event_file
            (
            -- TODO: Assign AzureStorageAccount name, and the associated Container name.
            -- Also, tweak the .xel file name at end, if you like.
            SET filename =
                'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b.xel'
            )
    WITH
        (MAX_MEMORY = 10 MB,
        MAX_DISPATCH_LATENCY = 3 SECONDS)
    ;
GO

------  Step 4.  Start the event session.  ----------------
------  Issue the SQL Update statements that will be traced.
------  Then stop the session.

------  Note: If the target fails to attach,
------  the session must be stopped and restarted.

ALTER
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE
    STATE = START;
GO

SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 13
    WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
GO

ALTER
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE
    STATE = STOP;
GO

-------------- Step 5.  Select the results. ----------

SELECT
        *, 'CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS!' as [CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS],
        CAST(event_data AS XML) AS [event_data_XML]  -- TODO: In ssms.exe results grid, double-click this cell!
    FROM
        sys.fn_xe_file_target_read_file
            (
                -- TODO: Fill in Storage Account name, and the associated Container name.
                -- TODO: The name of the .xel file needs to be an exact match to the files in the storage account Container (You can use Storage Account explorer from the portal to find out the exact file names or you can retrieve the name using the following DMV-query: select target_data from sys.dm_xe_database_session_targets. The 3rd xml-node, "File name", contains the name of the file currently written to.)
                'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b',
                null, null, null
            );
GO

-------------- Step 6.  Clean up. ----------

DROP
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE;
GO

DROP DATABASE SCOPED CREDENTIAL
    -- TODO: Assign AzureStorageAccount name, and the associated Container name.
    [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
    ;
GO

DROP TABLE gmTabEmployee;
GO

PRINT 'Use PowerShell Remove-AzStorageAccount to delete your Azure Storage account!';
GO
```

Se o alvo não se ligar quando correr, tem de parar e reiniciar a sessão do evento:

```sql
ALTER EVENT SESSION ... STATE = STOP;
GO
ALTER EVENT SESSION ... STATE = START;
GO
```

## <a name="output"></a>Saída

Quando o script Transact-SQL estiver completo, clique numa célula sob o cabeçalho da coluna **event_data_XML.** É apresentado um **\<event>** elemento que mostra uma declaração DE ATUALIZAÇÃO.

Aqui está um **\<event>** elemento que foi gerado durante os testes:

```xml
<event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T19:18:45.420Z">
  <data name="state">
    <value>0</value>
    <text>Normal</text>
  </data>
  <data name="line_number">
    <value>5</value>
  </data>
  <data name="offset">
    <value>148</value>
  </data>
  <data name="offset_end">
    <value>368</value>
  </data>
  <data name="statement">
    <value>UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe'</value>
  </data>
  <action name="sql_text" package="sqlserver">
    <value>

SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 13
    WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
</value>
  </action>
</event>
```

O script Transact-SQL anterior usou a seguinte função do sistema para ler o event_file:

- [sys.fn_xe_file_target_read_file (Transact-SQL)](https://msdn.microsoft.com/library/cc280743.aspx)

Uma explicação das opções avançadas para a visualização de dados de eventos alargados está disponível em:

- [Visualização avançada de dados-alvo de eventos alargados](https://msdn.microsoft.com/library/mt752502.aspx)

## <a name="converting-the-code-sample-to-run-on-sql-server"></a>Conversão da amostra de código para ser executada no SQL Server

Suponha que queria executar a amostra transact-SQL anterior no Microsoft SQL Server.

- Para simplificar, pretende substituir completamente a utilização do recipiente de armazenamento Azure por um ficheiro simples como *C:\myeventdata.xel*. O ficheiro seria escrito para o disco rígido local do computador que acolhe o SQL Server.
- Não necessitaria de qualquer tipo de declarações Transact-SQL para **criar a chave master** e criar **credenciais.**
- Na declaração **CREATE EVENT SESSION,** na sua cláusula **ADD TARGET,** substituiria o valor Http atribuído ao **nome de ficheiro=** por uma cadeia de caminho completa como *C:\myfile.xel*.
  
  - Nenhuma conta de armazenamento Azure precisa estar envolvida.

## <a name="more-information"></a>Mais informações

Para obter mais informações sobre contas e contentores no serviço de Armazenamento Azure, consulte:

- [Como utilizar o armazenamento blob a partir de .NET](../../storage/blobs/storage-quickstart-blobs-dotnet.md)
- [Naming e Referenciação de Contentores, Bolhas e Metadados](https://msdn.microsoft.com/library/azure/dd135715.aspx)
- [Trabalhar com o Recipiente raiz](https://msdn.microsoft.com/library/azure/ee395424.aspx)
- [Lição 1: Criar uma política de acesso armazenada e uma assinatura de acesso partilhado num recipiente Azure](https://msdn.microsoft.com/library/dn466430.aspx)
  - [Lição 2: Criar uma credencial sql server usando uma assinatura de acesso partilhado](https://msdn.microsoft.com/library/dn466435.aspx)
- [Eventos alargados para o Microsoft SQL Server](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events)
