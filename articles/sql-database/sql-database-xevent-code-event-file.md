---
title: Código de ficheiro de evento de XEvent para base de dados SQL | Documentos da Microsoft
description: Fornece o PowerShell e o Transact-SQL para obter um exemplo de código em duas fases que demonstra o destino de ficheiro de evento num evento expandido na base de dados do Azure SQL. Armazenamento do Azure é uma parte obrigatória de neste cenário.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: PowerShell
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: jrasnik
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 0a9472dec9b76dfbde1690e11f13836746b0dfaa
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57862901"
---
# <a name="event-file-target-code-for-extended-events-in-sql-database"></a>Código de destino de ficheiro de evento para eventos expandidos na base de dados SQL

[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Pretende um exemplo de código completo para uma forma robusta de coletar e reportar informações para um evento expandido.

No Microsoft SQL Server, o [destino de ficheiro de evento](https://msdn.microsoft.com/library/ff878115.aspx) é utilizada para armazenar as saídas de eventos para um ficheiro de disco rígido local. Mas esses ficheiros não estão disponíveis para a base de dados do Azure SQL. Em vez disso, podemos utilizar o serviço de armazenamento do Azure para suportar o destino de ficheiro de evento.

Este tópico apresenta um exemplo de código em duas fases:

* PowerShell para criar um contentor de armazenamento do Azure na cloud.
* Transact-SQL:
  
  * Para atribuir o contentor de armazenamento do Azure para um destino de ficheiro de evento.
  * Para criar e iniciar a sessão de eventos e assim por diante.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo do PowerShell do Azure Resource Manager ainda é suportado pelo SQL Database do Azure, mas todo o desenvolvimento futuro é para o módulo de Az.Sql. Para estes cmdlets, consulte [azurerm. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

* Uma conta e subscrição do Azure. Pode inscrever-se para obter uma [versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Qualquer base de dados que pode criar uma tabela.
  
  * Opcionalmente, pode [crie uma **AdventureWorksLT** base de dados de demonstração](sql-database-get-started.md) em minutos.
* SQL Server Management Studio (ssms.exe), o ideal é que a última versão atualização mensal. 
  Pode baixar o ssms.exe mais recentes do:
  
  * Tópico intitulado [transferir o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
  * [Uma ligação direta para o download.](https://go.microsoft.com/fwlink/?linkid=616025)
* Tem de ter o [módulos do Azure PowerShell](https://go.microsoft.com/?linkid=9811175) instalado.
  
  * Os módulos fornecem comandos como - **New-AzStorageAccount**.

## <a name="phase-1-powershell-code-for-azure-storage-container"></a>Fase 1: Código do PowerShell para o contentor de armazenamento do Azure

Este PowerShell é a fase 1 do exemplo de código em duas fases.

O script começa com os comandos a limpeza depois de executar uma possível anterior e é rerunnable.

1. Cole o script do PowerShell num editor de texto simples como Notepad.exe e guarde o script como um ficheiro com a extensão **. ps1**.
2. Inicie o ISE do PowerShell como administrador.
3. Na linha de comandos, escreva<br/>`Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser`<br/>e, em seguida, prima Enter.
4. No ISE do PowerShell, abra sua **. ps1** ficheiro. Execute o script.
5. O script começa primeiro uma nova janela, em que iniciar sessão Azure.
   
   * Se voltar a executar o script sem interromper a sua sessão, tem a opção conveniente de comente o **Add-AzureAccount** comando.

![ISE do PowerShell, com o módulo do Azure, a instalação, pronto para executar o script.][30_powershell_ise]

### <a name="powershell-code"></a>Código do PowerShell

Este script do PowerShell pressupõe que já instalou o módulo de Az. Para obter informações, consulte [instalar o módulo Azure PowerShell](/powershell/azure/install-Az-ps).

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

$storageAccountLocation = 'West US';
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


Tome nota dos valores nomeados alguns que o script do PowerShell imprime quando ele é encerrado. Tem de editar esses valores para o script de Transact-SQL que se segue como a fase 2.

## <a name="phase-2-transact-sql-code-that-uses-azure-storage-container"></a>Fase 2: Código de Transact-SQL que usa o contentor de armazenamento do Azure

* Fase 1 deste exemplo de código, executou um script do PowerShell para criar um contentor de armazenamento do Azure.
* Em seguida na fase 2, o seguinte script de Transact-SQL tem de utilizar o contentor.

O script começa com os comandos a limpeza depois de executar uma possível anterior e é rerunnable.

O script do PowerShell impresso alguns valores nomeados quando terminou. Tem de editar o script de Transact-SQL a utilizar esses valores. Encontrar **TODO** no script de Transact-SQL para localizar os pontos de edição.

1. Abra o SQL Server Management Studio (ssms.exe).
2. Ligar à base de dados SQL Database do Azure.
3. Clique para abrir um novo painel de consulta.
4. Cole o seguinte script de Transact-SQL no painel de consulta.
5. Localizar todos os **TODO** no script e faça as edições apropriadas.
6. Guarde e, em seguida, execute o script.


> [!WARNING]
> O valor da chave de SAS gerado pelo script do PowerShell anterior pode começar com um "?" (ponto de interrogação). Quando utiliza a chave SAS do seguinte script de T-SQL, tem *remover o líder "?"* . Caso contrário, os seus esforços poderão estar bloqueados por segurança.


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


Se não for possível anexar ao executar o destino, tem de parar e reiniciar a sessão do evento:

```sql
ALTER EVENT SESSION ... STATE = STOP;
GO
ALTER EVENT SESSION ... STATE = START;
GO
```


## <a name="output"></a>Saída

Quando tiver concluído o script de Transact-SQL, clique numa célula sob o **event_data_XML** cabeçalho da coluna. Um **<event>** elemento é apresentado o que mostra uma instrução de ATUALIZAÇÃO.

Aqui está uma **<event>** elemento que foi gerado durante o teste:


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


O script anterior do Transact-SQL usado a seguinte função de sistema para ler o event_file:

* [sys.fn_xe_file_target_read_file (Transact-SQL)](https://msdn.microsoft.com/library/cc280743.aspx)

Obter uma explicação sobre as opções avançadas para a visualização de dados de eventos expandidos está disponível em:

* [Visualização avançada de dados de destino dos eventos expandidos](https://msdn.microsoft.com/library/mt752502.aspx)


## <a name="converting-the-code-sample-to-run-on-sql-server"></a>Converter o código de exemplo para ser executado no SQL Server

Suponha que quiser executar o exemplo anterior do Transact-SQL no Microsoft SQL Server.

* Para simplicidade, deve substituir completamente o uso do contentor de armazenamento do Azure com um arquivo simples, como **C:\myeventdata.xel**. O ficheiro seria escrito no disco rígido local do computador que aloja o SQL Server.
* Não precisaria qualquer tipo de instruções de Transact-SQL para **CREATE MASTER KEY** e **criar CREDENCIAL**.
* Na **CREATE EVENT SESSION** instrução, no seu **adicionar destino** cláusula, poderia substituir o valor de Http atribuído feitas **filename =** com uma cadeia de caminho completo, como o **C:\myfile.xel**.
  
  * Não existe nenhuma conta de armazenamento do Azure tem de estar envolvida.

## <a name="more-information"></a>Mais informações

Para mais informações sobre contas e os contentores no serviço de armazenamento do Azure, veja:

* [Como utilizar o armazenamento de Blobs do .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md)
* [Nomenclatura e Referência para Contentores, Blobs e Metadados](https://msdn.microsoft.com/library/azure/dd135715.aspx)
* [Trabalhar com o contêiner raiz](https://msdn.microsoft.com/library/azure/ee395424.aspx)
* [Lição 1: Criar uma política de acesso armazenadas e uma assinatura de acesso partilhado num contentor do Azure](https://msdn.microsoft.com/library/dn466430.aspx)
  * [Lição 2: Criar uma credencial do SQL Server com uma assinatura de acesso partilhado](https://msdn.microsoft.com/library/dn466435.aspx)
* [Eventos expandidos para o Microsoft SQL Server](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events)

<!--
Image references.
-->

[30_powershell_ise]: ./media/sql-database-xevent-code-event-file/event-file-powershell-ise-b30.png

