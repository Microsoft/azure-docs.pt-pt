---
title: Código do arquivo de evento XEvent para o banco de dados SQL
description: Fornece o PowerShell e o Transact-SQL para um exemplo de código de duas fases que demonstra o destino do arquivo de evento em um evento estendido no banco de dados SQL do Azure. O armazenamento do Azure é uma parte necessária deste cenário.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: PowerShell
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: jrasnik
ms.date: 03/12/2019
ms.openlocfilehash: ea5c90433a4d2928e5fb88df149631c80df9dacf
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686828"
---
# <a name="event-file-target-code-for-extended-events-in-sql-database"></a>Código de destino do arquivo de evento para eventos estendidos no banco de dados SQL

[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Você deseja um exemplo de código completo para uma maneira robusta de capturar e relatar informações para um evento estendido.

No Microsoft SQL Server, o [destino do arquivo de evento](https://msdn.microsoft.com/library/ff878115.aspx) é usado para armazenar saídas de eventos em um arquivo de disco rígido local. Mas esses arquivos não estão disponíveis para o banco de dados SQL do Azure. Em vez disso, usamos o serviço de armazenamento do Azure para dar suporte ao destino do arquivo de evento.

Este tópico apresenta um exemplo de código de duas fases:

* PowerShell, para criar um contêiner de armazenamento do Azure na nuvem.
* Transact-SQL:
  
  * Para atribuir o contêiner de armazenamento do Azure a um destino de arquivo de evento.
  * Para criar e iniciar a sessão de evento e assim por diante.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte do banco de dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. Para esses cmdlets, consulte [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo AZ e nos módulos AzureRm são substancialmente idênticos.

* Uma conta e subscrição do Azure. Pode inscrever-se para obter uma [versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Qualquer banco de dados no qual você possa criar uma tabela.
  
  * Opcionalmente, você pode [criar um banco de dados de demonstração do **AdventureWorksLT** ](sql-database-get-started.md) em minutos.
* SQL Server Management Studio (SSMS. exe), idealmente sua última versão de atualização mensal. 
  Você pode baixar o SSMS. exe mais recente de:
  
  * Tópico intitulado [Download SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
  * [Um link direto para o download.](https://go.microsoft.com/fwlink/?linkid=616025)
* Você deve ter os [módulos do Azure PowerShell](https://go.microsoft.com/?linkid=9811175) instalados.
  
  * Os módulos fornecem comandos como- **New-AzStorageAccount**.

## <a name="phase-1-powershell-code-for-azure-storage-container"></a>Fase 1: código do PowerShell para o contêiner de armazenamento do Azure

Este PowerShell é a fase 1 do exemplo de código de duas fases.

O script começa com comandos para limpeza após uma possível execução anterior e é reutilizável.

1. Cole o script do PowerShell em um editor de texto simples, como o notepad. exe, e salve o script como um arquivo com a extensão **. ps1**.
2. Inicie o ISE do PowerShell como administrador.
3. No prompt, digite<br/>`Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser`<br/>e pressione Enter.
4. No ISE do PowerShell, abra o arquivo **. ps1** . Execute o script.
5. O script primeiro inicia uma nova janela na qual você faz logon no Azure.
   
   * Se você executar o script novamente sem interromper a sessão, terá a opção conveniente de comentar o comando **Add-AzureAccount** .

![PowerShell ISE, com o módulo do Azure instalado, pronto para executar o script.][30_powershell_ise]

### <a name="powershell-code"></a>Código do PowerShell

Este script do PowerShell pressupõe que você já instalou o módulo AZ. Para obter informações, consulte [instalar o módulo Azure PowerShell](/powershell/azure/install-Az-ps).

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

If ($storageAccountName)
{
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
Try
{
    $sasTokenWithPolicy = New-AzStorageContainerSASToken `
        -Name    $containerName `
        -Context $context `
        -Policy  $policySasToken;
}
Catch 
{
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


Anote os poucos valores nomeados que o script do PowerShell imprime quando termina. Você deve editar esses valores no script Transact-SQL que segue a fase 2.

## <a name="phase-2-transact-sql-code-that-uses-azure-storage-container"></a>Fase 2: código Transact-SQL que usa o contêiner de armazenamento do Azure

* Na fase 1 deste exemplo de código, você executou um script do PowerShell para criar um contêiner de armazenamento do Azure.
* Em seguida, na fase 2, o script Transact-SQL a seguir deve usar o contêiner.

O script começa com comandos para limpeza após uma possível execução anterior e é reutilizável.

O script do PowerShell imprimiu alguns valores nomeados quando ele terminou. Você deve editar o script Transact-SQL para usar esses valores. Localize **todo** no script TRANSACT-SQL para localizar os pontos de edição.

1. Abra SQL Server Management Studio (SSMS. exe).
2. Conecte-se ao banco de dados do banco de dados SQL do Azure.
3. Clique para abrir um novo painel de consulta.
4. Cole o script Transact-SQL a seguir no painel de consulta.
5. Localize todo o **todo** no script e faça as edições apropriadas.
6. Salve e, em seguida, execute o script.


> [!WARNING]
> O valor da chave SAS gerado pelo script do PowerShell anterior pode começar com um '? ' (ponto de interrogação). Ao usar a chave SAS no script T-SQL a seguir, você deve *remover o '? ' à esquerda*. Caso contrário, seus esforços podem ser bloqueados pela segurança.


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


Se o destino não for anexado quando você executar o, você deverá parar e reiniciar a sessão de evento:

```sql
ALTER EVENT SESSION ... STATE = STOP;
GO
ALTER EVENT SESSION ... STATE = START;
GO
```


## <a name="output"></a>Saída

Quando o script Transact-SQL for concluído, clique em uma célula sob o cabeçalho da coluna **event_data_XML** . Um **\<elemento > de evento** é exibido, que mostra uma instrução UPDATE.

Aqui está um **\<** elemento de > de evento que foi gerado durante o teste:


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

* [sys. fn_xe_file_target_read_file (Transact-SQL)](https://msdn.microsoft.com/library/cc280743.aspx)

Uma explicação das opções avançadas para a exibição de dados de eventos estendidos está disponível em:

* [Exibição avançada de dados de destino de eventos estendidos](https://msdn.microsoft.com/library/mt752502.aspx)


## <a name="converting-the-code-sample-to-run-on-sql-server"></a>Convertendo o exemplo de código para executar em SQL Server

Suponha que você quisesse executar o exemplo anterior de Transact-SQL em Microsoft SQL Server.

* Para simplificar, você desejaria substituir completamente o uso do contêiner de armazenamento do Azure por um arquivo simples, como **C:\myeventdata.xel**. O arquivo seria gravado no disco rígido local do computador que hospeda SQL Server.
* Você não precisaria de nenhum tipo de instrução Transact-SQL para **criar a chave mestra** e **criar a credencial**.
* Na instrução **Create Event Session** , em sua cláusula **Add Target** , você substituiria o valor http atribuído a **filename =** por uma cadeia de caracteres de caminho completo como **C:\MyFile.xel**.
  
  * Nenhuma conta de armazenamento do Azure precisa estar envolvida.

## <a name="more-information"></a>Mais informações

Para obter mais informações sobre contas e contêineres no serviço de armazenamento do Azure, consulte:

* [Como usar o armazenamento de BLOBs do .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md)
* [Nomenclatura e Referência para Contentores, Blobs e Metadados](https://msdn.microsoft.com/library/azure/dd135715.aspx)
* [Trabalhando com o contêiner raiz](https://msdn.microsoft.com/library/azure/ee395424.aspx)
* [Lição 1: criar uma política de acesso armazenado e uma assinatura de acesso compartilhado em um contêiner do Azure](https://msdn.microsoft.com/library/dn466430.aspx)
  * [Lição 2: criar uma credencial de SQL Server usando uma assinatura de acesso compartilhado](https://msdn.microsoft.com/library/dn466435.aspx)
* [Eventos estendidos para Microsoft SQL Server](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events)

<!--
Image references.
-->

[30_powershell_ise]: ./media/sql-database-xevent-code-event-file/event-file-powershell-ise-b30.png

