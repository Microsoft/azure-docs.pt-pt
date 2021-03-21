---
title: Gerir bases de dados SAP HANA em VMs Azure
description: Neste artigo, aprenda tarefas comuns para gerir e monitorizar bases de dados SAP HANA que estão a funcionar em máquinas virtuais Azure.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: 54d3341a83873ad3cc50815f04a0b252bb44438e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101703771"
---
# <a name="manage-and-monitor-backed-up-sap-hana-databases"></a>Gerir e monitorizar bases de dados do SAP HANA guardadas em cópia de segurança

Este artigo descreve tarefas comuns para gerir e monitorizar bases de dados SAP HANA que estão a funcionar numa máquina virtual Azure (VM) e que são apoiadas até um cofre dos Serviços de Recuperação de Backup Azure pelo serviço [de backup Azure.](./backup-overview.md) Você vai aprender a monitorizar empregos e alertas, desencadear uma cópia de segurança a pedido, editar políticas, parar e retomar a proteção da base de dados e não registar um VM de backups.

Se ainda não tiver configurado backups para as suas bases de dados SAP HANA, consulte [as bases de dados SAP HANA em VMs Azure](./backup-azure-sap-hana-database.md).

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>Monitorize trabalhos de backup manual no portal

A Azure Backup mostra todos os trabalhos desencadeados manualmente na secção **de trabalhos de reserva** no portal Azure.

![Secção de trabalhos de reserva](./media/sap-hana-db-manage/backup-jobs.png)

Os trabalhos que você vê neste portal incluem descoberta e registo de bases de dados, e operações de backup e restauro. Os trabalhos programados, incluindo os backups de registos, não são mostrados nesta secção. As cópias de segurança ativadas manualmente dos clientes nativos da SAP HANA (Studio/ Cockpit/ DBA Cockpit) também não aparecem aqui.

![Lista de empregos de reserva](./media/sap-hana-db-manage/backup-jobs-list.png)

Para saber mais sobre monitorização, vá à [Monitorização no portal Azure](./backup-azure-monitoring-built-in-monitor.md) e monitorização utilizando o [Azure Monitor](./backup-azure-monitoring-use-azuremonitor.md).

## <a name="view-backup-alerts"></a>Ver alertas de backup

Os alertas são um meio fácil de monitorizar as cópias de segurança das bases de dados SAP HANA. Os alertas ajudam-no a focar-se nos eventos que mais gostas sem te perderes na multiplicidade de eventos que um backup gera. O Azure Backup permite-lhe definir alertas, e podem ser monitorizados da seguinte forma:

* Inicie sessão no [portal do Azure](https://portal.azure.com/).
* No painel de **instrumentos** do cofre, selecione Alertas de Reserva .

  ![Alertas de backup no painel de abóbadas](./media/sap-hana-db-manage/backup-alerts-dashboard.png)

* Poderá ver os alertas:

  ![Lista de alertas de backup](./media/sap-hana-db-manage/backup-alerts-list.png)

* Selecione os alertas para ver mais detalhes:

  ![Detalhes do alerta](./media/sap-hana-db-manage/alert-details.png)

Hoje, o Azure Backup permite o envio de alertas por e-mail. Estes alertas são:

* Desencadeado por todas as falhas de reserva.
* Consolidado ao nível da base de dados por código de erro.
* Enviado apenas para a primeira falha de reserva de uma base de dados.

Para saber mais sobre monitorização, vá à [Monitorização no portal Azure](./backup-azure-monitoring-built-in-monitor.md) e monitorização utilizando o [Azure Monitor](./backup-azure-monitoring-use-azuremonitor.md).

## <a name="management-operations"></a>Operações de Gestão

O Azure Backup facilita a gestão de uma base de dados SAP HANA apoiada com uma abundância de operações de gestão que suporta. Estas operações são discutidas mais detalhadamente nas seguintes secções.

### <a name="run-an-on-demand-backup"></a>Executar uma cópia de segurança a pedido

Os backups são executados de acordo com o calendário de apólices. Pode executar uma cópia de segurança a pedido da seguinte forma:

1. No menu do cofre, selecione **itens de reserva**.
2. Em **Itens de Cópia de Segurança,** selecione o VM que executa a base de dados SAP HANA e, em seguida, selecione **Backup agora**.
3. In **Backup Now,** escolha o tipo de cópia de segurança que pretende realizar. Em seguida, selecione **OK**. Esta cópia de segurança será mantida de acordo com a política associada a este item de backup.
4. Monitorize as notificações do portal. Pode monitorizar o progresso do trabalho no painel de segurança do cofre > **trabalhos de reserva**  >  **em curso**. Dependendo do tamanho da sua base de dados, a criação da cópia de segurança inicial pode demorar algum tempo.

Por padrão, a retenção de backups a pedido é de 45 dias.

### <a name="hana-native-client-integration"></a>Integração de clientes nativos HANA

#### <a name="backup"></a>Backup

Cópias de segurança a pedido desencadeadas por qualquer um dos clientes nativos da HANA (para **Backint)** aparecerão na lista de backup na página **de Itens de Cópia de Segurança.**

![Últimas cópias de segurança](./media/sap-hana-db-manage/last-backups.png)

Também pode [monitorizar estas cópias de segurança](#monitor-manual-backup-jobs-in-the-portal) a partir da página de **trabalhos de backup.**

Estes backups a pedido também aparecerão na lista de pontos de restauro para restauro.

![Lista de pontos de restauro](./media/sap-hana-db-manage/list-restore-points.png)

#### <a name="restore"></a>Restauro

Os restauros desencadeados por clientes nativos da HANA (usando **Backint**) para restaurar a mesma máquina podem ser [monitorizados](#monitor-manual-backup-jobs-in-the-portal) a partir da página **de trabalhos de backup.**

### <a name="run-sap-hana-native-client-backup-to-local-disk-on-a-database-with-azure-backup-enabled"></a>Faça backup de clientes nativos SAP HANA para o disco local em uma base de dados com Azure Backup ativado

Se quiser fazer uma cópia de segurança local (usando o HANA Studio / Cockpit) de uma base de dados que está a ser apoiada com a Azure Backup, faça o seguinte:

1. Aguarde quaisquer cópias de segurança completas ou de registo para que a base de dados termine. Verifique o estado no ESTÚDIO SAP HANA/ Cockpit.
2. para o DB relevante
    1. Descodifice os parâmetros de retronaturação. Para isso, clique duas vezes em **configuração do sistema**  >  **Selecione** o filtro  >  **de base de dados**  >  **(Log)**.
        * enable_auto_log_backup: Não
        * log_backup_using_backint: Falso
        * catalog_backup_using_backint:Falso
3. Faça uma cópia de segurança a pedido da base de dados
4. Em seguida, inverter os passos. Para o mesmo DB relevante acima mencionado,
    1. voltar a ativar os parâmetros de retroteta
        1. catalog_backup_using_backint:Verdade
        1. log_backup_using_backint: Verdadeiro
        1. enable_auto_log_backup: Sim

### <a name="manage-or-clean-up-the-hana-catalog-for-a-database-with-azure-backup-enabled"></a>Gerir ou limpar o catálogo HANA para uma base de dados com Azure Backup ativado

Se pretender editar ou limpar o catálogo de backup, faça o seguinte:

1. Aguarde quaisquer cópias de segurança completas ou de registo para que a base de dados termine. Verifique o estado no ESTÚDIO SAP HANA/ Cockpit.
2. para o DB relevante
    1. Descodifice os parâmetros de retronaturação. Para isso, clique duas vezes em **configuração do sistema**  >  **Selecione** o filtro  >  **de base de dados**  >  **(Log)**.
        * enable_auto_log_backup: Não
        * log_backup_using_backint: Falso
        * catalog_backup_using_backint:Falso
3. Editar o catálogo e remover as entradas mais antigas
4. Em seguida, inverter os passos. Para o mesmo DB relevante acima mencionado,
    1. voltar a ativar os parâmetros de retroteta
        1. catalog_backup_using_backint:Verdade
        1. log_backup_using_backint: Verdadeiro
        1. enable_auto_log_backup: Sim

### <a name="change-policy"></a>Alterar política

Pode alterar a política subjacente a um item de backup SAP HANA.

* No painel de abóbada, vá a **itens de reserva:**

  ![Selecione itens de backup](./media/sap-hana-db-manage/backup-items.png)

* Escolha **SAP HANA em Azure VM**

  ![Escolha SAP HANA em Azure VM](./media/sap-hana-db-manage/sap-hana-in-azure-vm.png)

* Escolha o item de backup cuja política subjacente pretende alterar
* Selecione a política de backup existente.

  ![Selecione a política de backup existente](./media/sap-hana-db-manage/existing-backup-policy.png)

* Mude a política, escolhendo a lista. [Criar uma nova política de backup,](./backup-azure-sap-hana-database.md#create-a-backup-policy) se necessário.

  ![Escolha a política da lista de drop-down](./media/sap-hana-db-manage/choose-backup-policy.png)

* Guarde as alterações

  ![Guarde as alterações](./media/sap-hana-db-manage/save-changes.png)

* A modificação da política terá impacto em todos os itens de backup associados e desencadeará os correspondentes trabalhos **de proteção de configuração.**

>[!NOTE]
> Qualquer alteração no período de retenção será aplicada retrospetivamente a todos os pontos de recuperação mais antigos, além dos novos.

### <a name="modify-policy"></a>Alterar Política

Modifique a política para alterar tipos de backup, frequências e intervalo de retenção.

>[!NOTE]
>Qualquer alteração no período de retenção será aplicada retroativamente a todos os pontos de recuperação mais antigos, para além dos novos.

1. No painel de abóbadas, vá a **Gerir > Políticas de Backup** e escolha a política que pretende editar.

   ![Escolha a política para editar](./media/sap-hana-db-manage/manage-backup-policies.png)

1. Selecione **Modificar**.

   ![Selecione Modificar](./media/sap-hana-db-manage/modify-policy.png)

1. Escolha a frequência para os tipos de backup.

   ![Escolha a frequência de backup](./media/sap-hana-db-manage/choose-frequency.png)

A modificação da política terá impacto em todos os itens de backup associados e desencadeará os correspondentes trabalhos **de proteção de configuração.**

### <a name="inconsistent-policy"></a>Política inconsistente

Ocasionalmente, uma operação de modificação da política pode levar a uma versão política **inconsistente** para alguns itens de backup. Isto acontece quando o trabalho de **proteção de configuração** correspondente falha no item de backup após o funcionamento da política de modificação ser desencadeado. Aparece da seguinte forma na vista de produto de reserva:

![Política inconsistente](./media/sap-hana-db-manage/inconsistent-policy.png)

Pode corrigir a versão de política para todos os itens impactados num clique:

![Corrigir versão política](./media/sap-hana-db-manage/fix-policy-version.png)

### <a name="stop-protection-for-an-sap-hana-database"></a>Parar a proteção para uma base de dados SAP HANA

Pode parar de proteger uma base de dados SAP HANA de várias maneiras:

* Pare todos os futuros trabalhos de backup e elimine todos os pontos de recuperação.
* Parem com todos os futuros trabalhos de reserva e deixem os pontos de recuperação intactos.

Se optar por deixar pontos de recuperação, lembre-se destes detalhes:

* Todos os pontos de recuperação permanecerão intactos para sempre, e todas as poda pararão na proteção stop com os dados de retenção.
* Será cobrado pela instância protegida e pelo armazenamento consumido. Para mais informações, consulte [os preços de backup da Azure.](https://azure.microsoft.com/pricing/details/backup/)
* Se eliminar uma fonte de dados sem parar as cópias de segurança, novas cópias de segurança falharão.

Para parar a proteção de uma base de dados:

* No painel de instrumentos do cofre, selecione **Itens de Reserva**.
* Sob **o Tipo de Gestão de Backup**, selecione SAP **HANA em Azure VM**

  ![Selecione SAP HANA em Azure VM](./media/sap-hana-db-manage/sap-hana-azure-vm.png)

* Selecione a base de dados para a qual pretende parar a proteção em:

  ![Selecione base de dados para parar a proteção](./media/sap-hana-db-manage/select-database.png)

* No menu de base de **dados, selecione Parar a cópia de segurança**.

  ![Selecione stop backup](./media/sap-hana-db-manage/stop-backup.png)

* No menu **Stop Backup,** selecione se deve reter ou apagar dados. Se quiser, forneça uma razão e comente.

  ![Selecione reter ou eliminar dados](./media/sap-hana-db-manage/retain-backup-data.png)

* **Selecione Parar de fazer backup**.

### <a name="resume-protection-for-an-sap-hana-database"></a>Retomar a proteção para uma base de dados SAP HANA

Quando parar a proteção para a base de dados SAP HANA, se selecionar a opção **'Retenha dados de reserva',** poderá retomar a proteção posteriormente. Se não reter os dados de back-up, não pode retomar a proteção.

Para retomar a proteção de uma base de dados SAP HANA:

* Abra o item de cópia de segurança e **selecione Retomar a cópia de segurança**.

   ![Selecione retomar a cópia de segurança](./media/sap-hana-db-manage/resume-backup.png)

* No menu **de política de cópia de segurança,** selecione uma política e, em seguida, selecione **Guardar**.

### <a name="upgrading-from-sdc-to-mdc"></a>Upgrade de SDC para MDC

Saiba como continuar a fazer backup para uma base de dados SAP HANA [depois de atualizar de SDC para MDC](backup-azure-sap-hana-database-troubleshoot.md#sdc-to-mdc-upgrade-with-a-change-in-sid).

### <a name="upgrading-from-sdc-to-mdc-without-a-sid-change"></a>Upgrade de SDC para MDC sem alteração sid

Saiba como continuar a fazer backup de uma base de dados SAP HANA cujo [SID não mudou após o upgrade de SDC para MDC](backup-azure-sap-hana-database-troubleshoot.md#sdc-to-mdc-upgrade-with-no-change-in-sid).

### <a name="upgrading-to-a-new-version-in-either-sdc-or-mdc"></a>Upgrade para uma nova versão em SDC ou MDC

Saiba como continuar a fazer backup de uma base de dados SAP HANA [cuja versão está a ser atualizada.](backup-azure-sap-hana-database-troubleshoot.md#sdc-version-upgrade-or-mdc-version-upgrade-on-the-same-vm)

### <a name="unregister-an-sap-hana-instance"></a>Não registre um caso SAP HANA

Desativar uma instância SAP HANA depois de desativar a proteção, mas antes de apagar o cofre:

* No painel de instrumentos do cofre, em **Manage**, selecione **Infraestrutura de backup**.

   ![Selecione infraestrutura de backup](./media/sap-hana-db-manage/backup-infrastructure.png)

* Selecione o **tipo de Gestão de Cópia de** Segurança como Workload em **Azure VM**

   ![Selecione o tipo de Gestão de Cópia de Segurança como Workload em Azure VM](./media/sap-hana-db-manage/backup-management-type.png)

* Em **Servidores Protegidos,** selecione a instância para não registar. Para eliminar o cofre, tem de não registar todos os servidores/instâncias.

* Clique com o botão direito na instância protegida e selecione **Unregister**.

   ![Selecione não registro](./media/sap-hana-db-manage/unregister.png)

### <a name="re-register-extension-on-the-sap-hana-server-vm"></a>Re-registrar extensão no servidor SAP HANA VM

Por vezes, a extensão da carga de trabalho no VM pode ser afetada por uma razão ou outra. Nesses casos, todas as operações desencadeadas no VM começarão a falhar. Poderá então ser necessário voltar a registar a extensão no VM. Re-registar a operação reinstala a extensão de backup da carga de trabalho no VM para que as operações continuem.

Utilize esta opção com cuidado: quando acionado num VM com uma extensão já saudável, esta operação fará com que a extensão seja reiniciada. Isto pode fazer com que todos os empregos em curso falhem. Verifique se existem um ou mais [dos sintomas](backup-azure-sap-hana-database-troubleshoot.md#re-registration-failures) antes de desencadear a operação de re-registo.

## <a name="next-steps"></a>Passos seguintes

* Saiba como [resolver problemas comuns ao fazer o backup das bases de dados SAP HANA.](./backup-azure-sap-hana-database-troubleshoot.md)
