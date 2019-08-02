---
title: Gerenciar e monitorar bancos de dados SQL Server em uma VM do Azure que é submetida a backup pelo backup do Azure | Microsoft Docs
description: Este artigo descreve como gerenciar e monitorar SQL Server bancos de dados que estão em execução em uma VM do Azure.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/14/2018
ms.author: dacurwin
ms.openlocfilehash: c5f61c778849a57d9143580e0e1041c7fe992d52
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639583"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>Gerenciar e monitorar o backup de bancos de dados SQL Server

Este artigo descreve as tarefas comuns para gerenciar e monitorar SQL Server bancos de dados que estão em execução em uma VM (máquina virtual) do Azure e cujo backup é feito em um cofre dos serviços de recuperação de backup do Azure pelo serviço de [backup do Azure](backup-overview.md) . Você aprenderá como monitorar trabalhos e alertas, parar e retomar a proteção de banco de dados, executar trabalhos de backup e cancelar o registro de uma VM de backups.

Se você ainda não tiver configurado backups para seus bancos de dados SQL Server, consulte [fazer backup de bancos de dados SQL Server em VMs do Azure](backup-azure-sql-database.md)

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>Monitorar trabalhos de backup manuais no portal

O backup do Azure mostra todos os trabalhos disparados manualmente no portal de **trabalhos de backup** . Os trabalhos que você vê nesse portal incluem descoberta de banco de dados e registro e operações de backup e restauração.

![O portal de trabalhos de backup](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> O portal de **trabalhos de backup** não mostra os trabalhos de backup agendados. Use SQL Server Management Studio para monitorar os trabalhos de backup agendados, conforme descrito na próxima seção.
>

Para obter detalhes sobre cenários de monitoramento, acesse [monitoramento na portal do Azure](backup-azure-monitoring-built-in-monitor.md) e [monitoramento usando Azure monitor](backup-azure-monitoring-use-azuremonitor.md).  


## <a name="view-backup-alerts"></a>Exibir alertas de backup

Como os backups de log ocorrem a cada 15 minutos, o monitoramento de trabalhos de backup pode ser entediante. O backup do Azure facilita o monitoramento enviando alertas de email. Os alertas de email são:

- Disparado para todas as falhas de backup.
- Consolidado no nível do banco de dados por código de erro.
- Enviado somente para a primeira falha de backup de um banco de dados.

Para monitorar alertas de backup de banco de dados:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. No painel do cofre, selecione **alertas e eventos**.

   ![Selecionar alertas e eventos](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

3. Em **alertas e eventos**, selecione **alertas de backup**.

   ![Selecionar alertas de backup](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

## <a name="stop-protection-for-a-sql-server-database"></a>Interromper a proteção de um banco de dados SQL Server

Você pode interromper o backup de um banco de dados SQL Server de duas maneiras:

* Pare todos os trabalhos de backup futuros e exclua todos os pontos de recuperação.
* Pare todos os trabalhos de backup futuros e deixe os pontos de recuperação intactos.

Se você optar por deixar os pontos de recuperação, tenha em mente estes detalhes:

* Todos os pontos de recuperação permanecerão intactos para sempre, toda a remoção será interrompida em parar proteção com manter dados.
* Você será cobrado pela instância protegida e pelo armazenamento consumido. Para obter mais informações, consulte [preços do backup do Azure](https://azure.microsoft.com/pricing/details/backup/).
* Se você excluir uma fonte de dados sem interromper os backups, novos backups falharão.

Para interromper a proteção de um banco de dados:

1. No painel do cofre, selecione **itens de backup**.

2. Em **tipo de gerenciamento de backup**, selecione **SQL na VM do Azure**.

    ![Selecionar SQL na VM do Azure](./media/backup-azure-sql-database/sql-restore-backup-items.png)

3. Selecione o banco de dados para o qual você deseja interromper a proteção.

    ![Selecione o banco de dados para interromper a proteção](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

4. No menu banco de dados, selecione **parar backup**.

    ![Selecione parar backup](./media/backup-azure-sql-database/stop-db-button.png)


5. No menu **parar backup** , selecione se deseja reter ou excluir dados. Se desejar, forneça um motivo e um comentário.

    ![Reter ou excluir dados no menu parar backup](./media/backup-azure-sql-database/stop-backup-button.png)

6. Selecione **parar backup**.


> [!NOTE]
>
Consulte as perguntas frequentes abaixo para obter mais informações sobre a opção excluir dados:
* [Se eu excluir um banco de dados de uma instância autoprotegida, o que acontecerá com os backups?](faq-backup-sql-server.md#if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups)
* [Se eu parar a operação de backup de um banco de dados autoprotegido, qual será seu comportamento?](faq-backup-sql-server.md#if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior)
>
>


## <a name="resume-protection-for-a-sql-database"></a>Retomar a proteção para um banco de dados SQL

Ao interromper a proteção para o banco de dados SQL, se você selecionar a opção **reter backup data** , poderá posteriormente continuar a proteção. Se você não mantiver os dados de backup, não poderá retomar a proteção.

Para retomar a proteção de um banco de dados SQL:

1. Abra o item de backup e selecione **retomar backup**.

    ![Selecione retomar backup para retomar a proteção de banco de dados](./media/backup-azure-sql-database/resume-backup-button.png)

2. No menu **política de backup** , selecione uma política e, em seguida, selecione **salvar**.

## <a name="run-an-on-demand-backup"></a>Executar um backup sob demanda

Você pode executar diferentes tipos de backups sob demanda:

* Cópia de segurança completa
* Backup completo somente cópia
* Cópia de segurança diferencial
* Cópia de segurança de registo

Embora você precise especificar a duração da retenção para backup completo somente cópia, o período de retenção para outros tipos de backup é definido automaticamente como 30 dias a partir da hora atual. <br/>
Para obter mais informações, consulte [SQL Server tipos de backup](backup-architecture.md#sql-server-backup-types).

## <a name="unregister-a-sql-server-instance"></a>Cancelar o registro de uma instância de SQL Server

Cancele o registro de uma instância de SQL Server depois de desabilitar a proteção, mas antes de excluir o cofre:

1. No painel do cofre, em **gerenciar**, selecione **infraestrutura de backup**.  

   ![Selecionar a infraestrutura de backup](./media/backup-azure-sql-database/backup-infrastructure-button.png)

2. Em **servidores de gerenciamento**, selecione **servidores protegidos**.

   ![Selecionar servidores protegidos](./media/backup-azure-sql-database/protected-servers.png)

3. Em **servidores protegidos**, selecione o servidor para cancelar o registro. Para excluir o cofre, você deve cancelar o registro de todos os servidores.

4. Clique com o botão direito do mouse no servidor protegido e selecione **Cancelar registro**.

   ![Selecione Excluir](./media/backup-azure-sql-database/delete-protected-server.jpg)

## <a name="re-register-extension-on-the-sql-server-vm"></a>Registrar novamente a extensão na VM SQL Server

Às vezes, a extensão de carga de trabalho na VM pode ser afetada por um motivo ou pela outra. Nesses casos, todas as operações disparadas na VM começarão a falhar. Você pode precisar registrar novamente a extensão na VM. A operação de **novo registro** reinstala a extensão de backup de carga de trabalho na VM para que as operações continuem.  <br>

É aconselhável usar essa opção com cuidado; Quando disparado em uma VM com uma extensão já íntegra, essa operação fará com que a extensão seja reiniciada. Isso pode resultar na falha de todos os trabalhos em andamento. Por outro, verifique um ou mais [sintomas](backup-sql-server-azure-troubleshoot.md#re-registration-failures) antes de disparar a operação de novo registro.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações, consulte [solucionar problemas de backups em um banco de dados SQL Server](backup-sql-server-azure-troubleshoot.md).
