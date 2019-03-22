---
title: Gerir e monitorizar bases de dados do SQL Server numa VM do Azure que é apoiada por cópia de segurança do Azure | Documentos da Microsoft
description: Este artigo descreve como gerir e monitorizar bases de dados do SQL Server em execução numa VM do Azure.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/14/2018
ms.author: raynew
ms.openlocfilehash: 500986478e554a3a114d11ee4b25ea40b5decd97
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58004126"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>Gerir e monitorizar backup de bancos de dados do SQL Server


Este artigo descreve as tarefas comuns para gerir e monitorizar bases de dados do SQL Server que estão em execução numa máquina virtual do Azure (VM) e que são copiadas para uma cópia de segurança do Azure Recovery Services cofre o [Azure Backup](backup-overview.md) serviço. Aprenderá a monitorizar tarefas e alertas, parar e retomar a proteção de base de dados, executar tarefas de cópia de segurança e anular o registo de uma VM a partir de cópias de segurança.

Se ainda não tiver configurado cópias de segurança para seus bancos de dados do SQL Server, consulte [cópia de segurança de bases de dados do SQL Server em VMs do Azure](backup-azure-sql-database.md)

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>Monitorizar tarefas de cópia de segurança manuais no portal

Cópia de segurança do Azure mostra todas as tarefas manualmente acionadas no **tarefas de cópia de segurança** portal. As tarefas de ver esta deteção de base de dados de inclusão de portal e registrar e cópia de segurança e restaurar as operações.

![O portal de tarefas de cópia de segurança](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> O **tarefas de cópia de segurança** portal não mostra as tarefas de cópia de segurança agendadas. Utilize SQL Server Management Studio para monitorizar tarefas de cópia de segurança agendadas, conforme descrito na secção seguinte.
>

Para obter detalhes sobre os cenários de monitorização, aceda a [monitorização no Portal do Azure](backup-azure-monitoring-built-in-monitor.md) e [monitorização com o Azure Monitor](backup-azure-monitoring-use-azuremonitor.md).  


## <a name="view-backup-alerts"></a>Ver alertas de cópia de segurança

Porque os backups de log ocorrem a cada 15 minutos, a monitorizar tarefas de cópia de segurança pode ser enfadonho. Cópia de segurança do Azure facilita a monitorização ao enviar alertas por e-mail. Os alertas de e-mail são:

- Acionada para todas as falhas de cópia de segurança.
- Consolidados ao nível da base de dados por código de erro.
- Enviadas apenas para a primeira falha de cópia de segurança da base de dados.

Para monitorizar alertas de cópias de segurança da base de dados:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. No dashboard do cofre, selecione **alertas e eventos**.

   ![Selecione os alertas e eventos](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

3. Na **eventos e alertas**, selecione **alertas de cópia de segurança**.

   ![Selecione os alertas de cópia de segurança](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

## <a name="stop-protection-for-a-sql-server-database"></a>Parar a proteção de uma base de dados do SQL Server

É possível parar o backup de uma base de dados do SQL Server de duas formas:

* Parar todas as tarefas de cópia de segurança futuras e eliminar todos os pontos de recuperação.
* Parar todas as tarefas de cópia de segurança futuras e deixar os pontos de recuperação intacto.

Se optar por deixar os pontos de recuperação, considere estes detalhes:

* Todos os pontos de recuperação permanecerão intactos para sempre, a eliminação de todos os deve parar em Parar proteção com manutenção dos dados.
* Será cobrada a instância protegida e o armazenamento consumido. Para obter mais informações, consulte [preços de cópia de segurança do Azure](https://azure.microsoft.com/pricing/details/backup/).
* Se eliminar uma origem de dados sem parar as cópias de segurança, novas cópias de segurança irão falhar.

Para parar a proteção para uma base de dados:

1. No dashboard do cofre, selecione **itens de cópia de segurança**.

2. Sob **tipo de gestão de cópia de segurança**, selecione **SQL na VM do Azure**.

    ![Selecione o SQL numa VM do Azure](./media/backup-azure-sql-database/sql-restore-backup-items.png)

3. Selecione a base de dados para o qual pretende parar a proteção.

    ![Selecione a base de dados para parar a proteção](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

4. No menu da base de dados, selecione **parar cópia de segurança**.

    ![Selecione parar cópia de segurança](./media/backup-azure-sql-database/stop-db-button.png)


5. Sobre o **parar cópia de segurança** menu, selecione se pretende manter ou eliminar os dados. Se pretender, forneça um motivo e um comentário.

    ![Manter ou eliminar os dados no menu parar cópia de segurança](./media/backup-azure-sql-database/stop-backup-button.png)

6. Selecione **parar cópia de segurança**.


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

## <a name="re-register-extension-on-the-sql-server-vm"></a>Voltar a registar a extensão na VM do SQL Server

Às vezes, a extensão de carga de trabalho na VM pode ser afetada por um motivo ou outro. Nesses casos, todas as operações acionadas na VM começarão a falhar. Em seguida, terá de voltar a registar a extensão na VM. **Voltar a registar** operação reinstala a extensão de cópia de segurança da carga de trabalho na VM para a continuidade das operações.  <br>

Recomenda-se para utilizar esta opção com cuidado; Quando acionado numa VM com a extensão já em bom estada, esta operação fará com que a extensão para obter a ser reiniciado. Isso pode resultar em todas as tarefas em curso para efetuar a ativação. Consulte para um ou mais da [sintomas](backup-sql-server-azure-troubleshoot.md#symptoms) antes de acionar a operação de voltar a registar.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações, consulte [resolver problemas de cópias de segurança num banco de dados do SQL Server](backup-sql-server-azure-troubleshoot.md).
