---
title: Gerir bases de dados SAP HANA apoiadas em VMs Azure
description: Neste artigo, aprenda tarefas comuns para gerir e monitorizar as bases de dados SAP HANA que estão a funcionar em máquinas virtuais Azure.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: a9462f8608fc5ae35255ac321a0742b3f1834fde
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79252405"
---
# <a name="manage-and-monitor-backed-up-sap-hana-databases"></a>Gerir e monitorizar bases de dados do SAP HANA guardadas em cópia de segurança

Este artigo descreve tarefas comuns para gerir e monitorizar as bases de dados SAP HANA que estão a funcionar numa máquina virtual Azure (VM) e que são apoiadas até um cofre dos Serviços de Recuperação de Backup Azure pelo serviço [de backup Azure.](https://docs.microsoft.com/azure/backup/backup-overview) Você vai aprender a monitorizar empregos e alertas, desencadear um backup a pedido, editar políticas, parar e retomar a proteção de bases de dados e desregistar um VM a partir de backups.

Se ainda não configurar cópias de segurança para as suas bases de dados SAP HANA, consulte as bases de [dados SAP HANA em VMs Azure](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database).

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>Monitorize trabalhos de backup manual no portal

O Azure Backup mostra todos os trabalhos desencadeados manualmente na secção de **trabalhos de backup** no portal Azure.

![Secção de trabalhos de backup](./media/sap-hana-db-manage/backup-jobs.png)

Os trabalhos que você vê neste portal incluem descoberta e registo de bases de dados, e backup e restaurar operações. Os trabalhos programados, incluindo cópias de segurança não são mostrados nesta secção. Cópias de segurança acionadas manualmente dos clientes nativos SAP HANA (Studio/ Cockpit/ DBA Cockpit) também não aparecem aqui.

![Lista de empregos de backup](./media/sap-hana-db-manage/backup-jobs-list.png)

Para saber mais sobre monitorização, vá à [Monitorização no portal Azure](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor) e monitorização utilizando o [Azure Monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

## <a name="view-backup-alerts"></a>Ver alertas de backup

Os alertas são uma forma fácil de monitorizar as cópias de segurança das bases de dados SAP HANA. Os alertas ajudam-no a focar-se nos eventos com que mais se preocupa sem se perder na multiplicidade de eventos que um backup gera. O Azure Backup permite-lhe definir alertas e podem ser monitorizados da seguinte forma:

* Inicie sessão no [portal do Azure](https://portal.azure.com/).
* No painel do cofre, selecione **Alertas de Backup**.

  ![Alertas de backup no painel do cofre](./media/sap-hana-db-manage/backup-alerts-dashboard.png)

* Poderá ver os alertas:

  ![Lista de alertas de backup](./media/sap-hana-db-manage/backup-alerts-list.png)

* Clique nos alertas para ver mais detalhes:

  ![Detalhes do alerta](./media/sap-hana-db-manage/alert-details.png)

Hoje, o Azure Backup permite o envio de alertas por e-mail. Estes alertas são:

* Desencadeado por todas as falhas de reserva.
* Consolidado a nível da base de dados por código de erro.
* Enviado apenas para a primeira falha de reserva de uma base de dados.

ToTo saiba mais sobre monitorização, vá à [Monitorização no portal Azure](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor) e [monitorização utilizando o Monitor Azure](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

## <a name="management-operations"></a>Operações de Gestão

A Azure Backup facilita a gestão de uma base de dados SAP HANA apoiada com uma abundância de operações de gestão que suporta. Estas operações são discutidas mais detalhadamente nas seguintes secções.

### <a name="run-an-ad-hoc-backup"></a>Executar um backup ad-hoc

Os backups funcionam de acordo com o calendário de apólices. Pode executar um backup a pedido da seguinte forma:

1. No menu do cofre, clique em **itens de backup**.
2. Em Itens de **Backup,** selecione o VM que executa a base de dados SAP HANA e, em seguida, clique em **Backup agora**.
3. Em **Backup Now,** utilize o controlo do calendário para selecionar o último dia em que o ponto de recuperação deve ser mantido. Em seguida, clique em **OK**.
4. Monitorize as notificações do portal. Você pode monitorizar o progresso do trabalho no painel de abóbadas > **Backup Jobs** > **Em andamento**. Dependendo do tamanho da sua base de dados, a criação da cópia de segurança inicial pode demorar algum tempo.

### <a name="run-sap-hana-native-client-backup-on-a-database-with-azure-backup-enabled"></a>Executar backup de cliente nativo SAP HANA em uma base de dados com backup Azure ativado

Se quiser obter uma cópia de segurança local (usando o HANA Studio / Cockpit) de uma base de dados que está a ser apoiada com o Azure Backup, faça o seguinte:

1. Aguarde quaisquer cópias de segurança completas ou de registo para que a base de dados termine. Verifique o estado no Estúdio SAP HANA/ Cockpit.
2. Desative as cópias de segurança e desative as cópias de segurança e desative o catálogo de cópias de segurança no sistema de ficheiros para uma base de dados relevante.
3. Para isso, clique duplo no **sistema db** > **Configuração** > **Select Database** > **Filter (Log)** .
4. Definir **enable_auto_log_backup** para **Nº**.
5. Coloque **log_backup_using_backint** a **Falso**.
6. Pegue uma cópia de segurança completa da base de dados.
7. Aguarde o backup completo e o backup do catálogo termine.
8. Reverta as definições anteriores de volta às do Azure:
   * Definir **enable_auto_log_backup** para **Sim.**
   * Definir **log_backup_using_backint** para **true**.

### <a name="change-policy"></a>Mudar a política

Pode alterar a política subjacente a um item de reserva SAP HANA.

* No painel de instrumentos do cofre, vá a **itens de backup:**

  ![Selecione itens de backup](./media/sap-hana-db-manage/backup-items.png)

* Escolha **SAP HANA em Azure VM**

  ![Escolha SAP HANA em Azure VM](./media/sap-hana-db-manage/sap-hana-in-azure-vm.png)

* Escolha o item de backup cuja política subjacente quer mudar
* Clique na política de backup existente

  ![Selecione a política de backup existente](./media/sap-hana-db-manage/existing-backup-policy.png)

* Mude a política, escolhendo a partir da lista. [Criar uma nova política](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database#create-a-backup-policy) de backup, se necessário.

  ![Escolha a política da lista de drop-down](./media/sap-hana-db-manage/choose-backup-policy.png)

* Guarde as alterações

  ![Guarde as alterações](./media/sap-hana-db-manage/save-changes.png)

* A modificação da política afetará todos os itens de backup associados e desencadeará trabalhos de **proteção configurante** correspondentes.

>[!NOTE]
> Qualquer alteração no período de retenção será aplicada retrospetivamente a todos os pontos de recuperação mais antigos, além dos novos.
>
> As políticas incrementais de backup não podem ser utilizadas para bases de dados SAP HANA. A cópia de segurança incremental não é atualmente suportada para estas bases de dados.

### <a name="stop-protection-for-an-sap-hana-database"></a>Parar a proteção para uma base de dados SAP HANA

Pode parar de proteger uma base de dados SAP HANA de várias maneiras:

* Pare todos os futuros trabalhos de backup e elimine todos os pontos de recuperação.
* Pare todos os futuros trabalhos de reserva e deixe os pontos de recuperação intactos.

Se optar por deixar pontos de recuperação, tenha em mente estes detalhes:

* Todos os pontos de recuperação permanecerão intactos para sempre, todas as podas devem parar na proteção stop com dados de retenção.
* Será cobrado pela instância protegida e pelo armazenamento consumido. Para mais informações, consulte o preço de [Backup Azure](https://azure.microsoft.com/pricing/details/backup/).
* Se eliminar uma fonte de dados sem parar cópias de segurança, novas cópias de segurança falharão.

Para parar a proteção de uma base de dados:

* No painel do cofre, selecione **Itens de backup**.
* Sob **o Tipo de Gestão de Backup,** selecione **SAP HANA em VM Azure**

  ![Selecione SAP HANA em Azure VM](./media/sap-hana-db-manage/sap-hana-azure-vm.png)

* Selecione a base de dados para a qual pretende parar a proteção:

  ![Selecione base de dados para parar a proteção](./media/sap-hana-db-manage/select-database.png)

* No menu base de dados, selecione **Parar a cópia de segurança**.

  ![Selecione parar backup](./media/sap-hana-db-manage/stop-backup.png)

* No menu **Stop Backup,** selecione se conserva ou elimina os dados. Se quiser, forneça uma razão e comentou.

  ![Selecione reter ou eliminar dados](./media/sap-hana-db-manage/retain-backup-data.png)

* Selecione **parar a cópia de segurança**.

### <a name="resume-protection-for-an-sap-hana-database"></a>Retomar a proteção para uma base de dados SAP HANA

Quando parar a proteção para a base de dados SAP HANA, se selecionar a opção Reter Dados de **Backup,** poderá posteriormente retomar a proteção. Se não reter os dados de reserva, não poderá retomar a proteção.

Para retomar a proteção de uma base de dados SAP HANA:

* Abra o item de reserva e selecione **cópia de segurança Do Currículo**.

   ![Selecione retomar a cópia de segurança](./media/sap-hana-db-manage/resume-backup.png)

* No menu de **política de backup,** selecione uma apólice e, em seguida, selecione **Guardar**.

### <a name="upgrading-from-sap-hana-10-to-20"></a>Upgrade de SAP HANA 1.0 para 2.0

Saiba como continuar a fazer backup para uma base de dados SAP HANA [após a atualização de SAP HANA 1.0 a 2.0](backup-azure-sap-hana-database-troubleshoot.md#upgrading-from-sap-hana-10-to-20).

### <a name="upgrading-without-a-sid-change"></a>Upgrade sem uma mudança de SID

Saiba como continuar a cópia de segurança de uma base de dados SAP HANA cujo [SID não mudou após](backup-azure-sap-hana-database-troubleshoot.md#upgrading-without-an-sid-change)a atualização .

### <a name="unregister-an-sap-hana-database"></a>Desregistre uma base de dados SAP HANA

Desregilhe uma instância SAP HANA depois de desativar a proteção, mas antes de eliminar o cofre:

* No painel do cofre, em **Manage**, selecione **Backup Infrastructure**.

   ![Selecione Infraestrutura de backup](./media/sap-hana-db-manage/backup-infrastructure.png)

* Selecione o tipo de **Gestão de Backup** como **Carga de Trabalho em VM Azure**

   ![Selecione o tipo de Gestão de Backup como Carga de Trabalho em VM Azure](./media/sap-hana-db-manage/backup-management-type.png)

* Nos **Servidores Protegidos,** selecione a instância para não registar. Para eliminar o cofre, deve desmarcar todos os servidores/instâncias.

* Clique na instância protegida e selecione **Não registar**.

   ![Selecione não registar](./media/sap-hana-db-manage/unregister.png)

## <a name="next-steps"></a>Passos Seguintes

* Aprenda a [resolver problemas comuns ao apoiar as bases de dados SAP HANA.](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot)
