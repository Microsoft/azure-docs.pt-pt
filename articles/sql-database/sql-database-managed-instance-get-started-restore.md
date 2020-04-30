---
title: Restaurar uma cópia de segurança para a instância gerida
description: Restaure uma cópia de segurança para uma Instância Gerida da Base de Dados SQL do Azure através do SSMS.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab, bonova
ms.date: 12/14/2018
ms.openlocfilehash: 3b12aa860bee189f706bd98df63fc194a3a0cc71
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80874709"
---
# <a name="quickstart-restore-a-database-to-a-managed-instance"></a>Quickstart: Restaurar uma base de dados para uma instância gerida

Neste arranque rápido, utilizará o SQL Server Management Studio (SSMS) para restaurar uma base de dados (o Wide World Importers - Ficheiro de backup standard) do armazenamento Azure Blob numa [instância gerida](sql-database-managed-instance.md)pela Base de Dados Azure SQL.

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]

> [!NOTE]
> Para obter mais informações sobre migração utilizando o Serviço de Migração de Bases de Dados Azure (DMS), consulte a migração de [instâncias geridas utilizando DMS](../dms/tutorial-sql-server-to-managed-instance.md).
> Para obter mais informações sobre vários métodos de migração, consulte a migração por exemplo do SQL Server para a Instância gerida pela Base de [Dados Azure SQL](sql-database-managed-instance-migrate.md).

## <a name="prerequisites"></a>Pré-requisitos

Este guia de início rápido:

- Utiliza recursos do início rápido da [Create a Managed Instance.](sql-database-managed-instance-get-started.md)
- Requer que o seu computador tenha o mais recente Estúdio de [Gestão de Servidores SQL](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) instalado.
- Requer a utilização de SSMS para se ligar à sua Instância Gerida. Veja estes quickstarts sobre como ligar:
  - [Ativar o ponto final público](sql-database-managed-instance-public-endpoint-configure.md) em Caso Gerido - esta é uma abordagem recomendada para este tutorial.
  - [Ligar a uma Instância Gerida da Base de Dados SQL do Azure a partir de uma VM do Azure](sql-database-managed-instance-configure-vm.md)
  - [Configure uma ligação ponto-a-local a uma base de dados Azure SQL Gerida a partir do local](sql-database-managed-instance-configure-p2s.md).

> [!NOTE]
> Para obter mais informações sobre o backup e restauro de uma base de dados do SQL Server utilizando o armazenamento Do Blob e uma [chave Signature de Acesso Partilhado (SAS),](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)consulte a Cópia de Segurança do Servidor [SQL para URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url?view=sql-server-2017).

## <a name="restore-the-database-from-a-backup-file"></a>Restaurar a base de dados de um ficheiro de cópia de segurança

No SSMS, siga estes passos para restaurar a base de dados dos Importadores do Mundo Largo para a sua Instância Gerida. O ficheiro de backup da base de dados é armazenado numa conta de armazenamento Azure Blob pré-configurada.

1. Abra o SSMS e ligue-se à sua Instância Gerida.
2. A partir do menu à esquerda, clique na direita na sua Instância Gerida e selecione **New Consulta** para abrir uma nova janela de consulta.
3. Executar o seguinte script SQL, que usa uma conta de armazenamento pré-configurada e chave SAS para [criar uma credencial](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql) na sua Instância Gerida.

   ```sql
   CREATE CREDENTIAL [https://mitutorials.blob.core.windows.net/databases]
   WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
   , SECRET = 'sv=2017-11-09&ss=bfqt&srt=sco&sp=rwdlacup&se=2028-09-06T02:52:55Z&st=2018-09-04T18:52:55Z&spr=https&sig=WOTiM%2FS4GVF%2FEEs9DGQR9Im0W%2BwndxW2CQ7%2B5fHd7Is%3D'
   ```

    ![Criar credencial](./media/sql-database-managed-instance-get-started-restore/credential.png)

4. Para verificar a sua credencial, execute o seguinte script, que utiliza um URL de [recipiente](https://azure.microsoft.com/services/container-instances/) para obter uma lista de ficheiros de reserva.

   ```sql
   RESTORE FILELISTONLY FROM URL =
      'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![lista de ficheiros](./media/sql-database-managed-instance-get-started-restore/file-list.png)

5. Execute o seguinte guião para restaurar a base de dados dos Importadores do Mundo Largo.

   ```sql
   RESTORE DATABASE [Wide World Importers] FROM URL =
     'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![restore](./media/sql-database-managed-instance-get-started-restore/restore.png)

6. Execute o seguinte script para rastrear o estado do seu restauro.

   ```sql
   SELECT session_id as SPID, command, a.text AS Query, start_time, percent_complete
      , dateadd(second,estimated_completion_time/1000, getdate()) as estimated_completion_time
   FROM sys.dm_exec_requests r
   CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) a
   WHERE r.command in ('BACKUP DATABASE','RESTORE DATABASE')
   ```

7. Quando o restauro estiver concluído, consulte a base de dados no Object Explorer. Pode verificar se a restauração da base de dados está concluída utilizando [sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) visualização.

> [!NOTE]
> A operação de restauro da base de dados é assíncrona e retriável. Pode ter algum erro é o SQL Server Management Studio se a ligação se rompe ou algum tempo de saída expirar. A Base de Dados Azure SQL continuará a tentar restaurar a base de dados em segundo plano, podendo acompanhar o progresso da restauração utilizando as vistas [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) e [sys.dm_operation_status.](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)
> Em algumas fases do processo de restauro, você verá identificador único em vez de nome real de base de dados nas vistas do sistema. Conheça `RESTORE` as diferenças de comportamento da afirmação [aqui](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-transact-sql-information#restore-statement).

## <a name="next-steps"></a>Passos seguintes

- Para resolver um backup para um URL, consulte [o SQL Server Backup para URL Best Practices e Troubleshooting](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting).
- Para uma visão geral das opções de ligação de aplicações, consulte [Connect your applications to Managed Instance](sql-database-managed-instance-connect-app.md).
- Para consultar utilizando as suas ferramentas ou idiomas favoritos, consulte [Quickstarts: Azure SQL Database Connect and Query](sql-database-connect-query.md).
