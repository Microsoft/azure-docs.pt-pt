---
title: Restaurar bancos de dados SAP HANA em VMs do Azure
description: Neste artigo, descubra como restaurar SAP HANA bancos de dados que estão em execução em máquinas virtuais do Azure.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 999edba61177758ad9039e81e789efcef99ca1de
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74287921"
---
# <a name="restore-sap-hana-databases-on-azure-vms"></a>Restaurar bancos de dados SAP HANA em VMs do Azure

Este artigo descreve como restaurar SAP HANA bancos de dados que estão em execução na VM (máquina virtual) do Azure, que foi feito backup do serviço de backup do Azure em um cofre dos serviços de recuperação de backup do Azure. As restaurações podem ser usadas para criar cópias dos dados para cenários de desenvolvimento/teste ou para retornar a um estado anterior.

Para obter mais informações, sobre como fazer backup de bancos de dados do SAP HANA, consulte fazer backup de [bancos de dados do SAP Hana em VMs do Azure](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database).

## <a name="restore-to-a-point-in-time-or-to-a-recovery-point"></a>Restaurar para um ponto no tempo ou para um ponto de recuperação

O backup do Azure pode restaurar SAP HANA bancos de dados que estão em execução em VMs do Azure da seguinte maneira:

* Restaurar para uma data ou hora específica (para o segundo) usando backups de log. O backup do Azure determina automaticamente os backups completos e diferenciais apropriados e a cadeia de backups de log que são necessários para restaurar com base na hora selecionada.

* Restaure para um backup completo ou diferencial específico para restaurar para um ponto de recuperação específico.

## <a name="prerequisites"></a>Pré-requisitos

Antes de restaurar um banco de dados, observe o seguinte:

* Você pode restaurar o banco de dados somente para uma instância SAP HANA que esteja na mesma região

* A instância de destino deve ser registrada com o mesmo cofre que a origem

* O backup do Azure não pode identificar duas instâncias diferentes de SAP HANA na mesma VM. Portanto, a restauração de dados de uma instância para outra na mesma VM não é possível

* Para garantir que a instância de SAP HANA de destino esteja pronta para restauração, verifique seu status de **preparação de backup** :

  * Abra o cofre no qual a instância de SAP HANA de destino está registrada

  * No painel do cofre, em **introdução**, escolha **backup**

![Backup no painel do cofre](media/sap-hana-db-restore/getting-started-backup.png)

* Em **backup**, em **o que você deseja fazer backup?** escolha **SAP Hana na VM do Azure**

![Escolha SAP HANA na VM do Azure](media/sap-hana-db-restore/sap-hana-backup.png)

* Em **descobrir bancos de os em VMs** , clique em **Exibir detalhes**

![Ver detalhes](media/sap-hana-db-restore/view-details.png)

* Examinar a **prontidão de backup** da VM de destino

![Servidores protegidos](media/sap-hana-db-restore/protected-servers.png)

* Para saber mais sobre os tipos de restauração aos quais SAP HANA dá suporte, consulte a SAP HANA observação [1642148](https://launchpad.support.sap.com/#/notes/1642148)

## <a name="restore-a-database"></a>Restaurar uma base de dados

* Abra o cofre no qual o banco de dados do SAP HANA a ser restaurado está registrado

* No painel do cofre, em **itens protegidos**, escolha **itens de backup**

![Itens de backup](media/sap-hana-db-restore/backup-items.png)

* Em **itens de backup**, em **tipo de gerenciamento de backup** , selecione **SAP Hana na VM do Azure**

![Tipo de gerenciamento de backup](media/sap-hana-db-restore/backup-management-type.png)

* Selecione o banco de dados a ser restaurado

 ![Banco de dados a ser restaurado](media/sap-hana-db-restore/database-to-restore.png)

* Examine o menu banco de dados. Ele fornece informações sobre o backup de banco de dados, incluindo:

  * Os pontos de restauração mais antigos e mais recentes

  * O status do backup de log das últimas 24 e 72 horas para o banco de dados

![Menu banco de dados](media/sap-hana-db-restore/database-menu.png)

* Selecione **restaurar BD**

* Em **Restaurar configuração**, especifique onde (ou como) restaurar os dados:

  * **Local alternativo**: restaure o banco de dados para um local alternativo e mantenha o banco de dados de origem original.

  * **Substituir BD**: restaure os dados para a mesma instância de SAP Hana como a fonte original. Essa opção substitui o banco de dados original.

![Restaurar configuração](media/sap-hana-db-restore/restore-configuration.png)

### <a name="restore-to-alternate-location"></a>Restaurar para local alternativo

* No menu **configuração de restauração** , em **onde restaurar**, selecione **local alternativo**.

![Restaurar para local alternativo](media/sap-hana-db-restore/restore-alternate-location.png)

* Selecione o nome do host SAP HANA e o nome da instância para o qual você deseja restaurar o banco de dados.
* Verifique se a instância de SAP HANA de destino está pronta para restauração garantindo sua **prontidão de backup.** Consulte a [seção pré-requisitos](#prerequisites) para obter mais detalhes.
* Na caixa **nome do BD restaurado** , digite o nome do banco de dados de destino.

> [!NOTE]
> As restaurações de contêiner de Banco de Dados Individual (SDC) devem seguir estas [verificações](backup-azure-sap-hana-database-troubleshoot.md#single-container-database-sdc-restore).

* Se aplicável, selecione **substituir se o banco de BD com o mesmo nome já existir na instância do Hana selecionada**.
* Selecione **OK**.

![Restaurar configuração – tela final](media/sap-hana-db-restore/restore-configuration-last.png)

* Em **selecionar ponto de restauração**, selecione **logs (ponto no tempo)** para [restaurar para um ponto específico no tempo](#restore-to-a-specific-point-in-time). Ou selecione a **& total diferencial** para [restaurar para um ponto de recuperação específico](#restore-to-a-specific-recovery-point).

### <a name="restore-and-overwrite"></a>Restaurar e substituir

* No menu **configuração de restauração** , em **onde restaurar**, selecione **substituir DB** > **OK**.

![Substituir Base de Dados](media/sap-hana-db-restore/overwrite-db.png)

* Em **selecionar ponto de restauração**, selecione **logs (ponto no tempo)** para [restaurar para um ponto específico no tempo](#restore-to-a-specific-point-in-time). Ou selecione a **& total diferencial** para [restaurar para um ponto de recuperação específico](#restore-to-a-specific-recovery-point).

### <a name="restore-to-a-specific-point-in-time"></a>Restaurar para um ponto específico no tempo

Se você tiver selecionado **logs (ponto no tempo)** como o tipo de restauração, faça o seguinte:

* Selecione um ponto de recuperação no grafo de log e selecione **OK** para escolher o ponto de restauração.

![Ponto de restauração](media/sap-hana-db-restore/restore-point.png)

* No menu **restaurar** , selecione **restaurar** para iniciar o trabalho de restauração.

![Selecione restaurar](media/sap-hana-db-restore/restore-restore.png)

* Acompanhe o progresso da restauração na área **notificações** ou acompanhe-a selecionando **restaurar trabalhos** no menu banco de dados.

![Restauração disparada com êxito](media/sap-hana-db-restore/restore-triggered.png)

### <a name="restore-to-a-specific-recovery-point"></a>Restaurar para um ponto de recuperação específico

Se você tiver selecionado **& diferencial completo** como o tipo de restauração, faça o seguinte:

* Selecione um ponto de recuperação na lista e selecione **OK** para escolher o ponto de restauração.

![Restaurar ponto de recuperação específico](media/sap-hana-db-restore/specific-recovery-point.png)

* No menu **restaurar** , selecione **restaurar** para iniciar o trabalho de restauração.

![Iniciar trabalho de restauração](media/sap-hana-db-restore/restore-specific.png)

* Acompanhe o progresso da restauração na área **notificações** ou acompanhe-a selecionando **restaurar trabalhos** no menu banco de dados.

![Progresso da restauração](media/sap-hana-db-restore/restore-progress.png)

> [!NOTE]
> Em uma nova restauração de contêiner de banco de dados (MDC) depois que o BD do sistema é restaurado para uma instância de destino, é necessário executar o script de pré-registro novamente. Somente as restaurações subsequentes do banco de bancos de locatário serão realizadas com sucesso. Para saber mais, consulte [solução de problemas – a restauração MDC](backup-azure-sap-hana-database-troubleshoot.md#multiple-container-database-mdc-restore).

## <a name="next-steps"></a>Passos Seguintes

* [Saiba como gerenciar o](sap-hana-db-manage.md) backup de bancos de dados SAP Hana usando o backup do Azure
