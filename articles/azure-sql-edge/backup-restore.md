---
title: Backup e restaurar bases de dados - Azure SQL Edge (Pré-visualização)
description: Saiba mais sobre as capacidades de backup e restauro em Azure SQL Edge (Pré-visualização)
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 5c7bdbc49d8f1c6af7f38911919c660b03e1a37a
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83596954"
---
# <a name="backup-and-restore-databases-in-azure-sql-edge-preview"></a>Bases de dados de backup e restauro em Borda SQL (Pré-visualização) 

O Azure SQL Edge baseia-se nas versões mais recentes do Microsoft SQL Server Database Engine no Linux, fornecendo capacidades de backup semelhantes e restaurar capacidades de base de dados como as disponíveis no SQL Server no Linux e no SQL Server em execução em contentores. O componente de backup e restauro fornece uma salvaguarda essencial para proteger os dados armazenados nas suas bases de dados Azure SQL Edge. Para minimizar o risco de perda catastrófica de dados, recomenda-se que faça o repor as suas bases de dados periodicamente para preservar regularmente as modificações nos seus dados. Uma estratégia de cópia de segurança e restauro bem planeada ajuda a proteger as bases de dados contra perda de dados causada por vários tipos de falhas. Teste a sua estratégia ao restaurar um conjunto de cópias de segurança e, em seguida, ao recuperar a base de dados para se preparar para responder com eficiência a um desastre.

Para ler mais sobre o porquê das cópias de segurança serem importantes, consulte [Back Up e Restore of SQL Server Databases](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases/).

O Azure SQL Edge suporta backup e restauro a partir do armazenamento local ou das bolhas Azure. Para obter mais informações sobre a cópia de segurança e restaurar a partir do Armazenamento De Blob Azure, consulte a Cópia de [Segurança do Servidor SQL e restaure com o Microsoft Azure Blob Storage Service](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service/) e a Cópia de Segurança do Servidor [SQL para URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url).

## <a name="backing-up-a-database-in-azure-sql-edge"></a>Backup de uma base de dados em Azure SQL Edge

O Azure SQL Edge suporta os mesmos tipos de backup suportados pelo SQL Server. Para obter uma lista completa dos tipos de backup suportados no Servidor SQL, consulte a [visão geral da cópia de segurança](/sql/relational-databases/backup-restore/backup-overview-sql-server/).

> [!IMPORTANT] 
> As bases de dados criadas no Azure SQL Edge utilizam um modelo de recuperação simples por padrão. Como tais cópias de segurança de registo não podem ser realizadas nestas bases de dados. Se houver necessidade de efetuar cópias de segurança nestas bases de dados, um administrador terá de alterar o modelo de recuperação da base de dados para um modelo de recuperação completo. Para obter uma lista completa de modelos de recuperação suportados pelo SQL Server, consulte a visão geral do Modelo de [Recuperação](/sql/relational-databases/backup-restore/recovery-models-sql-server#RMov).

### <a name="backup-to-local-disk"></a>Backup para o disco local

No exemplo abaixo indicado, o comando Transact-SQL de backup é utilizado para criar uma cópia de segurança da base de dados no recipiente. Para efeitos deste exemplo, é criada uma nova pasta chamada "backup" para armazenar os ficheiros de backup.

1. Crie uma pasta para as cópias de segurança. Este comando tem de ser executado no hospedeiro onde está a funcionar o seu contentor Azure SQL Edge. No comando abaixo substitua **<AzureSQLEdge_Container_Name>** com o nome do recipiente Azure SQL Edge na sua implantação.

    ```bash
    sudo docker exec -it <AzureSQLEdge_Container_Name> mkdir /var/opt/mssql/backup
    ```

2. Ligue-se à instância Azure SQL Edge utilizando o SQL Server Management Studio (SSMS) ou utilizando o Azure Data Studio (ADS) e execute o comando de base de dados de backup para obter a cópia de segurança da sua base de dados de utilizador. No exemplo abaixo, estamos a receber a cópia de segurança da base de dados *IronOreSilicaPrediction.*

    ```sql
    BACKUP DATABASE [IronOreSilicaPrediction] 
    TO DISK = N'/var/opt/mssql/backup/IronOrePredictDB.bak' 
    WITH NOFORMAT, NOINIT,  NAME = N'IronOreSilicaPrediction-Full Database Backup', 
    SKIP, NOREWIND, NOUNLOAD, COMPRESSION,  STATS = 10
    GO
    ```

3. Depois de executar o comando e se a cópia de segurança da base de dados for bem sucedida, verá mensagens semelhantes às seguintes na secção de resultados de SSMS ou ADS.

    ```txt
    10 percent processed.
    20 percent processed.
    30 percent processed.
    40 percent processed.
    50 percent processed.
    60 percent processed.
    70 percent processed.
    80 percent processed.
    90 percent processed.
    100 percent processed.
    Processed 51648 pages for database 'IronOreSilicaPrediction', file 'IronOreSilicaPrediction' on file 1.
    Processed 2 pages for database 'IronOreSilicaPrediction', file 'IronOreSilicaPrediction_log' on file 1.
    BACKUP DATABASE successfully processed 51650 pages in 3.588 seconds (112.461 MB/sec).

    Completion time: 2020-04-09T23:54:48.4957691-07:00
    ```

### <a name="backup-to-url"></a>Backup para URL

O Azure SQL Edge suporta backups em ambas as bolhas de página e blocos. Para obter mais informações sobre as bolhas de página e as bolhas de bloco, consulte [backup para Block blob vs Page blob](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url?view=sql-server-ver15#blockbloborpageblob). No exemplo abaixo, a base de dados *IronOreSilicaPrediction* está a ser apoiada até uma bolha de bloco. 

1. O primeiro passo na configuração de backups para bloquear bolhas é gerar um símbolo de assinatura de acesso partilhado (SAS) que pode ser usado para criar um Credencial de Servidor SQL em Azure SQL Edge. O script cria uma Assinatura de Acesso Partilhado que está associada a uma Política de Acesso Armazenada. Para mais informações, consulte Assinaturas de [Acesso Partilhado, Parte 1: Compreender o Modelo SAS](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/). O script também escreve o comando T-SQL necessário para criar a credencial no Servidor SQL. O script abaixo assume que já tem uma subscrição Azure com uma conta de armazenamento e um recipiente de armazenamento para as cópias de segurança.

    ```PowerShell
    # Define global variables for the script  
    $subscriptionName='<your subscription name>'   # the name of subscription name you will use
    $resourcegroupName = '<your resource group name>' # the name of resource group you will use
    $storageAccountName= '<your storage account name>' # the storage account name you will use for backups
    $containerName= '<your storage container name>'  # the storage container name to which you will attach the SAS policy with its SAS token  
    $policyName = 'SASPolicy' # the name of the SAS policy  

    # adds an authenticated Azure account for use in the session
    Login-AzAccount

    # set the tenant, subscription and environment for use in the rest of
    Select-AzSubscription -Subscription $subscriptionName

    # Generate the SAS token
    $sa = Get-AzStorageAccount -ResourceGroupName $resourcegroupName -Name $storageAccountName 
    $storagekey = Get-AzStorageAccountKey -ResourceGroupName $resourcegroupName -Name $storageAccountName 
    $storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storagekey[0].Value
    $cbc = Get-AzStorageContainer -Name $containerName -Context $storageContext
    $policy = New-AzStorageContainerStoredAccessPolicy -Container $containerName -Policy $policyName -Context $storageContext -ExpiryTime $(Get-Date).ToUniversalTime().AddYears(10) -Permission "rwld"
    $sas = New-AzStorageContainerSASToken -Policy $policyName -Context $storageContext -Container $containerName
    Write-Host 'Shared Access Signature= '$($sas.Substring(1))''

    # Outputs the Transact SQL to the clipboard and to the screen to create the credential using the Shared Access Signature  
    Write-Host 'Credential T-SQL'  
    $tSql = "CREATE CREDENTIAL [{0}] WITH IDENTITY='Shared Access Signature', SECRET='{1}'" -f $cbc.CloudBlobContainer.Uri.AbsoluteUri,$sas.Substring(1)
    $tSql | clip  
    Write-Host $tSql
    ```

    Depois de executar com sucesso o script, copie o comando CREATE CREDENTIAL para uma ferramenta de consulta, ligue-se a uma instância do Servidor SQL e execute o comando para criar a credencial com a Assinatura de Acesso Partilhado.

2. Ligue-se à instância Azure SQL Edge utilizando o Estúdio de Gestão de Servidores SQL (SSMS) ou utilizando o Azure Data Studio (ADS) e crie a credencial utilizando o comando a partir do passo anterior. Certifique-se de que substitui o comando CREATE CREDENTIAL pela saída real do passo anterior.

    ```sql
    IF NOT EXISTS  
    (SELECT * FROM sys.credentials
    WHERE name = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>')  
    CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
       WITH IDENTITY = 'SHARED ACCESS SIGNATURE',  
       SECRET = '<SAS_TOKEN>';
    ```

3. O comando seguinte leva uma cópia de segurança da *Previsão IronOreSilica* para o recipiente de armazenamento Azure.

    ```sql
    BACKUP DATABASE IronOreSilicaPrediction
    TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/IronOreSilicaPrediction.bak'
    With MAXTRANSFERSIZE = 4194304,BLOCKSIZE=65536;  
    GO
    ```

## <a name="restoring-a-database-in-azure-sql-edge"></a>Restaurar uma base de dados em Azure SQL Edge

O Azure SQL Edge suporta a restauração de um disco local, de uma localização de rede ou de uma conta de Armazenamento Azure Blob. Para uma visão geral da Restauração e Recuperação no Servidor SQL, consulte a visão geral da [restauração e da recuperação](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-and-recovery-overview-sql-server?view=sql-server-ver15). Para uma visão geral do modelo de recuperação simples no Servidor SQL, consulte [Restauros completos de bases de dados (Modelo de Recuperação Simples)](https://docs.microsoft.com/sql/relational-databases/backup-restore/complete-database-restores-simple-recovery-model?view=sql-server-ver15).

### <a name="restore-from-local-disk"></a>Restaurar do disco local

Este exemplo utiliza a cópia de *segurança IronOreSilicaPrediction* realizada no exemplo anterior para a restauração como uma nova base de dados com um nome diferente.

1. Se o ficheiro de cópia de segurança da base de dados ainda não estiver presente no recipiente, pode utilizar o comando abaixo para copiar o ficheiro no recipiente. O exemplo abaixo pressupõe que o ficheiro de reserva está presente no hospedeiro local e está a ser copiado para a pasta /var/opt/mssql/backup num recipiente Azure SQL Edge chamado sql1.

    ```bash
    sudo docker cp IronOrePredictDB.bak sql1:/var/opt/mssql/backup
    ```

2. Ligue-se à instância Azure SQL Edge utilizando o Estúdio de Gestão de Servidores SQL (SSMS) ou utilizando o Azure Data Studio (ADS) para executar o comando de restauro. No exemplo abaixo, o **IronOrePredictDB.bak** é restaurado para criar uma nova base de dados **IronOreSilicaPrediction_2**

    ```sql
    Restore FilelistOnly from disk = N'/var/opt/mssql/backup/IronOrePredictDB.bak'

    Restore Database IronOreSilicaPrediction_2
    From disk = N'/var/opt/mssql/backup/IronOrePredictDB.bak'
    WITH MOVE 'IronOreSilicaPrediction' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_2.mdf',
    MOVE 'IronOreSilicaPrediction_log' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_2.ldf'
    ```

3. Depois de executar o comando de restauro e se a operação de restauro tiver sido bem sucedida, verá as mensagens semelhantes às seguintes na janela de saída. 

    ```txt
    Processed 51648 pages for database 'IronOreSilicaPrediction_2', file 'IronOreSilicaPrediction' on file 1.
    Processed 2 pages for database 'IronOreSilicaPrediction_2', file 'IronOreSilicaPrediction_log' on file 1.
    RESTORE DATABASE successfully processed 51650 pages in 6.543 seconds (61.670 MB/sec).

    Completion time: 2020-04-13T23:49:21.1600986-07:00
    ```

### <a name="restore-from-url"></a>Restaurar a partir de URL

O Azure SQL Edge também suporta a restauração de uma base de dados de uma conta de Armazenamento Azure. As restaurações podem ser realizadas a partir das bolhas de bloco ou das cópias de segurança blob da página. No exemplo abaixo indicado, o ficheiro de backup da base de dados *IronOreSilicaPrediction_2020_04_16.bak* numa bolha de bloco é restaurado para criar a base de dados *IronOreSilicaPrediction_3*.

```sql
RESTORE DATABASE IronOreSilicaPrediction_3
FROM URL = 'https://mystorageaccount.blob.core.windows.net/mysecondcontainer/IronOreSilicaPrediction_2020_04_16.bak'
WITH MOVE 'IronOreSilicaPrediction' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_3.mdf', 
MOVE 'IronOreSilicaPrediction_log' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_3.ldf',
STATS = 10;
```


