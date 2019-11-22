---
title: Gerenciar backup de bancos de dados SAP HANA em VMs do Azure
description: Neste artigo, aprenda tarefas comuns para gerenciar e monitorar SAP HANA bancos de dados que estão em execução em máquinas virtuais do Azure.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: f76054c7c78c55a9754975267ee4fa3caab968a3
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74288350"
---
# <a name="manage-and-monitor-backed-up-sap-hana-databases"></a>Gerenciar e monitorar o backup de bancos de dados SAP HANA

Este artigo descreve as tarefas comuns para gerenciar e monitorar SAP HANA bancos de dados que estão em execução em uma VM (máquina virtual) do Azure e cujo backup é feito em um cofre dos serviços de recuperação de backup do Azure pelo serviço de [backup do Azure](https://docs.microsoft.com/azure/backup/backup-overview) . Você aprenderá como monitorar trabalhos e alertas, disparar um backup sob demanda, editar políticas, parar e retomar a proteção de banco de dados e cancelar o registro de uma VM de backups.

Se você ainda não configurou backups para seus bancos de dados SAP HANA, consulte [fazer backup de bancos de dados SAP Hana em VMs do Azure](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database).

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>Monitorar trabalhos de backup manuais no portal

O backup do Azure mostra todos os trabalhos disparados manualmente na seção **trabalhos de backup** em portal do Azure.

![Seção de trabalhos de backup](./media/sap-hana-db-manage/backup-jobs.png)

Os trabalhos que você vê nesse portal incluem descoberta de banco de dados e registro e operações de backup e restauração. Os trabalhos agendados, incluindo backups de log, não são mostrados nesta seção. Os backups disparados manualmente do SAP HANA clientes nativos (ferramenta cockpit de Studio/cockpit/DBA) também não aparecem aqui.

![Lista de trabalhos de backup](./media/sap-hana-db-manage/backup-jobs-list.png)

Para saber mais sobre monitoramento, acesse [monitoramento na portal do Azure](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor) e [monitoramento usando Azure monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

## <a name="view-backup-alerts"></a>Exibir alertas de backup

Alertas são uma maneira fácil de monitorar backups de bancos de dados do SAP HANA. Os alertas ajudam você a se concentrar nos eventos mais importantes sem se perder na infinidade de eventos que um backup gera. O backup do Azure permite que você defina alertas e eles podem ser monitorados da seguinte maneira:

* Iniciar sessão no [portal do Azure](https://portal.azure.com/).
* No painel do cofre, selecione **alertas de backup**.

  ![Alertas de backup no painel do cofre](./media/sap-hana-db-manage/backup-alerts-dashboard.png)

* Você poderá ver os alertas:

  ![Lista de alertas de backup](./media/sap-hana-db-manage/backup-alerts-list.png)

* Clique nos alertas para ver mais detalhes:

  ![Detalhes do alerta](./media/sap-hana-db-manage/alert-details.png)

Hoje, o backup do Azure permite o envio de alertas por email. Esses alertas são:

* Disparado para todas as falhas de backup.
* Consolidado no nível do banco de dados por código de erro.
* Enviado somente para a primeira falha de backup de um banco de dados.

À saiba mais sobre monitoramento, acesse [monitoramento na portal do Azure](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor) e [monitoramento usando Azure monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

## <a name="management-operations"></a>Operações de gerenciamento

O backup do Azure facilita o gerenciamento de um banco de dados SAP HANA de backup com uma abundância de operações de gerenciamento que ele suporta. Essas operações são discutidas mais detalhadamente nas seções a seguir.

### <a name="run-an-ad-hoc-backup"></a>Executar um backup ad hoc

Os backups são executados de acordo com o agendamento da política. Você pode executar um backup sob demanda da seguinte maneira:

1. No menu do cofre, clique em **itens de backup**.
2. Em **itens de backup**, selecione a VM que executa o banco de dados SAP Hana e clique em **fazer backup agora**.
3. Em **fazer backup agora**, use o controle de calendário para selecionar o último dia em que o ponto de recuperação deve ser retido. Em seguida, clique em **OK**.
4. Monitore as notificações do Portal. Você pode monitorar o andamento do trabalho no painel do cofre > **trabalhos de Backup** > **em andamento**. Dependendo do tamanho do banco de dados, a criação do backup inicial pode demorar um pouco.

### <a name="run-sap-hana-native-client-backup-on-a-database-with-azure-backup-enabled"></a>Executar SAP HANA backup de cliente nativo em um banco de dados com o backup do Azure habilitado

Se você quiser fazer um backup local (usando o HANA Studio/cockpit) de um banco de dados cujo backup está sendo feito com o backup do Azure, faça o seguinte:

1. Aguarde até que os backups completos ou de log do banco de dados sejam concluídos. Verifique o status no SAP HANA Studio/cockpit.
2. Desabilite os backups de log e defina o catálogo de backup para o sistema de arquivos para o banco de dados relevante.
3. Para fazer isso, clique duas vezes em systemdb **configuração** de >  > selecionar **filtro de > de banco de dados (log)** .
4. Defina **enable_auto_log_backup** como **não**.
5. Defina **log_backup_using_backint** como **false**.
6. Faça um backup completo ad hoc do banco de dados.
7. Aguarde até que o backup completo e o backup do catálogo sejam concluídos.
8. Reverter as configurações anteriores para as do Azure:
   * Defina **enable_auto_log_backup** como **Sim**.
   * Defina **log_backup_using_backint** como **true**.

### <a name="edit-underlying-policy"></a>Editar política subjacente

Modifique a política para alterar a frequência de backup ou o período de retenção:

* No painel do cofre, vá para **gerenciar** > **políticas de backup**

  ![Políticas de backup no painel do cofre](./media/sap-hana-db-manage/backup-policies-dashboard.png)

* Escolha a política que você deseja editar:

  ![Lista de políticas de backup](./media/sap-hana-db-manage/backup-policies-list.png)

  ![Detalhes da política de backup](./media/sap-hana-db-manage/backup-policy-details.png)

>[!NOTE]
> Qualquer alteração no período de retenção será aplicada de forma retrospectiva a todos os pontos de recuperação mais antigos além dos novos.
>
> As políticas de backup incremental não podem ser usadas para bancos de dados SAP HANA. Atualmente, não há suporte para backup incremental para esses bancos de dados.

### <a name="stop-protection-for-an-sap-hana-database"></a>Interromper a proteção de um banco de dados SAP HANA

Você pode interromper a proteção de um banco de dados SAP HANA de duas maneiras:

* Pare todos os trabalhos de backup futuros e exclua todos os pontos de recuperação.
* Pare todos os trabalhos de backup futuros e deixe os pontos de recuperação intactos.

Se você optar por deixar os pontos de recuperação, tenha em mente estes detalhes:

* Todos os pontos de recuperação permanecerão intactos para sempre, toda a remoção será interrompida em parar proteção com manter dados.
* Você será cobrado pela instância protegida e pelo armazenamento consumido. Para obter mais informações, consulte [preços do backup do Azure](https://azure.microsoft.com/pricing/details/backup/).
* Se você excluir uma fonte de dados sem interromper os backups, novos backups falharão.

Para interromper a proteção de um banco de dados:

* No painel do cofre, selecione **itens de backup**.
* Em **tipo de gerenciamento de backup**, selecione **SAP Hana na VM do Azure**

  ![Selecionar SAP HANA na VM do Azure](./media/sap-hana-db-manage/sap-hana-azure-vm.png)

* Selecione o banco de dados para o qual você deseja interromper a proteção:

  ![Selecionar Banco de dados para interromper a proteção](./media/sap-hana-db-manage/select-database.png)

* No menu banco de dados, selecione **parar backup**.

  ![Selecione parar backup](./media/sap-hana-db-manage/stop-backup.png)

* No menu **parar backup** , selecione se deseja reter ou excluir dados. Se desejar, forneça um motivo e um comentário.

  ![Selecionar reter ou excluir dados](./media/sap-hana-db-manage/retain-backup-data.png)

* Selecione **parar backup**.

### <a name="resume-protection-for-an-sap-hana-database"></a>Retomar a proteção para um banco de dados SAP HANA

Ao interromper a proteção para o banco de dados do SAP HANA, se você selecionar a opção de **manter o dado do backup** , poderá continuar a proteção. Se você não mantiver os dados de backup, não será possível retomar a proteção.

Para retomar a proteção de um banco de dados SAP HANA:

* Abra o item de backup e selecione **retomar backup**.

   ![Selecione retomar backup](./media/sap-hana-db-manage/resume-backup.png)

* No menu **política de backup** , selecione uma política e, em seguida, selecione **salvar**.

### <a name="upgrading-from-sap-hana-10-to-20"></a>Atualizando do SAP HANA 1,0 para 2,0

Saiba como continuar o backup de um banco de dados SAP HANA [após a atualização de SAP HANA 1,0 para 2,0](backup-azure-sap-hana-database-troubleshoot.md#upgrading-from-sap-hana-10-to-20).

### <a name="upgrading-without-a-sid-change"></a>Atualizando sem uma alteração de SID

Saiba como continuar o backup de um banco de dados SAP HANA cujo [Sid não foi alterado após a atualização](backup-azure-sap-hana-database-troubleshoot.md#upgrading-without-an-sid-change).

### <a name="unregister-an-sap-hana-database"></a>Cancelar o registro de um banco de dados SAP HANA

Cancele o registro de uma instância de SAP HANA depois de desabilitar a proteção, mas antes de excluir o cofre:

* No painel do cofre, em **gerenciar**, selecione **infraestrutura de backup**.

   ![Selecionar a infraestrutura de backup](./media/sap-hana-db-manage/backup-infrastructure.png)

* Selecione o **tipo de gerenciamento de backup** como **carga de trabalho na VM do Azure**

   ![Selecione o tipo de gerenciamento de backup como carga de trabalho na VM do Azure](./media/sap-hana-db-manage/backup-management-type.png)

* Em **servidores protegidos**, selecione a instância para cancelar o registro. Para excluir o cofre, você deve cancelar o registro de todos os servidores/instâncias.

* Clique com o botão direito do mouse na instância protegida e selecione **Cancelar registro**.

   ![Selecione cancelar registro](./media/sap-hana-db-manage/unregister.png)

## <a name="next-steps"></a>Passos seguintes

* Saiba como [solucionar problemas comuns ao fazer backup de bancos de dados do SAP Hana.](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot)

