---
title: Gerir e monitorizar os DBs do Servidor SQL num VM Azure
description: Este artigo descreve como gerir e monitorizar bases de dados do SQL Server que estão a funcionar num VM Azure.
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: e37e6fc211b34b7e427b66db374a705faafd25f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97858743"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>Gerir e monitorizar bases de dados do SQL Server guardadas em cópia de segurança

Este artigo descreve tarefas comuns para gerir e monitorizar bases de dados do SQL Server que estão a ser executadas numa máquina virtual Azure (VM) e que são apoiadas até um cofre dos Serviços de Recuperação de Backup Azure pelo serviço [Azure Backup.](backup-overview.md) Você vai aprender a monitorizar empregos e alertas, parar e retomar a proteção da base de dados, executar trabalhos de backup, e não registar um VM de backups.

Se ainda não tiver configurado cópias de segurança para as bases de dados do SQL Server, consulte [bases de dados do SQL Server em VMs Azure](backup-azure-sql-database.md)

## <a name="monitor-backup-jobs-in-the-portal"></a>Monitorize trabalhos de backup no portal

O Azure Backup mostra todas as operações programadas e a pedido sob **trabalhos de Backup** no portal, exceto as cópias de segurança programadas, uma vez que podem ser muito frequentes. Os trabalhos que você vê neste portal incluem descoberta e registo de bases de dados, configurar backup, e backup e restaurar operações.

![O portal de empregos de reserva](./media/backup-azure-sql-database/sql-backup-jobs-list.png)

Para mais informações sobre os cenários de monitorização, vá à [Monitorização no portal Azure](backup-azure-monitoring-built-in-monitor.md) e monitorização utilizando o [Monitor Azure](backup-azure-monitoring-use-azuremonitor.md).  

## <a name="view-backup-alerts"></a>Ver alertas de backup

Como os backups de registo ocorrem a cada 15 minutos, monitorizar trabalhos de reserva pode ser aborrecido. O Azure Backup facilita a monitorização enviando alertas de e-mail. Os alertas de e-mail são:

- Desencadeado por todas as falhas de reserva.
- Consolidado ao nível da base de dados por código de erro.
- Enviado apenas para a primeira falha de reserva de uma base de dados.

Para monitorizar os alertas de backup da base de dados:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. No painel de **instrumentos** do cofre, selecione Alertas de Reserva .

   ![Selecione alertas de backup](./media/backup-azure-sql-database/sql-backup-alerts-list.png)

## <a name="stop-protection-for-a-sql-server-database"></a>Parar a proteção para uma base de dados do SQL Server

Pode parar de fazer o backup de uma base de dados do SQL Server de várias maneiras:

- Pare com todos os futuros trabalhos de reserva e elimine todos os pontos de recuperação.
- Parem com todos os futuros trabalhos de reserva e deixem os pontos de recuperação intactos.

Se optar por deixar pontos de recuperação, lembre-se destes detalhes:

- Todos os pontos de recuperação permanecerão intactos para sempre, e todas as poda pararão na proteção stop com os dados de retenção.
- Será cobrado pela instância protegida e pelo armazenamento consumido. Para mais informações, consulte [os preços de backup da Azure.](https://azure.microsoft.com/pricing/details/backup/)
- Se eliminar uma fonte de dados sem parar as cópias de segurança, novas cópias de segurança falharão. Os antigos pontos de recuperação caducarão de acordo com a apólice, mas o ponto de recuperação mais recente será sempre mantido até parar as cópias de segurança e apagar os dados.

Para parar a proteção de uma base de dados:

1. No painel de instrumentos do cofre, selecione **Itens de Reserva**.

2. Sob **o Tipo de Gestão de Cópias** de Segurança , selecione **SQL em Azure VM**.

    ![Selecione SQL em Azure VM](./media/backup-azure-sql-database/sql-restore-backup-items.png)

3. Selecione a base de dados para a qual pretende parar a proteção.

    ![Selecione a base de dados para parar a proteção](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

4. No menu de base de **dados, selecione Parar a cópia de segurança**.

    ![Selecione Parar backup](./media/backup-azure-sql-database/stop-db-button.png)

5. No menu **Stop Backup,** selecione se deve reter ou apagar dados. Se quiser, forneça uma razão e comente.

    ![Reter ou eliminar dados no menu Stop Backup](./media/backup-azure-sql-database/stop-backup-button.png)

6. **Selecione Parar de fazer backup**.

> [!NOTE]
>
>Para obter mais informações sobre a opção de eliminar dados, consulte as FAQ abaixo:
>
>- [Se eliminar uma base de dados de uma instância autoprotegida, o que acontecerá com as cópias de segurança?](faq-backup-sql-server.md#if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups)
>- [Se eu parar a operação de backup de uma base de dados autoprotegido, qual será o seu comportamento?](faq-backup-sql-server.md#if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior)
>
>

## <a name="resume-protection-for-a-sql-database"></a>Retomar a proteção para uma base de dados SQL

Quando parar a proteção para a base de dados SQL, se selecionar a opção **'Retenha dados de cópia de segurança',** poderá retomar a proteção posteriormente. Se não reter os dados de reserva, não pode retomar a proteção.

Para retomar a proteção de uma base de dados SQL:

1. Abra o item de cópia de segurança e **selecione Retomar a cópia de segurança**.

    ![Selecione retomar a cópia de segurança para retomar a proteção da base de dados](./media/backup-azure-sql-database/resume-backup-button.png)

2. No menu **de política de cópia de segurança,** selecione uma política e, em seguida, selecione **Guardar**.

## <a name="run-an-on-demand-backup"></a>Executar uma cópia de segurança a pedido

Pode executar diferentes tipos de backups a pedido:

- Cópia de segurança completa
- Cópia-apenas cópia de cópia
- Cópia de segurança diferencial
- Cópia de segurança de registo

Embora seja necessário especificar a duração de retenção para cópia de segurança completa, o intervalo de retenção para cópia de segurança completa a pedido será automaticamente definido para 45 dias a partir do momento atual.

Para obter mais informações, consulte [os tipos de backup do SQL Server](backup-architecture.md#sql-server-backup-types).

## <a name="modify-policy"></a>Alterar a política

Modificar a política para alterar a frequência de backup ou o intervalo de retenção.

> [!NOTE]
> Qualquer alteração no período de retenção será aplicada retrospetivamente a todos os pontos de recuperação mais antigos, além dos novos.

No painel de abóbadas, vá a **Manage**  >  **Backup Policies** e escolha a política que pretende editar.

  ![Gerir a política de backup](./media/backup-azure-sql-database/modify-backup-policy.png)

  ![Modificar a política de backup](./media/backup-azure-sql-database/modify-backup-policy-impact.png)

A modificação da política terá impacto em todos os itens de backup associados e desencadeará os correspondentes trabalhos **de proteção de configuração.**

### <a name="inconsistent-policy"></a>Política inconsistente

Por vezes, uma operação de política modificada pode levar a uma versão política **inconsistente** para alguns itens de backup. Isto acontece quando o trabalho de **proteção de configuração** correspondente falha no item de backup após o funcionamento da política de modificação ser desencadeado. Aparece da seguinte forma na vista de produto de reserva:

  ![Política inconsistente](./media/backup-azure-sql-database/inconsistent-policy.png)

Pode corrigir a versão de política para todos os itens impactados num clique:

  ![Corrigir política inconsistente](./media/backup-azure-sql-database/fix-inconsistent-policy.png)

## <a name="unregister-a-sql-server-instance"></a>Desregralar uma instância do Servidor SQL

Desativar uma instância do SQL Server depois de desativar a proteção, mas antes de eliminar o cofre:

1. No painel de instrumentos do cofre, em **Manage**, selecione **Infraestrutura de backup**.  

   ![Selecione infraestrutura de backup](./media/backup-azure-sql-database/backup-infrastructure-button.png)

2. Em **Servidores de Gestão**, selecione **Servidores Protegidos**.

   ![Selecione Servidores Protegidos](./media/backup-azure-sql-database/protected-servers.png)

3. Em **Servidores Protegidos,** selecione o servidor para não registar. Para apagar o cofre, tem de não registar todos os servidores.

4. Clique com o botão direito no servidor protegido e selecione **Unregister**.

   ![Selecione Eliminar](./media/backup-azure-sql-database/delete-protected-server.jpg)

## <a name="re-register-extension-on-the-sql-server-vm"></a>Re-registrar extensão no SQL Server VM

Por vezes, a extensão da carga de trabalho no VM pode ser impactada por uma razão ou outra. Nesses casos, todas as operações desencadeadas no VM começarão a falhar. Poderá então ser necessário voltar a registar a extensão no VM. A operação **de re-registo** reinstala a extensão de backup da carga de trabalho no VM para que as operações continuem. Pode encontrar esta opção em **Infraestrutura de Backup** no cofre dos Serviços de Recuperação.

![Servidores protegidos em infraestrutura de backup](./media/backup-azure-sql-database/protected-servers-backup-infrastructure.png)

Utilize esta opção com cuidado. Quando acionado num VM com uma extensão já saudável, esta operação fará com que a extensão seja reiniciada. Isto pode fazer com que todos os empregos em curso falhem. Verifique se existem um ou mais [dos sintomas](backup-sql-server-azure-troubleshoot.md#re-registration-failures) antes de desencadear a operação de re-registo.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte [as cópias de segurança de resolução de problemas numa base de dados do SQL Server](backup-sql-server-azure-troubleshoot.md).
