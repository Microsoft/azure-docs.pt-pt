---
title: Gerir e monitorizar bases de dados do SQL Server numa VM do Azure que é apoiada por cópia de segurança do Azure | Documentos da Microsoft
description: Este artigo descreve como restaurar bases de dados do SQL Server que estão em execução numa VM do Azure e que estão uma cópia de segurança pelo Azure Backup.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/19/2018
ms.author: raynew
ms.openlocfilehash: da4264047830b21b3ac4dae723dd1fd2f9d7a8f4
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57432860"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>Gerir e monitorizar backup de bancos de dados do SQL Server 


Este artigo descreve as tarefas comuns para gerir e monitorizar bases de dados do SQL Server que estão em execução numa máquina virtual do Azure (VM) e que são copiadas para uma cópia de segurança do Azure Recovery Services cofre o [Azure Backup](backup-overview.md) serviço. Aprenderá a monitorizar tarefas e alertas, parar e retomar a proteção de base de dados, executar tarefas de cópia de segurança e anular o registo de uma VM a partir de cópias de segurança.


> [!NOTE]
> A cópia de segurança de bases de dados do SQL Server em execução numa VM do Azure com o Azure Backup está atualmente em pré-visualização pública.


Se ainda não tiver configurado cópias de segurança para seus bancos de dados do SQL Server, consulte [cópia de segurança de bases de dados do SQL Server em VMs do Azure](backup-azure-sql-database.md)

##  <a name="monitor-manual-backup-jobs-in-the-portal"></a>Monitorizar tarefas de cópia de segurança manuais no portal

Cópia de segurança do Azure mostra todas as tarefas manualmente acionadas no **tarefas de cópia de segurança** portal. As tarefas de ver esta deteção de base de dados de inclusão de portal e registrar e cópia de segurança e restaurar as operações.

![O portal de tarefas de cópia de segurança](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> O **tarefas de cópia de segurança** portal não mostra as tarefas de cópia de segurança agendadas. Utilize SQL Server Management Studio para monitorizar tarefas de cópia de segurança agendadas, conforme descrito na secção seguinte.
>

## <a name="monitor-scheduled-backup-jobs-in-sql-server-management-studio"></a>Monitorizar tarefas de cópia de segurança agendadas no SQL Server Management Studio 

O Azure Backup utiliza as APIs nativas do SQL para todas as operações de cópia de segurança. Utilizar as APIs nativas para obter todas as informações de tarefa do [tabela de backupset SQL](https://docs.microsoft.com/sql/relational-databases/system-tables/backupset-transact-sql?view=sql-server-2017) na base de dados msdb.

O exemplo seguinte é uma consulta que obtém todas as tarefas de cópia de segurança para uma base de dados com o nome **DB1**. Personalize a consulta para a monitorização avançada.

```
select CAST (
Case type
                when 'D' 
                                 then 'Full'
                when  'I'
                               then 'Differential' 
                ELSE 'Log'
                END         
                AS varchar ) AS 'BackupType',
database_name, 
server_name,
machine_name,
backup_start_date,
backup_finish_date,
DATEDIFF(SECOND, backup_start_date, backup_finish_date) AS TimeTakenByBackupInSeconds,
backup_size AS BackupSizeInBytes
  from msdb.dbo.backupset where user_name = 'NT SERVICE\AzureWLBackupPluginSvc' AND database_name =  <DB1>  

```

## <a name="view-backup-alerts"></a>Ver alertas de cópia de segurança

Porque os backups de log ocorrem a cada 15 minutos, a monitorizar tarefas de cópia de segurança pode ser enfadonho. Cópia de segurança do Azure facilita a monitorização ao enviar alertas por e-mail. Os alertas de e-mail são:

- Acionada para todas as falhas de cópia de segurança.
- Consolidados ao nível da base de dados por código de erro.
- Enviadas apenas para a primeira falha de cópia de segurança da base de dados. 

Para monitorizar alertas de cópias de segurança da base de dados:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No dashboard do cofre, selecione **alertas e eventos**.

   ![Selecione os alertas e eventos](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

1. Na **eventos e alertas**, selecione **alertas de cópia de segurança**.

   ![Selecione os alertas de cópia de segurança](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

## <a name="stop-protection-for-a-sql-server-database"></a>Parar a proteção de uma base de dados do SQL Server

É possível parar o backup de uma base de dados do SQL Server de duas formas:

* Parar todas as tarefas de cópia de segurança futuras e eliminar todos os pontos de recuperação.
* Parar todas as tarefas de cópia de segurança futuras e deixar os pontos de recuperação intacto.

Se optar por deixar os pontos de recuperação, considere estes detalhes:

* Pontos de recuperação que deixar serão limpo, de acordo com a política de cópia de segurança. 
* Até que todos os pontos de recuperação são limpas, será cobrado para instância protegida e o armazenamento consumido. Para obter mais informações, consulte [preços de cópia de segurança do Azure](https://azure.microsoft.com/pricing/details/backup/).
* Cópia de segurança do Azure mantém sempre um último ponto de recuperação até que elimine os dados de cópia de segurança. 
* Se eliminar uma origem de dados sem parar as cópias de segurança, novas cópias de segurança irão falhar. 
* Se a sua base de dados estiver ativada para autoprotection, não é possível parar as cópias de segurança, a menos que desative autoprotection.

Para parar a proteção para uma base de dados:

1. No dashboard do cofre, sob **utilização**, selecione **itens de cópia de segurança**.

1. Sob **tipo de gestão de cópia de segurança**, selecione **SQL na VM do Azure**.

    ![Selecione o SQL numa VM do Azure](./media/backup-azure-sql-database/sql-restore-backup-items.png)


1. Selecione a base de dados para o qual pretende parar a proteção.

    ![Selecione a base de dados para parar a proteção](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)


1. No menu da base de dados, selecione **parar cópia de segurança**.

    ![Selecione parar cópia de segurança](./media/backup-azure-sql-database/stop-db-button.png)


1. Sobre o **parar cópia de segurança** menu, selecione se pretende manter ou eliminar os dados. Se pretender, forneça um motivo e um comentário.

    ![Manter ou eliminar os dados no menu parar cópia de segurança](./media/backup-azure-sql-database/stop-backup-button.png)

1. Selecione **parar cópia de segurança**.

  

## <a name="resume-protection-for-a-sql-database"></a>Retomar a proteção para uma base de dados SQL

Quando parar a proteção da base de dados SQL, se selecionar a **reter dados de cópia de segurança** opção, mais tarde pode retomar a proteção. Se não mantém os dados de cópia de segurança, não é possível retomar a proteção.

Para retomar a proteção para uma base de dados SQL:

1. Abra o item de cópia de segurança e selecione **retomar cópia de segurança**.

    ![Selecione a retomar a cópia de segurança para retomar a proteção de base de dados](./media/backup-azure-sql-database/resume-backup-button.png)

2. Sobre o **política de cópia de segurança** menu, selecione uma política e, em seguida, selecione **guardar**.

## <a name="run-an-on-demand-backup"></a>Executar cópias de segurança a pedido

Pode executar diferentes tipos de cópias de segurança a pedido:

* Cópia de segurança completa
* Cópia de segurança completa apenas de cópia
* Cópia de segurança diferencial
* Cópia de segurança de registo

Para obter mais informações, consulte [tipos de cópia de segurança do SQL Server](backup-architecture.md#sql-server-backup-types).

## <a name="unregister-a-sql-server-instance"></a>Anular o registo de uma instância do SQL Server

Depois de desativar a proteção, mas antes de eliminar o cofre, anular o registo uma instância do SQL Server:

1. No dashboard do cofre, sob **Manage**, selecione **infraestrutura de cópia de segurança**.  

   ![Selecione a infraestrutura de cópia de segurança](./media/backup-azure-sql-database/backup-infrastructure-button.png)

2. Sob **servidores de gestão**, selecione **servidores protegidos**.

   ![Selecione os servidores protegidos](./media/backup-azure-sql-database/protected-servers.png)


3. Na **servidores protegidos**, selecione o servidor ao anular o registo. Para eliminar o cofre, tem de anular o registo de todos os servidores.

4. O servidor protegido com o botão direito e selecione **eliminar**.

   ![Selecione Delete](./media/backup-azure-sql-database/delete-protected-server.png)


## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações, consulte [resolver problemas de cópias de segurança num banco de dados do SQL Server](backup-sql-server-azure-troubleshoot.md).
