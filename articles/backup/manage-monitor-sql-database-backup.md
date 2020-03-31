---
title: Gerir e monitorizar dBs do Servidor SQL num VM Azure
description: Este artigo descreve como gerir e monitorizar as bases de dados do SQL Server que estão a funcionar num VM Azure.
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 4daf068e97a08d1a611ef64cb64569cacd5d7420
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74172164"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>Gerir e monitorizar bases de dados do SQL Server guardadas em cópia de segurança

Este artigo descreve tarefas comuns para gerir e monitorizar as bases de dados do SQL Server que estão a funcionar numa máquina virtual Azure (VM) e que são apoiadas até um cofre dos Serviços de Recuperação de Backup Azure pelo serviço [de backup Azure.](backup-overview.md) Você vai aprender a monitorizar empregos e alertas, parar e retomar a proteção de bases de dados, executar trabalhos de backup e desregistar um VM de backups.

Se ainda não configurar cópias de segurança para as bases de dados do SQL Server, consulte as bases de [dados do SQL Server em VMs Azure](backup-azure-sql-database.md)

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>Monitorize trabalhos de backup manual no portal

O Azure Backup mostra todos os trabalhos desencadeados manualmente no portal de **trabalhos de backup.** Os trabalhos que você vê neste portal incluem descoberta e registo de bases de dados, e backup e restaurar operações.

![O portal de trabalhos de backup](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> O portal **de trabalhos de apoio** não mostra trabalhos de reserva programados. Utilize o SQL Server Management Studio para monitorizar os trabalhos de backup programados, conforme descrito na secção seguinte.
>

Para mais detalhes sobre os cenários de Monitorização, vá à [Monitorização no portal Azure](backup-azure-monitoring-built-in-monitor.md) e monitorização utilizando o [Monitor Azure.](backup-azure-monitoring-use-azuremonitor.md)  

## <a name="view-backup-alerts"></a>Ver alertas de backup

Como as cópias de segurança de registo ocorrem a cada 15 minutos, monitorizar os trabalhos de backup pode ser aborrecido. O Azure Backup facilita a monitorização enviando alertas de e-mail. Os alertas de e-mail são:

- Desencadeado por todas as falhas de reserva.
- Consolidado a nível da base de dados por código de erro.
- Enviado apenas para a primeira falha de reserva de uma base de dados.

Para monitorizar os alertas de backup da base de dados:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

2. No painel do cofre, selecione **Alertas e Eventos**.

   ![Selecione Alertas e Eventos](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

3. Em **Alertas e Eventos,** selecione **Alertas de Backup**.

   ![Selecione Alertas de Backup](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

## <a name="stop-protection-for-a-sql-server-database"></a>Parar a proteção para uma base de dados do Servidor SQL

Pode parar de fazer backup numa base de dados do SQL Server de várias maneiras:

- Pare todos os futuros trabalhos de backup e elimine todos os pontos de recuperação.
- Parem todos os futuros trabalhos de apoio e deixem intactos os pontos de recuperação.

Se optar por deixar pontos de recuperação, tenha em mente estes detalhes:

- Todos os pontos de recuperação permanecerão intactos para sempre, todas as podas devem parar na proteção stop com dados de retenção.
- Será cobrado pela instância protegida e pelo armazenamento consumido. Para mais informações, consulte o preço de [Backup Azure](https://azure.microsoft.com/pricing/details/backup/).
- Se eliminar uma fonte de dados sem parar cópias de segurança, novas cópias de segurança falharão.

Para parar a proteção de uma base de dados:

1. No painel do cofre, selecione **Itens de backup**.

2. Em **'Backup Management Type',** selecione **SQL em Azure VM**.

    ![Selecione SQL em Azure VM](./media/backup-azure-sql-database/sql-restore-backup-items.png)

3. Selecione a base de dados para a qual pretende parar a proteção.

    ![Selecione a base de dados para parar a proteção](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

4. No menu base de dados, selecione **Parar a cópia de segurança**.

    ![Selecione parar a cópia de segurança](./media/backup-azure-sql-database/stop-db-button.png)

5. No menu **Stop Backup,** selecione se conserva ou elimina os dados. Se quiser, forneça uma razão e comentou.

    ![Reter ou eliminar dados no menu Stop Backup](./media/backup-azure-sql-database/stop-backup-button.png)

6. Selecione **parar a cópia de segurança**.

> [!NOTE]
>
>Para obter mais informações sobre a opção de eliminar dados, consulte as FAQ abaixo:
>
>- [Se eu apagar uma base de dados de uma instância autoprotegida, o que acontecerá com as cópias de segurança?](faq-backup-sql-server.md#if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups)
>- [Se eu parar a operação de reserva de uma base de dados autoprotegida qual será o seu comportamento?](faq-backup-sql-server.md#if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior)
>
>

## <a name="resume-protection-for-a-sql-database"></a>Retomar a proteção para uma base de dados SQL

Quando parar a proteção para a base de dados SQL, se selecionar a opção Reter Dados de **Backup,** poderá posteriormente retomar a proteção. Se não reter os dados de cópia de segurança, não pode retomar a proteção.

Para retomar a proteção de uma base de dados SQL:

1. Abra o item de reserva e selecione **cópia de segurança Do Currículo**.

    ![Selecione retomar a cópia de segurança para retomar a proteção da base de dados](./media/backup-azure-sql-database/resume-backup-button.png)

2. No menu de **política de backup,** selecione uma apólice e, em seguida, selecione **Guardar**.

## <a name="run-an-on-demand-backup"></a>Faça um backup a pedido

Pode executar diferentes tipos de backups a pedido:

- Cópia de segurança completa
- Cópia de cópia cópia completa
- Cópia de segurança diferencial
- Cópia de segurança de registo

Embora necessite de especificar a duração de retenção para cópia de cópia de cópia completa, o intervalo de retenção para a cópia de segurança completa a pedido será automaticamente definido para 45 dias a partir da hora atual.

Para mais informações, consulte os tipos de [backup do SQL Server](backup-architecture.md#sql-server-backup-types).

## <a name="unregister-a-sql-server-instance"></a>Desregistre uma instância do Servidor SQL

Desregilhe uma instância do Servidor SQL depois de desativar a proteção, mas antes de eliminar o cofre:

1. No painel do cofre, em **Manage**, selecione **Backup Infrastructure**.  

   ![Selecione Infraestrutura de backup](./media/backup-azure-sql-database/backup-infrastructure-button.png)

2. Em **servidores de gestão,** selecione **Servidores Protegidos**.

   ![Selecione Servidores Protegidos](./media/backup-azure-sql-database/protected-servers.png)

3. Nos **Servidores Protegidos,** selecione o servidor para não registar. Para eliminar o cofre, deve desmarcar todos os servidores.

4. Clique no servidor protegido e selecione **Desregistar**.

   ![Selecione Excluir](./media/backup-azure-sql-database/delete-protected-server.jpg)

## <a name="modify-policy"></a>Modificar a política

Modifique a política para alterar a frequência de reserva ou o intervalo de retenção.

> [!NOTE]
> Qualquer alteração no período de retenção será aplicada retrospetivamente a todos os pontos de recuperação mais antigos, além dos novos.

No painel de instrumentos do cofre, vá à **Gestão** > de Políticas de**Backup** e escolha a política que pretende editar.

  ![Gerir a política de backup](./media/backup-azure-sql-database/modify-backup-policy.png)

  ![Modificar a política de backup](./media/backup-azure-sql-database/modify-backup-policy-impact.png)

A modificação da política afetará todos os itens de backup associados e desencadeará trabalhos de **proteção configurante** correspondentes.

### <a name="inconsistent-policy"></a>Política inconsistente

Por vezes, uma operação política modificada pode levar a uma versão política **inconsistente** para alguns itens de backup. Isto acontece quando o trabalho de **proteção configurado** correspondente falha no item de reserva após o desencadeamento de uma operação de modificação da política. Aparece da seguinte forma na vista do item de reserva:

  ![Política inconsistente](./media/backup-azure-sql-database/inconsistent-policy.png)

Pode corrigir a versão política de todos os itens impactados num clique:

  ![Fixar política inconsistente](./media/backup-azure-sql-database/fix-inconsistent-policy.png)

## <a name="re-register-extension-on-the-sql-server-vm"></a>Re-registe a extensão no VM do Servidor SQL

Por vezes, a extensão da carga de trabalho no VM pode ser impactada por uma razão ou outra. Nesses casos, todas as operações desencadeadas no VM começarão a falhar. Poderá então ter de voltar a registar a extensão do VM. **O reregisto** da operação reinstala a extensão de reserva de carga de trabalho no VM para que as operações continuem.

Utilize esta opção com cuidado; quando acionado num VM com uma extensão já saudável, esta operação fará com que a extensão seja reiniciada. Isto pode resultar em todos os postos de trabalho em curso a falhar. Verifique se há um ou mais [sintomas](backup-sql-server-azure-troubleshoot.md#re-registration-failures) antes de desencadear a operação de reregisto.

## <a name="next-steps"></a>Passos seguintes

Para mais informações, consulte as cópias de segurança da Troubleshoot numa base de dados do [SQL Server](backup-sql-server-azure-troubleshoot.md).
