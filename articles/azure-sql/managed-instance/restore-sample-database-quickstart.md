---
title: 'Quickstart: Restaurar uma cópia de segurança (SSMS)'
titleSuffix: Azure SQL Managed Instance
description: Neste quickstart, aprenda a restaurar uma cópia de segurança da base de dados para Azure SQL Managed Instance usando SQL Server Management Studio (SSMS).
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 12/14/2018
ms.openlocfilehash: 15aead5c9f93205baac28e8b93a88015c9689e3a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105625318"
---
# <a name="quickstart-restore-a-database-to-azure-sql-managed-instance-with-ssms"></a>Quickstart: Restaurar uma base de dados para Azure SQL Caso Gerido com SSMS
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Neste quickstart, você usará o SQL Server Management Studio (SSMS) para restaurar uma base de dados (o ficheiro de backup Standard World Importers - Standard) do armazenamento Azure Blob para [Azure SQL Managed Instance](sql-managed-instance-paas-overview.md).

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]

> [!NOTE]
> Para obter mais informações sobre migração utilizando o Serviço de Migração da Base de Dados Azure, consulte [a migração de instâncias geridas SQL utilizando o Serviço de Migração de Bases de Dados](../../dms/tutorial-sql-server-to-managed-instance.md).
> Para obter mais informações sobre vários métodos de migração, consulte [a migração do SQL Server para Azure SQL Managed Instance](migrate-to-instance-from-sql-server.md).

## <a name="prerequisites"></a>Pré-requisitos

Este guia de início rápido:

- Utiliza recursos da [Create a managed instance](instance-create-quickstart.md) quickstart.
- Requer a versão mais recente do [SSMS](/sql/ssms/sql-server-management-studio-ssms) instalada.
- Requer a utilização de SSMS para ligar à SQL Managed Instance. Consulte estes quickstarts sobre como ligar:
  - [Ativar um ponto final público](public-endpoint-configure.md) em SQL Managed Instance - esta é a abordagem recomendada para este tutorial.
  - [Ligue-se à SQL Managed Instance a partir de um Azure VM](connect-vm-instance-configure.md).
  - [Configure uma ligação ponto-a-local com a SQL Managed Instance a partir do local](point-to-site-p2s-configure.md).

> [!NOTE]
> Para obter mais informações sobre o backup e restauro de uma base de dados do SQL Server utilizando o armazenamento Azure Blob e uma [chave assinatura de acesso partilhado (SAS),](../../storage/common/storage-sas-overview.md)consulte o [SQL Server Backup para URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url).

## <a name="restore-from-a-backup-file"></a>Restaurar a partir de um ficheiro de reserva

No SQL Server Management Studio, siga estes passos para restaurar a base de dados de importadores do mundo largo para SQL Managed Instance. O ficheiro de backup da base de dados é armazenado numa conta de armazenamento Azure Blob pré-configurada.

1. Abra o SSMS e ligue-se à sua instância gerida.
2. No **Object Explorer,** clique com o botão direito na sua instância gerida e selecione **New Consulta** para abrir uma nova janela de consulta.
3. Execute o seguinte script SQL, que utiliza uma conta de armazenamento pré-configurada e uma chave SAS para [criar uma credencial](/sql/t-sql/statements/create-credential-transact-sql) no seu caso gerido.
 
   > [!IMPORTANT]
   > `CREDENTIAL` deve combinar com o caminho do recipiente, comece `https` com, e não pode conter um corte para a frente. `IDENTITY` deve ser `SHARED ACCESS SIGNATURE` . `SECRET` deve ser o token assinatura de acesso partilhado e não pode conter um líder `?` .

   ```sql
   CREATE CREDENTIAL [https://mitutorials.blob.core.windows.net/databases]
   WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
   , SECRET = 'sv=2017-11-09&ss=bfqt&srt=sco&sp=rwdlacup&se=2028-09-06T02:52:55Z&st=2018-09-04T18:52:55Z&spr=https&sig=WOTiM%2FS4GVF%2FEEs9DGQR9Im0W%2BwndxW2CQ7%2B5fHd7Is%3D'
   ```

    ![Criar credencial](./media/restore-sample-database-quickstart/credential.png)

4. Para verificar a sua credencial, execute o seguinte script, que utiliza um URL [de contentor](https://azure.microsoft.com/services/container-instances/) para obter uma lista de ficheiros de reserva.

   ```sql
   RESTORE FILELISTONLY FROM URL =
      'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![lista de ficheiros](./media/restore-sample-database-quickstart/file-list.png)

5. Execute o seguinte script para restaurar a base de dados de importadores do mundo largo.

   ```sql
   RESTORE DATABASE [Wide World Importers] FROM URL =
     'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![A screenshot mostra o script a correr no Object Explorer com uma mensagem de sucesso.](./media/restore-sample-database-quickstart/restore.png)

6. Execute o seguinte script para acompanhar o estado da sua restauração.

   ```sql
   SELECT session_id as SPID, command, a.text AS Query, start_time, percent_complete
      , dateadd(second,estimated_completion_time/1000, getdate()) as estimated_completion_time
   FROM sys.dm_exec_requests r
   CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) a
   WHERE r.command in ('BACKUP DATABASE','RESTORE DATABASE')
   ```

7. Quando a restauração estiver concluída, consulte a base de dados no Object Explorer. Pode verificar se a restauração da base de dados está concluída utilizando a [vista sys.dm_operation_status.](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)

> [!NOTE]
> Uma operação de restauro de base de dados é assíncrol e recímível. Pode obter um erro no SQL Server Management Studio se a ligação se partir ou se expirar uma pausa. A Azure SQL Database continuará a tentar restaurar a base de dados em segundo plano, e pode acompanhar o progresso da restauração utilizando as [vistas sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) e [sys.dm_operation_status.](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)
> Em algumas fases do processo de restauro, você verá um identificador único em vez do nome da base de dados real nas vistas do sistema. Saiba mais sobre `RESTORE` as diferenças de comportamento de afirmação [aqui.](./transact-sql-tsql-differences-sql-server.md#restore-statement)

## <a name="next-steps"></a>Passos seguintes

- Se, no passo 5, uma restauração da base de dados for terminada com o ID 22003 da mensagem, crie um novo ficheiro de backup contendo as cópias de segurança e efetue novamente a restauração. Consulte [Ativar ou desativar as contas de cópia de segurança durante a cópia de segurança ou restaurar](/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server).
- Para resolver problemas com uma cópia de segurança de um URL, consulte [o SQL Server Backup para as melhores práticas de URL e resolução de problemas](/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting).
- Para obter uma visão geral das opções de ligação de aplicações, consulte [Connect your applications to SQL Managed Instance](connect-application-instance.md).
- Para consultar as suas ferramentas ou idiomas favoritos, consulte [Quickstarts: Azure SQL Database connect and questionry](../database/connect-query-content-reference-guide.md).
