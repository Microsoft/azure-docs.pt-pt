---
title: Migrar uma base de dados do SQL Server para O Arco Azure habilitado para a sql Gestão de Instâncias
description: Migrar base de dados do SQL Server para Azure Arc ativado SQL Gestdited Instance
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 86563b0a44bade2cedaf76af3c247821756111fe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90939632"
---
# <a name="migrate-sql-server-to-azure-arc-enabled-sql-managed-instance"></a>Migrar: SQL Server para Azure Arc ativado SQL Gestdited Instance

Este cenário percorre os passos para migrar uma base de dados de uma instância do SQL Server para Azure SQL gerido em Azure Arc através de dois diferentes métodos de backup e restauro.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="use-azure-blob-storage"></a>Use o armazenamento de bolhas Azure 

Utilize o armazenamento de bolhas Azure para migrar para Azure Arc ativado SQL Managed Instance.

Este método utiliza o Azure Blob Storage como um local de armazenamento temporário que pode voltar atrás e depois restaurar.

### <a name="prerequisites"></a>Pré-requisitos

- [Instalar o Azure Data Studio](install-client-tools.md)
- [Instalar explorador de armazenamento Azure](https://azure.microsoft.com/features/storage-explorer/)
- Subscrição do Azure

### <a name="step-1-provision-azure-blob-storage"></a>Passo 1: Armazenamento de bolhas Azure Provision Azure

1. Siga os passos descritos na [Criar uma conta de armazenamento Azure Blob](../../storage/blobs/storage-blob-create-account-block-blob.md?tabs=azure-portal)
1. Launch Azure Storage Explorer
1. [Inscreva-se no Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#sign-in-to-azure) para aceder ao armazenamento de bolhas criado em passo anterior
1. Clique com o botão direito na conta de armazenamento do blob e selecione **Criar o Recipiente Blob** para criar um novo recipiente onde o ficheiro de cópia de segurança será armazenado

### <a name="step-2-get-storage-blob-credentials"></a>Passo 2: Obter credenciais de bolha de armazenamento

1. No Azure Storage Explorer, clique à direita no recipiente blob que acabou de ser criado e selecione **Obter Assinatura de Acesso Partilhado**

1. Selecione a **Leitura,** **Escrita** e **Lista**

1. Selecione **Criar**

   Tome nota do URI e da Sequência de Consultas deste ecrã. Estes serão necessários em etapas posteriores. Clique no botão **Copiar** para guardar para um Bloco de Notas/OneNote, etc.

1. Feche a janela **Assinatura de Acesso Partilhado.**

### <a name="step-3-backup-database-file-to-azure-blob-storage"></a>Passo 3: Ficheiro de base de dados de backup para o armazenamento do Blob Azure

Neste passo, vamos ligar-nos ao SqL Server de origem e criar o ficheiro de backup da base de dados que queremos migrar para o SQL Managed Instance - Azure Arc.

1. Lançar Azure Data Studio
1. Ligue-se à instância do SQL Server que tem a base de dados que pretende migrar para a SQL Managed Instance - Azure Arc
1. Clique com o botão direito na base de dados e **selecione Nova Consulta**
1. Prepare a sua consulta no seguinte formato substituindo os espaços reservados indicados pela `<...>` utilização das informações da assinatura de acesso partilhado em etapas anteriores.  Depois de ter substituído os valores, faça a consulta.

   ```sql
   IF NOT EXISTS  
   (SELECT * FROM sys.credentials
   WHERE name = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>')  
   CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
     WITH IDENTITY = 'SHARED ACCESS SIGNATURE',  
      SECRET = '<SAS_TOKEN>';  
   ```

1. Da mesma forma, prepare o comando **BASE DE BASE DE BACKUP** da seguinte forma para criar um ficheiro de reserva no recipiente blob.  Depois de ter substituído os valores, faça a consulta.

   ```sql
   BACKUP DATABASE <database name> TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>'
   ```

1. Abra o Explorador de Armazenamento Azure e valide que o ficheiro de backup criado no passo anterior é visível no recipiente Blob

### <a name="step-4-restore-the-database-from-azure-blob-storage-to-sql-managed-instance---azure-arc"></a>Passo 4: Restaurar a base de dados do armazenamento de blob Azure para SQL Managed Instance - Azure Arc

1. A partir do Azure Data Studio, faça login e ligue-se à SQL Managed Instance - Azure Arc.
1. Expandir as **Bases de Dados do Sistema,** clicar com o botão direito na base de dados **principal** e selecionar **Nova Consulta**.
1. Na janela do editor de consulta, prepare e execute a mesma consulta do passo anterior para criar as credenciais.

   ```sql
   IF NOT EXISTS  
   (SELECT * FROM sys.credentials
   WHERE name = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>')  
   CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
     WITH IDENTITY = 'SHARED ACCESS SIGNATURE',  
     SECRET = '<SAS_TOKEN>';  
   ```

1. Prepare e corra o comando abaixo para verificar se o ficheiro de reserva é legível e intacto.

   ```console
   RESTORE FILELISTONLY FROM URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>/<file name>.bak'
   ```

1. Prepare e execute o comando **RESTORE DATABASE** da seguinte forma para restaurar o ficheiro de cópia de segurança numa base de dados em SQL Managed Instance - Azure Arc

   ```sql
   RESTORE DATABASE <database name> FROM URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>/<file name>'
   WITH MOVE 'Test' to '/var/opt/mssql/data/<file name>.mdf'
   ,MOVE 'Test_log' to '/var/opt/mssql/data/<file name>.ldf'
   ,RECOVERY  
   ,REPLACE  
   ,STATS = 5;  
   GO
   ```

Saiba mais sobre o backup para URL aqui:

[Backup para docs URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url)

[Backup para URL usando SQL Server Management Studio (SSMS)](/sql/relational-databases/tutorial-sql-server-backup-and-restore-to-azure-blob-storage-service)

-------

## <a name="method-2-copy-the-backup-file-into-an-azure-sql-managed-instance---azure-arc-pod-using-kubectl"></a>Método 2: Copiar o ficheiro de cópia de segurança numa Instância Gerida Azure SQL - Azure Arc pod usando kubectl

Este método mostra-lhe como pegar num ficheiro de backup que cria através de qualquer método e depois copiá-lo para armazenamento local no pod de instância gerido Azure SQL para que possa restaurar a partir daí muito como faria num sistema de ficheiros típico no Windows ou Linux. Neste cenário, irá utilizar o comando `kubectl cp` para copiar o ficheiro de um lugar para o sistema de ficheiros da cápsula.

### <a name="prerequisites"></a>Pré-requisitos

- Instale e configure kubectl para apontar para o seu cluster Kubernetes onde os serviços de dados do Azure Arc são implantados
- Tenha uma ferramenta como o Azure Data Studio ou o SQL Server Management Server instalado e ligado ao SQL Server onde pretende criar o ficheiro de cópia de segurança ou ter um ficheiro .bak já criado no seu sistema de ficheiros local.

### <a name="step-1-backup-the-database-if-you-havent-already"></a>Passo 1: Faça cópia de segurança da base de dados se ainda não o fez

Faça o backup da base de dados do SQL Server para o seu caminho de ficheiro local como qualquer cópia de segurança típica do SQL Server para o disco:

 ```sql
BACKUP DATABASE Test
TO DISK = 'c:\tmp\test.bak'
WITH FORMAT, MEDIANAME = 'Test’ ;
GO
```

### <a name="step-2-copy-the-backup-file-into-the-pods-file-system"></a>Passo 2: Copiar o ficheiro de cópia de segurança no sistema de ficheiros da cápsula

Encontre o nome da cápsula onde a instância sql é implantada. Tipicamente deve parecer `pod/<sqlinstancename>-0`

Obtenha a lista de todas as cápsulas correndo:

 ```console
kubectl get pods -n <namespace of data controller>
```

Exemplo:

Copie o ficheiro de reserva do armazenamento local para a cápsula sql no cluster.

 ```console
kubectl cp <source file location> <pod name>:var/opt/mssql/data/<file name> -n <namespace name>

#Example:
kubectl cp C:\Backupfiles\test.bak sqlinstance1-0:var/opt/mssql/data/test.bak -n arc
```

### <a name="step-3-restore-the-database"></a>Passo 3: Restaurar a base de dados

Prepare e execute o comando RESTORE para restaurar o ficheiro de backup para a instância gerida Azure SQL - Azure Arc

```sql
RESTORE DATABASE test FROM DISK = '/var/opt/mssql/data/<file name>.bak'
WITH MOVE '<database name>' to '/var/opt/mssql/data/<file name>.mdf'  
,MOVE '<database name>' to '/var/opt/mssql/data/<file name>_log.ldf'  
,RECOVERY  
,REPLACE  
,STATS = 5;  
GO
```

Exemplo:

```sql
RESTORE DATABASE test FROM DISK = '/var/opt/mssql/data/test.bak'
WITH MOVE 'test' to '/var/opt/mssql/data/test.mdf'  
,MOVE 'test' to '/var/opt/mssql/data/test_log.ldf'  
,RECOVERY  
,REPLACE  
,STATS = 5;  
GO
```


## <a name="next-steps"></a>Passos seguintes

[Saiba mais sobre funcionalidades e capacidades do Arco Azure habilitado para a SQL Gestão de Instância](managed-instance-features.md)

[Comece por criar um Controlador de Dados](create-data-controller.md)

[Já criou um Controlador de Dados? Criar um Arco Azure habilitado sql instância gerida](create-sql-managed-instance.md)