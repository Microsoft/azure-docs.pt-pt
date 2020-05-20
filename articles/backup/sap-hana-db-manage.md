---
title: Gerir bases de dados SAP HANA apoiadas em VMs Azure
description: Neste artigo, aprenda tarefas comuns para gerir e monitorizar as bases de dados SAP HANA que estão a funcionar em máquinas virtuais Azure.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: e3705750e32b8b34ed397b8f68f22b0728129266
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83701117"
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

### <a name="run-an-on-demand-backup"></a>Faça um backup a pedido

Os backups funcionam de acordo com o calendário de apólices. Pode executar um backup a pedido da seguinte forma:

1. No menu do cofre, clique em **itens de backup**.
2. Em Itens de **Backup,** selecione o VM que executa a base de dados SAP HANA e, em seguida, clique em **Backup agora**.
3. Em **Backup Now,** utilize o controlo do calendário para selecionar o último dia em que o ponto de recuperação deve ser mantido. Em seguida, clique em **OK**.
4. Monitorize as notificações do portal. Você pode monitorizar o progresso do trabalho no painel de instrumentos do cofre > **Backup Jobs**  >  **Em andamento.** Dependendo do tamanho da sua base de dados, a criação da cópia de segurança inicial pode demorar algum tempo.

### <a name="hana-native-client-integration"></a>Integração de clientes nativos da HANA

#### <a name="backup"></a>Cópia de segurança

Os backups a pedido desencadeados por qualquer um dos clientes nativos da HANA (para **Backint)** aparecerão na lista de backup na página **'Backup Itens'.**

![Última corrida de backups](./media/sap-hana-db-manage/last-backups.png)

Também pode [monitorizar estes backups](https://docs.microsoft.com/azure/backup/sap-hana-db-manage#monitor-manual-backup-jobs-in-the-portal) a partir da página **de backup.**

Estes backups a pedido também aparecerão na lista de pontos de restauro para restauro.

![Lista de pontos de restauro](./media/sap-hana-db-manage/list-restore-points.png)

#### <a name="restore"></a>Restauro

Os restauros desencadeados por clientes nativos da HANA (utilizando **backint)** para restaurar a mesma máquina podem ser [monitorizados](https://docs.microsoft.com/azure/backup/sap-hana-db-manage#monitor-manual-backup-jobs-in-the-portal) a partir da página de **trabalhos de backup.**

### <a name="run-sap-hana-native-client-backup-on-a-database-with-azure-backup-enabled"></a>Executar backup de cliente nativo SAP HANA em uma base de dados com backup Azure ativado

Se quiser obter uma cópia de segurança local (usando o HANA Studio / Cockpit) de uma base de dados que está a ser apoiada com o Azure Backup, faça o seguinte:

1. Aguarde quaisquer cópias de segurança completas ou de registo para que a base de dados termine. Verifique o estado no Estúdio SAP HANA/ Cockpit.
2. Desative as cópias de segurança e desative as cópias de segurança e desative o catálogo de cópias de segurança no sistema de ficheiros para uma base de dados relevante.
3. Para isso, clique **systemdb**duas vezes na  >  **configuração**do  >  **sistema, selecione**filtro de base de dados  >  **(Log)**.
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

### <a name="modify-policy"></a>Modificar a Política

Modifique a política para alterar os tipos de backup, frequências e intervalo de retenção.

>[!NOTE]
>Qualquer alteração no período de retenção será aplicada retroativamente a todos os pontos de recuperação mais antigos, além dos novos.

1. No painel de instrumentos do cofre, vá à **Manage > Backup Policies** e escolha a política que pretende editar.

   ![Escolha a política para editar](./media/sap-hana-db-manage/manage-backup-policies.png)

1. Selecione **Modificar**.

   ![Selecione Modificar](./media/sap-hana-db-manage/modify-policy.png)

1. Escolha a frequência para os tipos de cópia de segurança.

   ![Escolha a frequência de backup](./media/sap-hana-db-manage/choose-frequency.png)

A modificação da política afetará todos os itens de backup associados e desencadeará trabalhos de **proteção configurante** correspondentes.

### <a name="inconsistent-policy"></a>Política inconsistente

Ocasionalmente, uma operação política modificada pode levar a uma versão política **inconsistente** para alguns itens de backup. Isto acontece quando o trabalho de **proteção configurado** correspondente falha no item de reserva após o desencadeamento de uma operação de modificação da política. Aparece da seguinte forma na vista do item de reserva:

![Política inconsistente](./media/sap-hana-db-manage/inconsistent-policy.png)

Pode corrigir a versão política de todos os itens impactados num clique:

![Corrigir versão política](./media/sap-hana-db-manage/fix-policy-version.png)

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

### <a name="upgrading-from-sdc-to-mdc"></a>Upgrade de SDC para MDC

Saiba como continuar a fazer backup para uma base de dados SAP HANA [após a atualização de SDC para MDC](backup-azure-sap-hana-database-troubleshoot.md#sdc-to-mdc-upgrade-with-a-change-in-sid).

### <a name="upgrading-from-sdc-to-mdc-without-a-sid-change"></a>Upgrade de SDC para MDC sem alteração SID

Saiba como continuar a cópia de segurança de uma base de dados SAP HANA cujo SID não mudou após a [atualização de SDC para MDC](backup-azure-sap-hana-database-troubleshoot.md#sdc-to-mdc-upgrade-with-no-change-in-sid).

### <a name="unregister-an-sap-hana-instance"></a>Desregistre uma instância SAP HANA

Desregilhe uma instância SAP HANA depois de desativar a proteção, mas antes de eliminar o cofre:

* No painel do cofre, em **Manage**, selecione **Backup Infrastructure**.

   ![Selecione Infraestrutura de backup](./media/sap-hana-db-manage/backup-infrastructure.png)

* Selecione o tipo de **Gestão de Backup** como **Carga de Trabalho em VM Azure**

   ![Selecione o tipo de Gestão de Backup como Carga de Trabalho em VM Azure](./media/sap-hana-db-manage/backup-management-type.png)

* Nos **Servidores Protegidos,** selecione a instância para não registar. Para eliminar o cofre, deve desmarcar todos os servidores/instâncias.

* Clique na instância protegida e selecione **Não registar**.

   ![Selecione não registar](./media/sap-hana-db-manage/unregister.png)

### <a name="re-register-extension-on-the-sap-hana-server-vm"></a>Re-registe a extensão do servidor SAP HANA VM

Por vezes, a extensão da carga de trabalho no VM pode ser impactada por uma razão ou outra. Nesses casos, todas as operações desencadeadas no VM começarão a falhar. Poderá então ter de voltar a registar a extensão do VM. O reregisto da operação reinstala a extensão de reserva de carga de trabalho no VM para que as operações continuem.

Utilize esta opção com cuidado: quando acionado num VM com uma extensão já saudável, esta operação fará com que a extensão seja reiniciada. Isto pode fazer com que todos os postos de trabalho em curso falhem. Verifique se há um ou mais [sintomas](backup-azure-sap-hana-database-troubleshoot.md#re-registration-failures) antes de desencadear a operação de reregisto.

## <a name="next-steps"></a>Passos seguintes

* Aprenda a [resolver problemas comuns ao apoiar as bases de dados SAP HANA.](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot)
