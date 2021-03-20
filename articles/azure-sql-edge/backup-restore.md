---
title: Apoiar e restaurar bases de dados - Azure SQL Edge
description: Saiba mais sobre backup e restaurar as capacidades em Azure SQL Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 114be810ea50f984c3211291691b4c4dd45ac2c7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93395245"
---
# <a name="back-up-and-restore-databases-in-azure-sql-edge"></a>Fazer o back up e restaurar bases de dados em Azure SQL Edge 

O Azure SQL Edge é construído nas versões mais recentes do Microsoft SQL Database Engine. Fornece backup semelhante e restaura capacidades de base de dados como as disponíveis no SQL Server em Linux e SQL Server em contentores. O componente de backup e restauro fornece uma salvaguarda essencial para proteger os dados armazenados nas suas bases de dados Azure SQL Edge. 

Para minimizar o risco de perda catastrófica de dados, deverá fazer o backback periodicamente das suas bases de dados para preservar as modificações dos seus dados regularmente. Uma estratégia de cópia de segurança e restauro bem planeada ajuda a proteger as bases de dados contra perda de dados causada por vários tipos de falhas. Teste a sua estratégia restaurando um conjunto de backups e, em seguida, recuperando a sua base de dados, para prepará-lo para responder eficazmente a um desastre.

Para ler mais sobre o porquê das cópias de segurança serem importantes, consulte [backup e restaure as bases de dados do SQL Server](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases/).

O Azure SQL Edge permite-lhe voltar atrás e restaurar tanto a partir de armazenamento local como de bolhas Azure. Para obter mais informações, consulte [a cópia de segurança do SQL Server e restaure com o armazenamento do Azure Blob](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service/) e a cópia de segurança do [SQL Server para URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url).

## <a name="back-up-a-database-in-azure-sql-edge"></a>Fazer o back-up de uma base de dados em Azure SQL Edge

O Azure SQL Edge suporta os mesmos tipos de backup que o SQL Server. Para obter uma lista completa, consulte [a visão geral da Cópia de Segurança](/sql/relational-databases/backup-restore/backup-overview-sql-server/).

> [!IMPORTANT] 
> As bases de dados criadas no Azure SQL Edge utilizam o modelo de recuperação simples por padrão. Como tal, não é possível realizar cópias de segurança nestas bases de dados. Se precisar de o fazer, precisará de um administrador para alterar o modelo de recuperação da base de dados para o modelo de recuperação total. Para obter uma lista completa de modelos de recuperação suportados pelo SQL Server, consulte [a visão geral do modelo Recovery](/sql/relational-databases/backup-restore/recovery-models-sql-server#RMov).

### <a name="back-up-to-local-disk"></a>Voltar para o disco local

No exemplo seguinte, utiliza o `BACKUP DATABASE` comando Transact-SQL para criar uma cópia de segurança da base de dados no recipiente. Para efeitos deste exemplo, cria uma nova pasta chamada *backup* para armazenar os ficheiros de cópia de segurança.

1. Crie uma pasta para as cópias de segurança. Coloque este comando no anfitrião onde o seu recipiente Azure SQL Edge está em funcionamento. No comando seguinte, substitua **<AzureSQLEdge_Container_Name>** pelo nome do recipiente Azure SQL Edge na sua colocação.

    ```bash
    sudo docker exec -it <AzureSQLEdge_Container_Name> mkdir /var/opt/mssql/backup
    ```

2. Ligue-se à instância Azure SQL Edge utilizando o SQL Server Management Studio (SSMS), ou utilizando o Azure Data Studio. Executar o `BACKUP DATABASE` comando para obter a cópia de segurança da sua base de dados de utilizadores. No exemplo seguinte, está a fazer o backup da base de *dados IronOreSilicaPrediction.*

    ```sql
    BACKUP DATABASE [IronOreSilicaPrediction] 
    TO DISK = N'/var/opt/mssql/backup/IronOrePredictDB.bak' 
    WITH NOFORMAT, NOINIT,  NAME = N'IronOreSilicaPrediction-Full Database Backup', 
    SKIP, NOREWIND, NOUNLOAD, COMPRESSION,  STATS = 10
    GO
    ```

3. Depois de executar o comando, se a cópia de segurança da base de dados for bem sucedida, verá mensagens semelhantes às seguintes na secção de resultados do SSMS ou do Azure Data Studio.

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

### <a name="back-up-to-url"></a>Voltar para a URL

O Azure SQL Edge suporta backups tanto para as bolhas de página como para as bolhas de bloqueio. Para obter mais informações, consulte [Back up para bloquear blob vs blob de página](/sql/relational-databases/backup-restore/sql-server-backup-to-url#blockbloborpageblob). No exemplo seguinte, a base de *dados IronOreSilicaPrediction* está a ser apoiada até uma bolha de bloco. 

1. Para configurar cópias de segurança para bloquear bolhas, primeiro gere um sinal de assinatura de acesso partilhado (SAS) que pode utilizar para criar uma credencial SQL Server no Azure SQL Edge. O script cria um SAS que está associado a uma política de acesso armazenada. Para mais informações, consulte [assinaturas de acesso partilhado, parte 1: Compreender o modelo SAS](../storage/common/storage-sas-overview.md). O script também escreve o comando T-SQL necessário para criar a credencial no SQL Server. O seguinte script pressupõe que você já tem uma subscrição Azure com uma conta de armazenamento, e um recipiente de armazenamento para as cópias de segurança.

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

    Depois de executar o script com sucesso, copie o `CREATE CREDENTIAL` comando para uma ferramenta de consulta. Em seguida, ligue-se a uma instância do SQL Server, e executar o comando para criar a credencial com o SAS.

2. Ligue-se à instância Azure SQL Edge utilizando o SSMS ou o Azure Data Studio e crie a credencial utilizando o comando a partir do passo anterior. Certifique-se de que substitui o `CREATE CREDENTIAL` comando pela saída real do passo anterior.

    ```sql
    IF NOT EXISTS  
    (SELECT * FROM sys.credentials
    WHERE name = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>')  
    CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
       WITH IDENTITY = 'SHARED ACCESS SIGNATURE',  
       SECRET = '<SAS_TOKEN>';
    ```

3. O seguinte comando requer uma cópia de segurança da *IronOreSilicaPrediction* para o contentor de armazenamento Azure.

    ```sql
    BACKUP DATABASE IronOreSilicaPrediction
    TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/IronOreSilicaPrediction.bak'
    With MAXTRANSFERSIZE = 4194304,BLOCKSIZE=65536;  
    GO
    ```

## <a name="restore-a-database-in-azure-sql-edge"></a>Restaurar uma base de dados em Azure SQL Edge

Em Azure SQL Edge, você pode restaurar a partir de um disco local, uma localização de rede ou uma conta de armazenamento Azure Blob. Para obter mais informações sobre a restauração e recuperação no SQL Server, consulte [a visão geral de Restauro e recuperação](/sql/relational-databases/backup-restore/restore-and-recovery-overview-sql-server). Para uma visão geral do modelo de recuperação simples no SQL Server, consulte [a base de dados completa (modelo de recuperação simples)](/sql/relational-databases/backup-restore/complete-database-restores-simple-recovery-model).

> [!IMPORTANT] 
> As bases de dados criadas no Azure SQL Edge não podem ser restauradas num caso de Microsoft SQL Server ou Azure SQL. Além disso, uma base de dados criada no Microsoft SQL Server ou Azure SQL pode ser restaurada no Azure SQL Edge, desde que a base de dados não contenha nenhuma das funcionalidades não suportadas pelo Azure SQL Edge. 

### <a name="restore-from-a-local-disk"></a>Restauro a partir de um disco local

Este exemplo utiliza o backup *ironOreSilicaPrediction* que fez no exemplo anterior. Agora, vai restaurá-lo como uma nova base de dados com um nome diferente.

1. Se o ficheiro de cópia de segurança da base de dados ainda não estiver presente no recipiente, pode utilizar o seguinte comando para copiar o ficheiro para o recipiente. O exemplo a seguir pressupõe que o ficheiro de cópia de segurança está presente no anfitrião local, e está a ser copiado para a pasta /var/opt/mssql/backup num recipiente Azure SQL Edge denominado *sql1*.

    ```bash
    sudo docker cp IronOrePredictDB.bak sql1:/var/opt/mssql/backup
    ```

2. Ligue-se à instância Azure SQL Edge utilizando o SSMS ou o Azure Data Studio para executar o comando de restauro. No exemplo seguinte, **o IronOrePredictDB.bak** é restaurado para criar uma nova base de dados, **IronOreSilicaPrediction_2**.

    ```sql
    Restore FilelistOnly from disk = N'/var/opt/mssql/backup/IronOrePredictDB.bak'

    Restore Database IronOreSilicaPrediction_2
    From disk = N'/var/opt/mssql/backup/IronOrePredictDB.bak'
    WITH MOVE 'IronOreSilicaPrediction' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_2.mdf',
    MOVE 'IronOreSilicaPrediction_log' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_2.ldf'
    ```

3. Depois de executar o comando de restauro, se a operação de restauro tiver sido bem sucedida, verá mensagens semelhantes às seguintes na janela de saída. 

    ```txt
    Processed 51648 pages for database 'IronOreSilicaPrediction_2', file 'IronOreSilicaPrediction' on file 1.
    Processed 2 pages for database 'IronOreSilicaPrediction_2', file 'IronOreSilicaPrediction_log' on file 1.
    RESTORE DATABASE successfully processed 51650 pages in 6.543 seconds (61.670 MB/sec).

    Completion time: 2020-04-13T23:49:21.1600986-07:00
    ```

### <a name="restore-from-url"></a>Restauro a partir de URL

O Azure SQL Edge também suporta restaurar uma base de dados a partir de uma conta de Armazenamento Azure. Pode restaurar a partir das bolhas de bloco ou das cópias de segurança da página. No exemplo seguinte, o ficheiro de backup da base de dados *IronOreSilicaPrediction_2020_04_16.bak* numa bolha de blocos é restaurado para criar a base de dados, *IronOreSilicaPrediction_3*.

```sql
RESTORE DATABASE IronOreSilicaPrediction_3
FROM URL = 'https://mystorageaccount.blob.core.windows.net/mysecondcontainer/IronOreSilicaPrediction_2020_04_16.bak'
WITH MOVE 'IronOreSilicaPrediction' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_3.mdf', 
MOVE 'IronOreSilicaPrediction_log' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_3.ldf',
STATS = 10;
```